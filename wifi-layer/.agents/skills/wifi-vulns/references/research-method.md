# Research method

Use this protocol for broad searches, fresh-CVE monitoring, vendor surveys, and
historical reviews. PRISMA 2020 is a transparency framework for reporting search
and selection; it is not a vulnerability quality score.

## Define the research frame

Record these values before searching:

- execution-date cutoff and UTC retrieval time;
- research question and wifit3 relevance rule;
- 90-day hot window, 24-month current window, and historical baseline;
- included protocol, component, platform, product, and geographic categories;
- explicit exclusions and why they are outside the wireless scope;
- synonyms, former product names, project forks, acquisitions, and CVE aliases.

Do not block on an unspecified category. Use worldwide coverage and the full
wifit3 relevance rule from [wifit3 scope](wifit3-scope.md).

## Search in layers

Search each layer independently so database lag does not hide current findings:

1. Vendor PSIRTs, security bulletins, release notes, and fixed-version tables.
2. CVE records and assigning-CNA publications.
3. Upstream repositories, patches, mailing lists, and issue trackers.
4. CISA KEV, national CERTs, and government technical guidance.
5. IEEE, IETF, RFC, and Wi-Fi Alliance material where accessible.
6. Original papers, conference artifacts, and researcher advisories.
7. NVD, CVSS, EPSS, and other structured enrichment.
8. High-quality independent analysis.
9. Exploit collections, social media, forums, and blogs only as leads.

Search by combinations of:

- `Wi-Fi`, `WLAN`, `802.11`, amendment, frame type, IE, handshake, or protocol;
- vendor, subsidiary, acquired brand, chipset, firmware, driver, and product;
- operating system, supplicant, authenticator, AP, controller, and embedded SDK;
- CWE or fault class such as bounds, lifetime, replay, downgrade, state, parsing,
  fragmentation, aggregation, certificate validation, or key installation;
- `CVE`, advisory, bulletin, patch, fixed, exploited, PoC, revision, and update;
- relevant non-English terms when they expose regional primary sources.

## Time tiers

### Hot window: last 90 days

Include new CVEs and older records with a material new event: advisory revision,
affected-product expansion, patch, public PoC, exploitation report, KEV change,
or corrected version boundary. Sort by event date as well as CVE publication.

### Current window: last 24 months

Seek systematic coverage across protocols, clients, APs, controllers, operating
systems, drivers, firmware, chipsets, supplicants, authenticators, Enterprise
Wi-Fi, embedded products, mesh, P2P, NAN, 6 GHz, Wi-Fi 6/6E/7, and MLO.

### Historical baseline

Include older findings that define a recurring class, motivate a regression,
remain deployed in unpatched or EOL products, reveal incomplete backports, or
explain a modern mitigation. Organize by root cause rather than fame or age.

## Inclusion and exclusion

Include evidence that establishes a central claim, changes scope, clarifies a
version or fix, documents a mechanism or observation, or contradicts an included
source. Include non-CVE attack families with an explicit `no CVE assigned` value.

Exclude duplicate aggregations without added evidence, anonymous or provenance-
free summaries, unverifiable AI-generated material, and generic router or cloud
bugs where Wi-Fi is neither necessary nor observably affected. Preserve excluded
candidates with a concise reason so scope decisions remain auditable.

## Normalize before synthesis

- Keep CVE publication, disclosure, modification, and retrieval dates separate.
- Resolve vendor and product aliases without discarding the original names.
- Separate upstream branches, downstream backports, firmware, OEM products, and
  device delivery status.
- Preserve exact version expressions and state who asserted each boundary.
- Mark rejected, reserved, disputed, duplicate, and withdrawn records.
- Remove tracking parameters from stable source URLs when possible.
- Deduplicate claims, not merely URLs.

## Evaluate each claim

Score these dimensions independently: source authority, proximity to original
evidence, technical specificity, reproducibility, currency, and independent
confirmation. Use the lowest defensible confidence when a key dimension is weak.

| Grade | Meaning |
| --- | --- |
| A — confirmed | Direct primary evidence plus an independent consistent source or reproducible original data |
| B — strongly supported | High-quality primary evidence without full independent replication |
| C — provisional | Plausible partial or secondary evidence, or incomplete affected-version data |
| D — unconfirmed | Claim lacks adequate traceable evidence |

Search deliberately for disconfirming evidence, revised advisories, unaffected
branches, negative controls, and narrower interpretations. A contradiction is a
result: record the exact claims, sources, dates, and current resolution state.

## Prioritize without collapsing signals

Keep these as separate fields:

- CVSS version, vector, and technical severity;
- EPSS value, percentile, model date, and retrieval date;
- KEV inclusion and date;
- exploitation-in-the-wild evidence and its source;
- public-PoC availability, quality, prerequisites, and demonstrated outcome;
- pre-authentication reachability, attacker position, RF proximity, interaction,
  default exposure, deployed prevalence, patch availability, and wifit3 relevance.

EPSS is a population-level estimate of observed exploitation in the next 30
days, not proof that a specific system is exploitable. KEV is authoritative for
its included known-exploited records, not a complete history of exploitation.

## Quality gates

Before release, run four explicit reviews:

1. Fact audit: identifiers, terminology, mechanisms, and impacts.
2. Citation audit: each material claim has a supporting source of sufficient
   authority and the link supports that exact claim.
3. Version audit: affected, unaffected, fixed, upstream, backported, firmware,
   chipset, OEM, and device-delivery states remain separate.
4. Uncertainty audit: unknowns, conflicts, negative evidence, inaccessible
   sources, and inference boundaries are visible.

Report counts for candidates found, included, excluded, unresolved, disputed,
and primary-source validated. Do not describe a search as exhaustive unless the
searched sources and coverage dimensions justify that claim.
