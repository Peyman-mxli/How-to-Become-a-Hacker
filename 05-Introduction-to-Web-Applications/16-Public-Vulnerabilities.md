# Section 16 — Public Vulnerabilities

**Provider:** Hack The Box Academy  
**Status:** Full theory documented; required CVSS v2.0 value corroborated from NVD-derived records; HTB submission acceptance pending.

## 1. Public vulnerabilities and terminology

A public vulnerability is a publicly disclosed security weakness. It may affect applications, libraries, plugins, frameworks, web servers, databases, operating systems and other components. A CVE is a public record identifier, not proof of an exploit, a fix or applicability to a particular deployment. Some disclosed weaknesses have no CVE.

| Term | Meaning |
| --- | --- |
| Zero-day | Weakness unknown to relevant defenders or without an available fix, depending on usage |
| N-day | Previously disclosed vulnerability |
| CVE | Common Vulnerabilities and Exposures, identifies a specific vulnerability |
| CWE | Common Weakness Enumeration, classifies weakness types |
| CVSS | Common Vulnerability Scoring System, measures technical severity |
| CPE | Common Platform Enumeration, identifies product/platform names |
| NVD | National Vulnerability Database, NIST's enriched vulnerability information |
| PoC | Proof of concept demonstrating a claim |
| Exploit | Technique/program taking advantage of a weakness |
| RCE | Remote code execution |
| Patch | Software update correcting a problem |
| Advisory | Published information about a security issue |
| Attack surface | Exposed components and interfaces |
| Disclosure | Communication of discovered vulnerability information |
| EOL | End of Life, end of product/version support lifecycle |

A CVE identifier has a prefix, year and sequence (CVE-year-sequence); the number itself does not communicate severity or affected version. A CVE may be published before a fix exists.

## 2. Disclosure lifecycle

Discovery -> validation -> coordinated vendor disclosure -> remediation -> public advisory/CVE -> patch deployment -> verification. Actual sequences differ; disclosure can precede remediation. Public proof-of-concept code can be incomplete, malicious or unsafe. Do not execute untrusted code or use it outside explicit authorization. Source review and patch verification often establish the necessary evidence without running exploits.

## 3. External and internal assessments

| Aspect | External | Internal |
| --- | --- | --- |
| Starting position | Outside protected network | Inside authorized environment |
| Components | Public sites, APIs, gateways | Internal apps, OS, databases |
| Objective | Evaluate externally reachable weaknesses | Evaluate internal security boundaries |
| Authorization | Explicit scope | Explicit scope |
| Example | Review public exposed services | Review internal database permissions |

The lesson emphasizes externally accessible RCE and local/internal weaknesses that can increase impact. Internal components may be externally exposed by misconfiguration. Internal weaknesses can enable privilege escalation or access to other systems; patch all affected layers. Network location alone is not a trust boundary.

## 4. Public vulnerability research workflow

The course suggests identifying versions and consulting public vulnerability/exploit databases early. In a professional assessment, first confirm permission/scope and inventory; then establish product, component, exact version/build, platform, configuration and patch state using authorized evidence. Compare vendor affected ranges, prerequisites and fixes. A banner, source file, HTML metadata or public repository version file may give a clue but is not authoritative for deployed patch status. For owned software, inspect installed package metadata and lockfiles. Distribution backports may fix a flaw without changing the upstream version string. Record unknowns and avoid declaring applicability from a search result alone.

