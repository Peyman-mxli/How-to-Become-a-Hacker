# JavaScript Obfuscator — Local Reference Guide

## Definition, architecture and limitations

A JavaScript obfuscator converts source to a less readable representation while attempting to retain its behavior. A typical AST-based tool parses source, transforms the syntax tree, and generates output. This is distinct from minification, encryption, bundling and access control. Obfuscation is not a confidentiality or authorization guarantee.

The free open-source javascript-obfuscator project offers a local npm package and command-line interface. The third-party Beautify Tools site cited by HTB is a separate browser utility. Do not upload private project source, secrets, student information or assessment data to online utilities.

## Installation and configuration

Official project: https://github.com/javascript-obfuscator/javascript-obfuscator
Package: https://www.npmjs.com/package/javascript-obfuscator
Node.js downloads: https://nodejs.org/en/download

Use a separate disposable local project, not UniTrack or ProTrack-MX. Node.js and npm must already be available; the learner's environment includes them. Reference commands for PowerShell or WSL2:

```bash
node --version
npm --version
mkdir js-obfuscation-demo
cd js-obfuscation-demo
npm init -y
npm install --save-dev javascript-obfuscator
npx javascript-obfuscator --help
```

--version prints the installed version. mkdir creates a folder; cd changes into it; npm init -y creates package.json with defaults; --save-dev registers a development dependency; npx invokes a package executable, preferably the locally installed version; --help lists supported options. Retain package-lock.json for reproducibility and review dependency provenance. No installation is required in Section 3.

## Original harmless example

Create input.js with a simple calculation:

```javascript
function calculatePrice(price, quantity) {
    return price * quantity;
}
console.log(calculatePrice(25, 4));
```

The following commands are reference material for owned, harmless code only. They are not an additional required HTB exercise:

```bash
node input.js
npx javascript-obfuscator input.js --output output.js
node output.js
```

node executes the specified trusted file. input.js identifies the source file and --output output.js selects a separate output path. The expected output is 100 for both programs, subject to actual verification. The exact transformed source varies by tool version and configuration. No tool execution or measured output is claimed.

Use ordinary default settings for this example; do not configure detection-evasion behavior. Keep the original source and generated output separate. Compare behavior and review errors before any production deployment.

## Troubleshooting and maintainability

| Symptom | Next check |
| --- | --- |
| node or npm not found | Check PATH, installed Node.js version and terminal environment. |
| npm install fails | Inspect the error, verify registry connectivity and package requirements. |
| npx command fails | Confirm the package is installed in the current folder and consult --help. |
| Output file missing | Check input path, working directory, permissions and CLI errors. |
| Output behaves differently | Compare the original behavior and generated file; retain the original and do not deploy the broken output. |
| Debugging becomes difficult | Debug readable source and manage source maps carefully. |
| Larger bundle or slower execution | Measure on the intended workload; prefer ordinary minification when size reduction is the goal. |

## Security and project applications

Do not treat hidden code as authorization. UniTrack must validate QR access, roles and visitor expiration server-side. ProTrack-MX must use server-side authorization and Supabase RLS for protected records. Obfuscation cannot protect secrets delivered to a browser. Never run unknown obfuscated code on the host or paste it into a normal browser console. Work only on code owned by the learner or expressly authorized for analysis.

## Official references

- [JavaScript Obfuscator project](https://github.com/javascript-obfuscator/javascript-obfuscator)
- [npm package](https://www.npmjs.com/package/javascript-obfuscator)
- [Node.js downloads](https://nodejs.org/en/download)
- [OWASP Web Frontend Security](https://cheatsheetseries.owasp.org/cheatsheets/Web_Frontend_Security_Cheat_Sheet.html)

Supplement only: no additional HTB section credit, tool installation or local execution claimed. Section 4 is the next official activity.

## Expanded Windows 11 and WSL2 installation guide

Official download/access locations:

- [GitHub releases and source downloads](https://github.com/javascript-obfuscator/javascript-obfuscator/releases)
- [Official npm package](https://www.npmjs.com/package/javascript-obfuscator)
- [Node.js official downloads, including Windows LTS MSI](https://nodejs.org/en/download/)
- [Release 5.10.1](https://github.com/javascript-obfuscator/javascript-obfuscator/releases/tag/5.10.1)

Recent releases beginning with 5.10.0 require Node.js 20 or newer according to the project's release notes. Use a supported LTS runtime. The local basic package is open-source; paid hosted options are not needed. The npm package can also be downloaded as a compressed archive using:

```powershell
npm pack javascript-obfuscator
```

npm pack retrieves the package archive; it does not install its dependencies. Prefer npm install for normal use. The official releases page was verified; individual downloadable release assets did not load in the research view. The npm website returned an access error when opened directly, but the official upstream GitHub README verifies the package name and CLI syntax.

### Windows PowerShell

```powershell
node --version
npm --version
cd $HOME
mkdir javascript-obfuscation-lab
cd javascript-obfuscation-lab
npm init -y
npm install --save-dev javascript-obfuscator
npx javascript-obfuscator --help
```

$HOME is the current user's home directory. Use an unused demonstration folder; if it already exists, inspect its contents before reusing it. If Node is missing, download the Windows LTS MSI from the official Node.js page, install it and reopen PowerShell. The existing learner environment already includes Node and npm; no reinstall is needed if versions meet the requirements. If PowerShell blocks npm.ps1, use npm.cmd and npx.cmd rather than lowering execution policy.

### Ubuntu WSL2

From PowerShell:

```powershell
wsl -d Ubuntu
```

Inside Ubuntu:

```bash
node --version
npm --version
mkdir -p ~/javascript-obfuscation-lab
cd ~/javascript-obfuscation-lab
npm init -y
npm install --save-dev javascript-obfuscator
npx javascript-obfuscator --help
```

wsl -d Ubuntu selects the Ubuntu distribution; mkdir -p creates a directory and parents as needed, and ~ refers to the Linux home. Check the runtime in the selected environment. If Node is absent or unsupported, follow the official Node.js download instructions for Linux. Windows and WSL2 are separate environments; choose one.

### File creation and validation

In the chosen project directory, create input.js using VS Code and enter the calculatePrice example above. Run node input.js, then npx javascript-obfuscator input.js --output output.js. Inspect your generated file, then run node output.js. The expected output of both is 100. The output is illustrative until actually executed. Do not overwrite input.js or execute unknown samples.

Expected project layout:

```text
javascript-obfuscation-lab/
  node_modules/
  input.js
  output.js
  package.json
  package-lock.json
```

node_modules contains installed dependencies; package.json declares project metadata/dependencies; package-lock.json records resolved versions for reproducibility. Retain readable source and the lockfile; do not commit node_modules to the educational repo.

## Comparing the two tools

| Feature | BeautifyTools | JavaScript Obfuscator |
| --- | --- | --- |
| Basic cost | Free | Free |
| Access | Browser | Local npm CLI and programming API |
| Installation | None | Node.js/npm and package |
| Windows/WSL2 | Browser access | Local supported environments |
| Internet | Required to access site | Required for initial installation |
| Open source | Not verified | Yes |
| Private source | Do not upload | Prefer local processing |

See [BeautifyTools individual guide](03-BeautifyTools-JavaScript-Obfuscator.md). Use only non-sensitive examples online. The local package is preferred for private owned code, but obfuscation never replaces backend authorization or secure credential management.

Both tutorials are supplements to Section 3, not additional HTB lessons. Installation and generated output have not been executed or verified on the learner's computer.
