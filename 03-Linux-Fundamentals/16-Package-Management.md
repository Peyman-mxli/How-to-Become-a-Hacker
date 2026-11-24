# Lesson 16/30 — Package Management

## Overview
Packages distribute software, configuration and dependency metadata. Package managers install, update, configure and remove software while tracking package state.

| Tool | Purpose |
| --- | --- |
| apt | High-level Debian-family package management |
| dpkg | Low-level Debian package management |
| aptitude | Alternative APT interface |
| snap | Manage Snap packages |
| gem | Ruby packages |
| pip | Python packages |
| git | Version control and repository cloning; not itself a package manager |

## APT — why and how
~~~bash
sudo apt update
apt search git
apt show git
sudo apt install git
apt list --installed
~~~
update refreshes package indexes, search finds available packages, show displays metadata, and install retrieves the selected package and resolves dependencies. sudo requires appropriate authorization. The optional -y flag confirms prompts automatically; review planned changes before using it.

Other commands: apt upgrade upgrades installed packages; apt remove removes selected packages; apt autoremove removes dependencies no longer required. These can modify the system and are not required for this lesson.

## Repository and cache information
APT repository configuration may reside in /etc/apt/sources.list or files under /etc/apt/sources.list.d/. The lesson illustrates the Parrot-specific parrot.list file. Ubuntu installations may use deb822 .sources files.

~~~bash
apt-cache search impacket
apt-cache show impacket-scripts
~~~
search finds packages matching a term; show displays metadata including dependencies, version and architecture. Alternatives: apt search and apt show. Package availability varies by distribution and configured repositories. The lesson's package metadata is illustrative rather than confirmed on the learner's machine.

## Git — why and how
~~~bash
git clone https://github.com/Peyman-mxli/How-to-Become-a-Hacker.git
~~~
clone creates a local repository copy, including Git metadata. This learner-owned public study repository is a supplemental example. Cloning source code is distinct from installing a program. Do not run unreviewed third-party scripts.

## DPKG — why and how
The lesson illustrates downloading a strace Debian package built for Ubuntu 18.04. Avoid installing an old distribution-specific binary on an incompatible system.

~~~bash
apt download strace
~~~
Downloads a package compatible with the configured repositories without installing it.

~~~bash
sudo dpkg -i filename.deb
~~~
-i installs a local Debian package; replace filename.deb with the actual compatible package filename. dpkg does not automatically fetch missing dependencies. APT can also install a compatible local package using apt install ./filename.deb.

Other options: -r removes an installed package; -l lists package records; -s shows status; -L lists installed files.

## Optional exercise
The source lesson asks learners to explore installation methods for Evil-WinRM on interactive lab instances. It is optional and not required for section completion. Pwnbox is currently unavailable to the learner. No installation was performed.

Supplemental read-only package inspection practice:
~~~bash
git --version
dpkg -s git
apt-cache show git
~~~
The first command reports the executable version; dpkg -s inspects installed package status; apt-cache show displays repository metadata. Output depends on the actual installation. These are suggested commands, not verified learner output.

## Verification
Section 16 has no mandatory questions and offers one optional exercise. No packages were installed or removed in this step. The learner previously used APT to install OpenVPN; that earlier practical setup is documented separately. Section 15 was followed by Section 16, but individual submission acceptance was not explicitly shown.

Independent English study notes based on HTB Academy.
