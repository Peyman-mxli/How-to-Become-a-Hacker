# Lesson 01 — Linux Structure

## Operating system and history

Linux-based operating systems manage hardware and provide services to applications. The Linux kernel is open source. Distributions combine the kernel with supporting software and libraries.

| Year | Milestone |
| --- | --- |
| 1970 | Early Unix development |
| 1977 | BSD release |
| 1983 | Richard Stallman began GNU |
| 1991 | Linus Torvalds began the Linux kernel project |

Examples of distributions include Debian, Ubuntu, Fedora, openSUSE and Linux Mint. HTB Pwnbox uses a customized Parrot OS environment based on Debian.

## Philosophy

- Many resources are exposed through file-like interfaces.
- Small tools perform focused tasks and can be combined.
- Shell interfaces allow flexible automation and interaction.
- System configuration is frequently stored as text.

## Components

| Component | Function |
| --- | --- |
| Bootloader | Loads the operating system kernel; GRUB is an example |
| Kernel | Manages processes, memory, devices and other resources |
| Daemons | Provide background services |
| Shell | Interprets commands |
| Graphics server | Supports graphical applications |
| Window manager | Manages graphical windows |
| Utilities | Perform specific user or system tasks |

## Architecture

A simplified conceptual view is user → applications and utilities / shell → kernel → hardware. Programs request kernel services; the shell is not a required intermediary for every application.

## Filesystem hierarchy

| Path | Purpose |
| --- | --- |
| / | Root of the filesystem |
| /bin | Essential command binaries |
| /boot | Bootloader, kernel and related boot files |
| /dev | Device interfaces |
| /etc | System configuration |
| /home | Ordinary users' home directories |
| /lib | Essential shared libraries |
| /media | Removable media mount locations |
| /mnt | Temporary mounts |
| /opt | Optional third-party software |
| /root | Root user's home directory |
| /sbin | System administration binaries |
| /tmp | Temporary data; may be removed |
| /usr | Programs, libraries and documentation |
| /var | Variable data, including logs |

The root directory (/) and root user's home (/root) are distinct. Layout details can vary by distribution.

## Security relevance

Understanding configuration, service, log and user directory locations helps with authorized system administration, troubleshooting and security assessment. Linux is not inherently immune to vulnerabilities.

## Exercise status

No required exercise or answer field appears in the supplied lesson. No practical completion is claimed.

## References

- [HTB Academy](https://academy.hackthebox.com/)
- [Filesystem Hierarchy Standard](https://refspecs.linuxfoundation.org/fhs.shtml)

Original study summary, not a copy of the course.
