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

SSH login succeeded. The remote login banner reports x86_64 architecture. Question 1: `uname -m` returned `x86_64`, confirmed by actual lab output. HTB submission acceptance not yet confirmed. See the verified practical outputs below for all six answers. All six answers have now been verified; section completion is pending confirmation.

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

### Practical output — Question 5

~~~bash
uname -r
~~~

Kernel release: `4.15.0-123-generic`.

The full release was rejected by HTB because the question requires a three-component numeric version. Correct submission: `4.15.0`. Acceptance confirmed by the learner.

### Verified practical output — Question 6

~~~bash
ip link show
~~~

Observed interface MTU values: `lo` = 65536; `ens192` = 1500. Correct answer: `ens192`. Session-specific MAC addresses are omitted.

## Exercise recap

| Question | Verified answer |
| --- | --- |
| 1 | `x86_64` |
| 2 | `/home/htb-student` |
| 3 | `/var/mail/htb-student` |
| 4 | `/bin/bash` |
| 5 | `4.15.0` |
| 6 | `ens192` |

All six answers verified from lab evidence; Question 5 acceptance reported. Learner has proceeded to section 7; individual submission confirmations beyond question 5 were not explicitly shown.

## Detailed exercise walkthroughs — Why and how

### Q1 — Machine hardware name
The keyword "hardware name" refers to machine architecture, not the hostname or distribution. Use:
~~~bash
uname -m
~~~
uname reports system information; -m selects the machine hardware name. Actual output: x86_64. Submit that exact string. Alternative: uname -a displays combined information, but requires identifying the architecture among other fields.

### Q2 — Home directory
The question requests a path, not the username. Use:
~~~bash
echo "$HOME"
~~~
echo prints its argument; $HOME expands to the current user's home directory; double quotes preserve the expanded value as one argument. Actual output: /home/htb-student. Alternative: getent passwd htb-student queries the account database; the sixth colon-separated field is the configured home directory. pwd only answers this question if the current working directory is actually the home directory.

### Q3 — Mail path
The keyword "mail" refers to the configured local mailbox path, not an email address.
~~~bash
echo "$MAIL"
~~~
echo prints the value of the MAIL environment variable. Reported output: /var/mail/htb-student. If MAIL is unset, inspect local mail spool configuration; ls -ld /var/mail/htb-student can check whether the conventional mailbox path exists, but does not by itself establish the configured location.

### Q4 — Configured shell
The question requests the user's login shell, rather than the terminal application.
~~~bash
echo "$SHELL"
~~~
SHELL contains the configured login shell in this session. Actual output: /bin/bash. Alternative: getent passwd htb-student; read the seventh colon-separated field. The account database is more authoritative for the configured shell; SHELL need not identify the currently executing shell.

### Q5 — Kernel release
The question requests the Linux kernel release, not the Ubuntu distribution version.
~~~bash
uname -r
~~~
-r selects the kernel release. Observed release: 4.15.0-123-generic. The full string was rejected by HTB, which requested a three-component numeric format. Accepted answer: 4.15.0. Alternative:
~~~bash
uname -r | cut -d '-' -f 1
~~~
The pipe sends output to cut; -d selects the hyphen delimiter; -f 1 selects the first field. This alternative is expected to work for this release format; it is not a universal kernel-version parser.

### Q6 — Interface with MTU 1500
The requested answer is an interface name, not its IP address or MTU value.
~~~bash
ip link show
~~~
ip manages and inspects networking; link selects network interfaces; show lists their properties. Actual observed values: lo has MTU 65536; ens192 has MTU 1500. Read the name immediately before the interface flags on the matching line. Answer: ens192.
Alternative:
~~~bash
ip -o link show | grep 'mtu 1500 '
~~~
-o produces one-line records; grep filters the output for the specified MTU. Inspect all matches if more than one interface qualifies. MTU is the maximum transmission unit, expressed in bytes.

### Verification and reusable approach
The primary commands and answers are grounded in the learner's lab evidence. Alternative commands are instructional examples, not claimed as executed. Only question 5 acceptance was explicitly reported in this section. Read the requested output field carefully, preserve exact spelling and punctuation, and follow any requested answer format. Do not publish lab credentials, VPN profiles or session-specific network identifiers.
