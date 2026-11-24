# VPN and OpenVPN — Extended Study Notes (Section 19)

## What is a VPN?
A virtual private network establishes a protected logical connection between a client and another network. A properly configured VPN encrypts traffic carried through its tunnel. It can provide access to private resources without exposing those resources directly to the public internet.

## How a VPN works
1. The VPN client establishes a connection to an authorized VPN gateway.
2. The peers authenticate and negotiate cryptographic protection.
3. A virtual network interface and appropriate routes are configured.
4. Selected network traffic passes through the encrypted tunnel.
5. The gateway forwards permitted traffic to the destination network.

A full-tunnel configuration routes general traffic through the VPN; split tunneling routes only selected destinations through it. Actual behavior depends on the configuration.

## HTB Academy architecture
Windows 11 -> WSL Ubuntu -> OpenVPN client -> encrypted tunnel -> HTB VPN gateway -> assigned lab target.

The learner previously installed OpenVPN, loaded the authorized Academy configuration, observed Initialization Sequence Completed, and successfully authenticated to an assigned Linux target using SSH. These are historical verified results, documented in the VPN setup notes. This conceptual extension does not establish that the VPN is currently connected.

## VPN versus SSH
| Feature | VPN | SSH |
| --- | --- | --- |
| Purpose | Network connectivity | Secure remote shell and related services |
| Scope | Traffic selected by network routes | SSH connection and configured forwarding |
| Example software | OpenVPN, WireGuard | OpenSSH |
| Common port | OpenVPN UDP 1194, configurable | TCP 22, configurable |

VPN access to a network does not automatically authorize access to every host. SSH requires appropriate remote credentials and permissions. The HTB learner uses VPN connectivity to reach assigned targets, then SSH to execute authorized exercises.

## Protocols
OpenVPN is an open-source configurable VPN solution. WireGuard is a modern VPN protocol. IKEv2/IPsec is common in enterprise and mobile environments. L2TP can be paired with IPsec; SSTP uses TLS. PPTP is obsolete for security-sensitive use.

## Installation and connection
~~~bash
sudo apt update
sudo apt install openvpn
openvpn --version
sudo openvpn --config academy-regular.ovpn
~~~
apt update refreshes package indexes; apt install installs software and resolves dependencies; --version displays version information; --config selects a configuration profile. Supply the actual local file path. sudo obtains authorized elevated privileges for network configuration. Keep the foreground VPN process running while using a separate terminal for SSH.

A successful initialization commonly prints Initialization Sequence Completed. This is not alone proof that every destination is reachable.

## Read-only diagnostics
~~~bash
ip -brief address
ip route
pgrep -a openvpn
~~~
ip -brief address displays interface addresses in compact form; inspect for a tunnel interface such as tun0. ip route displays routing information. pgrep -a prints matching process IDs and full command lines; do not publish private configuration paths or other sensitive output.

If VPN initialization succeeded but SSH freezes, check the assigned lab target's expiration and whether the route still exists. A new target may require a new SSH destination, not a new VPN profile. Do not restart a functioning VPN unnecessarily. Some lessons provide only Pwnbox rather than a separately spawned VPN-accessible target.

## Security and limitations
A VPN protects traffic inside the tunnel, not every application or endpoint. The VPN gateway is a trust boundary. HTTPS and SSH still provide important end-to-end protections. VPNs do not automatically prevent phishing, malware, compromised accounts or tracking. Use authorized profiles, trusted gateways, and appropriately configured authentication. Never publish VPN profiles, embedded keys, credentials, private target addresses or session identifiers.

## Practical verification
Previous installation, initialization and SSH use were observed during earlier lessons. The diagnostic commands above are supplemental suggestions, not newly executed results. No software was installed or configuration changed during this conceptual extension.

Further reading: https://openvpn.net/community-docs/

Independent English study notes extending HTB Academy Section 19.
