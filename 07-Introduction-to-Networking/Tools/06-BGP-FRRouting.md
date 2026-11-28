# BGP and FRRouting

BGP (Border Gateway Protocol, RFC 4271) exchanges reachability between ASes. Each route associates a destination prefix with attributes such as AS_PATH, NEXT_HOP and LOCAL_PREF. eBGP joins different autonomous systems; iBGP distributes reachability inside one. TCP 179 transports BGP messages. Selection is policy-driven; shortest physical distance is not the primary rule. Private-use ASNs include 64512–65534.

```mermaid
flowchart LR
    A[Private AS 64512] <-->|TCP 179 / BGP| B[Private AS 64513]
    B --> P[Example reachable prefix]
```
FRRouting (FRR) is a free routing suite. bgpd implements BGP; zebra integrates routing state with the kernel; vtysh supplies a CLI. [Official source](https://github.com/FRRouting/frr), [docs](https://docs.frrouting.org/en/latest/index.html), [packages](https://frrouting.org/).

Optional reference for an isolated disposable Ubuntu VM: sudo apt update; sudo apt install frr. Consult the version-specific installation guide, enable only needed daemons in /etc/frr/daemons and use authorized configuration. Installation may start services; do not install on a production router for this lesson. WSL2 may not be suitable for realistic routing labs.

```bash
vtysh -c "show version"
sudo vtysh -c "show ip bgp summary"
sudo vtysh -c "show ip bgp"
```
-c sends a single CLI command. show version identifies software; summary lists peer state and prefix counts; show ip bgp lists known BGP routes. Requires installed FRR and running appropriate daemons. Illustrative output: Neighbor 192.0.2.2, AS 64513, State/PfxRcd 1. Numeric prefix count can indicate established session; inspect headers. No public peer or real prefix configured.

Troubleshoot peer reachability, ASN mismatch, TCP connectivity, policy and daemon logs. Defense: peer authorization, prefix filtering, max-prefix, RPKI origin validation, change control and monitoring. RPKI does not validate the whole AS path. UniTrack and ProTrack-MX normally consume provider routing, not operate public BGP. No installation or execution performed.
