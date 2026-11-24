# Lesson 22/30 — File System Management

## Filesystem types
A filesystem organizes stored data. ext2 lacks journaling; ext3/ext4 support journaling; XFS is suited to high-I/O workloads; Btrfs supports snapshots and checksums; NTFS provides Windows compatibility. FAT32 is widely compatible. Choose according to workload, reliability, compatibility and storage needs. Journaling assists recovery of filesystem consistency but does not replace backups.

## Inodes and file types
Inodes store metadata, including ownership, permissions, size, timestamps and references to data. Filenames are stored in directory entries. Inode numbers are unique within a filesystem. A filesystem may exhaust its inode capacity before exhausting its data capacity.

~~~bash
ls -il
df -i
~~~
ls -i displays inode numbers; -l displays detailed information. df -i shows inode usage. Regular files contain data, directories organize entries, and symbolic links reference paths. Other Linux file types include block devices, character devices, sockets and named pipes.

~~~bash
ln -s original.txt shortcut.txt
~~~
-s creates a symbolic link rather than a hard link. This is an illustrative example, not an executed command.

## Disks and partitions
A disk is a storage device; partitions are logical subdivisions. Do not count a parent disk as a partition.

~~~bash
sudo fdisk -l
lsblk
lsblk -f
df -h
~~~
fdisk -l lists partition tables without editing them; sudo may be necessary for raw device access. lsblk lists block devices and their hierarchy; -f displays filesystem information; df -h displays filesystem capacity in readable units. These listing operations are non-destructive. Do not alter or format actual storage for this lesson.

## Mounting
A mount point connects a filesystem to the directory hierarchy.
~~~bash
mount
cat /etc/fstab
findmnt
~~~
mount without arguments lists mounts; cat reads persistent mount configuration; findmnt provides a structured mount listing.

Illustrative mount and unmount (only for a known authorized device and existing mount point):
~~~bash
sudo mount /dev/sdb1 /mnt/usb
sudo umount /mnt/usb
~~~
Check for open files or working directories before unmounting. lsof can help identify processes using a filesystem. /etc/fstab contains source, mount point, type, options, dump and filesystem-check order. UUIDs can identify devices more reliably than changing device names. Options include ro, rw, noauto, noexec and nosuid. No mounting or configuration changes were performed.

## Swap
Swap is storage used for memory pages moved out of RAM. It is generally slower than physical RAM. Swap may support hibernation if configured appropriately. Sensitive memory contents warrant appropriate encryption.

~~~bash
free -h
swapon --show
~~~
These display memory and active swap usage. mkswap initializes a swap area; swapon activates it; swapoff deactivates it. mkswap can overwrite filesystem signatures and must not be run on an unidentified disk.

## Mandatory question — Pwnbox partition count
The question asks for the number of partitions on the HTB Pwnbox, not a local WSL instance or a separate target.

Primary method:
~~~bash
sudo fdisk -l
~~~
Use -l to list partition tables and count partition entries, not disk headings.

Alternative:
~~~bash
lsblk
lsblk -nr -o TYPE | grep -cx 'part'
~~~
-n suppresses headers; -r produces raw output; -o TYPE selects device type; grep -x matches complete lines; -c counts matches. Only entries of type part qualify as partitions.

Expected platform submission: **3**.

Evidence: multiple published learner walkthroughs report 3. Some describe counting a parent disk together with two partitions; that interpretation is technically incorrect. The supplied lesson's illustrative device listing is not proof of the current Pwnbox layout. The learner has exhausted the free Pwnbox spawn allowance, so no live partition table was inspected. Platform acceptance remains pending; this is a community-corroborated expected answer rather than an independently measured result.

References:
- https://kaitlinw-dev.github.io/cybersecurity/htb-linux-fundamentals-lab/
- https://medium.com/@ayanbro287/hackthebox-academy-linux-fundamentals-complete-walkthrough-2467b8de3aa3
- https://statick88.github.io/challenges/learning-journey/security/linux-fundamentals/11-file-system-management.html

Independent English study notes based on HTB Academy.

The learner proceeded to Section 23; explicit platform acceptance of the proposed answer was not shown.
