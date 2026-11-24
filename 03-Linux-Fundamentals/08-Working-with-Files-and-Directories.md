# Lesson 08/30 — Working with Files and Directories

## Overview
Linux shell commands allow file creation, directory organization, copying, moving, renaming and automation. Redirection and interactive editors are covered in later lessons.

## Commands and why they are useful

| Command | Purpose |
| --- | --- |
| touch info.txt | Create an empty file if absent; otherwise update timestamps |
| mkdir Storage | Create a directory |
| mkdir -p Storage/local/user/documents | Create missing parent directories automatically |
| touch ./Storage/local/user/userinfo.txt | Create a file using a relative path |
| tree . | Inspect the directory hierarchy from the current directory |
| mv info.txt information.txt | Rename a file |
| touch readme.txt | Create another practice file |
| mv information.txt readme.txt Storage/ | Move multiple files into an existing directory |
| cp Storage/readme.txt Storage/local/ | Copy a file while retaining the original |

The dot in a relative path represents the current directory. The tree command may need to be installed separately. Use a dedicated practice directory and avoid overwriting existing files.

## Optional exercise — deletion
These are supplemental examples, not verified executions.

~~~bash
rm -i filename.txt
rmdir foldername
rm -ri foldername
~~~

rm removes files; -i requests confirmation. rmdir removes empty directories. With rm, -r recursively removes directory contents; combining -r and -i requests interactive confirmations. Practice only on disposable files you created.

## Practical question 1 — Most recently modified file

The question asks for a filename in /var/backups based on modification time, not creation time or alphabetical order.

### Method 1 — Detailed listing

~~~bash
ls -lt /var/backups
~~~

- ls lists directory entries.
- -l shows file type, permissions, ownership, size, timestamp and name.
- -t sorts by modification time, newest first.
- /var/backups selects the requested directory.

Read the first regular-file entry after the total line. A directory entry may appear first; distinguish it from a regular file by the first character in the permissions column. Hidden entries are normally omitted; use -a if relevant.

### Method 2 — Short listing

~~~bash
ls -t /var/backups
~~~

This sorts names by modification time without the long-format metadata. It is faster to read but does not distinguish regular files from directories. If necessary, use the detailed method to verify the result.

Actual lab output listed `apt.extended_states.0` first, followed by `apt.extended_states.1.gz` and `dpkg.status.0`. The first entry is a regular file and the listing is sorted by modification time. Verified answer: `apt.extended_states.0`. HTB submission acceptance pending.

## Practical question 2 — Inode number

The question asks for the inode number of shadow.bak in /var/backups, not the contents of the file.

### Method 1

~~~bash
ls -i /var/backups/shadow.bak
~~~

-i requests the inode number. Read the first field of the output.

### Method 2

~~~bash
stat -c '%i' /var/backups/shadow.bak
~~~

stat reads file metadata; -c specifies the format; %i selects the inode number only. Inode values are filesystem-specific. There is no need to read the contents of shadow.bak.

Method 1 verified from actual lab output; Method 2 remains an instructional alternative.

Source: [HTB Academy](https://academy.hackthebox.com/). Independent English study notes.

## Question 1 — Verified result and additional alternative

~~~text
-rw-r--r-- 1 root root 41872 Nov 12 2020 apt.extended_states.0
-rw-r--r-- 1 root root 4437 Nov 12 2020 apt.extended_states.1.gz
-rw-r--r-- 1 root root 742750 Nov 11 2020 dpkg.status.0
~~~

The first regular file in the time-sorted output is the answer. The displayed date can be identical for multiple files because the listing hides finer timestamp precision. Sorting uses the underlying modification timestamps.

### Method 3 — Display only the first entry

~~~bash
ls -t /var/backups | head -n 1
~~~

The -t option sorts newest first, the pipe passes standard output to head, and -n 1 limits output to one line. Expected result: apt.extended_states.0. This alternative is not yet executed and assumes the first visible entry is a regular file, as verified in the detailed listing.

Question 2 was subsequently verified using ls -i /var/backups/shadow.bak. See the detailed results below.

## Question 2 — Verified output, why and how

The question asks for the inode number (filesystem metadata), not the content, size, permissions or name of the file. Select the exact requested path.

### Method 1 — Verified in the assigned lab

~~~bash
ls -i /var/backups/shadow.bak
~~~

- ls lists information about the requested file.
- -i requests its inode number.
- /var/backups/shadow.bak selects the exact file mentioned in the question.

Actual terminal output:

~~~text
265293 /var/backups/shadow.bak
~~~

The first field is the inode number, while the second field is the filename. Submit only **265293**.

### Method 2 — Alternative

~~~bash
stat -c '%i' /var/backups/shadow.bak
~~~

- stat retrieves filesystem metadata.
- -c selects a custom output format.
- %i requests the inode number only.

Expected output: 265293. This method has not yet been executed in the lab. It is useful when a script needs only the inode number. Inode values may differ between filesystems or lab instances. Neither method reads the sensitive file's contents.

## Final exercise recap

| Question | Verified answer |
| --- | --- |
| Most recently modified file | apt.extended_states.0 |
| Inode number of shadow.bak | 265293 |

Both answers have been verified from the learner's actual terminal output. Learner proceeded to section 9; both answers were verified from terminal output, but individual submission acceptance was not explicitly shown.
