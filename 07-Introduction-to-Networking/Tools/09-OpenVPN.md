# OpenVPN Community Edition

Open-source VPN client/server. TLS control channel authenticates endpoints and negotiates data-channel protection. TUN passes IP packets, TAP Ethernet frames; client routes determine traffic selection. A .ovpn file may contain embedded certificates, private keys and remote configuration, so never commit actual files. [Official downloads](https://openvpn.net/community/), [installation](https://openvpn.net/community-docs/installing-openvpn/), [HOWTO](https://community.openvpn.net/HOWTO), [HTB Academy connection](https://help.hackthebox.com/en/articles/12710285-connecting-to-academy-vpn).

Windows 11: download architecture-appropriate Community installer; verify publisher/signature; install required drivers; launch OpenVPN GUI; import authorized Academy profile; connect; inspect logs. Use a supported client version; do not disable certificate verification.

Ubuntu WSL2 optional reference:
```bash
sudo apt update
sudo apt install openvpn
openvpn --version
ls -l /dev/net/tun
sudo openvpn --config "$HOME/academy.ovpn"
```
sudo elevates; apt update refreshes package lists; apt install installs; --version displays version; ls -l checks TUN device availability; --config selects profile path. Use real authorized local file, not example name unless it matches. Keep foreground process running; Ctrl+C disconnects. Illustrative message: Initialization Sequence Completed. It is not proof all targets are reachable.

```bash
ip -br address
ip route
ip route get 10.10.10.50
```
Illustrative routes:
```text
default via 192.168.1.1 dev eth0
10.10.10.0/24 dev tun0
192.168.1.0/24 dev eth0
```
The most specific matching route takes precedence; route get does not send a probe. Linux VPN inside WSL requires appropriate TUN support; Windows-host VPN may not appear in WSL. See [Microsoft WSL networking](https://learn.microsoft.com/en-us/windows/wsl/networking). Do not change firewall or disable protections to force access. Use HTB supported VM or Pwnbox if WSL connectivity is unsuitable.

Troubleshooting: validate profile download, system time, certificate and auth errors; inspect assigned routes, overlap with local subnets, DNS and gateway. Follow HTB official server/protocol/MTU troubleshooting if needed. Keep profiles outside Git repositories; do not expose logs containing private endpoints or credentials. UniTrack management access could use a separately authorized VPN; ProTrack-MX still requires TLS/RLS. No installation, connection or test required today.
