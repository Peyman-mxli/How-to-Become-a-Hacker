# Section 24 Supplement — Network Monitoring Tools

Environment: Windows 11, Ubuntu WSL2 and Docker Desktop. All exercises are proposed, not yet executed or verified by the learner. Only capture traffic on owned systems or explicitly authorized lab interfaces. Do not publish packet captures, real credentials, VPN profiles, private IP inventories or student records.

## Wireshark and Npcap
Official download: https://www.wireshark.org/download.html
Windows: select the appropriate installer (x64 for Intel/AMD 64-bit), include TShark and Npcap, and launch Wireshark. Npcap provides Windows capture access. Linux option: sudo apt install wireshark; use the distro's capture permissions guidance rather than running the GUI as root.
Wireshark captures packets and dissects protocols into readable fields. Select an authorized interface, start a short capture, generate a test DNS query with nslookup example.com in PowerShell, stop, and apply the display filter dns. Inspect query and answer, source/destination, protocol and timing. Other display filters: tcp, udp, icmp, http, tls, tcp.port == 5000. Encrypted HTTPS/VPN payloads are not automatically readable. Windows and WSL capture visibility can differ; use Linux capture for WSL loopback.

## Ubuntu installation
Open PowerShell and run wsl -d Ubuntu. Then in Ubuntu:
~~~bash
sudo apt update
sudo apt install tcpdump tshark iproute2 lsof
~~~
If TShark asks whether non-superusers may capture packets, choose No for this beginner lab and use sudo only for capture. No third-party package downloads are required. Verify:
~~~bash
tcpdump --version
tshark --version
ss -V
lsof -v
~~~

