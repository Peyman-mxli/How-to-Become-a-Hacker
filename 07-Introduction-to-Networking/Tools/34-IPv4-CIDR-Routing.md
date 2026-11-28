# IPv4 addressing, CIDR and routing

IPv4 addresses contain 32 bits; CIDR prefix /n identifies n network bits. Historical class A/B/C default /8,/16,/24 masks are obsolete for modern routing; specify actual prefix. RFC 1918 ranges 10/8, 172.16/12, 192.168/16 are private, not all non-private addresses are public. /27 mask 255.255.255.224 gives 32 total addresses. 192.168.20.34/27 belongs to .32–.63, conventional usable .33–.62 (30). /31 and /32 exceptions apply. IPv6 uses 128-bit addresses and different neighbor discovery.

Routing maintains paths; forwarding selects next hop from applicable routes. Connected, static, dynamic and default routes exist. Longest prefix match: 10.20.30.50 matches 10/8, 10.20/16, 10.20.30/24 and /0; /24 wins under ordinary applicable routing policy. Equal specificity may depend on metrics or ECMP. An off-link destination uses gateway MAC for local Ethernet. IP best effort does not guarantee arrival. Routers decrement TTL/Hop Limit and rebuild local frames; NAT may alter endpoints. A route is not authorization.

```mermaid
flowchart LR
    C[Client] --> G[Local next hop]
    G --> R[Router]
    R --> S[Remote destination]
```
Windows built-in: Get-NetRoute -AddressFamily IPv4; Find-NetRoute -RemoteIPAddress 203.0.113.20. Linux: ip route; ip route get 203.0.113.20. The documentation address is illustrative; route get calculates local decision without sending packets. iproute2 package if missing on Ubuntu: sudo apt update; sudo apt install iproute2. No install required. Example via 192.168.1.254 dev eth0 src 192.168.1.50 is fictional. WSL2 routes may differ from Windows. Python standard library ipaddress.ip_interface("192.168.20.34/27").network gives 192.168.20.32/27. No new Python package.

Troubleshooting: prefix, next-hop reachability, route policy, overlap, VPN, firewall, return path; do not infer service failure from a single timeout. Security: anti-spoofing, segmentation, route validation, documented egress. UniTrack proposed subnet plan and ProTrack-MX cloud routing are design examples only. [CIDR RFC 4632](https://www.rfc-editor.org/rfc/rfc4632), [IPv4 RFC 791](https://www.rfc-editor.org/rfc/rfc791), [ipaddress docs](https://docs.python.org/3/library/ipaddress.html), [iproute2 source](https://git.kernel.org/pub/scm/network/iproute2/iproute2.git/), [Microsoft Find-NetRoute](https://learn.microsoft.com/en-us/powershell/module/nettcpip/find-netroute). No commands executed.
