# Linux command notes

| Command | Purpose | Status |
| --- | --- | --- |
| `cat /etc/issue` | Read the system login banner | Practiced in HTB Pwnbox |

| `ls` | List directory contents | Studied |
| `man ls` | Open manual | Studied |
| `ls --help` | Display options | Studied |
| `curl -h` | Display curl help | Studied |
| `apropos sudo` | Search manual descriptions | Studied |

## Syntax lesson
Commands and arguments are separated by whitespace. Absolute Linux paths begin with a forward slash. Read error messages and correct the command rather than repeating it unchanged.

More commands will be added as they are studied and practiced.

## System information (studied; lab pending)

- `whoami`, `id`, `hostname`, `pwd`: identity and context.
- `uname -a`, `uname -m`, `uname -r`: system, architecture and kernel release.
- `ip link show`: interface names and MTU.
- `getent passwd htb-student`: configured account record in the assigned lab.
- `echo "$MAIL"`: mail environment variable.
- `ps`, `ss`, `who`, `env`, `lsblk`, `lsusb`, `lsof`, `lspci`: system inspection.
