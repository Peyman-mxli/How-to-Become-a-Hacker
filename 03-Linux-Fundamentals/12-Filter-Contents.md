# Lesson 12/30 — Filter Contents

## Tools and purpose
| Tool | Why and how |
| --- | --- |
| more, less | Read text interactively without editing; q exits |
| head, tail | Display first or last lines; -n selects count |
| sort | Sort text |
| grep | Filter matching lines; -v inverts, -E enables extended regex |
| cut | Extract fields; -d selects delimiter, -f selects fields |
| tr | Translate characters |
| column -t | Format input as columns |
| awk | Process records and extract fields |
| sed | Transform text; s substitutes, g replaces all matches per line |
| wc -l | Count lines |

## Account record structure
/etc/passwd contains seven colon-separated fields: username, password placeholder, UID, GID, account information, home directory, login shell.

## Examples
~~~bash
less /etc/passwd
head /etc/passwd
tail /etc/passwd
sort /etc/passwd
grep '/bin/bash$' /etc/passwd
grep -vE 'false|nologin' /etc/passwd
cut -d':' -f1 /etc/passwd
awk -F: '{print $1, $7}' /etc/passwd
echo '/bin/bash' | sed 's/bin/HTB/g'
wc -l /etc/passwd
~~~
These are read-only examples. sed without in-place editing changes output, not the original file.

## Optional exercises — suggested commands
The lesson asks to filter account records. These methods are instructional, not executed or verified.
~~~bash
grep '^cry0l1t3:' /etc/passwd
cut -d: -f1 /etc/passwd
awk -F: '$1=="cry0l1t3" {print $1, $3}' /etc/passwd
awk -F: '$1=="cry0l1t3" {print $1 "," $3}' /etc/passwd
awk -F: '$1=="cry0l1t3" {print $1 "," $3 "," $7}' /etc/passwd
awk -F: '{print $1 "," $3 "," $7}' /etc/passwd
awk -F: '$7 !~ /nologin|false/ {print $1 "," $3 "," $7}' /etc/passwd
awk -F: '$7 !~ /nologin/ {print $1 "," $3 "," $7}' /etc/passwd | wc -l
~~~
-F sets the colon separator; $1, $3 and $7 select username, UID and shell. The comma is an output separator where requested. !~ excludes matching shell patterns. The final exercise excludes nologin only, as specified.

## Mandatory question 1 — IPv4 services on all interfaces
Use the assigned HTB machine.
~~~bash
ss -lntu4
~~~
ss inspects sockets; -l selects listening sockets (including unconnected UDP sockets when requested), -n numeric output, -t TCP, -u UDP, -4 IPv4. Inspect the local address column for 0.0.0.0 bindings and exclude loopback and specific-interface addresses. Count matching socket rows after inspecting the output; distinguish socket count from distinct applications if needed. Actual result pending.

## Mandatory question 2 — ProFTPd process owner
The question asks for the username under which the FTP server process runs. Inspect process ownership on the assigned training machine. Verified username: proftpd.

## Mandatory question 3 — Unique website paths
The lesson requests fetching the designated training website's HTML with curl from Pwnbox, not the target machine, then filtering and deduplicating same-domain paths. The local WSL environment may be a practical alternative when Pwnbox is unavailable, but this has not yet been tested. Actual result pending.

## Verification
Question 1 has a proposed answer pending acceptance; question 2 is verified from terminal output; question 3 and optional exercises remain pending. No lab credentials, private addresses or VPN profiles are included. Independent English study notes based on HTB Academy.

## Question 1 — Observed socket results and interpretation

The learner ran `ss -lntu4`. Seven TCP LISTEN rows were bound to 0.0.0.0, on ports 22, 445, 993, 995, 139, 110 and 143. Three UDP UNCONN rows were bound to 0.0.0.0, on ports 68, 137 and 138. Loopback, broadcast and specific-interface addresses are excluded. Proposed answer for TCP listening services: 7. If the exercise includes UDP wildcard-bound sockets, the combined count is 10. HTB acceptance is pending.

### Alternative automatic count

~~~bash
ss -lnt4 | awk '$4 ~ /^0\.0\.0\.0:/ {print $4}' | wc -l
~~~

The -l option selects listening sockets, -n numeric output, -t TCP, and -4 IPv4. awk tests the fourth field (local address) against a regex anchored to the wildcard IPv4 address; escaped dots match literal dots. The pipe forwards matching lines to wc -l. Expected count: 7. This alternative has not yet been executed. Private target addresses are omitted.

## Question 2 — Verified process owner

~~~bash
ps aux | grep '[p]roftpd'
~~~

The actual process listing showed owner **proftpd**, PID 1723, and the process description proftpd: (accepting connections). The first column of ps aux is the process owner, so the answer is proftpd. Submission acceptance has not yet been confirmed.

### Why and how

ps displays process information. BSD-style a broadens selection to processes belonging to other users with terminals, x includes processes without terminals, and u selects a user-oriented output format. The pipe transfers the process listing to grep. The regex [p]roftpd matches the process name but does not match the literal bracketed search expression in grep's own command line.

Alternative, not yet executed:

~~~bash
ps -eo user,comm | grep '[p]roftpd'
~~~

-e selects all processes and -o specifies output columns, here username and command name.

## Question 3 — Initial HTML retrieval

The exercise specifies Pwnbox rather than the SSH target. Because the learner's Pwnbox allowance is exhausted, local WSL Ubuntu is a proposed alternative. Keep the existing VPN and SSH sessions open and start a third terminal.

~~~powershell
wsl -d Ubuntu
~~~

~~~bash
curl -sL https://www.inlanefreight.com/ -o website.html
wc -c website.html
~~~

curl retrieves the designated training website. -s suppresses the progress meter, -L follows redirects, and -o writes the response to a local file. wc -c counts bytes to check that data was saved. Download, path extraction, deduplication and final count remain pending. A successful byte count alone does not establish that the downloaded response is the intended HTML.

## Question 3 — Verified download checkpoint

Local WSL Ubuntu was started successfully. The learner executed curl -sL with the designated training website and saved website.html. The command wc -c website.html returned 22266 bytes. This verifies that a nonempty response was saved, but not that every byte belongs to the intended HTML page. No local username or machine identifier is recorded.

### Initial absolute URL extraction

~~~bash
grep -oE 'https://www\.inlanefreight\.com/[^"<>[:space:]]*' website.html | sort -u | wc -l
~~~

grep -o prints matched portions and -E enables extended regular expressions. Escaped dots match literal domain separators. The negated character class stops at quotes, angle brackets or whitespace. sort -u deduplicates matching strings; wc -l counts them. This is a preliminary absolute-URL count, not a complete normalized same-domain path count. Relative paths, other URL forms and query/fragment normalization must be considered before submitting the final answer. Actual preliminary extraction output: 35 unique absolute URL strings.

## Question 3 — Preliminary count verified

The absolute URL extraction pipeline returned **35**. This is an observed intermediate result, not yet the final answer, because the question also includes relative paths.

Next, extract both same-domain absolute URLs and quoted root-relative candidates, remove the domain and opening quotes with sed, deduplicate using sort -u, and count using wc -l. Inspect protocol-relative URLs, query strings, fragments and non-path matches before treating this count as definitive. The combined extraction has not yet been executed.