## Tcpdump and TShark: local-only lab
Tcpdump (https://www.tcpdump.org/) captures and summarizes packets. TShark (https://www.wireshark.org/docs/man-pages/tshark.html) uses Wireshark's protocol dissectors at the command line.
In one WSL terminal:
~~~bash
sudo tcpdump -i lo -nn -c 8 icmp
~~~
In a second WSL terminal:
~~~bash
ping -c 4 127.0.0.1
~~~
-i lo selects loopback, -nn disables name and port resolution, -c 8 limits capture to eight packets; icmp selects IPv4 ICMP. Expect four requests and four replies. Ctrl+C stops capture. Repeat using:
~~~bash
sudo tshark -i lo -f icmp -c 8
~~~
-f sets a capture filter; -Y sets a display filter. To demonstrate file analysis, capture only synthetic local ICMP traffic:
~~~bash
sudo tcpdump -i lo -nn -c 8 -w ~/htb-loopback.pcap icmp
~~~
Generate four pings again in another terminal, then read the file:
~~~bash
sudo tshark -r ~/htb-loopback.pcap -Y icmp
~~~
-w saves packets, -r reads them, -Y filters decoded output. Do not commit captures to GitHub. No VPN or remote host is needed.

## ss and lsof
ss (https://man7.org/linux/man-pages/man8/ss.8.html) reads Linux socket information. lsof (https://github.com/lsof-org/lsof) lists process-associated open files and sockets. No packet capture is necessary.
~~~bash
ss -tuln
sudo ss -tulnp
sudo lsof -nP -iTCP -sTCP:LISTEN
~~~
ss flags: -t TCP, -u UDP, -l listening, -n numeric, -p process. lsof: -n no DNS resolution, -P numeric ports, -iTCP TCP sockets, -sTCP:LISTEN selects listening state.
Optional local server exercise, if port 8080 is available and python3 installed:
~~~bash
python3 -m http.server 8080 --bind 127.0.0.1
~~~
In another WSL terminal run ss -ltn and lsof -nP -iTCP:8080 -sTCP:LISTEN, then curl http://127.0.0.1:8080/ from an empty practice directory to avoid serving personal files. Stop the server with Ctrl+C. A bind on 127.0.0.1 limits the service to loopback within the relevant network environment. These commands show the socket and process, unlike Wireshark, which inspects traffic. UniTrack's port 5000 can be inspected in the environment where it is actually running.

## Syslog and rsyslog
Syslog is a logging convention/protocol, not one standalone application. Rsyslog is a daemon that collects, filters and forwards logs. On a systemd-based WSL distro, check PID 1 with ps -p 1 -o comm= and inspect the journal:
~~~bash
logger -t htb-study "Synthetic logging test"
journalctl -t htb-study -n 10 --no-pager
~~~
If permissions require it, use sudo journalctl. Install rsyslog only if desired:
~~~bash
sudo apt install rsyslog
sudo systemctl enable --now rsyslog
systemctl status rsyslog --no-pager
logger -t htb-study "Rsyslog practice"
sudo tail -n 30 /var/log/syslog
~~~
The service and file depend on systemd and logging configuration. If systemd is unavailable, do not change WSL settings or shut down active labs without planning; see Microsoft's guide. logger creates a synthetic message; tail reads recent lines. Rsyslog is optional for journal-only practice.
Sources: https://www.rsyslog.com/ ; https://docs.rsyslog.com/doc/getting_started/beginner_tutorials/01-installation.html ; https://learn.microsoft.com/en-us/windows/wsl/systemd/

## ELK Stack (Elasticsearch, Logstash, Kibana)
Elasticsearch indexes/searches structured events, Logstash ingests and transforms them, Kibana provides search and dashboards. Logs and packet captures are different data types; ELK does not automatically capture traffic. Basic self-managed features are free, while certain features require subscriptions. Use a local test deployment, not paid cloud.
Downloads: https://www.elastic.co/downloads ; https://www.elastic.co/downloads/logstash/
Start Docker Desktop with WSL integration, check docker version and docker compose version. The official local quickstart installs Elasticsearch and Kibana only, not Logstash:
~~~bash
mkdir -p ~/htb-elastic-lab
cd ~/htb-elastic-lab
curl -fsSL https://elastic.co/start-local -o start-local.sh
less start-local.sh
sh start-local.sh
~~~
Review downloaded scripts before executing. Official quickstart is not production-secure; keep ports local, protect generated credentials, and do not upload the generated .env or configuration secrets. Trial features expire; self-managed Basic remains available. Open http://localhost:5601 in Windows browser, sign in using the generated local credentials, then Dev Tools Console. Create a synthetic event and search for it:
~~~text
POST htb-study/_doc
{
  "event": "practice_login",
  "status": "success",
  "source": "synthetic"
}

GET htb-study/_search
{
  "query": {
    "match": {
      "event": "practice_login"
    }
  }
}
~~~
Use Kibana Discover with a data view matching htb-study (no time field for this simple example). Elasticsearch endpoint is localhost:9200. For cleanup use the generated project's Docker Compose stop command; do not remove data volumes unintentionally.
Logstash is separate. Download the archive matching the Elasticsearch/Kibana version for a future integrated setup, extract into a practice folder, and from the extracted directory on Linux run:
~~~bash
bin/logstash -e 'input { stdin {} } output { stdout {} }'
~~~
Enter a synthetic text event after startup; observe its structured output, then Ctrl+C. This exercise does not send data to Elasticsearch. A later integrated pipeline requires a secured Elasticsearch output and credentials.
Official references: https://www.elastic.co/docs/deploy-manage/deploy/self-managed/local-development-installation-quickstart ; https://www.elastic.co/docs/reference/logstash/first-event ; https://www.elastic.co/docs/reference/logstash/docker

## Learning sequence and GitHub
1. Install Wireshark and Npcap on Windows.
2. Practice tcpdump/TShark on WSL loopback.
3. Learn ss and lsof for local service diagnostics.
4. Generate and inspect a synthetic syslog event.
5. Explore ELK in a separate local Docker lab when resources permit.

Record actual commands, tool versions, sanitized observations and troubleshooting notes only after testing. Suggested exercises are not evidence of completion. This supplement does not change the Section 24/30 progress checkpoint.
