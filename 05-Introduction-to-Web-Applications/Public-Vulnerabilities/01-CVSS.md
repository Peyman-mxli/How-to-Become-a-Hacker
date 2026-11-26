# CVSS — Severity Scoring Reference

Supplement to [Section 16](../16-Public-Vulnerabilities.md). FIRST maintains CVSS, a standardized 0–10 technical severity scale. Scores are not probabilities or organizational risk. Always preserve score, vector, version, source and date.

## Metric groups

| Version | Groups | Notes |
| --- | --- | --- |
| 2.0 | Base, Temporal, Environmental | Legacy; NVD no longer creates new v2 data |
| 3.0/3.1 | Base, Temporal, Environmental | Privileges, interaction and scope explicitly represented |
| 4.0 | Base, Threat, Environmental, Supplemental | New attack requirements and separate vulnerable/subsequent system impacts; supplemental context does not change score |

Temporal metrics in v3.1 are Exploit Code Maturity (E), Remediation Level (RL), Report Confidence (RC). Environmental metrics include confidentiality, integrity and availability requirements (CR, IR, AR), and modified base metrics. v2 has corresponding temporal metrics plus environmental collateral damage potential, target distribution and security requirements. v4 Threat uses Exploit Maturity; environmental metrics adapt assessment to deployment; supplemental metrics add context. Use the appropriate official calculator; do not manually mix weights between versions.

## CVSS v2.0 base metrics

| Metric | Values | Meaning |
| --- | --- | --- |
| AV | L Local, A Adjacent Network, N Network | Required access position |
| AC | H High, M Medium, L Low | Conditions/complexity |
| Au | M Multiple, S Single, N None | Number of required authentications |
| C | N None, P Partial, C Complete | Confidentiality impact |
| I | N, P, C | Integrity impact |
| A | N, P, C | Availability impact |

CIA: confidentiality protects information against unauthorized disclosure, integrity protects accuracy and prevents unauthorized modification, availability preserves usable services.

Original illustrative vector for a fictional network-accessible information disclosure with medium complexity, no authentication and partial confidentiality impact:
```text
AV:N/AC:M/Au:N/C:P/I:N/A:N
```
Its v2 base score is 4.3 (Medium), calculated using the v2 formula. This is an example, not the assessment answer or a real finding.

CVSS v2 base equation from FIRST: round to one decimal of ((0.6 × Impact) + (0.4 × Exploitability) − 1.5) × f(Impact). Impact = 10.41 × (1 − (1−C)(1−I)(1−A)); Exploitability = 20 × AV × AC × Au; f is 0 for zero impact, otherwise 1.176. Metric weights come from the specification; the equation is not interchangeable with v3/v4.

## CVSS v3.1 base metrics

| Metric | Values |
| --- | --- |
| AV Attack Vector | N Network, A Adjacent, L Local, P Physical |
| AC Attack Complexity | L Low, H High |
| PR Privileges Required | N None, L Low, H High |
| UI User Interaction | N None, R Required |
| S Scope | U Unchanged, C Changed |
| C/I/A impact | N None, L Low, H High |

Original fictional read-only disclosure example:
```text
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N
```
Base score 5.3 (Medium). This assumes network access, low complexity, no required privileges or interaction, unchanged scope, low confidentiality impact and no integrity or availability impact. It is hypothetical. A different vector or version may yield a different score. Do not infer a score from vulnerability title alone.

## Severity comparison

| Rating | v2 | v3.x/v4 |
| --- | --- | --- |
| None | No separate category | 0.0 |
| Low | 0.0–3.9 | 0.1–3.9 |
| Medium | 4.0–6.9 | 4.0–6.9 |
| High | 7.0–10.0 | 7.0–8.9 |
| Critical | No separate category | 9.0–10.0 |

## Calculators and environmental context
Use [NVD v2](https://nvd.nist.gov/vuln-metrics/cvss/v2-calculator), [NVD v3](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator) or [FIRST v4](https://www.first.org/cvss/calculator/cvsscalc40). Choose the requested version, enter the documented vector, compare the resulting base score, then optionally assess environment-specific factors. No required additional exercise here. A patch available in theory is not a patch installed. A service behind a firewall may have lower exposure but still require remediation. Consider known exploitation, asset criticality, exposure and compensating controls alongside CVSS. CISA KEV is evidence of known exploitation; EPSS estimates the likelihood of exploitation activity in the next 30 days, not severity or an individual deployment's chance of compromise.

## References
[FIRST v2](https://www.first.org/cvss/v2/guide), [v3.1](https://www.first.org/cvss/v3.1/specification-document), [v4.0](https://www.first.org/cvss/v4.0/specification-document), [NVD metrics](https://nvd.nist.gov/vuln-metrics/cvss), [EPSS](https://www.first.org/epss/), [CISA KEV](https://www.cisa.gov/known-exploited-vulnerabilities-catalog). Examples are calculated illustrative vectors, not live findings.
