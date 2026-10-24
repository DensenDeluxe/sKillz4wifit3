# RSN, WPA classification, and PMF

Use for advertised/selected security and protection claims. Condensed from the
supplied report, pp. 6-10 and 21-24; sources checked on 2026-10-23 are indexed in
[sources.md](sources.md). Tables below are relevant subsets, not complete or
permanently current registries or certification requirements.

## Parsing and suite identity

**WIFI-RSN-001 — Bound RSNE fields and counts.** Validate version, each declared
list, and every present optional field against the enclosing IE. Preserve the
difference between a permitted omitted tail and a tail cut midway through its
count/selector. Interpret counts/capabilities in their specified byte order;
suite selectors are four wire bytes. Do not import compatibility defaults from
another parser without establishing the applicable format and omission rules.
Basis: report p. 6 and [S-HOSTAP](sources.md#s-hostap), implementation evidence.

**WIFI-RSN-002 — Compare all four bytes.** OUI plus type defines the selector.
AKM type 8 under another OUI is not RSN SAE. Keep AKM and cipher namespaces
separate: `00-0F-AC-0C` means different things in those two fields.

**WIFI-RSN-003/004 — Preserve lists and unknowns.** Retain offered suites and
their full identifiers; list order is not proof of client negotiation. Unknown
OUI/type/RSNXE bits remain unknown raw evidence. Missing or malformed RSN must
not silently replace known security with a confident OPEN classification.
Selector basis: [S-LINUX](sources.md#s-linux); preservation/state policy is a
project recommendation. Do not discard selectors absent from the subset below.

Relevant AKMs, checked against Linux v6.18 `WLAN_AKM_SUITE_*` definitions:

| Full selector (hex) | Meaning |
| --- | --- |
| 00-0F-AC-01 | 802.1X |
| 00-0F-AC-02 | PSK |
| 00-0F-AC-03 | FT over 802.1X |
| 00-0F-AC-04 | FT-PSK |
| 00-0F-AC-05 | 802.1X SHA-256 |
| 00-0F-AC-06 | PSK SHA-256; not SAE |
| 00-0F-AC-08 | SAE |
| 00-0F-AC-09 | FT over SAE |
| 00-0F-AC-0B | 802.1X Suite B |
| 00-0F-AC-0C | 802.1X Suite B-192 |
| 00-0F-AC-0D | FT 802.1X SHA-384 |
| 00-0F-AC-0E | FILS SHA-256 |
| 00-0F-AC-0F | FILS SHA-384 |
| 00-0F-AC-10 | FT-FILS SHA-256 |
| 00-0F-AC-11 | FT-FILS SHA-384 |
| 00-0F-AC-12 | OWE; not WPA3-Personal/Enterprise |
| 00-0F-AC-13 | FT-PSK SHA-384 |
| 00-0F-AC-14 | PSK SHA-384 |

Selectors past that header. Linux v6.18 `ieee80211.h` does not define them.
Checked 2026-10-23 against hostap `RSN_AUTH_KEY_MGMT_*` in
[S-HOSTAP-AKM](sources.md#s-hostap-akm). Types 24 and 25 are also named in
WPA3 Specification v3.5 ([S-WPA3](sources.md#s-wpa3)). That specification writes the
suite type in decimal after a colon, so `00-0F-AC:24` is decimal 24, the same
selector as `00-0F-AC-18` in this hex table. Wireshark's RSN key-management
table names 21, 24, and 25 and does not name 22 or 23, so it is not a complete list.

| Full selector (hex) | Meaning |
| --- | --- |
| 00-0F-AC-15 | PASN (decimal 21). Not in `SAE_AKM_SUITES` or `ENTERPRISE_AKM_SUITES` |
| 00-0F-AC-16 | FT-802.1X SHA-384 unrestricted (decimal 22). Distinct from 00-0F-AC-0D |
| 00-0F-AC-17 | 802.1X SHA-384 (decimal 23). Distinct from 00-0F-AC-05 and from Suite B-192 (00-0F-AC-0C) |
| 00-0F-AC-18 | SAE-EXT-KEY (decimal 24); SAE using a group-dependent hash. Same project family as 00-0F-AC-08 |
| 00-0F-AC-19 | FT-SAE-EXT-KEY (decimal 25). Same project family as 00-0F-AC-09 |

The last byte above is hex. The project sets store that byte as a decimal integer.
Decimal 22 is not a second spelling of 00-0F-AC-0D, and decimal 23 is not a second
spelling of 00-0F-AC-0C. Keep the full selector.

Relevant ciphers, same implementation source, `WLAN_CIPHER_SUITE_*`:

| Full selector (hex) | Meaning | Role |
| --- | --- | --- |
| 00-0F-AC-00 | Use Group Cipher | Context-dependent selector |
| 00-0F-AC-01 | WEP-40 | Legacy data |
| 00-0F-AC-02 | TKIP | Legacy data |
| 00-0F-AC-04 | CCMP-128 | Data |
| 00-0F-AC-05 | WEP-104 | Legacy data |
| 00-0F-AC-06 | BIP-CMAC-128 / AES-CMAC | Management |
| 00-0F-AC-08 | GCMP-128 | Data |
| 00-0F-AC-09 | GCMP-256 | Data |
| 00-0F-AC-0A | CCMP-256 | Data |
| 00-0F-AC-0B | BIP-GMAC-128 | Management |
| 00-0F-AC-0C | BIP-GMAC-256 | Management |
| 00-0F-AC-0D | BIP-CMAC-256 | Management |

## Evidence-based WPA classification

**WIFI-WPA-001 — Identify the observation stage.** Beacon/Probe Response is AP
advertisement. A valid Association Request is client proposal/selection evidence,
not AP acceptance; a matching successful response strengthens session evidence.
Neither alone proves subsequent EAP, key establishment, or authorized traffic.
This separation is a project inference grounded in the exchange roles.

**WIFI-WPA-002 — A suite name is not full WPA3 conformance.** Interpret AKMs with
the entire offered/selected suite set, data/management ciphers, PMF, legacy WPA
co-advertisement, band, and applicable profile version. Keep Personal and
Enterprise transition concepts separate. Exact Wi-Fi Alliance requirements must
be established from an accessible identified profile, not guessed from one AKM.

**WIFI-WPA-003 — Enterprise-192 has more than beacon evidence.** Suite-B-192,
GCMP-256, suitable management protection, and PMF provide relevant advertised
profile evidence. TLS/EAP/PKI requirements need their own evidence and the exact
profile version. Do not claim 192-bit end-to-end compliance from a beacon.
Basis: report pp. 8-10; [S-PROFILE](sources.md#s-profile) is vendor implementation
documentation, not the Wi-Fi Alliance specification.

Interpret these as examples, not a complete classifier or an instruction to
rename every existing product label:

| Observation | Supported interpretation | Still unestablished |
| --- | --- | --- |
| Fully parsed legacy WPA Vendor IE | Legacy WPA advertised | WPA2/WPA3 or client outcome |
| RSN PSK | PSK offered; WPA2-Personal-compatible context | Exclusive AP mode, client choice |
| SAE or FT-SAE, including SAE-EXT-KEY (24) and FT-SAE-EXT-KEY (25) | SAE-family capability offered. Project `SAE_AKM_SUITES` is `{8, 9, 24, 25}` | Successful SAE session, full profile compliance |
| PSK plus SAE | Personal transition candidate | Which suite a client selected |
| 802.1X | Enterprise AKM offered | Actual EAP method, success |
| 802.1X SHA-256 | WPA3-Enterprise profile candidate with other required evidence | A successful WPA3 client session |
| 802.1X plus 802.1X SHA-256 | Enterprise transition candidate subject to profile checks | Concrete client mode |
| Suite-B-192 and matching cipher/PMF evidence | Enterprise-192 advertised profile candidate | EAP/TLS/PKI conformance |
| OWE | OWE / Enhanced Open | Password or Enterprise authentication |
| 802.1X SHA-384 (decimal 23) or FT-802.1X SHA-384 unrestricted (decimal 22) | Enterprise AKM offered. `ENTERPRISE_AKM_SUITES` includes both. Not 00-0F-AC-0C and not 00-0F-AC-0D | EAP method, 192-bit profile, a successful client session |
| PASN (21) | PASN offered | Membership in the SAE or Enterprise sets |
| Unknown/malformed suites or conflicting valid data | Raw evidence plus unknown/conflict state | A confidently invented WPA generation |

## Project suite families

Checked against wifit3 Git HEAD `ecf61387` on 2026-10-23. Re-read the constants
before editing; this table is a snapshot, not a second definition.

| Set | Definition | Suites | What a consumer may claim |
| --- | --- | --- | --- |
| `SAE_AKM_SUITES` | `src/wifit3/dot11/rsn.py` | 8, 9, 24, 25 | SAE family, including group-dependent hash |
| `ENTERPRISE_AKM_SUITES` | `src/wifit3/dot11/rsn.py` | 1, 3, 5, 11–17, 22, 23 | Enterprise / FILS family |
| `PSK_CAPTURE_AKMS` | `src/wifit3/crack/handshake.py` | 2, 4, 6 | The product offers a PSK capture |
| PMKID harvestable | `src/wifit3/campaigns/pmkid.py` | 2 only | Narrower than `PSK_CAPTURE_AKMS` on purpose |
| OWE | suite 18, outside those sets | 18 | Enhanced Open, no passphrase capture |
| PSK SHA-384 | suites 19 and 20, outside `PSK_CAPTURE_AKMS` | 19, 20 | Known PSK AKMs with no capture claim |
| PASN | suite 21, outside the SAE and Enterprise sets | 21 | Not SAE and not Enterprise |

Import the set. Do not paste a fresh `{2, 4, 6}`, and do not drop 22–25 because
they are missing from the Linux v6.18 subset. Do not widen the PMKID harvestable
set up to `PSK_CAPTURE_AKMS` unless the campaign's negotiated AKM changes in the
same change.

Security-claim consumers, checked at the same commit:

| Consumer | Gate |
| --- | --- |
| `src/wifit3/ui/encryption_format.py` | `psk_capture_supported` reads `PSK_CAPTURE_AKMS` |
| `src/wifit3/ui/focus_model.py` | PMF text comes from `classify_pmf`; deauth availability comes from `deauth_attack_allowed` |
| `src/wifit3/models/access_point.py` | `deauth_attack_allowed` is true only when `deauth_protected` is false. `classify_pmf` / `deauth_resistant` in `src/wifit3/dot11/pmf.py` own the MFPC/MFPR reading |
| `src/wifit3/campaigns/deauth.py` | `visible` is `PSK_CAPTURE_AKMS`. A PMF block changes what may be transmitted; it does not prove the station left |
| `src/wifit3/campaigns/pmkid.py` | Harvestable AKM is 2, not the whole capture set |
| `src/wifit3/campaigns/eviltwin/campaign.py` | `visible` requires a non-empty SSID and `PSK_CAPTURE_AKMS \| SAE_AKM_SUITES` |

A label or a visibility bit is still an observation claim. OWE, SAE including
24 and 25, Enterprise, and PSK SHA-384 do not become a passphrase capture
because a beacon was parsed.

WPA3-Enterprise is not universally password-based; EAP-TLS is a counterexample.
Do not import vendor prose suggesting username/password is intrinsic to every
Enterprise deployment. Preserve known FT/FILS distinctions rather than silently
mapping every Enterprise AKM to the same non-FT exchange.

## PMF evidence

**WIFI-PMF-001 — Separate claims.** RSN capabilities MFPC (mask `0x0080`) and
MFPR (`0x0040`) are distinct advertised fields. Validate their combination and
the applicable profile instead of silently repairing contradictions.

| Claim | Evidence needed | Insufficient by itself |
| --- | --- | --- |
| Capable/offered | MFPC from a valid relevant RSNE | PMF negotiated for a client |
| Required by AP | MFPR in AP advertisement | Successful association |
| Association-level PMF evidence | Correlated compatible AP/STA parameters and successful association | Verified later frame protection |
| Protected management traffic observed | Appropriate robust-frame protection structure and session context | Cryptographic authenticity |
| Protection verified | A named successful integrity/authenticity check using valid keys | Merely seeing a protection indicator |

**WIFI-PMF-002 — A protection indicator is not a verifier.** Inspect the applicable
unicast/group management protection format; do not use a Protected-bit check as
a universal detector for every PMF frame. Treat an observed protected frame as
unverified until its relevant check succeeds. Deauth/Disassoc presence and lack
of a visible response do not prove disconnection or success of an intervention.
Basis: report p. 10; management-frame definitions in [S-LINUX](sources.md#s-linux).
