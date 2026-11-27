# Impacket — network protocol toolkit

Impacket is an open-source Python collection maintained by Fortra for constructing and interpreting protocol messages. Protocol classes represent fields and encode/decode messages; command-line utilities build on them. SMB supports file and service communication, Kerberos and NTLM support authentication. Tool access never establishes permission to use it.

## Official downloads and optional setup reference
[Repository and install instructions](https://github.com/fortra/impacket), [releases](https://github.com/fortra/impacket/releases). For Ubuntu WSL2, use an isolated environment via pipx:
```bash
sudo apt update
sudo apt install pipx
pipx ensurepath
pipx install impacket
pipx list
```
ensurepath configures command search paths; restart shell if necessary. install creates an isolated environment; list confirms packages and available entry points. Use the official supported Python version and stable release. No installation required for this lesson.

## Original non-network example
To study message structure without contacting a host, a separate Python virtual environment can import the library:
```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install impacket
python -c "from impacket import smb; print(smb.__name__)"
```
Expected illustrative output: impacket.smb. venv isolates dependencies; -m runs a Python module; -c executes a short Python statement. If venv is missing, install python3-venv via Ubuntu package manager. Use one installation approach according to need, not both.

## Troubleshooting and security
Check Python support, pipx PATH and virtual environment. Do not paste real credentials or run utilities against unauthorized systems. The lesson's assessor anecdote shows tool success does not establish network coverage. Defend against authentication misuse with segmentation, reduced NTLM exposure, appropriate SMB signing and least privilege. UniTrack/ProTrack-MX: inventory infrastructure dependencies and restrict unnecessary Windows file-sharing paths where relevant. No learner execution verified.
