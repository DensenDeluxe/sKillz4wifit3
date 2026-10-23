# Enterprise EAP, TLS visibility, and EAPOL-Key

Use for Enterprise method detection and exchange correlation. Condensed from the
supplied report, pp. 10-16 and 22-23. Source checks: 2026-10-23. Consult linked
primary documents for exact protocol implementation rather than extending a
metadata parser from the summaries here.

## EAP and backend roles

The STA/supplicant exchanges EAPOL with the AP/authenticator. Backend EAP can be
carried in RADIUS between the authenticator/NAS and authentication server; a
normal WLAN air capture does not thereby expose backend attributes, policies,
or authorization decisions. Keep 802.11 association, EAP outcome, key exchange,
and network authorization separate. Source: [S-AAA](sources.md#s-aaa), especially
EAP-Message and the RADIUS/EAP exchange descriptions.

**WIFI-EAP-001 — Dispatch at each layer.** EAPOL Type 0 carries EAP; Type 3
carries EAPOL-Key. Start (1), Logoff (2), ASF Alert (4), and MKA (5) are not EAP
Request/Response messages. Recognize or leave unsupported types unclassified;
do not force them into either parser. Source: [S-EAPOL](sources.md#s-eapol),
implementation enumeration; verify additional types against the relevant 802.1X
edition before adding them.

**WIFI-EAP-002 — Bound nested lengths separately.** Check EAPOL body length
against the enclosing captured data; then check EAP's big-endian two-byte Length
against that body. RFC 3748's four-byte base header includes Code, Identifier,
and Length. Request/Response need a Type; Success/Failure have no method Type
and have Length 4. Ignore bytes beyond the declared EAP Length as padding.
Overdeclared lengths do not establish a valid EAP packet. Source:
[S-EAP](sources.md#s-eap), sections 4, 4.1, and 4.2.

**WIFI-EAP-003 — Identifier is exchange-local.** A retransmitted request retains
its Identifier; a corresponding response uses that Identifier. It is eight bits,
can wrap/recur, and is not a durable session key. Preserve direction, AP/STA,
method, temporal context, and duplicate status for correlation. Source:
[S-EAP](sources.md#s-eap), section 4.1; session-key design is project inference.

RFC 3748 codes 1/2/3/4 mean Request/Response/Success/Failure. The current IANA
registry also assigns 5/6 to ERP Initiate/Finish (RFC 6696). Lack of ERP support
does not mean those codes are intrinsically malformed. Source:
[S-IANA](sources.md#s-iana). Preserve the parser's declared support boundary.

**WIFI-EAP-004 — Expanded identity includes vendor fields.** Type 254 requires
three-byte Vendor-Id and four-byte Vendor-Type after the five-byte basic
Request/Response header. Validate all twelve bytes before reading them. Preserve
unknown vendor tuples. WSC adds its own fields and keeps its existing project
compatibility path. Source: [S-EAP](sources.md#s-eap), section 5.7.

Selected method assignments from [S-IANA](sources.md#s-iana), registry updated
2026-08-31; this is not an exhaustive list:

| Type (decimal) | Registry name / interpretation |
| --- | --- |
| 1 / 2 / 3 | Identity / Notification / Legacy Nak; not successful authentication methods |
| 13 | EAP-TLS |
| 18 | EAP-SIM |
| 21 | EAP-TTLS |
| 23 | EAP-AKA |
| 25 | PEAP |
| 26 | MS-EAP-Authentication; Microsoft MS-CHAPv2 usage needs the note below |
| 29 | EAP-MSCHAP-V2; distinct IANA assignment |
| 43 | EAP-FAST |
| 50 | EAP-AKA' |
| 55 | TEAP |
| 254 / 255 | Expanded / Experimental |

**WIFI-EAP-008 — Preserve naming provenance.** Do not mechanically replace
Type 26 with 29 in existing code. Microsoft names
its MS-CHAPv2 configuration with Type 26, while IANA labels these assignments
differently. Retain numeric type and naming provenance; a registry label is not
evidence that a vendor's Type-26 usage is impossible. Sources:
[S-IANA](sources.md#s-iana), [S-MS](sources.md#s-ms). The report's table alone is
not enough to revise interoperability behavior.

## Method-specific visibility

**WIFI-EAP-005 — Method recognition is not a client policy audit.** Observing
EAP-TLS/PEAP/TTLS/TEAP does not reveal whether the client validated the server's
trust chain, name, or revocation policy. Do not manufacture
`certificate_validation_ok`, safe inner credentials, or authorized access from
an outer method. Source: [S-TTLS](sources.md#s-ttls), method security model;
passive-observation limits are inference.

**WIFI-EAP-006 — TLS version controls certificate visibility.** EAP-TLS 1.3
encrypts certificate messages; absence of a visible certificate is not a parser
error or proof that certificates were unused. Older full handshakes may expose
certificates; resumption and capture loss change that expectation. Inspect actual
negotiation evidence rather than guessing TLS version from a legacy version
field. Source: [S-TLS13](sources.md#s-tls13), section 2.1.8.

**WIFI-EAP-007 — Outer method does not reveal encrypted inner method.** For
PEAP/TTLS/TEAP, report visible outer method and mark inner authentication unknown
unless separately available authorized evidence establishes it. In particular,
PEAP does not prove MSCHAPv2 and TTLS does not prove PAP. Keep identities and
credential bodies out of logs/committed fixtures. Sources:
[S-TTLS](sources.md#s-ttls), [S-TUNNELS](sources.md#s-tunnels), and
[S-TEAP](sources.md#s-teap); data minimization is project policy.

Fragmentation is method-specific. For EAP-TLS, L indicates the presence of a
four-byte TLS Message Length, M indicates more fragments, and S identifies the
start. Fragment ACKs can carry no TLS data. Bound reassembly by declared sizes
and session context; do not parse a truncated fragment as a complete TLS record.
Source: [S-EAPTLS](sources.md#s-eaptls), sections 2.1.5 and 3.1. Do not apply one
method's flag/version layout blindly to another TLS-based method.

TEAP Type 55 now references RFC 9930 (February 2026), which obsoletes RFC 7170
and updates RFC 9427. It discusses TLS 1.2 as well as TLS 1.3; its resumption
rules are not exclusively a TLS-1.3 feature. Do not assume every observed device
implements the newest RFC or infer full inner authentication from resumption.
Sources: [S-TEAP](sources.md#s-teap), sections 1.1 and 3.5;
[S-TUNNELS](sources.md#s-tunnels) for other TLS-based methods.

## Key exchange and MIC claims

**WIFI-KEY-001 — Pairwise and group exchanges are different.** Interpret
descriptor type, Key Type, and context before assigning a four-way role. Group
rekeys, legacy descriptors, and error/request frames must not be counted as
ordinary pairwise M1-M4 merely because they are EAPOL-Key. Source:
[S-HOSTAP](sources.md#s-hostap), key-info definitions and state-machine evidence
in the report; use the relevant current implementation for algorithm details.

**WIFI-KEY-002 — Message role is a correlated candidate.** In the ordinary
pairwise exchange, M1/M3 are AP-to-STA and M2/M4 STA-to-AP. ACK/MIC/Install/Secure
bits, replay relationships, nonce consistency, key data, and session generation
together establish plausibility. Do not reduce identification to one bitmask
or key-data-length test. Retries may produce more than four captured frames;
new M1/nonces must not overwrite unrelated unfinished generations. These are
project tracking recommendations grounded in the report pp. 14-16, not a new
normative state-machine specification.

**WIFI-KEY-003 — MIC layout is context-dependent.** Common EAPOL-Key uses a
16-byte MIC; hostap's Suite-B-192 structure has 24 bytes. Determine the supported
descriptor/AKM/algorithm context before choosing field boundaries. Do not use a
beacon from an unrelated session to guess layout, or infer layout solely from
packet length. Unknown context should stay partial/unsupported. Source:
[S-KEY192](sources.md#s-key192), historical structure evidence.

EAPOL/Key multibyte fields use network byte order, unlike many MAC/RSN fields.
Bound Key Data by both its declared length and the enclosing EAPOL PDU. For MIC
verification use exactly the specified EAPOL PDU and neutralized MIC field;
exclude LLC, MAC headers, capture padding, and FCS. Do not extend the MIC input
to the end of a capture buffer merely because the report says 'frame end'.
Source: [S-HOSTAP](sources.md#s-hostap); check the algorithm-specific definition
when implementing a verifier.

**WIFI-KEY-004 — Observation is not verification.** Track observed/correlated
messages separately from a successful cryptographic check. A usable verifier
input is not necessarily all four messages; do not require four messages for
every legitimate offline check or call every incomplete capture unusable.
Name the evidence and check actually available. Basis: report pp. 15-16 and
the project's offline-crypto boundary.

**WIFI-KEY-005 — Key provenance matters.** Enterprise EAPOL-Key/PMKID evidence
does not itself make a user's password testable like WPA2-PSK. EAP/AAA-derived
key material, PSK, and SAE have different provenance. Do not set a generic
`password_crackable` flag from 'handshake present'. Source: report p. 16;
protocol-specific verification requires its own documented prerequisites.
