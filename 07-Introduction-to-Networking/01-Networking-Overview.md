# Section 01 — Networking Overview

Source: learner-supplied HTB Section 1, [official module](https://academy.hackthebox.com/course/preview/introduction-to-networking). Independent explanations and original examples.

## Fundamentals
A network enables devices to exchange data. A client requests a service and a server supplies it. A switch forwards local Ethernet frames using MAC addresses; a router forwards IP packets across networks. An access point connects wireless devices. An ISP supplies connectivity. A default gateway is the next hop for traffic without a more specific route.

| Topology | Structure | Tradeoff |
| --- | --- | --- |
| Star | Central switch | Central point of failure |
| Mesh | Multiple paths | Resilience, greater complexity |
| Tree | Hierarchical branches | Scalable, upstream dependencies |

Copper Ethernet and coaxial cable transmit electrical signals, fiber uses light, and wireless uses radio waves. Ethernet can also operate over fiber. Protocols define communication rules: TCP provides a reliable ordered byte stream; UDP sends datagrams without built-in reliability or ordering; IP handles addressing and routing; IPX is a legacy Novell protocol; DHCP distributes network settings; DNS resolves names and other records.

## Defense in depth
A flat network offers broad reachability. Segmentation separates trust zones and controls traffic between them. VLANs separate Layer 2 broadcast domains; subnets define Layer 3 prefixes; firewall rules and ACLs enforce policy. A subnet alone is not a security boundary. Lateral movement is movement between compromised systems; pivoting uses an intermediary to reach otherwise inaccessible resources.

The lesson's house analogy: a fence represents ACLs and controlled entry points; lights represent documented assets and traffic visibility; window deterrents represent intrusion detection. Maintain an inventory and approved traffic matrix. Unexpected printer-to-server HTTP, printer internet access, or scanning originating from a printer network should prompt investigation, not automatically prove compromise. DHCP leases may change, so enforce policy at the zone level and maintain asset attribution. An IDS alerts; an inline IPS may drop traffic. Suricata and Snort are open-source engines with rule-based detection and PCAP analysis. Encrypted traffic and missing sensor visibility limit detection.

## Subnetting and the assessor's oversight
| Prefix | Mask | Total addresses | Conventional usable hosts |
| --- | --- | ---: | ---: |
| /24 | 255.255.255.0 | 256 | 254 |
| /25 | 255.255.255.128 | 128 | 126 |

A /24 has 24 network bits and 8 host bits; /25 has 25 network bits and 7 host bits. A /24 can be split into two /25 prefixes. For 192.168.1.0/24, usable addresses are .1–.254. The two /25s have usable .1–.126 and .129–.254; broadcast addresses .127 and .255. Network and broadcast addresses are reserved in these conventional examples; /31 and /32 have special semantics.

The supplied HTB scenario:
| Device | Address |
| --- | --- |
| Server gateway | 10.20.0.1/25 |
| Domain controller | 10.20.0.10/25 |
| Client gateway | 10.20.0.129/25 |
| Workstation | 10.20.0.200/25 |
| Tester | 10.20.0.252/24, gateway 10.20.0.1 |

Server subnet: 10.20.0.0/25, usable .1–.126. Client subnet: 10.20.0.128/25, usable .129–.254. The tester mistakenly treats both as on-link and may send ARP requests for the server or invalid-for-this-segment gateway instead of routing through the client gateway. On isolated Layer 2 segments, these requests receive no reply. Proxy ARP or shared Layer 2 could change behavior. Correct addressing would use the client prefix and approved client gateway, subject to network policy and explicit authorization; it does not guarantee server access. The lesson describes an assessor who reached workstations but mistakenly reported a domain controller offline. A domain controller provides centralized identity and authentication services. Impacket is a Python network protocol toolkit; successful use of a tool is not proof that assessment coverage is complete. Check addressing, routing, filtering and scope before concluding that a host is offline. Never change production network settings without authorization.

## Internet communication and addressing
```text
Home laptop -> home router -> ISP / interconnected networks
                                  |
                           DNS lookup (separate)
                                  |
                     company firewall/router -> web server
```
The browser parses a URL, looks up its hostname if not cached, selects a route, and sends off-link traffic to a gateway. Routers forward packets. The browser establishes the appropriate transport and, for HTTPS, uses TLS to protect HTTP; HTTP/3 uses QUIC over UDP. The server returns a response. Return paths can differ. DNS may be provided by parties other than the ISP, and an IP is a logical address, not precise geographic coordinates.

Original URL: https://portal.example.com/students?id=17
- Scheme: https
- FQDN (Fully Qualified Domain Name): portal.example.com; a trailing root dot can explicitly denote a complete DNS name.
- Path: /students
- Query: id=17

A URL locates a resource and its access scheme; an FQDN names a host in the DNS hierarchy; an IP address identifies a logical network interface/address (e.g. 192.0.2.25, reserved for documentation). DNS can return multiple addresses or none. HTTP is a web request/response protocol; HTTPS protects it with TLS.

## Five enterprise zones
| Zone | Purpose and controls |
| --- | --- |
| Web/DMZ | Isolate public services; permit only intended inbound HTTPS; restrict access to internal networks |
| Workstations | Host firewalls, restrict peer-to-peer connections, separate servers |
| Administration | Restrict router/switch management to authorized administrators, MFA where supported, secure management protocols |
| IP phones | Isolate VoIP; control eavesdropping risk; QoS prioritizes latency-sensitive traffic |
| Printers | Limit printing and scanning flows, internet egress and sensitive data exposure |

Spoofing means impersonating an identity or address; an on-path (man-in-the-middle) attack interferes with communication. OSPF (Open Shortest Path First) is a link-state routing protocol; unexpected routing advertisements can alter traffic paths if untrusted adjacencies are allowed. Restrict routing interfaces and authenticate neighbors where supported. QoS (Quality of Service) prioritizes traffic but does not authenticate or encrypt it. NTLMv2 is Windows challenge-response authentication; captured responses are not plaintext passwords, but may enable offline guessing or relay under certain conditions. SMB commonly uses TCP 445; restrict unnecessary SMB, enforce signing where appropriate and reduce NTLM use.

## Printer security case
The lesson's authorized physical assessment illustrates malicious peripheral/supply-chain risk: an unexpected printer was connected to an internal network, allowing remote access and sensitive authentication exposure. A reverse shell is an outbound connection enabling remote command access. Phishing exploits trust to induce an action. Defenses: verify unexpected deliveries through a known channel, inventory and quarantine new devices, restrict printer internet access and device-initiated connections, deny unnecessary workstation-to-printer TCP 445, allow only approved print and scan paths, and monitor anomalous outbound connections. A scanner may legitimately need an approved mail server. Blocking one port alone is not sufficient; test required functions and maintain secure device updates.

## Original examples and project applications
Example: a campus printer needs printing only from an approved print server, not direct database or internet access. A student network may access an HTTPS application, but never its database directly. UniTrack: proposed isolated scanner, student/guest, backend/database and management zones; scanners reach the approved API only, and backend reaches its database. ProTrack-MX: constrain privileged cloud access, protect Supabase service-role credentials, use authentication and row-level security; VLANs do not replace application authorization. These are design recommendations, not findings about either deployment.

## Read-only command reference
Windows PowerShell: ipconfig /all (adapter configuration), route print -4 (IPv4 routes), Resolve-DnsName example.com (DNS records).
Ubuntu WSL2: ip -br address (brief addresses), ip route (routes), ip route get 192.0.2.25 (local route decision, no probe), getent ahosts example.com (system name resolution). Illustrative output: default via 192.168.1.1 dev eth0. Actual WSL2 interfaces and gateways vary. Do not publish sensitive internal output.

## Individual guides
- [Network diagnostics](Tools/01-Network-Diagnostics.md)
- [Network segmentation and firewalls](Tools/02-Network-Segmentation.md)
- [Suricata](Tools/03-Suricata.md)
- [Snort 3](Tools/04-Snort.md)
- [Impacket](Tools/05-Impacket.md)

References: [DNS RFC 1034](https://www.rfc-editor.org/rfc/rfc1034), [URI syntax RFC 3986](https://www.rfc-editor.org/rfc/rfc3986), [CIDR RFC 4632](https://www.rfc-editor.org/rfc/rfc4632).

## Verification
Full learner-supplied Section 1 studied and documented. No mandatory question or lab shown. +10 reward displayed, not yet verified as earned. No tool installed or executed by learner. Next: Mark Complete & Next in Academy; await next section and completion evidence.
