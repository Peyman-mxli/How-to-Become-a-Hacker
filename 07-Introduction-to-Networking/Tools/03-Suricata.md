# Suricata — network IDS/IPS

OISF's open-source engine reads packets from an authorized interface or PCAP, decodes protocols, tracks flows, evaluates rules, and writes alerts/events such as EVE JSON. Passive IDS alerts; inline IPS can drop traffic with suitable deployment. Encrypted payloads and sensor placement limit visibility.

## Official downloads and setup reference
[Download](https://suricata.io/download/), [installation](https://docs.suricata.io/en/latest/install.html), [quickstart](https://docs.suricata.io/en/latest/quickstart.html). Choose stable-version documentation for the installed release. On supported Ubuntu use the official stable PPA per quickstart, then:
```bash
sudo apt update
sudo apt install suricata
suricata -V
sudo suricata -T -c /etc/suricata/suricata.yaml
```
-V prints version; -T tests configuration; -c specifies file. Configure HOME_NET, rules and intended interfaces. Offline run on an existing authorized capture:
```bash
mkdir -p "$HOME/suri-output"
suricata -r "$HOME/lab.pcap" -c /etc/suricata/suricata.yaml -l "$HOME/suri-output"
```
-r reads capture; -l selects output directory. Inspect configured eve.json/fast.log.
Original local detection rule:
```text
alert icmp any any -> $HOME_NET any (msg:"LOCAL lab ICMP observation"; itype:8; sid:1000001; rev:1;)
```
alert emits an event; icmp is the protocol; arrow indicates direction; itype:8 matches IPv4 echo requests; sid/rev identify the rule. Configure rule loading and validate before use. A match may be benign.

## Troubleshooting and applications
Verify rule loading, HOME_NET, permissions, interface visibility and logs. WSL2 is not automatically a network tap; use an authorized sensor or offline PCAP. Tune noise and protect captured data. In an authorized UniTrack or ProTrack-MX environment, correlate network alerts with service logs. No installation or execution performed; reference only.
