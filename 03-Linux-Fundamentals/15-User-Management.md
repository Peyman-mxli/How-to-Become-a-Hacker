# Lesson 15/30 — User Management

## Overview
Linux user management includes creating accounts, assigning groups, changing passwords and executing commands with authorized alternate identities. Least privilege limits access to necessary resources.

| Command | Purpose |
| --- | --- |
| sudo | Run a command as another user according to policy |
| su | Switch user identity or execute a command as another user |
| useradd | Create user accounts |
| userdel | Delete user accounts; file removal depends on options |
| usermod | Modify user accounts |
| addgroup | Create a group on Debian-based systems |
| delgroup | Remove a group on Debian-based systems |
| passwd | Change passwords |

## Authentication and account files
/etc/passwd contains account metadata; /etc/shadow contains restricted password hashes and aging information; /etc/group contains group records; /etc/sudoers defines sudo policy. sudo and su have different authorization and authentication mechanisms. Do not expose password hashes or credentials in public documentation.

## Mandatory question 1 — Create a home directory

~~~bash
useradd --help | grep 'create-home'
~~~

Verified help output:
~~~text
-m, --create-home             create the user's home directory
-M, --no-create-home          do not create the user's home directory
~~~

Answer: **-m**. The question does not require the long version; --create-home is equivalent. -M has the opposite purpose. --help lists options without creating an account; grep selects relevant documentation. Alternative: man useradd, search for create-home.

## Mandatory question 2 — Lock a user account, long option

~~~bash
usermod --help | grep 'lock'
~~~

Verified help output:
~~~text
-L, --lock                    lock the user account
-U, --unlock                  unlock the user account
~~~

Answer: **--lock**. The long option is requested. -L is equivalent. Password locking prevents password authentication but does not necessarily disable all access methods. Alternative: man usermod, search for --lock.

## Mandatory question 3 — Execute a command as another user, long option

~~~bash
su --help | grep 'command'
~~~

Verified help output:
~~~text
-c, --command <command>         pass a single command to the shell with -c
 --session-command <command>     pass a single command to the shell with -c
~~~

Answer: **--command**. -c is the ordinary short equivalent. --session-command is a distinct option that does not create a new session. Authentication and authorization still apply. Alternative: man su, search for --command.

## Verification
All three option names were verified using actual command help in an independent Linux environment. Learner proceeded to Section 16; individual submission acceptance was not explicitly shown. No users, passwords, privileges or system files were modified.

Independent English study notes based on HTB Academy.
