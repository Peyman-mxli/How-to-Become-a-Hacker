# Lesson 18/30 — Task Scheduling

## Overview
Task scheduling automates maintenance, backups, script execution and other recurring tasks. Systemd timers and cron are two common Linux scheduling mechanisms. Security audits should review scheduled jobs, executable paths, permissions and execution identities.

## Systemd timers
A timer defines when a task runs; its associated service defines what runs. The lesson illustrates:

~~~ini
[Unit]
Description=My Timer

[Timer]
OnBootSec=3min
OnUnitActiveSec=1hour

[Install]
WantedBy=timers.target
~~~

OnBootSec schedules relative to system boot; OnUnitActiveSec schedules relative to the last activation of the associated unit. The Install section supports enabling the timer.

The associated mytimer.service example:

~~~ini
[Unit]
Description=My Service

[Service]
ExecStart=/full/path/to/my/script.sh

[Install]
WantedBy=multi-user.target
~~~

ExecStart identifies the command or script. These are instructional configurations, not deployed on the learner's machine. A timer can activate its associated service without separately enabling the service.

| Command | Why and how |
| --- | --- |
| systemctl daemon-reload | Reload unit definitions |
| systemctl start mytimer.timer | Activate the timer immediately |
| systemctl enable mytimer.timer | Configure activation at startup |
| systemctl list-timers --all | Inspect timers, including inactive loaded timers |

## Cron
Crontab uses five schedule fields followed by a command: minute (0–59), hour (0–23), day of month (1–31), month (1–12), weekday (0–7, Sunday is 0 or 7 in common Linux cron implementations).

| Expression | Meaning |
| --- | --- |
| 0 */6 * * * | Every six hours at minute zero |
| 0 0 1 * * | First day of each month at midnight |
| 0 0 * * 0 | Sunday at midnight |
| 0 0 * * 7 | Sunday at midnight |
| 0 6 * * 1-5 | Weekdays at 6 AM |

An asterisk matches all permitted values; slash specifies a step; hyphen specifies a range. When both day fields are restricted, common Linux cron implementations match either day field. crontab -l lists the current user's jobs; crontab -e edits them. System crontab files may include an additional username field.

## Mandatory question — Type of dconf.service

### Method 1: inspect service properties
~~~bash
systemctl --user show dconf.service --property=Type
~~~
--user selects the user service manager, show prints properties, and --property=Type limits output to the requested property. Expected output: Type=dbus.

### Method 2: inspect the unit definition
~~~bash
systemctl --user cat dconf.service
~~~
Displays the unit and applicable drop-ins when available. Another read-only method is to locate the unit file:
~~~bash
find /usr/lib/systemd /lib/systemd /etc/systemd -name 'dconf.service' 2>/dev/null
~~~
Then inspect the Type setting in the located file with grep. These commands have not yet been executed by the learner. Desktop services may not be present in WSL.

### Independent upstream verification
The GNOME project source commit adding the systemd user unit explicitly defines Type=dbus and BusName=ca.desrt.dconf. Ubuntu package documentation also identifies dconf-service as a D-Bus service and lists the systemd user unit.

Sources:
- https://lists.gnome.org/archives/commits-list/2021-February/msg02969.html
- https://manpages.ubuntu.com/manpages/noble/man1/dconf-service.1.html
- https://packages.ubuntu.com/noble/s390x/dconf-service/filelist

Answer: **dbus**. Submit only the value, not Type=.

## Verification
One mandatory question worth 20 HP. Answer independently verified against the upstream service definition; learner-side output and HTB acceptance remain pending. No timers, cron jobs or services were changed. Pwnbox is currently unavailable to the learner.

Independent English study notes based on HTB Academy.

## Learner verification — empty service property

The learner ran `systemctl --user show dconf.service --property=Type` on the assigned SSH target. Actual output: `Type=` (empty). This does not establish the expected unit's type; the unit may be unavailable in that environment. Next, locate the unit definition using the previously documented find command and inspect its Type setting if present. Upstream expected answer: dbus. Local verification and HTB acceptance remain pending.
