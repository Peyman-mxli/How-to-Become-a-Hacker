# Lesson 14/30 — Permission Management

## Overview
Linux permissions control access to files and directories. Each has an owner and an associated group. Permission classes are user/owner (u), group (g), others (o), and all (a).

| Permission | Octal value | Regular file | Directory |
| --- | --- | --- | --- |
| r | 4 | Read contents | List entry names |
| w | 2 | Modify contents | Create, delete or rename entries, normally requiring execute permission too |
| x | 1 | Execute | Traverse directory and access entries |

Directory traversal requires search permission for ordinary users; privileged processes may bypass discretionary permission checks. Directory permissions and individual file permissions must be considered separately.

## Reading permissions
~~~bash
ls -l
ls -ld /tmp
~~~
-l displays detailed metadata; -d displays the directory entry rather than listing its contents. A mode such as -rwxr-xr-- consists of a file-type indicator followed by owner, group and others permission triplets. The owner has rwx, group has r-x and others have r--. Common type indicators are - for regular file, d for directory and l for symbolic link.

## Octal conversion
| Number | Symbolic | Sum |
| --- | --- | --- |
| 0 | --- | 0 |
| 1 | --x | 1 |
| 2 | -w- | 2 |
| 3 | -wx | 2+1 |
| 4 | r-- | 4 |
| 5 | r-x | 4+1 |
| 6 | rw- | 4+2 |
| 7 | rwx | 4+2+1 |

754 means owner 7, group 5 and others 4.

## chmod — why and how
~~~bash
chmod 754 shell
chmod u=rwx,g=rx,o=r shell
~~~
These are equivalent ordinary permission assignments. Numeric mode sets the three permission triplets; symbolic mode names the user classes and desired rights. chmod changes permissions, not ownership.

Other symbolic examples:
~~~bash
chmod u+x shell
chmod g-w shell
chmod o=r shell
chmod a+r shell
chmod a-x shell
~~~
The plus operator adds rights, minus removes rights, and equals sets the specified rights. The examples are instructional and not executed.

## chown — why and how
~~~bash
chown root:root shell
~~~
The first root is the new owner, the second is the new group, and shell is the target file. Changing ownership generally requires appropriate administrative privileges. This is a lesson example, not an instruction to change ownership of system files.

## Special permission bits
| Special bit | Octal leading digit | Purpose |
| --- | --- | --- |
| SUID | 4 | Eligible executable may run with effective user ID of file owner |
| SGID | 2 | Eligible executable may run with effective group ID of file group; directory entries normally inherit directory group |
| Sticky | 1 | Restricts deletion and renaming of entries in shared directories |

Lowercase s indicates SUID/SGID plus the relevant execute bit; uppercase S indicates the special bit without the relevant execute bit. Lowercase t indicates sticky plus others execute permission; uppercase T indicates sticky without others execute permission. Sticky bit does not itself prevent writing the contents of an existing file when its own permissions allow writing. Directory owner, file owner and appropriately privileged processes can delete or rename eligible entries, subject to other permission checks.

Improperly configured special permissions can create privilege-escalation risks. The principle of least privilege should guide their assignment.

## Optional self-practice — ordinary permissions only
The lesson has no mandatory questions. The following supplemental read/write example uses a learner-owned disposable file, not a system configuration file.

~~~bash
touch permission-test.txt
chmod 754 permission-test.txt
ls -l permission-test.txt
stat -c '%a %A' permission-test.txt
~~~

touch creates an empty file if it does not exist; use a new filename to avoid altering an existing file. chmod assigns the mode. ls -l shows symbolic permissions. stat -c selects a custom format: %a prints octal permission bits and %A prints symbolic mode. Expected output from stat: 754 -rwxr-xr--. Alternative assignment: chmod u=rwx,g=rx,o=r permission-test.txt.

## Verification
No mandatory questions are present in Section 14. The supplemental commands and expected outputs are illustrative, not actual learner terminal results. No changes to system files, ownership or special permission bits are required.

Independent English study notes based on HTB Academy.
