# Rapid7 Vulnerability Database

Website: https://www.rapid7.com/db/. Rapid7 publishes searchable vulnerability references and separate exploit-module documentation. Its public site is a research resource, distinct from commercial vulnerability scanners such as InsightVM and tools such as Metasploit. No scanner installation or paid account is necessary for public reading.

## Browser example
Open https://www.rapid7.com/db/vulnerabilities/cve-2021-44228/. Review the Log4j advisory description, affected component, references and available solution information. Corroborate the affected version and mitigation using Apache's vendor advisory. The entry does not mean that an installation is vulnerable.

## Download and local access
Use browser Save Page As (Ctrl+S) for individual reference pages when available. No verified public bulk download/API for this database is assumed here. Local HTML is a document, not a program to execute. Open the saved reference in a browser; online links or scripts may require network access. For structured records use NVD/CVE APIs instead. Do not confuse a Rapid7 module entry with authorization to execute it. No Metasploit execution, live scanning or paid trial is required.

References: https://www.rapid7.com/db/, https://docs.rapid7.com/insightvm/apache-log4j/, https://logging.apache.org/log4j/2.x/security.html.

## Environment and safety
Run commands in Ubuntu WSL2, not Windows PowerShell. From PowerShell enter `wsl -d Ubuntu`. Install common prerequisites with `sudo apt update` and `sudo apt install -y curl jq git ca-certificates` if missing. Work in `mkdir -p ~/vulnerability-research; cd ~/vulnerability-research`. These examples retrieve public information, not test target systems. Do not execute untrusted proof-of-concept code. No paid subscriptions or changes to UniTrack/ProTrack-MX are needed. Commands are documented examples, not executed on the learner's computer.
