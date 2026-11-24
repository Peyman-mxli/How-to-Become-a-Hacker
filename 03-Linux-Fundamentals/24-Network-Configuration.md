# Lesson 24/30 — Network Configuration

## Concepts
Linux networking combines physical or virtual interfaces, IP addressing, routing, name resolution and access policies. TCP/IP is the protocol suite; DNS resolves names; DHCP supplies network settings dynamically; FTP transfers files but traditional FTP does not encrypt credentials. ICMP supports diagnostics. Network configuration and troubleshooting support authorized penetration testing and defensive administration.

## Interface inspection
| Command | Purpose |
| --- | --- |
| `ifconfig` | Legacy interface and traffic statistics |
| `ip addr` | IPv4/IPv6 addresses and prefixes |
| `ip -br addr` | Concise interface summary |
| `ip link` | Link state, MAC addresses and MTU |
| `ip route` | IPv4 routing table |
| `hostname -I` | Configured non-loopback IP addresses |

Interpretation: eth0 and eth1 are example Ethernet interfaces; lo is loopback (127.0.0.1/8 and ::1/128); tun0 is a common VPN interface name. inet and inet6 denote address families, brd is broadcast, link/ether identifies a link-layer address, dynamic indicates dynamic assignment, valid_lft and preferred_lft are address lifetimes, RX/TX are receive/transmit statistics, UP is administrative state and LOWER_UP indicates lower-layer connectivity. MTU is maximum transmission unit. Course examples illustrate /18 and /20 network prefixes; they are not learner measurements.

## Configuration examples
These modify network state. Practice only in an owned disposable VM with a snapshot and console access, not on the active WSL, VPN or SSH interface.

~~~bash
sudo ifconfig eth0 up
sudo ip link set eth0 up
sudo ifconfig eth0 192.168.1.2
sudo ifconfig eth0 netmask 255.255.255.0
sudo ip addr add 192.168.1.2/24 dev eth0
sudo route add default gw 192.168.1.1 eth0
sudo ip route add default via 192.168.1.1 dev eth0
~~~

sudo elevates privileges; link set changes an interface; addr add adds an address rather than replacing all existing ones; dev selects the interface; default identifies a fallback route; via/gw selects the next hop. /24 corresponds to 255.255.255.0. Do not run both alternative commands blindly; an existing default route can conflict.

## DNS and persistence
Read current settings with `cat /etc/resolv.conf`. The lesson illustrates editing the resolver file using `sudo vim /etc/resolv.conf` and specifying public resolvers 8.8.8.8 and 8.8.4.4. Direct edits may be overwritten by NetworkManager, systemd-resolved or WSL.

The lesson's legacy ifupdown static configuration example uses /etc/network/interfaces:
~~~text
auto eth0
iface eth0 inet static
  address 192.168.1.2
  netmask 255.255.255.0
  gateway 192.168.1.1
  dns-nameservers 8.8.8.8 8.8.4.4
~~~
auto enables boot activation; iface declares an IPv4 static interface. The example uses `sudo systemctl restart networking` to apply settings on systems with that service. Persistent configuration depends on the actual manager: ifupdown, Netplan, NetworkManager, systemd-networkd or WSL. Do not overwrite automatically managed files or restart networking during an active remote session.

## Access control and hardening
DAC lets resource owners manage permissions (Linux chmod/chown). MAC enforces centrally defined security policies, independent of owner discretion; security labels and policy govern access. RBAC grants permissions through roles (e.g., a school application's administrator, teacher and student roles). These are authorization models that complement network admission control, not interchangeable network admission protocols.

SELinux and AppArmor implement mandatory access control through Linux security modules. SELinux uses security contexts and policies; AppArmor commonly confines applications with path-based profiles. TCP wrappers are legacy host-based controls using client addresses for services that actually support libwrap; they do not protect arbitrary modern services. Use supported firewall and service-specific controls where appropriate.

## Monitoring
syslog/rsyslog collect and forward logs; ELK (Elasticsearch, Logstash, Kibana) supports indexing, ingestion and visualization; ss shows sockets; lsof lists open files including sockets; Wireshark, tshark and tcpdump analyze packets in authorized environments. Unencrypted protocols can expose confidential data; prefer encrypted alternatives such as SFTP or FTPS where appropriate. Do not collect third-party credentials.

## Troubleshooting
| Command/tool | Purpose |
| --- | --- |
| `ping -c 4 127.0.0.1` | Four local ICMP echo requests |
| `ping -c 4 8.8.8.8` | Example IP connectivity check |
| `traceroute HOST` | Trace responding intermediate hops |
| `netstat -a` | Legacy display of listening and connected sockets |
| `ss -tuln` | Numeric listening TCP and UDP sockets |
| `ss -tun` | Numeric non-listening TCP and UDP sockets |
| `nslookup example.com` | Example DNS resolution test |
| `lsof -i` | Processes with network-related open files |
| tcpdump / Wireshark | Packet analysis |
| Nmap | Authorized network/service discovery |

ping output includes sequence, TTL, RTT and loss. The lesson shows four replies and zero loss; not a learner execution. No ICMP response does not prove a host is offline. traceroute increases TTL; asterisks mean no response for the probe, not necessarily failed forwarding. The lesson excerpt shows responding devices at hops 2 and 4; hop 1 and 3 are unanswered. netstat Recv-Q/Send-Q describe queued data, not lifetime byte totals. LISTEN means a service awaits connections; ESTABLISHED indicates a connected TCP session. A bind on 127.0.0.1 is local; 0.0.0.0 represents all IPv4 interfaces, subject to firewall and routing. ss flags: -t TCP, -u UDP, -l listening, -n numeric. Common issues include incorrect IP/routes/DNS, filtering, packet loss, congestion, cables/hardware and outdated software.

## Optional exercises and verification
The source lists ten optional hardening tasks: four SELinux tasks (installation, file policy, service allow/deny policy); three AppArmor tasks (file and service restrictions); three TCP wrapper tasks (allow one address, deny one address, allow a range). These are conceptual goals, not guaranteed direct capabilities of each mechanism; user-specific service policies may require additional identity and access controls. Use a personal VM and snapshots. No mandatory question or answer field is present in the supplied Section 24 text. No new lab commands were executed, and none of the optional tasks were verified. Full supplied lesson reviewed and explained; platform Mark Complete & Next not yet confirmed.

Suggested read-only WSL practice: ip -br addr, ip route, cat /etc/resolv.conf, ss -tuln. Do not publish private VPN details, credentials or sensitive lab data.

Independent English study notes based on the learner-supplied HTB Academy Section 24, with supplemental technical clarifications. Next: Section 25/30.
