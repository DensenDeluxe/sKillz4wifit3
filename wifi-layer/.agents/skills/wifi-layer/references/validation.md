# Regression choices and project inspection

Use this matrix when implementing or reviewing a relevant change. These are test
designs condensed from report pp. 24-28, not a claim that every scenario currently
passes. Select cases that exercise the changed behavior; do not add every case
to an unrelated patch. Stable rule IDs are defined in the other references.

## Choose the affected boundary

| Area | Existing starting points |
| --- | --- |
| MPDU/RSN parser | `tests/wlan/test_parser.py`, `tests/dot11/test_ie_walkers.py` |
| Enterprise advertisement | `tests/wlan/test_wpa3_enterprise_pcap.py`, `tests/wlan/test_wpa3_enterprise_warnings.py` |
| EAP and EAPOL routing | `tests/wlan/test_eapol.py` |
| Handshake/crypto | `tests/wlan/test_sink.py`, `tests/dot11/test_wpa.py` |
| PMF | `tests/dot11/test_pmf.py` |
| AP/client state and warnings | `tests/wlan/test_sink.py`, `tests/models/test_security_warnings.py` |
| Multiple devices | `tests/wlan/test_dedupe.py`, `tests/wlan/test_array.py` |
| Channel lifecycle | `tests/wlan/test_channels.py`, `tests/wlan/test_lease.py`, `tests/wlan/test_replug.py` |

Run selected tests with `uv run pytest` and lint changed source paths with
`uv run ruff check`. For skill/reference-only edits validate metadata, paths,
rule references, and ignore rules; Python runtime tests do not prove improved
agent decisions. Do not run formatting or hardware scripts for these edits.

## High-value parser and security scenarios

| Scenario, paired with a nearby valid case | Required observation/assertion | Rules |
| --- | --- | --- |
| Same MPDU through declared raw/Radiotap adapters; short `it_len` | Same MPDU meaning for valid adapters; bad envelope rejected | CAP-001 |
| FCS retained/removed; payload happens to end with four CRC-like bytes | No speculative payload trimming | CAP-002 |
| Complete baseline/QoS header versus one byte short | No optional-field reads or false EAPOL on short input | MAC-001 |
| All DS combinations; unsupported A4 and Control input | Correct address roles or deliberate rejection, no phantom client | MAC-002/003 |
| IE exact end, zero length, dangling byte, overdeclared value | Distinct supported/invalid outcomes, no confident security from bad tail | IE-001 |
| Pairwise/AKM counts with complete list versus cut selector/count/tail | Bound nested fields; retain validity/partial status | RSN-001 |
| Same suite type under RSN and foreign OUI; same selector in AKM/cipher fields | Correct namespace/OUI handling | RSN-002 |
| Multiple AKMs reordered, known plus unknown suites/bits | Offered set preserved; no first-suite negotiation assumption | RSN-003/004 |
| Personal PSK+SAE AP and PSK-selected client | AP offer separate from concrete selection | WPA-001, STATE-002 |
| Enterprise mixed offer and different valid client selection | No retroactive AP/session conflation | WPA-001/002 |
| SHA-256 Enterprise AKM with incomplete PMF/cipher/profile evidence | No claim of a verified successful WPA3 session | WPA-002 |
| Suite-B-192 beacon without EAP/TLS evidence | Advertised candidate, no full end-to-end compliance claim | WPA-003 |
| MFPC/MFPR observations, successful association, protected traffic, invalid MIC | Keep each PMF evidence claim independent | PMF-001/002 |

Use the `WIFI-` prefix for the abbreviated IDs in these tables.

## Enterprise and key-exchange scenarios

