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

## Navigation

- `pwd`, `ls`, `ls -l`, `ls -la`: inspect current location and contents.
- `ls -i PATH`: display inode numbers.
- `cd ..`, `cd -`, `cd ~`: parent, previous and home directories.
- `clear`, Ctrl+L: clear display; Ctrl+R: reverse history search; Tab: completion.

## Working with files and directories

- `touch FILE`: create an absent file or update timestamps.
- `mkdir -p PATH`: create nested directories.
- `tree .`: show directory structure.
- `mv SOURCE DESTINATION`: move or rename.
- `cp SOURCE DESTINATION`: copy.
- `rm -i FILE`, `rmdir DIRECTORY`: interactive file removal and empty-directory removal.
- `ls -lt DIRECTORY`: detailed listing sorted newest modification first.
- `stat -c '%i' FILE`: print inode number.

## Editing files

- `nano FILE`: edit text; Ctrl+O then Enter saves, Ctrl+X exits, Ctrl+W searches.
- `cat FILE`: display saved content.
- `vim FILE`: modal editing; i inserts, Esc returns to Normal mode, :wq saves and quits, :q! discards unsaved changes.
- `vimtutor`: interactive Vim practice.

## Find files and directories

- `which PROGRAM`, `command -v PROGRAM`: resolve an executable or command.
- `find LOCATION -type f -name 'PATTERN'`: search regular files.
- `-size`, `-user`, `-newermt`: filter by size, owner and modification time.
- `2>/dev/null`: suppress standard error without changing access permissions.
- `locate PATTERN`: search indexed filenames; `sudo updatedb` refreshes the database on an owned system.

## File descriptors and redirection

- FD 0: stdin; FD 1: stdout; FD 2: stderr.
- `> FILE`: write/truncate; `>> FILE`: append; `< FILE`: input.
- `2> FILE`: save errors; `2>/dev/null`: discard errors.
- `<< EOF`: here-document input.
- Pipe: forward stdout; `grep PATTERN`: filter; `wc -l`: count lines.

## Content filtering

- `more`, `less`, `head`, `tail`: read text; `sort`: sort lines.
- `grep -vE PATTERN`: exclude matching lines using extended regex.
- `cut -d: -f1`: extract first colon-delimited field.
- `awk -F: '{print $1, $7}'`: extract selected fields.
- `tr`, `column -t`, `sed`: transform or format text.
- `ss -lntu4`: inspect listening IPv4 TCP and UDP sockets.
