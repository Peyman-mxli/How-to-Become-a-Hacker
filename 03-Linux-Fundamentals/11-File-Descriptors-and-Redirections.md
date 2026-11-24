# Lesson 11/30 — File Descriptors and Redirections

## Overview
A file descriptor is a process-local integer referring to an open I/O resource. Standard streams are stdin (0), stdout (1), and stderr (2).

| Operator | Why and how |
| --- | --- |
| > or 1> | Write stdout to a file, normally truncating existing contents |
| >> | Append stdout |
| 2> | Redirect stderr |
| 2>/dev/null | Discard diagnostics without granting extra permissions |
| < | Read standard input from a file |
| << | Supply here-document input until a chosen delimiter |
| Pipe | Connect stdout of one command to stdin of the next |

Use disposable practice files because > can overwrite existing data.

## Examples
~~~bash
find /etc/ -name shadow 2>/dev/null
find /etc/ -name shadow 2>/dev/null > results.txt
cat results.txt
find /etc/ -name shadow 2> stderr.txt 1> stdout.txt
cat < stdout.txt
find /etc/ -name passwd >> stdout.txt 2>/dev/null
~~~
These commands locate filenames rather than reading sensitive account-file contents. Separate redirections distinguish normal results and errors; append preserves previous output.

~~~bash
cat << EOF > stream.txt
Hack The Box
Linux Fundamentals
EOF
~~~
EOF is a chosen delimiter, not a mandatory keyword.

~~~bash
find /etc/ -name '*.conf' 2>/dev/null | grep systemd | wc -l
~~~
find discovers paths, grep filters lines containing systemd, and wc -l counts lines. The lesson example returns 6, not a verified result on the current target. Quoting prevents premature shell glob expansion.

## Practical question 1 — Count regular files ending in .log

~~~bash
find / -type f -name '*.log' 2>/dev/null | wc -l
~~~

The question asks for a number. / starts at the filesystem root; -type f restricts matches to regular files; -name matches the suffix; 2>/dev/null discards errors; the pipe forwards results to wc; -l counts lines.

Alternative: run the find portion without the pipe and count paths manually. For filenames containing newlines, use:
~~~bash
find / -type f -name '*.log' -printf '.' 2>/dev/null | wc -c
~~~
These count discoverable matching paths, not inaccessible files. Actual answer pending.

## Practical question 2 — Count installed packages

Ubuntu is Debian-based, so query its package database. Count records whose current status indicates installed, not headers or removed packages.

~~~bash
dpkg-query -W -f='${Status}\n' | grep -c ' installed$'
~~~

-W lists package records, -f defines their format, and grep -c counts lines ending in installed. Alternative:
~~~bash
dpkg -l | grep -c '^ii '
~~~
The ii prefix represents requested installation and current installed state; other desired states such as held packages may need separate consideration. Actual output pending.

## Verification
Both exercises await actual lab results and HTB acceptance. Alternatives are untested. No lab credentials or VPN profiles are included.

Source: [HTB Academy](https://academy.hackthebox.com/). Independent English study notes.
