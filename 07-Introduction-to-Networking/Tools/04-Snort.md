# Snort 3 — network IDS/IPS

Snort decodes packets, tracks flows, uses protocol inspectors and matches rules. Snort 3 uses Lua configuration and LibDAQ for packet sources. Passive detection alerts; inline prevention requires suitable forwarding and DAQ configuration. Visibility is limited to traffic delivered to the sensor.

## Official downloads and setup reference
[Downloads](https://www.snort.org/snort3), [installation](https://docs.snort.org/start/installation), [source](https://github.com/snort3/snort3). On Ubuntu Linux, follow the official version-specific prerequisites and LibDAQ build instructions. From extracted Snort source:
```bash
./configure_cmake.sh --prefix=/usr/local
cd build
make -j2
sudo make install
snort -V
```
--prefix selects install location; -j2 sets build concurrency; -V prints version. Verify dependency and configuration paths.
```bash
snort -T -c /usr/local/etc/snort/snort.lua
snort -c /usr/local/etc/snort/snort.lua -r "$HOME/lab.pcap" -A alert_fast
```
-T tests config; -c selects Lua file; -r reads an existing authorized capture; -A chooses alert output. Adjust actual configuration location. [Alert logging](https://docs.snort.org/start/alert_logging).
Original rule:
```text
alert icmp any any -> $HOME_NET any (msg:"LOCAL lab ICMP observation"; itype:8; sid:1000001; rev:1;)
```
Set HOME_NET and load local rules; validate syntax. This is a detection example, not proof of attack.

## Troubleshooting and application
Check DAQ, library paths, Lua settings, rule paths and capture visibility. Tune false positives; protect capture data; encrypted payloads may be unavailable. Use authorized sensor or offline capture rather than assuming WSL2 sees the LAN. Correlate alerts with UniTrack/ProTrack-MX service logs where available. No installation or execution performed.
