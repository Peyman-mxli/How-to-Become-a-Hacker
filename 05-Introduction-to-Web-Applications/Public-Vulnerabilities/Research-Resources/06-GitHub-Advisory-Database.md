# GitHub Advisory Database

Website: https://github.com/advisories. GitHub Security Advisory (GHSA) identifiers are distinct from CVE identifiers; an advisory may include both. Reviewed advisories associate weaknesses with package ecosystems (pip, npm, Maven etc.), vulnerable ranges, patched versions and references. GitHub also includes unreviewed advisories and malware records. Dependabot alerts can match a repository's dependency graph to applicable advisories when enabled and supported; alerts require validation.

## Browser example relevant to UniTrack
Open https://github.com/advisories/GHSA-m2qf-hxjv-5gpq, the historical Flask session-cookie/cache advisory CVE-2023-30861. Inspect package name, ecosystem, affected ranges, patched versions and references. A report about Flask is not proof UniTrack is affected; verify installed version and prerequisites.

## API and download
No separate application required. GitHub documents unauthenticated public global advisory lookup.
```bash
curl -fsSL -H "Accept: application/vnd.github+json" "https://api.github.com/advisories/GHSA-m2qf-hxjv-5gpq" -o flask-advisory.json
jq '{ghsa_id,cve_id,summary,severity,vulnerabilities}' flask-advisory.json
curl -fsSL -H "Accept: application/vnd.github+json" "https://api.github.com/advisories?ecosystem=pip&affects=flask&per_page=5" | jq '.[] | {ghsa_id,cve_id,summary}'
```
-H sets HTTP header; ecosystem=pip filters Python package advisories; affects=flask filters package name; per_page=5 limits returned page; jq .[] iterates records. The returned advisory may contain more than one affected version range. An empty page does not prove absence of vulnerabilities; pagination, review type and filtering matter. Public requests are rate limited. Check project dependency versions locally with `python -m pip show Flask` in the correct virtual environment; for ProTrack-MX use `npm ls --depth=0` and review lockfiles and `npm audit --omit=dev`. Do not post tokens. Full open data repository: https://github.com/github/advisory-database (optional large download).

References: https://docs.github.com/en/rest/security-advisories/global-advisories, https://github.com/pallets/flask/security/advisories/GHSA-m2qf-hxjv-5gpq.

## Environment and safety
Run commands in Ubuntu WSL2, not Windows PowerShell. From PowerShell enter `wsl -d Ubuntu`. Install common prerequisites with `sudo apt update` and `sudo apt install -y curl jq git ca-certificates` if missing. Work in `mkdir -p ~/vulnerability-research; cd ~/vulnerability-research`. These examples retrieve public information, not test target systems. Do not execute untrusted proof-of-concept code. No paid subscriptions or changes to UniTrack/ProTrack-MX are needed. Commands are documented examples, not executed on the learner's computer.
