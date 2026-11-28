# Network hardware and NICs

A NIC is a physical or virtual interface. Physical Ethernet NICs handle signaling and frames, often with a MAC address; virtual devices can be Ethernet-like or Layer 3. Repeaters regenerate signal; hubs repeat to other ports, bridges and switches learn source MAC addresses and forward/filter frames, routers select destination IP routes. Modems adapt signals to the access medium; gateways may provide next-hop routing or protocol translation; firewalls enforce policy. A combined home router may implement all several functions. Layer assignments describe primary roles, not every capability.

| Component | Example failure | Defensive measure |
| --- | --- | --- |
| NIC | Driver or link fault | Supported driver, physical link check |
| Switch | Central outage or loop | Secure management, loop prevention, redundancy |
| Router | Wrong route | Approved routing configuration and monitoring |
| Firewall | Unexpected denied traffic | Review policy, logs and change history |
| Modem | Access link unavailable | Verify carrier link and equipment state |

Windows built-in PowerShell:
```powershell
Get-NetAdapter
Get-NetIPConfiguration
Get-NetRoute -AddressFamily IPv4
```
Adapter list includes link status; IP configuration includes addresses, gateway and DNS; route command filters IPv4. Linux Ubuntu: ip -br link; ip -br address; ip route; bridge link; bridge fdb show. Install iproute2 only if missing: sudo apt update; sudo apt install iproute2. The bridge FDB stores MAC forwarding entries for bridge ports. A bridge may not exist; empty output is not proof of failure. WSL2 often exposes virtual Ethernet rather than physical Wi-Fi/NICs. Illustrative MAC 02:00:00:00:00:10 is locally administered. Use hardware manufacturer official driver download for exact model, not random driver sites. [iproute2](https://git.kernel.org/pub/scm/network/iproute2/iproute2.git/), [Microsoft Get-NetAdapter](https://learn.microsoft.com/en-us/powershell/module/netadapter/get-netadapter), [WSL networking](https://learn.microsoft.com/en-us/windows/wsl/networking).

Original example: scanner Ethernet link is down; inspect NIC status, cable, switch port and approved VLAN before blaming UniTrack API. For ProTrack-MX distinguish local adapter/routing failures from cloud API/auth failures. Never change production equipment without authorization. No installation or live inspection performed.
