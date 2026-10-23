# Scientific audit evidence

Use this reference to document an audit the user already scoped. Do not conduct
the experiment, invent a lab run, or fill hardware and capture fields that were
not observed. This skill stays research-only.

## Audit record

Capture:

- audit ID, test ID, report version, research question, and hypothesis;
- authorization basis, scope, exclusions, stop conditions, and execution class;
- UTC evidence timestamps, local time zone, researcher or agent identity;
- hardware model, revision, serial alias, USB VID:PID, firmware, and driver;
- operating system, dependencies, tool versions, source revision, and config hash;
- RF topology, isolation, regulatory domain, channel, width, and transmit setup;
- AP and client configurations with identities and secrets sanitized;
- preconditions, procedure, expected result, positive and negative controls;
- raw observations, packet captures, logs, crash artifacts, and cryptographic
  hashes for each retained artifact;
- repetitions, reproducibility rate, deviations, failed attempts, unexpected
  behavior, and inconclusive outcomes;
- observation, inference, conclusion, uncertainty, and confidence as separate
  fields;
- demonstrated impact, severity rationale, privacy controls, remediation,
  retest, disclosure state, and limitations.

## Evidence manifest

Each artifact entry should include a stable ID, filename or storage reference,
media type, byte size, SHA-256, creation and collection times, producing tool,
source device alias, channel/session association, sanitization performed, access
restrictions, and the claims it supports. Never store credentials or unrelated
identity data merely because they appeared in a capture.

## Result vocabulary

Use `confirmed`, `not_reproduced`, `inconclusive`, `invalid_test`, and
`not_executed` as distinct experiment outcomes. `Not reproduced` is not the same
as `unaffected`. An offline parser result, simulated result, controlled hardware
observation, and third-party published result must retain separate evidence
classes.

## Claim construction

Write findings as a chain:

1. Raw observation with artifact reference.
2. Interpretation and protocol context.
3. Alternative explanations considered.
4. Negative or positive control result.
5. Bounded conclusion.
6. Confidence and remaining uncertainty.

Do not turn a packet, disconnect, timeout, crash, Protected bit, advertised AKM,
or handshake into a stronger conclusion than its evidence supports.

## Research deliverables

For large reviews, produce an executive summary, method and search log, annotated
bibliography, evidence matrix, normalized dataset, machine-readable catalog,
exclusion and conflict registers, visual timelines or taxonomies when useful,
and a prioritized open-question register. Record candidates found, screened,
included, excluded, disputed, and primary-source validated.
