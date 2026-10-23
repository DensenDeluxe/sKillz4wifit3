# Capture, frame layout, and observation state

Use for parsing, address roles, deduplication, channel behavior, or incomplete
captures. Rule IDs follow the supplied report, pp. 3-6 and 16-24. Implementation
cross-checks and project inferences below are distinguished from protocol rules.
Sources are indexed in [sources.md](sources.md).

## Capture and layout

**WIFI-CAP-001 — Decode the declared envelope.** Establish link type and the
producer's RX contract before interpreting an MPDU. For Radiotap, validate its
header and little-endian `it_len` against the captured bytes before advancing;
raw MPDU input must not be scanned for an imagined Radiotap header. USB RX
descriptors, Ethernet, and Radiotap belong to their respective input adapters.
Source: [S-RADIOTAP](sources.md#s-radiotap); adapter separation is project design.

**WIFI-CAP-002 — FCS and RX transformations require provenance.** Determine
whether the producer retains FCS, decrypts payloads, strips IV/ICV/MIC, inserts
DMA padding, splits A-MPDUs, or decapsulates A-MSDUs/802.3. Do not remove four
bytes merely because the frame is long enough. A CRC check can corroborate an
interpretation but does not replace the interface contract. A Protected bit and
plaintext can coexist after hardware decryption. A-MPDU and A-MSDU require
different handling. Sources: report pp. 3-6 and [S-RX](sources.md#s-rx).

**WIFI-MAC-001 — Layout precedes offsets.** Read only available Frame Control
bytes, determine supported type/subtype and relevant DS/QoS/control flags, then
check the full required header before accessing its fields. Twenty-four bytes
is a baseline for selected layouts, not a universal 802.11 header size. Derive
optional control-field presence from the applicable layout, not one global flag
rule. Source: [S-LINUX](sources.md#s-linux), header structures and QoS helpers.

**WIFI-MAC-002 — Preserve address roles.** For classical Data layouts:

| ToDS | FromDS | Addr1 | Addr2 | Addr3 | Addr4 |
| --- | --- | --- | --- | --- | --- |
| 0 | 0 | RA/DA | TA/SA | BSSID | absent |
| 0 | 1 | RA/DA | TA/BSSID | SA | absent |
| 1 | 0 | RA/BSSID | TA/SA | DA | absent |
| 1 | 1 | RA | TA | DA | SA |

This table does not define every management, control, mesh, or modern extension
layout. Source: [S-LINUX](sources.md#s-linux), DS-bit table and SA/DA helpers.

**WIFI-MAC-003 — Unsupported is a valid boundary.** Preserve deliberate rejection
of unsupported layouts instead of decoding them using neighboring offsets.
Returning `None` can remain the current API contract; this rule does not mandate
a new error enum. The current parser rejects Control and four-address WDS input.
Source: project inspection; see [validation.md](validation.md).

**WIFI-MAC-004 — Sequence numbers are scoped signals.** Account for transmitter,
frame class, fragment number, QoS TID, sequence reuse/wrap, Retry, and a bounded
time window when assessing dedupe collisions. A retried transmission can retain
Retry=1 across several attempts: the Retry bit does not uniquely identify each
attempt. Distinguish merging receiver copies from merging separate on-air
transmissions. Source: header fields in [S-LINUX](sources.md#s-linux); dedupe-key
choice is project inference, not an IEEE-prescribed tuple.

**WIFI-IE-001 — Bound each IE before interpreting it.** Require a complete
two-byte IE header, then `2 + declared_length <= remaining`. A dangling header
byte or overlong value is truncation. Zero length is not a generic end marker;
validate the specific IE's semantics separately. Keep useful partial evidence
marked as partial instead of treating the whole advertisement as valid. Source:
report p. 6; defensive parsing follows the TLV structure.

Parse LLC/SNAP and EtherType only at a validated MSDU boundary with known
plaintext/decryption status. Do not discover EAPOL by searching arbitrary
ciphertext, fragments, or A-MSDU contents. Existing DMA-padding accommodations
need a producer-specific justification before they are preserved or changed.

## Evidence and state

Use these conceptual distinctions without requiring a new schema in every task:

| Evidence | Meaning |
| --- | --- |
| unknown | Available evidence cannot establish the value. |
| not observed | The capture has no relevant observation; no negative claim follows. |
| malformed/truncated | Observed bytes cannot establish a valid complete structure. |
| observed positive/negative | A complete relevant field explicitly carries that value. |
| inconsistent | Valid observations conflict, or a combination contradicts its profile. |
| verified | A named stronger check actually succeeded; say which check. |

These are project modeling terms. Malformed input and unsupported-but-valid
protocol extensions are different cases. A parsed negative field is observation,
not automatically authenticated truth on a spoofable wireless medium.

**WIFI-STATE-001 — Infer wireless clients from wireless roles.** Do not create a
client solely from a bridged SA/DA behind the AP. Use DS direction and actual
station/session evidence; group destinations are not client identities.
Basis: WIFI-MAC-002 plus project inference.

**WIFI-STATE-002 — Keep AP and session evidence separate.** Preserve frame kind,
time, AP/STA context, and provenance for advertised versus selected security.
New beacons must not retroactively rewrite historical client selections.
Reassociation to a new BSSID is not automatically the old exchange. Missing EAP
may reflect caching, FT, resumption, or capture loss. Basis: report pp. 16-18;
[S-TTLS](sources.md#s-ttls) and [S-TEAP](sources.md#s-teap) on resumption.

**WIFI-STATE-003 — Dedupe logical frames, retain reception evidence.** Per-device
RSSI and source metadata must survive cross-device dedupe. Preserve the current
own-MAC/TX filtering contract; do not turn a locally emitted frame into independent
evidence of a remote action. A richer `receptions` model is an option, not a
required refactor. This is a project recommendation, not a new device capability.

**WIFI-STATE-004 — MAC is a link identifier.** A locally administered bit does
not prove randomization, a permanent device identity, or common ownership.
Avoid merging unrelated sessions based on an assumed stable physical identity.
Basis: report pp. 16-18; verify actual identity/roaming evidence for the task.

**WIFI-CHAN-001 — Frequency needs band context.** Keep channel number, band,
frequency, and width distinct. Do not generalize one 2.4/5 GHz formula to 6 GHz.
Technical tuning support is separate from regulatory permission. Source:
[S-CHANNEL](sources.md#s-channel), band-aware conversion and regulatory flags.

**WIFI-CHAN-002 — Advertisement is not RX metadata.** Preserve advertised primary
channel separately from measured reception frequency. A mutable current-channel
variable is not proof of the channel of an earlier buffered frame. This is a
project inference from the capture/receiver distinction.

**WIFI-OBS-001/002 — Observer loss is not protocol failure.** Hopping, timeout,
unplugging, or capture end can leave an outcome unknown. Keep these causes
separate from an observed EAP Failure or association reject, and correlate even
explicit results to the relevant session. Check lease/hopping cleanup on error
and cancellation. Basis: capture limits in [S-RX](sources.md#s-rx), EAP semantics
in [S-EAP](sources.md#s-eap), and project lifecycle inference.
