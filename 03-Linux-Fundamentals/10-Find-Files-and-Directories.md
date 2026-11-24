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
The c suffix means bytes; 25 KiB = 25600 bytes and 28 KiB = 28672 bytes. This avoids KiB rounding. The actual lab output and answer are pending.

## Practical question 2 — Count backup files
~~~bash
find / -type f -name '*.bak' 2>/dev/null | wc -l
~~~
The file filter selects regular files ending in .bak; the pipe passes paths to wc; -l counts output lines. This counts visible matching paths, not inaccessible files. For filenames containing newlines, GNU find -printf '.' piped to wc -c is a more robust counting alternative. Answer pending.

## Practical question 3 — Locate xxd
~~~bash
which xxd
~~~
The question requests the full executable path, not its filename. Alternative: command -v xxd. Answer pending.

## Verification
All three questions remain pending actual lab verification. Alternatives are instructional examples, not confirmed executions. No lab credentials, private addresses or VPN profiles are included.

Source: [HTB Academy](https://academy.hackthebox.com/). Independent English study notes.
