# Lesson 04/30 — Prompt Description

## Overview

The Bash prompt indicates that the shell is ready for input. A typical prompt includes the username, hostname and current directory.

~~~text
user@htb[~]$
root@htb[/htb]#
~~~

The tilde represents the user's home directory. A dollar sign commonly indicates an ordinary user and a hash commonly indicates root. These are conventions, not proof of privileges, because prompts are customizable. Use whoami or id to verify identity.

## PS1

PS1 is the Bash variable defining the primary prompt. Bash configuration can be stored in ~/.bashrc.

| Escape | Meaning |
| --- | --- |
| \u | Username |
| \h | Short hostname |
| \H | Full hostname |
| \w | Working directory, abbreviating home with a tilde |
| \d | Date |
| \D{%Y-%m-%d} | Formatted date |
| \t | 24-hour time |
| \T | 12-hour time |
| \@ | 12-hour time with AM/PM |
| \j | Number of shell jobs |
| \n | Newline |
| \r | Carriage return |
| \s | Shell name |
| \$ | Hash for effective UID zero, dollar sign otherwise |

## Supplemental examples

~~~bash
echo "$PS1"
whoami
hostname
pwd
~~~

These commands are suggested practice, not confirmed executions.

## Documentation

Bash history commonly uses ~/.bash_history. The script utility can record a terminal session. History and transcripts may contain sensitive data; review and redact them before publishing. Prompt customization can help identify the current authorized lab environment.

## Exercise status

No required question or exercise appears in the supplied lesson. Prompt customization is outside the stated module scope.

## References

- [HTB Academy](https://academy.hackthebox.com/)
- [GNU Bash manual](https://www.gnu.org/software/bash/manual/)
- [Powerline](https://github.com/powerline/powerline)

Independent English study notes.
