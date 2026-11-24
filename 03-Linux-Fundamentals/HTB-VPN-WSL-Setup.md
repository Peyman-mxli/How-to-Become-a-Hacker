# HTB Academy — Free Lab Access with Ubuntu WSL 2

## Purpose
When the free Pwnbox allocation is exhausted, use a local Ubuntu WSL 2 environment and the Academy VPN to access assigned training machines without purchasing additional Pwnbox time.

## Step 1 — Download the Academy VPN profile (completed)
1. Open the Connect to HTB section of the Academy lesson.
2. Select VPN instead of Pwnbox.
3. Select a suitable low-load Academy server. In this session, US Academy 5 and UDP 1337 were selected.
4. Click Download VPN file. The downloaded filename was academy-regular.ovpn.

Keep this file private because it may contain authentication material. Never commit it to GitHub.

## Step 2 — Check Ubuntu and WSL (completed)
Open Windows PowerShell and run:

~~~powershell
wsl -l -v
~~~

Observed output:

~~~text
  NAME      STATE           VERSION
* Ubuntu    Stopped         2
~~~

This confirms Ubuntu is already installed, uses WSL 2, and is currently stopped. No duplicate Ubuntu installation is needed.

## Step 3 — Start Ubuntu (completed)

~~~powershell
wsl -d Ubuntu
~~~

This starts the existing Ubuntu distribution and opens its Linux shell. Successfully verified: Ubuntu 24.04.4 LTS, x86_64, running under WSL 2.

## Step 4 — Install OpenVPN (verified)

~~~bash
sudo apt update && sudo apt install openvpn -y
~~~

Use the local Ubuntu password if sudo requests authentication. Installation completed successfully; OpenVPN 2.6.19 was installed.

## Step 5 — Locate the VPN profile (verified)

From Ubuntu WSL, locate the downloaded profile in the Windows Downloads directory:

~~~bash
ls /mnt/c/Users/YOUR_WINDOWS_USERNAME/Downloads/*.ovpn
~~~

Replace the username placeholder with your Windows account folder name. Do not publish the VPN profile.

The downloaded academy-regular.ovpn file was successfully located.

## Step 6 — Connect to the Academy VPN (verified)

~~~bash
sudo openvpn --config /mnt/c/Users/YOUR_WINDOWS_USERNAME/Downloads/academy-regular.ovpn
~~~

Replace the placeholder with the local Windows account folder. Look for `Initialization Sequence Completed` in the output. Keep this terminal running and use a separate Ubuntu terminal for SSH. OpenVPN initialization was confirmed by the message `Initialization Sequence Completed`. The foreground process remains running to maintain the tunnel. Remote SSH connectivity is still pending verification.

## Step 7 — Open a separate Ubuntu terminal (verified)

Keep the VPN terminal open. Open another Windows Terminal tab using Ctrl+Shift+T. If it opens PowerShell, run `wsl -d Ubuntu` to enter the same Ubuntu distribution. Use the second terminal for lab SSH commands.

A separate Ubuntu shell was successfully opened through PowerShell. Keep the first terminal running OpenVPN; commands typed into that foreground process will not execute as shell commands.

## Step 8 — Connect to the assigned lab via SSH (pending verification)

~~~bash
ssh htb-student@TARGET_IP
~~~

Replace TARGET_IP with the current assigned Academy target. Confirm the host key when appropriate for the assigned lab, and enter the exercise-provided password when prompted. Do not publish credentials or session-specific addresses.

## Remaining steps (pending)
- Open a separate terminal and connect via SSH to the assigned lab.
- Complete and verify the six System Information exercises.

This guide will be expanded as the actual setup progresses. Credentials, private VPN profiles, local usernames, target IPs and session details are intentionally excluded.

## Reference
[Hack The Box Academy](https://academy.hackthebox.com/)

## Troubleshooting: forgotten sudo password

OpenVPN installation was blocked by failed sudo authentication. Follow the [local WSL password recovery guide](WSL-Ubuntu-Password-Recovery.md), then retry installation. Password recovery completed successfully. Normal-user authentication verified with `sudo -v`. Resume OpenVPN installation.
