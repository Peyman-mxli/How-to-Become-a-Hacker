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

## Regular expressions

- `grep -E`: extended regex; `grep -v`: exclude matches; `grep -w`: whole words.
- `^` and `$`: beginning and end of line; `.*`: zero or more arbitrary characters.
- `[0-9]`: digit range; `{1,10}`: repetition; parentheses: group; pipe in regex: OR.
- `grep -E 'my.*false'`: ordered match; two grep commands in a shell pipeline: both patterns in either order.

## Permission management

- `ls -l`: inspect permissions; `ls -ld DIR`: inspect directory metadata.
- `r=4`, `w=2`, `x=1`; sum each user/group/others triplet.
- `chmod 754 FILE`: owner rwx, group r-x, others r--.
- `chmod u+x FILE`: add owner execute; `chmod g-w FILE`: remove group write.
- `chown USER:GROUP FILE`: change ownership (requires appropriate privileges).
- `stat -c '%a %A' FILE`: show octal and symbolic permissions.
- Special bits: SUID=4, SGID=2, sticky=1 in the leading octal digit.

## User management

- `useradd -m` / `--create-home`: create a home directory when adding an account.
- `usermod -L` / `--lock`: lock password authentication.
- `su -c` / `--command`: execute a command as another user subject to authentication.
- `sudo`: run commands according to administrative policy.
- `--help` and `man`: verify options without changing system state.

## Package management

- `sudo apt update`: refresh package indexes; `apt search NAME`: search; `apt show NAME`: metadata.
- `sudo apt install NAME`: install with dependency resolution.
- `apt list --installed`: list installed packages.
- `dpkg -s NAME`: inspect installed package status; `dpkg -L NAME`: list package files.
- `apt download NAME`: download without installation; `dpkg -i FILE.deb`: install local package.
- `git clone URL`: copy a repository, not necessarily install its software.

## Service and process management

- `systemctl status NAME`: inspect service; `systemctl list-units --type=service --all`: list loaded service units.
- `journalctl -u NAME --no-pager`: inspect service logs.
- `ps aux`, `pgrep`: inspect processes; `kill -l`: list signals.
- SIGTERM requests termination; SIGKILL forces it.
- `jobs`, `bg`, `fg %1`, `&`: shell job control.
- `;`, `&&`, `||`, pipe: command sequencing and output transfer.

## Task scheduling

- `systemctl list-timers --all`: inspect loaded timers.
- Timer units define when; service units define what executes.
- `crontab -l`: list user jobs; `crontab -e`: edit jobs.
- Cron fields: minute, hour, day of month, month, weekday.
- `0 */6 * * *`: every six hours at minute zero.
- `systemctl --user show NAME --property=Type`: inspect user service type.

## Network services

- `ssh USER@HOST`: connect to an authorized remote SSH account.
- `systemctl status ssh`: inspect SSH service status.
- `/etc/ssh/sshd_config`: SSH server configuration; `/etc/exports`: NFS exports.
- NFS options: `ro`, `rw`, `root_squash`, `sync`, `async`; review risks of `no_root_squash`.
- `/etc/apache2/apache2.conf`: Apache global configuration.
- `python3 -m http.server`: temporary HTTP server, default port 8000; not HTTPS.
- `sudo openvpn --config FILE.ovpn`: connect using an authorized VPN profile.

## Working with web services

- `curl -I URL`: HTTP headers; `curl -L URL`: follow redirects; `curl -o FILE URL`: save response.
- `wget URL`: download a response to a file.
- `http-server -p 8080`: start installed npm HTTP server; `npx http-server -p 8080`: alternative.
- `php -S 127.0.0.1:8080`: PHP local development server.
- `python3 -m http.server 8080 --bind 127.0.0.1`: local-only Python server.
- Use dedicated non-sensitive directories and stop servers with Ctrl+C.

## Backup and restore

- `rsync -av SOURCE/ DEST/`: synchronize directory contents in archive mode.
- `rsync -avn SOURCE/ DEST/`: preview without making changes.
- `rsync -avz -e ssh SOURCE/ USER@HOST:DEST/`: encrypted remote transfer.
- `--backup` and `--backup-dir`: retain replaced destination files; review `--delete` carefully.
- Source trailing slash changes directory-copy semantics.
- Restore to a separate directory and verify the recovered data.
