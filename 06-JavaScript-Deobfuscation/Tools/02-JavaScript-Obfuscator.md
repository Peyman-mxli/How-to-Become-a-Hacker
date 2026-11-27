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
