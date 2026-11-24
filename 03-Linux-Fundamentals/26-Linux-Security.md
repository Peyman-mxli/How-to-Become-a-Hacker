# Lesson 26/30 — Linux Security

## Security principles
Linux systems are not immune to compromise. Exposure, software complexity, vulnerable packages, incorrect permissions and insecure services affect risk. Defense in depth combines patching, network controls, authentication, least privilege, mandatory access control, logging and ongoing audits.

## Package and kernel updates
The lesson demonstrates:
~~~bash
sudo apt update && sudo apt dist-upgrade
~~~
apt update refreshes package metadata; && runs the second command only after success; dist-upgrade resolves changing dependencies and may remove packages. Review the proposed changes first. apt upgrade is a less disruptive alternative for ordinary updates. apt list --upgradable shows available upgrades. Kernel updates can require a reboot. WSL2's Microsoft-maintained kernel normally follows the WSL update mechanism rather than ordinary Ubuntu kernel packages.

## Network filtering
Host firewalls restrict incoming/outgoing traffic. iptables manages packet-filtering rules; nftables is the modern Linux framework; UFW and firewalld are higher-level management tools. Supplemental read-only examples (not executed):
~~~bash
sudo iptables -L -n -v
sudo ufw status verbose
sudo nft list ruleset
~~~
-L lists rules, -n disables name resolution, -v displays detailed counters. Use the tool matching the installed firewall; not all are installed. Avoid changing rules on an active SSH or VPN connection without recovery access.

## SSH hardening and least privilege
Use individual accounts and key-based SSH authentication. Relevant sshd configuration directives are:
~~~text
PermitRootLogin no
PasswordAuthentication no
KbdInteractiveAuthentication no
PubkeyAuthentication yes
~~~
These are illustrative settings for a server configured for key-only authentication; preserve intentional MFA configurations. Verify authorized key access in a second session and validate configuration with sudo sshd -t before applying changes. Include files and Match blocks affect effective settings. Do not change an active HTB target or WSL SSH configuration solely to complete this lesson.

Prefer non-root accounts, grant only necessary privileges, and edit sudoers using visudo. sudo -l shows permitted commands for the current user. Avoid granting broad administrative permissions when a narrow privilege suffices. UniTrack application roles are an analogous application-level least-privilege example, not an OS sudo configuration.

## Fail2ban and authentication
Fail2ban monitors configured log sources for repeated failures and temporarily applies configured ban actions, often firewall rules against source IP addresses. Typical jail settings include maxretry (threshold), findtime (observation interval), and bantime (ban duration). It is not the same as locking a specific user account; shared IPs can cause collateral blocking and distributed attempts may evade per-IP limits. Use account-level controls, strong passwords, rate limiting and MFA where appropriate. Supplemental inspection: sudo fail2ban-client status (if installed). No installation or change performed.

## Auditing
Review kernel and packages, users and groups, sudo privileges, writable files, SUID/SGID programs, scheduled tasks, active services, and network listeners. Suggested read-only commands, not executed:
~~~bash
uname -r
id
sudo -l
ls -ld /tmp
crontab -l
systemctl list-units --type=service --state=running
ss -tuln
~~~
/tmp commonly has world-write access with a sticky bit for a legitimate reason. SUID/SGID are special permission bits; on suitable executables they allow effective user/group identity changes. Legitimate binaries may require them. Audit before changing permissions, removing packages or disabling services. Do not indiscriminately remove special bits.

## SELinux and AppArmor
Both are Linux security modules implementing mandatory access control. SELinux uses labels/security contexts on processes and objects and kernel-enforced policy; AppArmor primarily uses application profiles with path-based restrictions. They complement traditional UNIX permissions. Restrict applications to required files and operations, patch regularly, and review policy denials rather than disabling protection. Examples of status inspection: getenforce (SELinux) or sudo aa-status (AppArmor), if available.

## Security tools
- Snort: network intrusion detection/prevention; https://www.snort.org/
- chkrootkit: checks for indicators associated with known rootkits; https://www.chkrootkit.org/
- rkhunter: checks suspicious files, properties and known rootkit indicators; https://packages.debian.org/sid/rkhunter
- Lynis: security auditing and hardening recommendations; https://cisofy.com/lynis/
- Fail2ban: log-driven temporary bans; https://github.com/fail2ban/fail2ban

IDS alerts on suspicious traffic; IPS may block it when configured inline. Rootkit checks are not proof that a system is clean; false positives and negatives are possible. Lynis is an audit tool rather than an antivirus. No tools installed in this lesson.

## Additional hardening
Remove unnecessary services and packages; replace plaintext authentication protocols with encrypted alternatives (SSH/SFTP rather than Telnet/FTP where appropriate); synchronize clocks through NTP-compatible services and preserve logs; individual accounts and strong unique passwords; prevent compromised/common password choices and inappropriate reuse; throttle or lock repeated failed logins with recovery safeguards; audit unnecessary SUID/SGID programs. The course mentions password aging; modern guidance generally avoids arbitrary periodic password changes absent compromise or a specific policy requirement.

## TCP wrappers
Legacy TCP wrappers (libwrap) provide host-based service access control only for services built/configured to support them. Rules are in /etc/hosts.allow and /etc/hosts.deny. Evaluation: first matching allow rule permits; otherwise first matching deny rule denies; otherwise default allow. An allow rule can take precedence over a deny rule even when the latter is more specific. Format: daemon_list : client_list. Examples in the source use SSH, FTP and Telnet daemon names and IP/hostname/domain patterns. These names and CIDR support are implementation-dependent; classic IPv4 rules can use network/netmask notation such as 192.0.2.0/255.255.255.0 (documentation-only address range).

Illustrative legacy policy, not applied:
~~~text
# /etc/hosts.allow
sshd : 192.0.2.0/255.255.255.0
# /etc/hosts.deny
sshd : ALL
~~~
This is effective only if the actual daemon supports libwrap; many modern OpenSSH builds do not. Do not rely on these files to protect modern SSH, and do not deploy Telnet/FTP merely to test the lesson. Use supported firewall and service-specific controls. TCP wrappers are not a general packet firewall.

## Verification and next step
The provided Section 26 contains no mandatory questions. Full lesson reviewed and documented; examples are conceptual or suggested, not executed. No installation, configuration changes or platform submission verified. Platform Mark Complete & Next pending. Next: Section 27/30.

Independent English study notes based on the learner-provided HTB Academy lesson, with supplemental technical clarifications.
