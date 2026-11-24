# Recover a Forgotten Ubuntu Password in WSL 2

## Scenario

A local Ubuntu WSL user forgot their sudo password. The package installation command failed after three incorrect password attempts. This recovery procedure applies to your own Windows and WSL environment, not remote systems.

## Step 1 — Return to PowerShell (verified)

From Ubuntu, run:

~~~bash
exit
~~~

## Step 2 — Start Ubuntu as root (verified)

From Windows PowerShell:

~~~powershell
wsl -d Ubuntu -u root
~~~

A root shell was successfully opened. Its prompt commonly ends in #.

## Step 3 — Reset the local user's password (pending verification)

~~~bash
passwd YOUR_LINUX_USERNAME
~~~

Replace YOUR_LINUX_USERNAME with your actual Ubuntu username. Enter the new password twice. Terminal password input is intentionally invisible.

Expected success message:

~~~text
passwd: password updated successfully
~~~

## Step 4 — Return to your normal account

~~~bash
exit
~~~

Then, from PowerShell:

~~~powershell
wsl -d Ubuntu
~~~

Verify sudo authentication:

~~~bash
sudo -v
~~~

Use the newly chosen password. Once verified, resume the OpenVPN installation.

## Security notes

- This is local account recovery on an owned WSL installation.
- Use a strong, unique password.
- Do not publish passwords, personal usernames, screenshots containing secrets, or VPN profiles.
- Return to the regular user account after recovery; avoid routine work as root.
- This procedure does not reset Windows or HTB account passwords.

## Status

Root shell access confirmed. Password change and sudo verification pending.

See also [HTB VPN setup](HTB-VPN-WSL-Setup.md).
