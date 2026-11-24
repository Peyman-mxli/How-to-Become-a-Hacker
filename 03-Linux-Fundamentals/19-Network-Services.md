# Lesson 19/30 — Network Services

## Overview
Network services support remote administration, resource sharing, file transfers and web communication. Understanding their protocols, configuration and access controls is important for authorized security assessments.

| Service | Common port | Purpose |
| --- | --- | --- |
| SSH | TCP 22 | Encrypted remote administration |
| FTP | TCP 21 | File transfer control connection |
| NFS | TCP 2049 | Network file sharing |
| HTTP | TCP 80 | Web communication |
| HTTPS | TCP 443 | TLS-protected web communication |
| OpenVPN | UDP 1194 | VPN connectivity |
| Python HTTP server | TCP 8000 | Temporary HTTP hosting |

Ports are defaults or common conventions and can be changed.

## SSH — why and how
~~~bash
sudo apt install openssh-server
systemctl status ssh
ssh username@SERVER_IP
~~~
APT installs the server, systemctl inspects the service, and ssh connects to an authorized remote account. Verify host identity before accepting a new host key. The server configuration is /etc/ssh/sshd_config. SSH encrypts connections; SFTP provides file transfer over SSH. Standard FTP does not encrypt credentials or transfers.

The learner has previously established SSH connections to assigned HTB targets, including reconnection after expiration. Those actual results are recorded in earlier lesson and VPN setup notes.

## NFS
NFS presents remote filesystems through local mount points. The server's /etc/exports file specifies exported directories, permitted clients and options.

~~~bash
sudo apt install nfs-kernel-server
systemctl status nfs-kernel-server
~~~
These are lesson examples, not newly executed commands.

| Option | Meaning |
| --- | --- |
| rw | Read/write access |
| ro | Read-only access |
| root_squash | Map remote root to an unprivileged identity |
| no_root_squash | Preserve remote root privileges |
| sync | Respond after committing changes to stable storage |
| async | Allow asynchronous writes |

Prefer least privilege and trusted client restrictions. no_root_squash can introduce serious security risks. NFS security and encryption depend on configuration and protocol version.

## Web servers
Apache, Nginx, Lighttpd and Caddy deliver web content over HTTP or HTTPS.

~~~bash
sudo apt install apache2
~~~
Apache global configuration commonly resides at /etc/apache2/apache2.conf and the default document root is /var/www/html on Debian-family installations.

Options Indexes enables directory listing; FollowSymLinks permits following symbolic links; AllowOverride All permits supported .htaccess overrides; Require all granted permits access. Review these settings to avoid unintended disclosure.

Python supplies a temporary HTTP server:
~~~bash
python3 -m http.server
~~~
-m runs the named module; default port is TCP 8000. This is an illustrative command. For local-only practice, restrict binding to loopback and serve a dedicated non-sensitive folder. Changing the port to 443 does not by itself provide HTTPS.

## VPN and OpenVPN
A VPN establishes a protected tunnel to another network. It does not guarantee anonymity or prevent all attacks.

~~~bash
sudo apt install openvpn
sudo openvpn --config academy-regular.ovpn
~~~
The first command installs the package; --config selects the downloaded connection profile. Supply its actual local path. These commands were used earlier in the learner's WSL Ubuntu VPN setup, which is documented separately. No profile, credential or private address is published.

## Verification
Section 19 has no mandatory questions. No new services were installed, started or reconfigured during this lesson. Previous SSH and OpenVPN practice was verified in earlier exercises. Section 18 was followed by Section 19; the learner did not explicitly show HTB acceptance of dbus. Section 18 provided Pwnbox only, with no target or VPN option visible, and its upstream answer was independently identified.

Independent English study notes based on HTB Academy.
