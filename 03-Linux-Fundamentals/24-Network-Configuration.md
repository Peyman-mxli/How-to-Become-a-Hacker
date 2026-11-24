# Lesson 24/30 — Network Configuration

**Status:** Introductory study notes; official lesson text and any required exercises have not yet been reviewed. No lab commands or platform submissions are verified for this section.

## Concepts

Linux network configuration determines how interfaces, IP addresses, routes and name resolution allow a system to communicate. An interface represents a physical or virtual network connection. IPv4 and IPv6 identify network endpoints; a prefix length describes the network portion of an address. The default gateway forwards traffic for destinations without a more specific route. DNS resolves names to records such as IP addresses. DHCP can supply addresses, routes and DNS settings automatically; static configuration sets them explicitly.

## Inspection commands

| Command | Purpose |
| --- | --- |
| `ip addr` | Show interface addresses and prefix lengths |
| `ip -br addr` | Show a concise address summary |
| `ip link` | Show interfaces, link state, MAC addresses and MTU |
| `ifconfig` | Legacy interface inspection tool; may not be installed |
| `ip route` | Show IPv4 routes, including the default gateway |
| `hostname -I` | List configured non-loopback IP addresses |
| `cat /etc/resolv.conf` | Inspect resolver configuration; may be generated automatically |
| `ss -tuln` | Inspect listening TCP and UDP sockets numerically |
| `ping -c 4 127.0.0.1` | Send four ICMP echo requests to the local loopback address |

## Reading output

In an illustrative address such as 192.168.1.10/24, 192.168.1.10 is the IPv4 address and /24 is the network prefix length. This is an example, not a measured lab address. The loopback interface (lo) commonly has 127.0.0.1/8. Interface names vary by environment; eth0, enp0s3 and tun0 are examples, not confirmed local interfaces. MTU is maximum transmission unit. A route such as `default via 192.168.1.1 dev eth0` specifies a next-hop gateway and outgoing interface.

The ss flags -t, -u, -l and -n select TCP, UDP, listening sockets and numeric addresses/ports respectively.

## WSL and HTB

The learner uses Ubuntu under WSL on Windows, with previously documented HTB VPN and SSH setup. A local WSL interface and an HTB remote lab interface belong to different environments. Read-only inspection is appropriate for either; do not change interface addresses, routes or resolver settings on an active VPN or SSH connection without understanding the consequences. A VPN creates a logical private network path; SSH provides a secure remote terminal over a reachable network.

## Suggested practice

Run `ip -br addr` and `ip route` in the appropriate authorized environment, then identify interface names, addresses and the default route. Use `ip --help` or `man ip` to review syntax. Commands here are suggestions only; no output is fabricated.

## Next action

Review the learner's actual Section 24 content and questions. Record question keywords, selected commands, parameter meanings, real output, extracted answers and verification status where applicable. Keep the module completion count at 23/30 until the section is studied.

Source: [HTB Academy Linux Fundamentals course outline](https://academy.hackthebox.com/course/preview/linux-fundamentals). Independent introductory notes.