| Resource | Purpose |
| --- | --- |
| [CVE Program](https://www.cve.org/) | Official CVE records |
| [NVD](https://nvd.nist.gov/) | Enriched records, CVSS and product details |
| [Exploit Database](https://www.exploit-db.com/) | Public research archive |
| [Rapid7 DB](https://www.rapid7.com/db/) | Vulnerability research references |
| [Vulnerability Lab](https://www.vulnerability-lab.com/) | Public advisories/research |
| [GitHub Advisories](https://github.com/advisories) | Package security advisories |
| [CISA KEV](https://www.cisa.gov/known-exploited-vulnerabilities-catalog) | Known exploitation catalog |
| [FIRST CVSS](https://www.first.org/cvss/) | Official scoring standards |

Use vendor advisories for affected versions and patches; third-party records can disagree. Search dependencies and plugins as well as primary products. Historical HTB WordPress database search illustrates this research approach; it is not evidence of a vulnerability on a particular deployment.

## 5. CVSS fundamentals

CVSS is an open industry standard maintained by FIRST. It describes technical severity on a 0–10 scale, not the probability of exploitation, actual loss, or complete organization-specific risk. Record the version, source, vector and score together. A vector encodes metric choices; the version determines the scoring rules.

The supplied HTB text describes v2 and v3, with Base, Temporal and Environmental groups. Current clarification: v4.0 also exists; NVD supports v2, v3.x and v4.0 and no longer creates new v2 data (since July 13, 2022). Existing legacy scores remain useful for historical comparisons.

| Group | v2/v3 meaning |
| --- | --- |
| Base | Intrinsic vulnerability characteristics |
| Temporal | Time-dependent factors, such as exploit maturity, remediation and report confidence |
| Environmental | Organization-specific impact and modified characteristics |

NVD primarily provides base assessments; organizations can use relevant additional metrics. CVSS v4.0 uses Base, Threat, Environmental and Supplemental groups. Supplemental metrics provide context without altering the score. v4 replaces Temporal with Threat, separates vulnerable-system and subsequent-system impact, and includes Attack Requirements.

### Severity thresholds (NVD)

| Severity | v2.0 | v3.x / v4.0 |
| --- | --- | --- |
| None | No separate category | 0.0 |
| Low | 0.0–3.9 | 0.1–3.9 |
| Medium | 4.0–6.9 | 4.0–6.9 |
| High | 7.0–10.0 | 7.0–8.9 |
| Critical | No separate category | 9.0–10.0 |

A 9.x v2 score is High, not Critical under this scale. Never substitute a v3 score when asked for v2.

## 6. Interpreting metrics

The CIA triad means Confidentiality (prevent unauthorized disclosure), Integrity (prevent unauthorized modification) and Availability (maintain accessible services).

CVSS v2 base metrics: Access Vector (AV), Access Complexity (AC), Authentication (Au), Confidentiality (C), Integrity (I), Availability (A). v2 impact values are None, Partial and Complete. v3.1 uses Attack Vector, Attack Complexity, Privileges Required (PR), User Interaction (UI), Scope (S) and C/I/A (None, Low, High). v3 Scope indicates whether impact crosses the vulnerable component's security authority. v4 adds attack requirements and distinguishes impacts to vulnerable and subsequent systems. See [CVSS guide](Public-Vulnerabilities/01-CVSS.md) for vector examples and calculators.

## 7. Backend server vulnerabilities

| Layer | Example concern | Defensive response |
| --- | --- | --- |
| Web app | Broken access control or vulnerable code | Fix code, regression tests |
| Framework/dependency | Known library flaw | Upgrade supported dependency |
| Web server | Request processing or configuration weakness | Patch, reduce exposure |
| OS | Local privilege boundary weakness | Security updates, least privilege |
| Database | Vulnerable engine, overbroad grants | Patch, isolate and restrict access |
| Network service | Legacy protocol vulnerability | Disable unnecessary services, patch and segment |

Technical correction: Shellshock (CVE-2014-6271) is a GNU Bash vulnerability, not inherently an Apache defect. Apache deployments invoking vulnerable Bash through CGI could be affected. Not every Apache installation before 2014 was vulnerable. HTTP/1.1 and HTTP/2 generally use TCP, while HTTP/3 uses QUIC over UDP. External RCE is important, but a lower CVSS score can still represent major business risk (e.g. unauthorized grades or access logs).

## 8. Original vulnerability management examples

On an owned development machine:
```bash
python -m pip list
python -m pip show Flask
node --version
npm ls --depth=0
npm audit --omit=dev
```
pip list displays installed Python packages in the active environment; pip show displays metadata for Flask; node --version identifies the runtime; npm ls --depth=0 lists immediate dependencies; npm audit --omit=dev checks reported production dependency advisories and may contact the configured registry. None of these proves exploitable impact or guarantees safety. Review the full dependency tree and lockfiles separately; do not apply automatic major-version fixes without review.

UniTrack: review Python environment, Flask/WSGI server, image parsers, OS and database components; prioritize access control and privacy. ProTrack-MX: review React/Vite/npm dependencies and production build exposure, distinguish development-only tooling from deployed code, and maintain Supabase policies. Managed infrastructure has shared responsibilities; do not test provider infrastructure without authorization. No project files or live services were modified.

### Example finding record (fictional)

Component: image-processing dependency. Version: unknown until inventory. Advisory: vendor reference required. Reachability: authenticated upload, to be established. Evidence: lockfile and installed metadata. Status: candidate, not confirmed. Remediation: vendor-supported upgrade, regression tests, verify installed version. Prioritization factors: actual exposure, permissions, data sensitivity, known exploitation, patch availability and business impact.

## 9. Mandatory CVE question — verification methodology

The question asks for the historical CVSS v2.0 base score of CVE-2017-0144. Consult the NVD record, select the v2.0 section rather than v3.x, and corroborate the legacy metric in NVD-derived records. The NVD detail page returned no readable lines during this session, so the numeric value was independently corroborated by Tenable and Aqua Security, both attributing their data to NVD/MITRE or NVD, with matching v2 vector. The Microsoft bulletin MS17-010 confirms that the CVE concerns Windows SMBv1 remote code execution and provides patch guidance. No Pwnbox or exploit execution is required. No separate assessment answer key is published. Submission acceptance pending.

## 10. References

- [NVD vulnerability metrics](https://nvd.nist.gov/vuln-metrics/cvss)
- [NVD record](https://nvd.nist.gov/vuln/detail/CVE-2017-0144)
- [NVD detail page guidance](https://nvd.nist.gov/vuln/Vulnerability-Detail-Pages)
- [Tenable record](https://www.tenable.com/cve/CVE-2017-0144)
- [Aqua record](https://avd.aquasec.com/nvd/2017/cve-2017-0144/)
- [Microsoft MS17-010](https://learn.microsoft.com/en-us/security-updates/securitybulletins/2017/ms17-010)
- [FIRST v2 guide](https://www.first.org/cvss/v2/guide)
- [FIRST v3.1 specification](https://www.first.org/cvss/v3.1/specification-document)
- [FIRST v4.0 specification](https://www.first.org/cvss/v4.0/specification-document)
- [NVD v2 calculator](https://nvd.nist.gov/vuln-metrics/cvss/v2-calculator)
- [NVD v3 calculator](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator)
- [FIRST v4 calculator](https://www.first.org/cvss/calculator/cvsscalc40)

Sources consulted November 26, 2026. Original examples are illustrative; no commands executed or live vulnerability testing performed.

## 11. Extended vulnerability research resource guides

[Complete eight-resource toolkit](Public-Vulnerabilities/Research-Resources/README.md) — individual guides for CVE Program, NVD, Exploit Database/SearchSploit, Rapid7, Vulnerability Lab, GitHub Advisory Database, CISA KEV, and FIRST. Includes free Ubuntu WSL2 setup, download options, browser/API usage and original examples. These supplements do not add lesson credit.
