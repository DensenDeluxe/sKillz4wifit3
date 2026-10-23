# Worldwide Wi-Fi and wifit3 scope

Apply the depth table in `../SKILL.md` before this list. The list is the
universe for a broad review, not a quota for a named CVE or product.

Include a finding when Wi-Fi is part of the vulnerability or its observable
security state and wifit3 could plausibly capture, parse, correlate, fingerprint,
detect, report, or support offline analysis of the evidence.

## Technology coverage

Cover relevant behavior across legacy 802.11 and 802.11a/b/g/n/ac/ax/be; 2.4,
5, and 6 GHz; Wi-Fi 4 through 7; management, control, extension, and data frames;
scanning, authentication, association, reassociation, roaming, channel changes,
power save, QoS, fragmentation, reassembly, A-MSDU/A-MPDU, retransmission, and
duplicate processing.

Include RSN, WPA/WPA2/WPA3 Personal and Enterprise, transition modes, SAE, OWE,
DPP, FILS, FT, WPS/WSC, PMF, EAPOL/EAPOL-Key, EAP-TLS, PEAP, TTLS, TEAP, FAST,
PWD, SIM/AKA where relevant, certificate validation, key derivation, nonce and
replay handling, MIC processing, cipher and AKM negotiation, pairwise and group
keys, 802.11k/r/v/w, mesh, P2P, TDLS, NAN/Aware, WDS, and MLO.

Cover memory safety, length handling, parser differentials, state confusion,
authentication or authorization bypass, downgrade, cryptographic misuse, replay,
key reinstallation, randomness, certificate validation, identity leakage,
fragmentation and aggregation, cross-network injection, PMF failures, client
isolation, evil-twin and rogue-authenticator conditions, firmware command paths,
DMA and descriptor handling, USB boundaries, races, side channels, privacy,
fingerprinting, and tracking.

## Worldwide ecosystem coverage

Search consumer, ISP, enterprise, public hotspot, mobile, desktop, USB/PCIe,
embedded, IoT, industrial, medical, automotive, robotics, surveillance, office,
gaming, retail, education, hospitality, and transportation deployments.

Cover major and regional vendors from North America, Europe, China, Taiwan,
South Korea, Japan, India, and other markets. Search chipset and implementation
families including Qualcomm/Atheros, Broadcom/Cypress/Infineon, Realtek,
MediaTek/Ralink, Intel, Marvell/NXP, Texas Instruments, Espressif, Nordic,
Silicon Labs, Microchip, Linux mac80211/cfg80211 and vendor drivers, Windows,
Android and vendor HALs, Apple platforms, BSD, hostapd, wpa_supplicant, iwd,
OpenWrt, controllers, AP firmware, embedded SDKs, FullMAC, and SoftMAC. Treat
this as a starting set, not an exhaustive vendor claim.

## Exclusions

Exclude a generic router web UI, cloud control-plane, Bluetooth, Ethernet,
cellular, or local privilege-escalation issue unless Wi-Fi is necessary to the
attack path or the result changes observable wireless security behavior. Record
the candidate and reason in the exclusion register when it was seriously
investigated.

## Project map

Map evidence and capability questions to the current repository, inspecting the
code before relying on this guide:

| Area | Responsibility |
| --- | --- |
| `src/wifit3/dot11/` | Pure 802.11, RSN, EAP/EAPOL, WPA, PMF, and WSC parsing/building |
| `src/wifit3/device/` | USB discovery and driver selection |
| `src/wifit3/wlan/` | Channel control, observations, AP/client state, dedupe, and orchestration |
| `src/wifit3/chips/` | Independent chipset transport, firmware, RX/TX, MAC/PHY, and channel behavior |
| `src/wifit3/models/` | Project-wide typed evidence |
| `src/wifit3/campaigns/` | Active audit workflows |
| `src/wifit3/vault/`, `crack/` | Tool jobs and offline cryptographic checks |
| `src/wifit3/persist/` | Configuration, captures, history, and stored evidence |
| `src/wifit3/id/` | Vendor and device fingerprinting |
| `src/wifit3/ui/` | User-visible evidence and claims |

## Mapping questions

For each included finding, report whether wifit3 could theoretically observe the
traffic, parse the fields, retain provenance, correlate the session, fingerprint
the implementation, detect an indicator, support offline analysis, distinguish
confirmed from suspected behavior, or verify a remediation. Identify necessary
RX format, frame fields, cryptographic context, channel state, device evidence,
driver behavior, capture metadata, and remaining limitations.

This research skill does not authorize or request implementation. Describe
gaps and relevant areas without editing code or claiming support.

## Protocol evidence invariants

Parser and evidence rules live in the wifi-layer skill
(`references/capture-and-state.md`, `references/rsn-and-pmf.md`,
`references/enterprise-and-keys.md`), including the project suite sets
(`SAE_AKM_SUITES` includes decimal 24 and 25; `ENTERPRISE_AKM_SUITES` includes
22 and 23; `PSK_CAPTURE_AKMS` is only 2, 4, and 6). When that skill is not
installed, keep these boundaries anyway:

- Raw MPDU, Radiotap, Ethernet, USB transfer data, firmware descriptors, padding,
  and FCS are different evidence envelopes.
- Preserve complete suite selectors. Decimal 22 is not 00-0F-AC-0D, and decimal
  24 stays in the SAE family.
- Separate AP advertisement, client selection, observed traffic, cryptographic
  verification, vulnerability confirmation, and impact.
- An outer TLS method does not reveal the inner method or certificate policy.
- A handshake observation is not a MIC verification or a passphrase-capture claim.
