# Network segmentation, ACLs, firewalls and secure zones

VLANs separate Layer 2 broadcast domains; IP subnets specify Layer 3 prefixes. Routers forward between prefixes. ACLs match traffic by addresses, protocols, ports and direction, often with ordered rules. Stateful firewalls track connections. Host firewalls protect endpoints; DMZs isolate exposed services; IDS alerts and IPS may block. OSPF exchanges routing information between routers; restrict adjacencies and authenticate where supported. QoS prioritizes latency-sensitive voice but does not encrypt it.

## Original traffic policy (illustrative)
| Source | Destination | Service | Action |
| --- | --- | --- | --- |
| Student | Application proxy | TCP 443 | Allow |
| Student | Database | Any | Deny |
| Backend | PostgreSQL database | TCP 5432 if applicable | Allow approved hosts |
| Printer | Internet | Any | Deny except approved updates |
| Management | Network devices | Approved SSH/HTTPS | Allow authorized administrators only |

Use default deny, stateful return traffic, logs and documented exceptions. Restrict unneeded SMB 445 and NTLM; use TLS, trusted procurement and device quarantine. No production changes are proposed without authorization.

## Built-in and free tools
Windows PowerShell:
```powershell
Get-NetFirewallProfile
Get-NetFirewallRule -Enabled True
```
First lists profile settings; second filters enabled rules. Ubuntu:
```bash
sudo ufw status verbose
```
If missing: sudo apt update; sudo apt install ufw. Do not enable/change policy without approved configuration and rollback access. UFW is a host firewall frontend, not an enterprise VLAN implementation. [Microsoft Firewall](https://learn.microsoft.com/en-us/windows/security/operating-system-security/network-security/windows-firewall/), [Ubuntu firewall](https://documentation.ubuntu.com/server/how-to/security/firewalls/), [NIST firewall guidance](https://csrc.nist.gov/pubs/sp/800/41/r1/final).

## Troubleshooting
Check route, direction, rule ordering, return path, service listener and logs. Verify approved flows and denied flows in authorized environments. WSL2 network policy may differ from the host. UniTrack: isolate scanner and backend flows. ProTrack-MX: restrict privileged services and enforce Supabase RLS. No deployment or execution verified.
