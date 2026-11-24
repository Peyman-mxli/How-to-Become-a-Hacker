# Lesson 28/30 — System Logs

## Concepts
Linux logs record operating system, application, authentication, network and security events. They support troubleshooting, incident investigation, monitoring and evaluating whether authorized security tests trigger alerts. Logs can contain sensitive information; never publish credentials, tokens, personal records or raw sensitive logs.

## Log types and locations
| Type | Typical location | Purpose |
| --- | --- | --- |
| Kernel | /var/log/kern.log | Drivers, hardware, kernel messages and failures |
| System | /var/log/syslog | Services, scheduled jobs, errors and system events |
| Authentication | /var/log/auth.log | Authentication, SSH, sudo and session events |
| Application | /var/log/apache2/error.log, /var/log/mysql/error.log | Application-specific events and errors |
| Security | /var/log/fail2ban.log, /var/log/ufw.log | Security tool events |

These are course examples, not universally present files. Linux distributions and service configurations differ. Journald may store records in /run/log/journal (volatile) or /var/log/journal (persistent); read with journalctl. Kernel logs do not normally record all system calls without additional instrumentation.

## Reading log entries
A typical record contains timestamp, hostname, program, optional PID and message. The course's syslog illustration contains a scheduled backup, failed SSH authentication, storage error, successful HTTP request, successful SSH authentication, EXT4 remount and PHP session cleanup. Correlate time and context; a failed authentication followed by success is not proof of compromise.

The authentication example includes successful public-key login, sudo commands, a disconnect, a firewall warning, audit daemon startup and session events. Accepted publickey indicates successful key authentication; an RSA SHA256 fingerprint identifies a key and is not a private key. Sudo entries show authorized command execution but do not establish membership in a particular Unix group. TTY identifies the terminal, PWD the working directory, USER the target identity, and COMMAND the executed command. Unexpected firewall traffic warrants investigation, not an automatic conclusion of compromise.

## Application, access and audit logs
Access logs describe requests or access events; audit logs record security-relevant actions such as configuration changes. An illustrative course entry describes a privileged script accessing a sensitive file. This is evidence to investigate authorization and process behavior, not a recommendation to retrieve secrets.

| Service | Common location |
| --- | --- |
| Apache | /var/log/apache2/access.log and error.log |
| Nginx | /var/log/nginx/access.log and error.log |
| OpenSSH | /var/log/auth.log (Debian/Ubuntu), /var/log/secure (RHEL family) |
| MySQL | /var/log/mysql/error.log; general query log location/configuration varies |
| PostgreSQL | /var/log/postgresql/postgresql-VERSION-main.log on some Debian-family installations |
| systemd | /var/log/journal/ or /run/log/journal/ |

Database logging can be disabled or routed elsewhere. Apache access log fields commonly include client IP, timestamp, request, HTTP status, response size and user agent, according to configured format. 200 means successful HTTP processing; 401 indicates authentication required, 403 forbidden, 404 not found and 500 internal server error.

## Supplemental read-only commands (not executed)
~~~bash
sudo dmesg
sudo journalctl -k
sudo tail -n 50 /var/log/syslog
sudo tail -f /var/log/auth.log
sudo grep -i 'failed password' /var/log/auth.log
sudo grep -i 'accepted publickey' /var/log/auth.log
sudo sed -n '1,20p' /var/log/syslog
sudo journalctl -u ssh --since today --no-pager
sudo journalctl -p warning --since today
~~~
tail -n selects the last N lines; -f follows new records (Ctrl+C stops); grep -i performs case-insensitive matching; sed -n suppresses default output and 1,20p prints the first 20 lines; journalctl -k selects kernel messages, -u a service, --since a time range, -p a priority threshold, --no-pager disables interactive paging. SSH unit names may be ssh or sshd. Commands require appropriate permissions and existing files/services.

## Logging security and lifecycle
Configure suitable severity levels, permissions, retention, rotation, reliable timestamps and regular review. logrotate manages rotation of configured text logs; journald manages its own storage limits. Centralized log collection can support correlation and reduce dependence on local records. Avoid logging plaintext passwords or tokens; restrict access and preserve evidence appropriately. Fail2ban, UFW, syslog, rsyslog and ELK relate to earlier lessons.

## UniTrack connection
Potential application audit events include successful/failed logins, role changes, access denials, QR check-in/out and server errors. Store only necessary fields, protect student data, avoid passwords, session tokens and raw sensitive identifiers, and distinguish application events from Linux OS logs. This is an illustrative design recommendation, not a verified implementation.

## Verification
Full learner-supplied Section 28 reviewed and explained. No mandatory questions are present. No practical commands executed, output verified or platform completion confirmed. Learner requested no optional quizzes. Next: Mark Complete & Next, then Section 29/30.

Independent English study notes based on the learner-provided HTB Academy lesson, with supplemental technical clarifications.
