# Lesson 25/30 — Remote Desktop Protocols in Linux

## Concepts
Remote desktop technologies provide graphical remote administration, maintenance and support. RDP (default port 3389) is commonly associated with Windows, while VNC is cross-platform and common on Linux. SSH (TCP 22 by default) provides encrypted shell access and forwarding rather than a full desktop by default.

## X11 and X server
X11 provides network-transparent graphical application communication. The X server manages the display and input on the user's side; X clients are applications that may execute remotely. X11 can use Unix-domain sockets or TCP. If TCP listening is enabled, display N conventionally corresponds to TCP 6000+N. Modern systems often disable direct TCP listening; some desktops use Wayland and Xwayland rather than a traditional Xorg session.

Course examples:
~~~bash
cat /etc/ssh/sshd_config | grep X11Forwarding
ssh -X htb-student@LAB_HOST /usr/bin/firefox
~~~
The first command reads and filters the SSH daemon configuration; X11Forwarding yes is the expected setting in the example. Included configuration files and Match blocks may alter effective settings. -X requests X11 forwarding; the remote application runs on the server while its window is displayed locally through an encrypted SSH connection. LAB_HOST is a placeholder for an authorized host. A compatible local display, remote xauth and server support may be needed. Do not enable forwarding globally without a legitimate need. Direct X11 is unencrypted and unauthorized access may expose windows or input. The course mentions xwd as a window capture tool and historical Xorg vulnerabilities CVE-2017-2624, CVE-2017-2625 and CVE-2017-2626. Keep packages patched, restrict display access and avoid disabling X access controls.

## XDMCP
X Display Manager Control Protocol uses UDP 177 to negotiate remote graphical login sessions with a display manager. It is legacy and lacks adequate transport protection; interception and impersonation are risks. Prefer secured remote access mechanisms and avoid exposing it to untrusted networks.

## VNC
Virtual Network Computing uses Remote Framebuffer (RFB) to send graphical updates and receive keyboard/mouse input. Servers may share a physical desktop or provide independent virtual desktops. Display N conventionally uses TCP 5900+N (display :0 = 5900; :1 = 5901). Authentication and encryption depend on implementation and selected security type; traditional VNC authentication does not encrypt the entire session. Restrict exposure and use authenticated SSH tunneling or an appropriately configured encrypted VNC mode.

Course-listed implementations:
- https://tigervnc.org/
- https://www.tightvnc.com/
- https://www.realvnc.com/en/
- https://uvnc.com/

## TigerVNC example (not executed)
The course installs XFCE and TigerVNC on Ubuntu:
~~~bash
sudo apt install xfce4 xfce4-goodies tigervnc-standalone-server -y
vncpasswd
~~~
XFCE is a desktop environment; tigervnc-standalone-server hosts virtual sessions; -y automatically accepts package prompts. vncpasswd configures VNC authentication and may offer a view-only password. Never commit passwords.

The course's legacy setup uses ~/.vnc/xstartup (session startup script) and ~/.vnc/config (display settings). touch creates missing files; the course uses a shell heredoc with >> to append content. Avoid repeatedly appending duplicate settings. A simplified XFCE startup example is:
~~~bash
#!/bin/sh
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
exec startxfce4
~~~
Example config: geometry=1920x1080 and dpi=96. These define display resolution and pixel density. Set the startup script executable with chmod +x ~/.vnc/xstartup. Paths and session startup methods vary by TigerVNC version; consult the installed manual. Do not install a full desktop on the active WSL environment just to complete the lesson.

~~~bash
vncserver -localhost yes :1
vncserver -list
~~~
The first example explicitly restricts the VNC listener to loopback and starts display :1 (verify supported syntax in the installed version); the second lists sessions, ports and process IDs. The lesson's example output shows display :1 on port 5901, not a verified learner result.

## SSH local forwarding
From the authorized client, using an account on the owned or assigned lab server:
~~~bash
ssh -L 127.0.0.1:5901:127.0.0.1:5901 -N htb-student@LAB_HOST
~~~
-L defines local bind address and port, remote destination address and port; -N requests no remote shell. The first loopback belongs to the client, the second to the remote SSH server. The source also demonstrates -f (background after authentication) and -l USER (login name). Foreground mode is easier to stop with Ctrl+C. Keep VNC bound to server loopback. Authenticate the SSH host key and account. Then connect a compatible viewer to 127.0.0.1 port 5901; explicit port syntax varies (TigerVNC commonly uses localhost::5901, while the course uses xtightvncviewer localhost:5901). VNC authentication remains separate from SSH. Stop a dedicated session with vncserver -kill :1 if supported.

## Review and verification
RDP 3389; SSH 22; X11 TCP 6000+display when enabled; XDMCP UDP 177; VNC TCP 5900+display. Remote desktop security depends on authentication, encryption, patching and network exposure. The supplied lesson contains setup demonstrations but no mandatory question. The learner chose to continue the course without optional installations. No commands, connections or screenshots newly verified. Section 25 studied; HTB Mark Complete & Next pending.

Independent English notes based on the learner-provided HTB Academy Section 25, with clearly distinguished technical clarifications. Next: Section 26/30.
