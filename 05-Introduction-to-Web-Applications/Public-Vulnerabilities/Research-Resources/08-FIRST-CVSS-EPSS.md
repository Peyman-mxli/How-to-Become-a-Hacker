# FIRST — CVSS and EPSS

Website: https://www.first.org/. FIRST (Forum of Incident Response and Security Teams) is an international security incident response organization. Its CVSS standard describes technical severity; EPSS estimates probability of exploitation activity in the next 30 days. EPSS is not the chance that an individual organization is compromised. CVSS is not a probability; do not multiply the two as a risk formula. EPSS percentile is the relative position among scored vulnerabilities.

## Browser example: CVSS
Open https://www.first.org/cvss/calculator/3.1. Select a fictional network-accessible read-only disclosure vector AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N. It produces a base score of 5.3 (Medium). This is illustrative, not a real finding. Compare with v4 at https://www.first.org/cvss/calculator/4.0. Specifications and calculators are free browser resources; no installation required. Do not interchange versions.

## API example: EPSS
```bash
curl -fsS "https://api.first.org/data/v1/epss?cve=CVE-2021-44228" -o epss.json
jq '.data[] | {cve,epss,percentile,date}' epss.json
```
The cve query selects the ID; jq selects score, percentile and date. Values are dynamic and typically represented as decimal strings from 0 to 1; multiply by 100 to express as percentage. Do not invent a current value. For example, a hypothetical 0.20 is 20% predicted exploitation activity, not 20% severity. Data is refreshed regularly; absence of a score is not proof of safety. Download full daily CSV from https://www.first.org/epss/data.html for batch use, not needed for single records. Use CVSS, EPSS, KEV, reachability and asset criticality together to guide remediation.

References: https://www.first.org/cvss/, https://api.first.org/epss/, https://www.first.org/epss/data.html, https://www.first.org/epss/faq.

## Environment and safety
Run commands in Ubuntu WSL2, not Windows PowerShell. From PowerShell enter `wsl -d Ubuntu`. Install common prerequisites with `sudo apt update` and `sudo apt install -y curl jq git ca-certificates` if missing. Work in `mkdir -p ~/vulnerability-research; cd ~/vulnerability-research`. These examples retrieve public information, not test target systems. Do not execute untrusted proof-of-concept code. No paid subscriptions or changes to UniTrack/ProTrack-MX are needed. Commands are documented examples, not executed on the learner's computer.
