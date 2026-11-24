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

## Step 4 — Install OpenVPN (pending execution)

~~~bash
sudo apt update && sudo apt install openvpn -y
~~~

Use the local Ubuntu password if sudo requests authentication. Installation has not yet been verified.

## Remaining steps (pending)
- Locate the downloaded VPN profile locally.
- Connect to the Academy VPN and verify connectivity.
- Open a separate terminal and connect via SSH to the assigned lab.
- Complete and verify the six System Information exercises.

This guide will be expanded as the actual setup progresses. Credentials, private VPN profiles, local usernames, target IPs and session details are intentionally excluded.

## Reference
[Hack The Box Academy](https://academy.hackthebox.com/)

## Troubleshooting: forgotten sudo password

OpenVPN installation was blocked by failed sudo authentication. Follow the [local WSL password recovery guide](WSL-Ubuntu-Password-Recovery.md), then retry installation. Password recovery completed successfully. Normal-user authentication verified with `sudo -v`. Resume OpenVPN installation.