| Scenario | Required observation/assertion | Rules |
| --- | --- | --- |
| EAPOL types 0-5 with similar-looking payloads | Correct dispatch or supported rejection, no cross-parser confusion | EAP-001 |
| Inner EAP Length below minimum, beyond outer body, exact, with padding | Invalid declarations rejected; padding ignored | EAP-002 |
| Request/Response versus four-byte Success/Failure | No invented Type on result messages | EAP-002 |
| Repeated request Identifier and later reuse/wrap | Retransmission distinguished from new session/context | EAP-003 |
| Valid ERP code outside current support | Unsupported does not become normative 'invalid EAP' | EAP-003 |
| Expanded unknown vendor tuple and existing WSC frames | Raw vendor metadata or existing WSC compatibility; no false method | EAP-004 |
| Type 26 vendor use and separate Type 29 assignment | Preserve numeric value/provenance; no blind substitution | EAP-008 |
| Fragmented EAP-TLS, empty fragment ACK, incomplete TLS record | Method-specific bounded reassembly; no premature TLS conclusions | EAP-005/006 |
| TLS 1.2/full, TLS 1.3, and resumed traces | Visibility matches actual negotiation; no 'missing cert' accusation | EAP-006 |
| PEAP/TTLS/TEAP outer method plus encrypted tunnel | Inner method, identities, and validation policy remain unknown | EAP-005/007 |
| Ordinary pairwise M1-M4 with retries, mismatched replay, wrong direction | Correlated candidates, no duplicate logical completion | KEY-001/002 |
| Group rekey between pairwise messages | Not counted as a pairwise four-way message | KEY-001 |
| New M1/nonce context before earlier exchange completes | Preserve separate plausible exchange generations | KEY-002 |
| Supported 16-byte versus 24-byte MIC format | Correct boundaries from established context, not guessed length | KEY-003 |
| Valid EAPOL PDU plus padding/FCS | MIC input ends at the PDU boundary | KEY-003 |
| Complete captured exchange without verifier keys | Observation complete can coexist with unverified integrity | KEY-004 |
| Enterprise EAPOL-Key/PMKID capture | No automatic WPA2-PSK-style password-verification assertion | KEY-005 |

## State and observation scenarios

| Scenario | Required observation/assertion | Rules |
| --- | --- | --- |
| FromDS frame whose SA is behind the AP | No extra client for the upstream source | STATE-001 |
| Security advertisement changes after a prior association | Retain historical session evidence | STATE-002 |
| Same frame received by two devices with different RSSIs | One logical update, appropriate per-device signal evidence | STATE-003 |
| Same sequence with different TID/fragment/payload; repeated Retry=1 | Do not assume global packet identity; test actual dedupe contract | MAC-004 |
| Locally emitted frame seen by RX | Preserve own-MAC/origin handling, no false remote action | STATE-003 |
| New randomized/LAA link identifier | No invented durable physical-device identity | STATE-004 |
| Same channel number in different bands; advertised/RX mismatch | Keep band/frequency and evidence sources separate | CHAN-001/002 |
| Hop/unplug/timeout after request versus explicit correlated reject/failure | Unknown observer outcome versus observed negative result | OBS-001/002 |

Derive expected fields independently using hand-checked wire bytes, known
vectors, or sanitized fixtures cross-checked with a named Wireshark/TShark
version. Compare relevant raw fields as well as display labels. A builder/parser
roundtrip can supplement this but is not an independent oracle. Distinguish
offline fixtures from hardware checks of a particular driver's RX contract.

## Inspected project boundaries

Inspected 2026-10-23 against working files based on Git HEAD `ecf61387`; reopen
before relying on them later. These observations locate future review work;
they are not a completed bug audit or authorization to change product behavior.

- `WlanFrameParser` consumes raw MPDU plus RSSI and rejects Control and A4/WDS.
  It returns typed packets or `None`; do not assume a new diagnostic result API.
- `_parse_eap` already bounds EAP lengths, handles four-byte result messages and
  Expanded vendor tuples, and preserves the existing WSC compatibility route.
- `eap.py` currently labels Type 26 `MSCHAPv2`; consider both Microsoft usage
  and IANA naming before treating that as an error. TEAP recognition/support
  should be checked independently of publication of RFC 9930.
- `_parse_rsn_ie` already retains full AKM selectors. The report's question
  'is only the type stored?' is not a confirmed absence. Optional-tail validity
  and cipher-list representation still need task-specific inspection.
- Enterprise advertisement classification already uses AKM, cipher, and PMF
  helpers. Do not replace it with a simpler AKM-only rule or claim the report
  proves that all existing labels are wrong.
- `eapol.py` declares `MIC_LEN = 16`; parser EAPOL-Key fields also use fixed
  ordinary-layout offsets. This is a support boundary to inspect before
  claiming Suite-B-192 key-frame support, even when its beacon is recognized.
- `StreamMerger.key` uses FC plus bytes `[4:24]`; it is not keyed only on a
  sequence number, and it does not include QoS TID. Test a demonstrated collision
  before changing cross-device dedupe behavior.
- `WlanArray` uses `submit_detailed` and own-MAC filtering; the sink already
  tracks `signal_by_card`. The report's proposed reception model is not proof
  that per-device RSSI is currently lost.

Per-chip FCS/decryption/padding/aggregation contracts, complete session-tracker
correctness, profile conformance, and live device behavior were not audited by
this skill-only change. Keep those limits explicit.
