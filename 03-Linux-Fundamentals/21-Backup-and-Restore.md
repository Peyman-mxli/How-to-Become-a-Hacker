# Lesson 21/30 — Backup and Restore

## Overview
Backups provide separate recoverable copies of data. Restoration recovers data from those copies. Linux tools include rsync for efficient synchronization, Duplicity for encrypted incremental backups, and Déjà Dup for graphical backup management. GnuPG and LUKS provide additional encryption options. Synchronization alone is not a versioned backup; deletions or corruption may propagate.

## Rsync — why and how
~~~bash
sudo apt install rsync
rsync --version
~~~
APT installs the repository package; --version checks the installed version.

| Option | Purpose |
| --- | --- |
| -a | Archive mode, preserving supported file attributes and recursing |
| -v | Verbose output |
| -z | Compress transfer data |
| -e ssh | Select SSH as remote transport |
| --backup | Preserve replaced or deleted destination files |
| --backup-dir | Select location for preserved versions |
| --delete | Remove destination entries missing from source |
| -n / --dry-run | Preview proposed changes |

The lesson demonstrates remote copies and preservation of previous destination files. --delete can cause data loss; inspect dry-run output and verify both paths before using it. Archive mode does not by itself preserve every possible attribute, such as ACLs and extended attributes.

~~~bash
rsync -av ~/to_backup/ ~/synced_backup/
rsync -avz -e ssh /path/to/source/ user@backup_server:/path/to/backup/
~~~
The source is the first path; the destination is the second. A trailing slash on the source copies its contents rather than the containing directory itself. Remote delta transfers can avoid retransmitting unchanged portions of files. SSH protects data in transit, not necessarily at rest.

## Restore
~~~bash
rsync -av ~/synced_backup/ ~/restored_files/
~~~
Reverse the source and destination to restore. Prefer restoring into a separate directory and verifying the result before replacing original files.

## Automated backup
The lesson combines an rsync script with cron and SSH key-based authentication. The illustrative schedule is:
~~~cron
0 * * * * /path/to/RSYNC_Backup.sh
~~~
This executes at minute zero each hour. Protect script ownership and permissions, use absolute paths, restrict backup account access, and keep private SSH keys out of public repositories. The lesson illustrates ssh-keygen and ssh-copy-id for authorized backup hosts; neither was executed in this step.

## Optional local practice
Use dedicated test directories rather than project or system files:
~~~bash
mkdir -p ~/backup-lab/{to_backup,synced_backup}
echo "Linux Fundamentals backup test" > ~/backup-lab/to_backup/test.txt
rsync -avn ~/backup-lab/to_backup/ ~/backup-lab/synced_backup/
rsync -av ~/backup-lab/to_backup/ ~/backup-lab/synced_backup/
cat ~/backup-lab/synced_backup/test.txt
~~~
mkdir -p creates directories; echo and redirection create a disposable test file; -n previews changes; the second rsync copies the file; cat verifies its content. Expected output: Linux Fundamentals backup test. Alternative verification: diff ~/backup-lab/to_backup/test.txt ~/backup-lab/synced_backup/test.txt (no output indicates identical content). The lesson also suggests scheduling local synchronization every minute; no recurring job was created.

## Supplemental backup strategy
The 3-2-1 rule recommends three copies, two storage media types, and one off-site copy. Maintain retained versions, protect backup credentials, encrypt sensitive backups, and regularly test restoration. Git/GitHub track source code but do not automatically back up databases, untracked uploads, or local configuration. For UniTrack and ProTrack-MX, use database-consistent exports or supported database backup tools; never commit secrets or sensitive records to public repositories.

## Verification
Section 21 has no mandatory questions. All commands and expected outputs are instructional, not newly executed or verified. No files, SSH keys, cron jobs or backup services were changed on the learner's machine. Section 20 was followed by Section 21; individual platform acceptance was not explicitly shown.

Independent English study notes based on HTB Academy.
