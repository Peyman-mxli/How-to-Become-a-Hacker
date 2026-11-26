# NIST National Vulnerability Database (NVD)

Website: https://nvd.nist.gov/. NVD enriches CVE records with information such as severity vectors, CWE weakness classifications, references and CPE product configurations where available. Data may be incomplete or updated later. CPE match rules are not proof that a deployed instance is exploitable.

## Browser example
Open https://nvd.nist.gov/vuln/detail/CVE-2021-44228. Inspect the description, CVSS version and source, CWE, affected product configurations and references. Compare with vendor fixes.

## API and download
No NVD application to install. Use its public CVE API 2.0:
```bash
curl -fsS "https://services.nvd.nist.gov/rest/json/cves/2.0?cveId=CVE-2021-44228" -o nvd-log4j.json
jq '.totalResults' nvd-log4j.json
jq '.vulnerabilities[0].cve | {id, descriptions, metrics, weaknesses}' nvd-log4j.json
jq '.vulnerabilities[0].cve.metrics.cvssMetricV31[]? | {source, type, score: .cvssData.baseScore, vector: .cvssData.vectorString}' nvd-log4j.json
```
-f fails on HTTP errors, -s suppresses the progress meter, -S displays errors, -o saves output. cveId filters by identifier; [0] reads first result; {} constructs a selected JSON object; []? tolerates an absent array. Check totalResults before indexing. Expect one matching record when available; metric arrays may be missing or contain multiple sources. Do not confuse v2 and v3 scores. Bulk feeds: https://nvd.nist.gov/vuln/data-feeds. API keys are optional for small public requests; respect rate limits, avoid repeated rapid requests; never publish a key. If 403/429 or temporary failure, wait and use browser/vendor reference; do not bypass rate limits.

References: https://nvd.nist.gov/developers/vulnerabilities, https://nvd.nist.gov/developers/start-here.

## Environment and safety
Run commands in Ubuntu WSL2, not Windows PowerShell. From PowerShell enter `wsl -d Ubuntu`. Install common prerequisites with `sudo apt update` and `sudo apt install -y curl jq git ca-certificates` if missing. Work in `mkdir -p ~/vulnerability-research; cd ~/vulnerability-research`. These examples retrieve public information, not test target systems. Do not execute untrusted proof-of-concept code. No paid subscriptions or changes to UniTrack/ProTrack-MX are needed. Commands are documented examples, not executed on the learner's computer.
