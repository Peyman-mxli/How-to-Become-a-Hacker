# CISA Known Exploited Vulnerabilities Catalog

Website: https://www.cisa.gov/known-exploited-vulnerabilities-catalog. CISA KEV identifies vulnerabilities for which there is evidence of exploitation in the wild. Unlike CVSS, it is not a severity score; unlike EPSS, it is not a prediction. Presence means prioritize applicable remediation; absence does not prove safety. Government deadlines are not universal private-sector deadlines.

## Browser example
Look up CVE-2021-44228 (Log4j) and inspect vendor/project, product, date added, required action, due date and known ransomware campaign use. The date added is not necessarily discovery or first exploitation date.

## Download and local research
No software installation required. Download the public JSON catalog, then filter locally:
```bash
curl -fL "https://www.cisa.gov/sites/default/files/feeds/known_exploited_vulnerabilities.json" -o kev.json
jq '{catalogVersion,dateReleased,count}' kev.json
jq '.vulnerabilities[] | select(.cveID == "CVE-2021-44228") | {cveID,vendorProject,product,dateAdded,requiredAction,dueDate,knownRansomwareCampaignUse}' kev.json
```
If CISA's direct site fails, use the official agency-maintained mirror at https://github.com/cisagov/kev-data and download its JSON file via browser. Do not mistake an HTTP error page for JSON. jq validates syntax and filters by exact ID. CSV is also available via the catalog/mirror for spreadsheet analysis. Record download date; refresh periodically for actual vulnerability management. Compare against inventory and vendor patches.

References: https://www.cisa.gov/known-exploited-vulnerabilities-catalog, https://github.com/cisagov/kev-data.

## Environment and safety
Run commands in Ubuntu WSL2, not Windows PowerShell. From PowerShell enter `wsl -d Ubuntu`. Install common prerequisites with `sudo apt update` and `sudo apt install -y curl jq git ca-certificates` if missing. Work in `mkdir -p ~/vulnerability-research; cd ~/vulnerability-research`. These examples retrieve public information, not test target systems. Do not execute untrusted proof-of-concept code. No paid subscriptions or changes to UniTrack/ProTrack-MX are needed. Commands are documented examples, not executed on the learner's computer.
