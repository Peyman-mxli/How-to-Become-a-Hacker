# Lesson 17/30 — Service and Process Management

## Overview
Services provide managed system functionality, often through background daemons. systemd commonly runs as PID 1 and manages services. PID identifies a process; PPID identifies its parent. /proc exposes process information.

## Systemctl
| Command | Purpose |
| --- | --- |
| systemctl start ssh | Start service now |
| systemctl stop ssh | Stop service |
| systemctl restart ssh | Restart service |
| systemctl status ssh | Inspect status |
| systemctl enable ssh | Configure automatic startup |
| systemctl disable ssh | Disable automatic startup |
| systemctl list-units --type=service | List loaded service units |

Starting and enabling are distinct. Do not stop or restart SSH merely for practice on a remote machine.

## Service logs
~~~bash
journalctl -u ssh.service --no-pager
~~~
-u selects the unit; --no-pager displays logs directly. Access to logs depends on privileges.

## Processes and signals
ps aux lists processes, pgrep finds process IDs, kill sends signals to a process, pkill selects matching processes, and killall selects by process name. kill -l lists signals.

| Signal | Meaning |
| --- | --- |
| SIGHUP (1) | Hangup or application-specific reload |
| SIGINT (2) | Interrupt, normally Ctrl+C |
| SIGQUIT (3) | Quit, normally Ctrl+backslash |
| SIGKILL (9) | Force termination |
| SIGTERM (15) | Request termination |
| SIGCONT (18) | Continue stopped process |
| SIGSTOP (19) | Suspend, cannot be caught |
| SIGTSTP (20) | Interactive suspension, normally Ctrl+Z |

Corrections to lesson examples: Ctrl+D normally indicates end-of-input, not SIGQUIT. Force termination syntax is kill -9 PID, not kill 9 PID. Prefer graceful termination first. A zombie is a terminated process whose parent has not yet collected its exit status.

## Shell job control
Ctrl+Z suspends a foreground job; jobs lists shell jobs; bg resumes a job in the background; fg %1 foregrounds job 1; & starts a background job.

Suggested disposable practice:
~~~bash
sleep 60 &
jobs
fg %1
~~~
Use the actual job number returned by jobs. This example has not been executed.

## Command chaining
Semicolon runs sequential commands regardless of earlier exit status; && continues after success; || runs the next command after failure; pipe transfers stdout to stdin and does not itself require earlier success; & starts background execution.

## Mandatory question — identify the AppArmor service unit

~~~bash
systemctl list-units --type=service --all --no-pager | grep -F 'Load AppArmor profiles managed internally by snapd'
~~~

### Why and how
systemctl inspects systemd units; list-units lists loaded units; --type=service limits output to services; --all includes inactive loaded units; --no-pager avoids interactive paging. grep -F filters the literal description. The first column gives the requested unit name. Verified answer from the learner's target: snapd.apparmor.service; platform acceptance pending.

Alternative:
~~~bash
systemctl list-units --type=service --all --no-pager | grep -F 'Load AppArmor profiles managed internally by snapd' | awk '{print $1}'
~~~
awk extracts the first whitespace-delimited field. Inspect the full output first because markers may precede some unit names. The alternative is untested.

The supplied lesson showed approximately five minutes remaining on the assigned target. If the target expires, reconnect to a newly assigned target using the existing VPN as documented in the setup notes.

## Verification
One mandatory question worth 20 HP. The unit name is verified from the learner's actual target output; HTB acceptance remains pending. No service changes were performed. No credentials or session-specific addresses included.

Independent English study notes based on HTB Academy.

## Mandatory question — Verified terminal output

~~~text
snapd.apparmor.service    loaded    active    exited    Load AppArmor profiles managed internally by snapd
~~~

Answer: **snapd.apparmor.service**. The learner's output exactly matches the requested description. UNIT identifies the service; LOAD indicates that its unit definition is loaded; ACTIVE is the high-level state; SUB is the detailed state. An active/exited service can have completed its task without a continuously running process.

The alternative awk '{print $1}' pipeline extracts the first field from this observed row. Expected output: snapd.apparmor.service. The alternative has not been executed. No service settings were changed. HTB submission acceptance remains pending.
