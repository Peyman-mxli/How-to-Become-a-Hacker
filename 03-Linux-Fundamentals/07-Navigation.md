# Lesson 07/30 — Navigation

## Overview
Linux navigation involves moving between directories and inspecting files using shell commands.

## Commands
| Command | Purpose |
| --- | --- |
| `pwd` | Display current working directory |
| `ls` | List directory contents |
| `ls -l` | Long listing with metadata |
| `ls -la` | Long listing including hidden entries |
| `ls -i` | Display inode numbers (supplemental command for exercise 2) |
| `ls -l /var/` | Inspect another directory without changing location |
| `cd /dev/shm` | Navigate using an absolute path |
| `cd -` | Return to previous working directory |
| `cd ..` | Navigate to parent directory |
| `cd ~` | Navigate to home directory |
| `cd shm && clear` | Change directory and clear display if successful |

A single dot represents the current directory and two dots represent its parent. Hidden filenames generally begin with a dot.

## Long listing metadata
A typical long listing includes file type and permissions, hard link count, owner, group, size in bytes, modification timestamp and filename. The total line reports allocated blocks; block units can depend on configuration.

## Shortcuts
- Tab: command/path completion; twice to display matches where available.
- Ctrl+L: clear terminal display.
- Ctrl+R: reverse search command history.
- Up/down arrows: navigate command history.

## Lab exercises
Use the existing authorized HTB SSH session.

| Question | Suggested command | Status |
| --- | --- | --- |
| Identify the hidden history filename in the student's home directory | `ls -la ~` | Answer verified: `.bash_history` |
| Identify the inode number of the sudoers file | `ls -i /etc/sudoers` | Answer verified: `147627` |

Question 1: `ls -la ~` confirmed the hidden file `.bash_history`, with permissions `-rw-------`. This file stores saved Bash command history. HTB submission acceptance is pending. Inode numbers must be read from the assigned machine rather than guessed.

Source: [HTB Academy](https://academy.hackthebox.com/). Independent English study notes.

## Practical checkpoint

Question 1 verified from actual terminal output: `.bash_history`.

Question 2: `ls -i /etc/sudoers` returned `147627 /etc/sudoers`. The verified answer is `147627`. Inode numbers identify files within a filesystem and can vary between machines.

Both answers verified from actual lab output. HTB submission and section completion remain pending confirmation.

## Question 1 — Two methods, why they work, and how to identify the answer

**Question:** What is the name of the hidden history file in the student's home directory?

### Reasoning from the question
- Hidden: Linux filenames beginning with a dot are normally omitted by plain ls.
- History: search for a filename associated with saved shell command history.
- Home directory: ~ expands to the current user's home directory.

### Method 1 — Manual inspection (verified in the lab)

~~~bash
ls -la ~
~~~

| Component | Meaning and reason for using it |
| --- | --- |
| ls | Lists directory entries so we can inspect filenames |
| -l | Long listing provides permissions, ownership and other metadata |
| -a | Includes hidden entries, necessary because the question asks for a hidden file |
| ~ | Selects the current user's home directory regardless of the current working directory |

The actual lab output contained this entry:

~~~text
-rw------- 1 htb-student htb-student 5 Sep 23 2020 .bash_history
~~~

Read the filename in the last column. The leading dot indicates a hidden filename, bash identifies the shell, and history indicates saved command history. The permissions show that only the owner has read and write permissions. The answer is the filename, including its leading dot, not the entire output line.

### Method 2 — Filter the listing (alternative, not yet executed in the lab)

~~~bash
ls -a ~ | grep history
~~~

| Component | Meaning and reason for using it |
| --- | --- |
| ls -a ~ | Lists all entries in the home directory, including hidden names |
| pipe (vertical bar) | Sends the listing's standard output to the next command's standard input |
| grep | Filters input lines matching a text pattern |
| history | The keyword taken from the exercise question |

Expected output:

~~~text
.bash_history
~~~

This method is more efficient when a directory contains many files because unrelated entries are filtered out. The long-listing option is unnecessary when only the filename is requested. If several names match, inspect them and identify which actually stores Bash command history.

### Final answer and transferable lesson

Answer: **.bash_history**

Method 1 was verified by actual terminal output. Method 2 is an alternative expected to produce the same filename; its execution has not yet been confirmed. Bash normally saves command history in this file, although its location can be customized. Always submit the requested result rather than the command used to find it.
