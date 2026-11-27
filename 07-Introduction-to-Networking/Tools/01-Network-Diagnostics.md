# Network diagnostics and DNS

Hosts choose routes by longest matching prefix, select the next hop and resolve local link addresses when necessary. DNS resolves records independently of routing. DHCP provides addresses, masks, gateways and resolvers.

## Windows 11 (built in)
```powershell
ipconfig /all
route print -4
Resolve-DnsName example.com
```
/all displays detailed adapter settings; -4 filters IPv4 routes; Resolve-DnsName returns DNS records. No installer required. [ipconfig](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/ipconfig), [route](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/route_ws2008), [DNS cmdlet](https://learn.microsoft.com/en-us/powershell/module/dnsclient/resolve-dnsname).

## Ubuntu WSL2
```bash
ip -br address
ip route
ip route get 192.0.2.25
getent ahosts example.com
```
-br is brief output; route get computes the local routing decision without sending a probe. getent queries the system name service. Example (illustrative): default via 192.168.1.1 dev eth0. If ip is missing, sudo apt update then sudo apt install iproute2. Official [iproute2 source](https://git.kernel.org/pub/scm/network/iproute2/iproute2.git/) and [WSL networking](https://learn.microsoft.com/en-us/windows/wsl/networking). NAT and mirrored networking can produce different routes and addresses.

## Troubleshooting and security
Compare prefix, interface, gateway, resolver and policy. DNS success does not establish application reachability; missing replies do not prove a host offline. Never modify production configuration without approval. Redact sensitive internal IPs and identifiers from public logs. UniTrack: distinguish loopback binding from intended LAN access. ProTrack-MX: separate DNS, TLS, routes and API authentication problems. No learner-side command execution verified; reference only.
