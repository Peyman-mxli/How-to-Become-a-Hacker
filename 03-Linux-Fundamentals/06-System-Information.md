# Lesson 06/30 — System Information

## Overview
System information commands help administrators and authorized security practitioners understand users, hardware, processes, storage and network configuration.

## Commands
| Command | Purpose |
| --- | --- |
| whoami | Effective username |
| id | User and group IDs and memberships |
| hostname | Host name |
| uname | Kernel and system information |
| pwd | Working directory |
| ifconfig | Network interface configuration |
| ip | Network interfaces and routing |
| netstat | Network status |
| ss | Socket information |
| ps | Process status |
| who | Logged-in users |
| env | Environment variables |
| lsblk | Block devices |
| lsusb | USB devices |
| lsof | Open files |
| lspci | PCI devices |

## SSH
SSH provides secure remote command-line access. Use the assigned HTB lab only.

~~~bash
ssh htb-student@TARGET_IP
~~~

Lab credentials, IP addresses and session identifiers are intentionally excluded from this public repository.

## uname options
-a displays combined system information; -s kernel name; -n hostname; -r kernel release; -v kernel version; -m hardware architecture; -p processor; -i hardware platform; -o operating system. Some fields may be unavailable.

## Exercises — pending verification
| Question | Suggested command |
| --- | --- |
| Machine hardware name | uname -m |
| Student home directory | getent passwd htb-student |
| Student mail path | echo "$MAIL" |
| Configured shell | getent passwd htb-student |
| Kernel release | uname -r |
| Interface with MTU 1500 | ip link show |

The home directory and configured shell are fields six and seven of the passwd record. If MAIL is unset, investigate the local mail spool configuration. For the kernel question, use the requested numeric format based on the actual output. Identify the interface from its MTU field.

SSH login succeeded. The remote login banner reports x86_64 architecture. Question 1: `uname -m` returned `x86_64`, confirmed by actual lab output. HTB submission acceptance not yet confirmed. Other answers and submissions remain pending. No practical completion is claimed.

Source: [HTB Academy](https://academy.hackthebox.com/). Independent English study notes.

### Verified practical output — Question 1

~~~bash
uname -m
~~~

Output: `x86_64`.

### Verified practical output — Question 2

~~~bash
echo "$HOME"
~~~

Output: `/home/htb-student`.

Question 2 answer verified from actual lab output; submission acceptance not yet confirmed.

### Verified practical output — Question 3

~~~bash
echo "$MAIL"
~~~

Output: `/var/mail/htb-student`.

Question 3 answer verified from reported lab output; submission acceptance not yet confirmed.

### Verified practical output — Question 4

~~~bash
echo "$SHELL"
~~~

Output: `/bin/bash`.

Question 4 answer verified from actual lab output; submission acceptance not yet confirmed.

Question 5 next command: `uname -r` (pending output).
