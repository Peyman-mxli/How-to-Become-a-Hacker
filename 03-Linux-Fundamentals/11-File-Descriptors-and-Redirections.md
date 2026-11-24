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
These count discoverable matching paths, not inaccessible files. Actual verified answer: 32.

## Practical question 2 — Count installed packages

Ubuntu is Debian-based, so query its package database. Count records whose current status indicates installed, not headers or removed packages.

~~~bash
dpkg-query -W -f='${Status}\n' | grep -c ' installed$'
~~~

-W lists package records, -f defines their format, and grep -c counts lines ending in installed. Alternative:
~~~bash
dpkg -l | grep -c '^ii '
~~~
The ii prefix represents requested installation and current installed state; other desired states such as held packages may need separate consideration. Actual verified output: 737.

## Verification
Both questions verified from actual terminal output; HTB acceptance remains pending. Alternatives are untested. No lab credentials or VPN profiles are included.

Source: [HTB Academy](https://academy.hackthebox.com/). Independent English study notes.

## Question 1 — Verified output

~~~bash
find / -type f -name '*.log' 2>/dev/null | wc -l
~~~

Actual output: **32**. The search returned discoverable regular-file paths ending in .log; the pipe forwarded them to wc -l, which counted the lines. Submit only 32. HTB acceptance is not yet confirmed.

## Question 2 — Additional package counting method

~~~bash
dpkg -l | grep '^ii ' | wc -l
~~~

The question asks for a count, not package names. dpkg -l lists package records; grep filters lines beginning with the normal installed status ii followed by a space; the caret anchors the pattern to the beginning of the line; wc -l counts those records. This method excludes headers and removed packages, but may omit installed packages with other desired states such as held packages. Compare with the dpkg-query method above if necessary. Actual result: 737.

## Question 2 — Verified output, why and how

~~~bash
dpkg -l | grep '^ii ' | wc -l
~~~

Actual output:

~~~text
737
~~~

The question requests a number, not package names. dpkg is the Debian package management tool; -l lists package records. The first pipe transfers the listing to grep. The caret anchors the pattern at the beginning of each line, and ii followed by a space selects normally installed package records. The second pipe transfers matching records to wc -l, which counts lines. This excludes headers and removed package records. Other selection states, such as held packages, may require checking the current installation status separately.

### Alternative method

~~~bash
dpkg -l | grep -c '^ii '
~~~

The -c option counts matching lines directly, removing the need for wc -l. Expected output: 737. This alternative has not been executed in the lab.

## Final exercise recap

| Question | Verified answer |
| --- | --- |
| Number of discoverable regular .log files | 32 |
| Number of normally installed packages | 737 |

Both answers are verified from the learner's actual terminal output. Learner proceeded to section 12; individual submission acceptance was not explicitly shown.
