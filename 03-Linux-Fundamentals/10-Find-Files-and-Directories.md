# Lesson 10/30 — Find Files and Directories

## Overview
Linux offers tools to locate executable programs, files and directories without manually browsing the filesystem.

## which
~~~bash
which python
~~~
which searches PATH for executable programs. The output is the executable path if found. command -v is a shell-based alternative.

## find
~~~bash
find / -type f -name '*.conf' -user root -size +20k -newermt 2020-03-03 -exec ls -al {} \; 2>/dev/null
~~~

| Component | Why and how |
| --- | --- |
| / | Start at filesystem root |
| -type f | Match regular files |
| -type d | Match directories instead |
| -name '*.conf' | Match filenames ending in .conf; quoting prevents shell glob expansion |
| -user root | Match files owned by root |
| -size +20k | Match sizes above the specified threshold in rounded-up KiB units |
| -newermt 2020-03-03 | Match modification timestamps after the specified date |
| -exec ls -al {} \; | Run detailed listing for each matched path; braces represent the path; escaped semicolon terminates the action |
| 2>/dev/null | Redirect standard error to the null device; does not bypass permissions |

## locate
~~~bash
locate '*.conf'
~~~
locate searches a prebuilt filename database. It is usually faster than live traversal but may be stale. On an owned system, sudo updatedb refreshes the database when installed and authorized.

## Practical question 1 — Configuration file by size and date

The question asks for a configuration filename after March 3, 2020, larger than 25 KiB and smaller than 28 KiB.

### Method 1 — Lesson-style filters
~~~bash
find / -type f -name '*.conf' -size +25k -size -28k -newermt 2020-03-03 2>/dev/null
~~~
Each filter narrows the results by type, extension, size and timestamp. Read the basename from the matching path. The lesson's filter uses modification time, not creation time. GNU find rounds up sizes expressed in k, so boundary matches can differ from exact byte comparisons.

### Method 2 — Exact byte thresholds
~~~bash
find / -type f -name '*.conf' -size +25600c -size -28672c -newermt 2020-03-03 2>/dev/null
~~~
The c suffix means bytes; 25 KiB = 25600 bytes and 28 KiB = 28672 bytes. This avoids KiB rounding. The lesson-style search displayed /usr/share/drirc.d/00-mesa-defaults.conf on the newly assigned HTB target. The filename answer is 00-mesa-defaults.conf; search completion confirmed by the subsequent terminal output; submission acceptance remains unconfirmed. The exact-byte alternative remains untested.

## Practical question 2 — Count backup files
~~~bash
find / -type f -name '*.bak' 2>/dev/null | wc -l
~~~
The file filter selects regular files ending in .bak; the pipe passes paths to wc; -l counts output lines. This counts visible matching paths, not inaccessible files. For filenames containing newlines, GNU find -printf '.' piped to wc -c is a more robust counting alternative. Verified answer: 4.

## Practical question 3 — Locate xxd
~~~bash
which xxd
~~~
The question requests the full executable path, not its filename. Alternative: command -v xxd. Verified answer: /usr/bin/xxd.

## Verification
Question 1 has a matching result observed in the lab. Question 2 count verified as 4; question 3 verified as /usr/bin/xxd. Alternatives are instructional examples, not confirmed executions. No lab credentials, private addresses or VPN profiles are included.

Source: [HTB Academy](https://academy.hackthebox.com/). Independent English study notes.

## Question 1 — Observed lab result and filename extraction

The learner recovered the expired SSH target using the existing VPN, then ran the lesson-style find command on the remote machine. Observed matching path:

~~~text
/usr/share/drirc.d/00-mesa-defaults.conf
~~~

The question requests the filename rather than its full path. Read the last path component: **00-mesa-defaults.conf**.

### Additional alternative — Extract the basename

~~~bash
basename /usr/share/drirc.d/00-mesa-defaults.conf
~~~

The basename command removes leading directory components and returns only the filename. Expected output: 00-mesa-defaults.conf. This alternative has not yet been executed. The screenshot showed a matching path but did not show the shell prompt returning; wait for search completion before executing the next command. The search uses modification time, as taught in the lesson, rather than a true file creation timestamp.

## Question 2 — Why and how to count matches

~~~bash
find / -type f -name '*.bak' 2>/dev/null | wc -l
~~~

The starting location / requests a filesystem-wide search; -type f limits results to regular files; -name '*.bak' matches the requested suffix, with quotes preventing shell expansion. The shell redirects standard error to /dev/null. The pipe sends matching paths to wc, and -l counts newline-delimited output. The result is the count of discoverable matching paths under the current account permissions, not proof that inaccessible directories contain no additional files. Actual count returned by the assigned lab: 4.

## Question 2 — Verified count and alternative methods

~~~bash
find / -type f -name '*.bak' 2>/dev/null | wc -l
~~~

Actual output: **4**. Submit only the number. The pipe connects the filesystem search to the line-counting utility. This counts discoverable regular-file paths under the current account's permissions.

Alternative manual verification:

~~~bash
find / -type f -name '*.bak' 2>/dev/null
~~~

This lists the matching paths individually rather than counting them. The alternative has not yet been executed.

## Question 3 — Why and how to resolve an executable

~~~bash
which xxd
~~~

which searches PATH for the executable named xxd. The question requests the entire executable path, so retain all directory components. Alternative: command -v xxd, which uses the shell's command resolution. Actual output: /usr/bin/xxd. Answer verified; HTB acceptance pending.

## Question 3 — Verified output, why and how

The question asks for the complete executable path of xxd. Using which is appropriate because the program is expected to be available through PATH.

~~~bash
which xxd
~~~

Actual output:

~~~text
/usr/bin/xxd
~~~

The full path is the answer, including the leading slash and all directory components. xxd is a utility for hexadecimal dumps and reversing that representation.

Alternative:

~~~bash
command -v xxd
~~~

command -v uses shell command resolution. Expected output: /usr/bin/xxd. This alternative has not been executed in the lab.

## Final exercise recap

| Question | Verified answer |
| --- | --- |
| Configuration filename | 00-mesa-defaults.conf |
| Number of discoverable regular backup files | 4 |
| Full xxd executable path | /usr/bin/xxd |

All three answers are verified from actual lab output. HTB submission acceptance and section completion remain pending confirmation.
