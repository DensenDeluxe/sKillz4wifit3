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
| SAE or FT-SAE | SAE-family capability offered | Successful SAE session, full profile compliance |
| PSK plus SAE | Personal transition candidate | Which suite a client selected |
| 802.1X | Enterprise AKM offered | Actual EAP method, success |
| 802.1X SHA-256 | WPA3-Enterprise profile candidate with other required evidence | A successful WPA3 client session |
| 802.1X plus 802.1X SHA-256 | Enterprise transition candidate subject to profile checks | Concrete client mode |
| Suite-B-192 and matching cipher/PMF evidence | Enterprise-192 advertised profile candidate | EAP/TLS/PKI conformance |
| OWE | OWE / Enhanced Open | Password or Enterprise authentication |
| Unknown/malformed suites or conflicting valid data | Raw evidence plus unknown/conflict state | A confidently invented WPA generation |

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
