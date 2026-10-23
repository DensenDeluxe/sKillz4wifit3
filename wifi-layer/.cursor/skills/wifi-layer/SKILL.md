---
name: wifi-layer
description: >-
  Analyze, implement, debug, and review the Wi-Fi/WLAN protocol layer in wifit3:
  802.11 frames, RSN, WPA2/WPA3, Enterprise EAP/EAPOL, PMF, AP/client tracking,
  channel control, and WLAN PCAP interpretation. Use for WLAN correctness in
  dot11/ or wlan/, and when UI or campaigns display or gate a security claim
  (encryption label, PMF, Enterprise, which captures are offered). Not for
  unrelated layout or chipset register bring-up alone.
---

# Wi-Fi protocol layer

Read and follow [the shared Wi-Fi workflow](../../../.agents/skills/wifi-layer/SKILL.md)
before analyzing or changing the WLAN layer. Its repository-root path is
`.agents/skills/wifi-layer/SKILL.md`. Load the relevant reference files linked
there; resolve those links relative to the shared file, and code paths from the
repository root. The shared file wins if this summary and that file disagree.

Until that file is open, keep these boundaries:

- Decode the declared envelope. Raw MPDU, Radiotap, Ethernet, and USB data are
  different inputs. Do not guess FCS or padding.
- Compare all four suite-selector bytes. Decimal 22 is not `00-0F-AC-0D`, and
  decimal 23 is not `00-0F-AC-0C`. `SAE_AKM_SUITES` includes 24 and 25.
  `PSK_CAPTURE_AKMS` is only 2, 4, and 6. Import those sets; do not paste a
  new `{2, 4, 6}` or drop 22–25 because a Linux v6.18 table ends at 20.
- The PMKID campaign's harvestable set is AKM 2. Do not widen it to the
  capture set unless the campaign's negotiated AKM changes.
- Separate advertisement, client selection, observed traffic, and cryptographic
  verification. An outer TLS method does not reveal the inner method. A
  handshake observation is not a MIC check or a passphrase-capture claim.
- PMF capable, required, observed, and verified are separate. Use
  `classify_pmf` and `deauth_attack_allowed`. A blocked transmission is not
  proof the station left.
- Security-claim UI and campaign gates are in scope. Unrelated layout and
  chipset register bring-up are not.
