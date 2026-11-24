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
| Identify the hidden history filename in the student's home directory | `ls -la ~` | Pending lab verification |
| Identify the inode number of the sudoers file | `ls -i /etc/sudoers` | Pending lab verification |

Expected first answer is .bash_history, but actual lab output and acceptance are pending. Inode numbers must be read from the assigned machine rather than guessed.

Source: [HTB Academy](https://academy.hackthebox.com/). Independent English study notes.
