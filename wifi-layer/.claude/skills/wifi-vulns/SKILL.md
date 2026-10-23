---
name: wifi-vulns
description: >-
  Research Wi-Fi vulnerabilities, fresh CVEs, historical attack families,
  affected versions, patches, public PoC evidence, detection artifacts, and
  scientific audit provenance for wifit3. Use for evidence gathering and
  vulnerability reports, not exploit execution, RF testing, or implementation.
  A named CVE, product, or attack family gets one dossier. A worldwide
  multi-window survey runs only when the request is a broad review.
---

# Wi-Fi vulnerability research

Read and follow [the shared vulnerability-research workflow](../../../.agents/skills/wifi-vulns/SKILL.md)
before researching Wi-Fi vulnerabilities. Its repository-root path is
`.agents/skills/wifi-vulns/SKILL.md`. Load only the relevant references linked
there and resolve their links relative to the shared file. The shared file wins
if this summary and that file disagree.

Until that file is open:

- Research only. Do not execute, compile, or improve exploit code, scan
  networks, transmit frames, or edit product code.
- A named CVE, product, chipset, or attack family is one compact dossier.
  A 90-day watchlist is the hot window only. Worldwide coverage across 90
  days, 24 months, and history is for a survey or a request with no subject.
- Missing evidence is `unknown`, not `unaffected`. Omit unchecked optional
  fields. Do not fill a schema with placeholders.
- A public PoC is evidence to read, not code to run. `documented_mechanics`
  stays a non-operational summary.
