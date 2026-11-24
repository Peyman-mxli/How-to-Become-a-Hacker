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

Actual output and final answer pending.

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

Actual output and final answer pending. Both alternatives are instructional suggestions until tested.

Source: [HTB Academy](https://academy.hackthebox.com/). Independent English study notes.
