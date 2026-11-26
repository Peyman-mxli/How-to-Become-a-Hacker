# OS Command Injection — Defensive Study Guide

OS command injection occurs when untrusted data can alter the commands or arguments a system executes. A shell interprets command language, whereas an application using a structured process API can pass arguments separately. Avoid shell execution altogether where a language library can perform the task. Argument injection may remain possible even without a shell if an executable interprets user-controlled options. RCE is a potential consequence, not a synonym for every injection. Impact depends on process privileges, sandboxing and network access.

## Original example
A school application wants to calculate the SHA-256 checksum of an approved uploaded document. No operating system command is necessary:
```python
from hashlib import sha256
from pathlib import Path

def checksum(approved_path: Path):
    digest = sha256()
    with approved_path.open("rb") as source:
        for block in iter(lambda: source.read(65536), b""):
            digest.update(block)
    return digest.hexdigest()
```
The caller must resolve an authorized file from an internal identifier, not accept an arbitrary filesystem path. rb reads bytes; read(65536) bounds chunks; update consumes bytes; hexdigest returns hexadecimal digest. A hash does not prove that content is safe or authentic.

If a subprocess is unavoidable, use a fixed executable, structured argument list, shell=False, allowlisted inputs, timeout and least privilege. Avoid client-controlled executable names, flags and environment. Do not rely on a blacklist of metacharacters. Review shell wrappers, CGI integrations and third-party tools.

## Defensive assessment
Review subprocess calls, shell invocation, command construction and environment handling in code. Use benign unit-test strings containing spaces and punctuation to verify that data is rejected or treated as data, not executed. Do not run arbitrary commands against real services. The supplied course references a historical WordPress plugin flaw involving command construction from an IP input; the safe design validates the address using a proper parser and avoids a shell.

## Public vulnerability research
A well-known historical Bash parsing flaw demonstrates why vulnerable dependencies matter even when application code is not directly concatenating command strings. Consult the CVE record, NVD and distribution advisories, check affected package versions and apply vendor fixes. Package backports can invalidate simple version-based conclusions.

References: [OWASP OS Command Injection Defense](https://cheatsheetseries.owasp.org/cheatsheets/OS_Command_Injection_Defense_Cheat_Sheet.html), [Python subprocess](https://docs.python.org/3/library/subprocess.html), [Red Hat Shellshock advisory](https://access.redhat.com/security/vulnerabilities/shellshock). No exploit payloads or live tests included.

## Expanded OS concepts

| Term | Meaning |
| --- | --- |
| OS | Operating system |
| Shell | Interpreter for command language |
| Bash | Bourne Again Shell |
| PowerShell | Shell and scripting environment |
| Subprocess | Process launched by another process |
| Environment variable | Named value inherited or supplied to a process |
| Argument | Value supplied to an executable |
| CWE-78 | OS command injection weakness class |
| RCE | Remote code execution, a possible impact |

Unintended execution may affect confidentiality, integrity and availability. The process identity, filesystem access, network permissions and isolation determine the possible impact. Structured process APIs help separate arguments from shell syntax, but arguments themselves must still be validated.

### Checksum example explained

| Expression | Purpose |
| --- | --- |
| sha256() | Creates a SHA-256 hashing object |
| Path.open("rb") | Opens the authorized file as binary data |
| read(65536) | Reads up to 65,536 bytes at a time |
| iter(callable, sentinel) | Repeats reading until the empty byte string |
| digest.update | Adds bytes to the hash |
| hexdigest | Returns the hexadecimal digest |

### Historical Shellshock and safe local inspection

CVE-2014-6271 concerns vulnerable GNU Bash environment-variable function parsing; NVD and Red Hat classify it under CWE-78. Vendor package updates and least privilege are important defenses. The following commands inspect only the local shell environment; none is a vulnerability test:

```bash
echo "$HOME"
echo "$PATH"
bash --version
```

HOME is the current user's home directory; PATH is the command-search path; --version prints the shell version. Do not publish sensitive local configuration. A displayed version does not establish vulnerability, because Linux distributions may backport fixes.

Verified references: [NVD](https://nvd.nist.gov/vuln/detail/CVE-2014-6271), [Red Hat CVE record](https://access.redhat.com/security/cve/cve-2014-6271).
