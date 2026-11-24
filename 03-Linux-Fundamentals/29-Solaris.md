# Lesson 29/30 — Solaris

## Concepts
Solaris is a Unix operating system originally developed by Sun Microsystems and now maintained by Oracle. It is distinct from Linux and designed for enterprise reliability, scalability, security and high-end hardware. Solaris supports SPARC and x86 systems; Oracle VM Server for SPARC provides virtualization on supported SPARC hardware, while Solaris Zones provide OS-level isolation. Oracle Solaris is proprietary; historical OpenSolaris code and related open-source projects are distinct.

## Solaris and Linux
Solaris 11 uses ZFS as its default root filesystem, SMF (Service Management Facility) for services, IPS (Image Packaging System) for packages and RBAC for delegated privileges. Ubuntu commonly uses ext4, systemd, APT and sudo. Linux can also support ZFS and mandatory access controls. The supplied lesson's comparison of ZFS and SMF mixes unrelated subsystems: ZFS is storage, SMF is service management.

## Directories
/ root; /bin essential programs; /boot boot files where applicable; /dev device nodes; /etc settings; /home user home mount points (often backed by /export/home on Solaris); /kernel kernel components; /lib libraries; /lost+found recovered files on applicable filesystems; /mnt temporary mounts; /opt optional software; /proc process information; /sbin administration binaries; /tmp temporary files; /usr system programs and data; /var variable data and logs. Layout varies by OS release and filesystem.

## System information
Ubuntu: `uname -a` displays kernel, hostname, release and architecture. Solaris: `showrev -a` in the course's Solaris 10 example reports system revision and hardware information; `uname -a` also works on Solaris. Example output is not a learner measurement.

## Package installation
Ubuntu example: `sudo apt-get install apache2`.
Legacy Solaris example from lesson: `pkgadd -d SUNWapchr`. Here -d identifies the package source/device, not simply a repository package name; the actual invocation depends on available package media. Solaris 11 primarily uses IPS `pkg install PACKAGE`. Administrative privileges are required for installation; RBAC and authorized sudo configurations may delegate them.

## Permissions
`chmod 700 filename` grants owner read/write/execute and removes group/other ordinary permissions on both platforms. For SUID auditing the lesson contrasts find / -perm 4000 and find / -perm -4000. Important correction: on GNU find as well as Solaris, -perm -4000 matches files with the SUID bit set regardless of other permission bits, while -perm 4000 matches the exact mode. Use read-only audits in authorized environments, and do not remove legitimate SUID bits blindly.

## NFS
NFS (Network File System) allows clients to access server-exported directories. Legacy Solaris course examples:
~~~sh
share -F nfs -o rw /export/home
mount -F nfs LAB_HOST:/nfs_share /mnt/local
cat /etc/dfs/dfstab
~~~
-F selects filesystem type, -o rw requests read/write sharing, LAB_HOST is a placeholder, and /mnt/local is an existing local mount point. Export only explicitly authorized test data with appropriately restricted clients. These examples were not executed. Solaris 11 no longer uses dfstab to persist shares; SMF/ZFS sharing manages persistence. Linux typically uses /etc/exports and mount -t nfs.

## Process mapping
Ubuntu: `sudo lsof -c apache2` lists open files for matching processes. Solaris: `pfiles $(pgrep httpd)` reports file descriptors for matching processes, including files and sockets. pgrep searches process names and returns PIDs; $(...) is command substitution. Process inspection requires appropriate access.

## System-call tracing
Linux strace and Solaris truss diagnose application/kernel interactions. Course examples use strace -p with pgrep apache2 and truss ls. A simple supplemental local Linux example is `strace ls`; Solaris uses `truss ls`. For a known authorized Linux process use `strace -p PID`; -p attaches to a process. Both tools can report signals and follow child processes (strace -f and truss -f); the source's claim otherwise is inaccurate. Tracing can affect performance and expose sensitive process data; restrict it to owned or authorized environments. No trace executed.

## References and verification
- Oracle ZFS root filesystem: https://docs.oracle.com/cd/E23824_01/html/821-1448/zfsboot-2.html
- Oracle Solaris 11 sharing: https://docs.oracle.com/en/operating-systems/solaris/oracle-solaris/11.4/manage-zfs/sharing-unsharing-zfs-file-systems.html
- Oracle legacy package management: https://docs.oracle.com/cd/E26505_01/html/E29492/ewbej.html
- Linux find manual: https://www.man7.org/linux/man-pages/man1/find.1.html
- Linux strace manual: https://man7.org/linux/man-pages/man1/strace.1.html

The supplied Section 29 text contains no mandatory questions. Lesson studied and documented; no Solaris installation, commands or lab output verified. Platform completion pending. Next: Section 30/30.

Independent English notes based on the learner-provided HTB Academy lesson, with clearly distinguished technical corrections.
