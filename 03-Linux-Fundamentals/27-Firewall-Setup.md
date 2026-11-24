# Lesson 27/30 — Firewall Setup

## Concepts
A firewall controls traffic using rules based on addresses, protocols, ports and other packet or connection properties. Netfilter is the Linux kernel framework for filtering, connection tracking and NAT. iptables is a traditional rule management interface (often using an nftables backend today); nftables is the modern framework and rule syntax. UFW simplifies administration; firewalld manages dynamic zones and services. Firewall configuration complements authentication, patching and application security; it cannot guarantee prevention of denial-of-service attacks.

## Components
| Component | Purpose |
| --- | --- |
| Table | Organizes rules by function |
| Chain | Ordered group of rules |
| Rule | Matching conditions and action |
| Match | Packet or connection criteria |
| Target | Action or chain jump |

## Tables and chains
| Table | Purpose | Built-in chains |
| --- | --- | --- |
| filter | Packet filtering | INPUT, OUTPUT, FORWARD |
| nat | Address/port translation | PREROUTING, INPUT, OUTPUT, POSTROUTING |
| mangle | Modify packet fields/metadata | PREROUTING, INPUT, FORWARD, OUTPUT, POSTROUTING |
| raw | Early processing, including connection tracking exemptions | PREROUTING, OUTPUT |

The course emphasizes PREROUTING and POSTROUTING for NAT; modern iptables also provides INPUT and OUTPUT NAT chains. A security table is available on suitable systems. INPUT handles locally destined packets; OUTPUT locally generated packets; FORWARD routed transit packets. PREROUTING occurs before routing and POSTROUTING after routing for applicable paths. User-defined chains organize related rules and can be reached by jumps. Rules are processed in order; terminal verdicts stop relevant traversal, while LOG is non-terminating. Unmatched built-in chains apply their policy.

## Targets
| Target | Meaning |
| --- | --- |
| ACCEPT | Permit packet |
| DROP | Silently discard |
| REJECT | Discard with an appropriate error response |
| LOG | Record packet information; continue processing |
| SNAT | Translate source address |
| DNAT | Translate destination address |
| MASQUERADE | Source NAT using outgoing interface address |
| REDIRECT | Redirect to the local machine, optionally another port |
| MARK | Set packet metadata used by routing/filtering |

## Course rule examples (not executed)
~~~bash
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
~~~
sudo elevates privileges; -A appends to the specified chain; -p selects protocol; --dport selects destination port; -j selects target; -m tcp explicitly loads the TCP match (often implicit with -p tcp). Without -t, the default table is filter. Appending an ACCEPT rule does not override an earlier terminal DROP. These rules allow matching traffic from any source unless additional conditions constrain it; do not apply them blindly to live hosts.

## Matches
| Option | Function |
| --- | --- |
| -p / --protocol | Protocol (tcp, udp, icmp) |
| --dport / --sport | Destination / source port |
| -s / --source | Source address or subnet |
| -d / --destination | Destination address or subnet |
| -m state | Older connection state match |
| -m conntrack | Connection tracking information (e.g. --ctstate ESTABLISHED,RELATED) |
| -m multiport | Multiple ports |
| -m tcp / -m udp | Protocol-specific options |
| -m string | Match packet content patterns |
| -m limit | Rate-limited match, not automatically a complete rate limiter |
| -m mark | Packet mark |
| -m mac | Source link-layer MAC where available |
| -m iprange | Address range |

## Safe inspection
~~~bash
sudo iptables -L -n -v --line-numbers
sudo iptables -S
sudo iptables -t nat -L -n -v
~~~
-L lists rules; -n avoids name resolution; -v shows counters/details; --line-numbers displays rule positions; -S prints rules in command-like syntax; -t selects table. Availability and permissions depend on the environment. IPv4 iptables does not itself manage IPv6 rules; use appropriate ip6tables/nftables controls.

## Optional practice and verification
The course lists ten optional tasks: start a test web server on TCP 8080 and block/allow access; block/allow a source IP; block/allow a protocol; create a user-defined chain; jump to it; delete a rule; list rules. The learner chose to continue without optional practice or quizzes. No rules changed, no server started, and no output verified. Practice only on an owned disposable VM with console recovery and a snapshot; avoid modifying active HTB SSH/VPN, WSL networking or Docker-managed rules. Rules may not persist across reboot unless saved through the appropriate distribution mechanism.

## Review status
Full supplied Section 27 reviewed and explained. No mandatory question is present in the provided text. HTB Mark Complete & Next pending. Next: Section 28/30.

Independent English study notes based on the learner-provided HTB Academy lesson, with supplemental technical clarifications.
