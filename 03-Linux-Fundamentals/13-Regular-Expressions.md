# Lesson 13/30 — Regular Expressions

## Overview
Regular expressions describe text patterns for searching, filtering, extraction, validation and substitution. They are useful for logs, configuration files and other text-based data.

## Operators
| Operator | Purpose |
| --- | --- |
| (abc) | Group a subexpression |
| [a-z], [A-Z], [0-9] | Character ranges |
| {1,10} | Repeat the preceding expression one to ten times |
| Pipe inside an extended regex | Alternation (OR) |
| .* | Zero or more arbitrary characters within a line in grep |
| ^ | Beginning of line |
| $ | End of line |
| + | One or more repetitions |
| * | Zero or more repetitions |
| \. | Literal dot |
| \b | GNU grep word boundary |

## Alternation and ordered matching

~~~bash
grep -E '(my|false)' /etc/passwd
grep -E 'my.*false' /etc/passwd
grep 'my' /etc/passwd | grep 'false'
~~~

-E enables extended regex syntax. The first expression matches either substring. The second requires my before false on the same line; .* is not itself a logical AND operator. The third requires both substrings regardless of order. The provided lesson illustrates three matching records for the first pattern and one for the second; these are course examples, not independently observed lab output.

## Optional exercises — suggested solutions

The lesson specifies /etc/ssh/sshd_config on Pwnbox. The learner's free Pwnbox session is exhausted, so the authorized HTB SSH target is a possible practice alternative if the file is readable. Outputs may differ from Pwnbox. No changes to configuration are required.

### 1. Lines without a hash character
~~~bash
grep -v '#' /etc/ssh/sshd_config
~~~
-v inverts the match, excluding any line containing #. Blank lines can still match. Alternative: awk '!/#/' /etc/ssh/sshd_config.

### 2. Words starting with Permit
~~~bash
grep -E '\bPermit[[:alnum:]_]*' /etc/ssh/sshd_config
~~~
\b marks a word boundary; Permit is a literal prefix; the character class matches letters, digits or underscore. The star allows zero or more such characters. Alternative: grep '\<Permit' /etc/ssh/sshd_config (GNU grep).

### 3. Words ending with Authentication
~~~bash
grep -E 'Authentication\b' /etc/ssh/sshd_config
~~~
The boundary after Authentication requires the end of a word. Alternative: grep 'Authentication\>' /etc/ssh/sshd_config.

### 4. Lines containing the complete word Key
~~~bash
grep -w 'Key' /etc/ssh/sshd_config
~~~
-w matches a whole word. It does not match a substring of HostKey or other longer words. Alternative: grep -E '\bKey\b' /etc/ssh/sshd_config. If substring matching were intended, grep 'Key' would be appropriate instead.

### 5. Lines beginning with Password and containing yes
~~~bash
grep -E '^Password.*yes' /etc/ssh/sshd_config
~~~
^ requires Password at the start of the line; .* allows intervening characters. Alternative: grep '^Password' /etc/ssh/sshd_config | grep 'yes'.

### 6. Lines ending with yes
~~~bash
grep -E 'yes$' /etc/ssh/sshd_config
~~~
$ anchors the match to the end of a line. If trailing whitespace should be allowed, use grep -E 'yes[[:space:]]*$' instead.

## Verification
This lesson has six optional exercises and no mandatory questions. All commands are proposed, read-only examples; none of these optional exercises has been verified on the learner's terminal yet. Empty output can be a legitimate result when no line matches. No lab credentials or private identifiers included.

Independent English study notes based on HTB Academy.
