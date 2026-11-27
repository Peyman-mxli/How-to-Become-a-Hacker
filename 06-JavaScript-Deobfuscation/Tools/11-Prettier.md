# Prettier — Source Code Formatting

## Purpose and architecture

Prettier is an open-source formatter for JavaScript, TypeScript, JSX, CSS, HTML, JSON and other supported languages. It parses source, builds a syntax representation, and prints consistent formatting according to its configuration. Formatting is not execution or general-purpose deobfuscation, and does not recover original comments or identifiers.

## Official access and installation

- Playground: https://prettier.io/playground/
- Source and releases: https://github.com/prettier/prettier/releases
- npm: https://www.npmjs.com/package/prettier
- Install: https://prettier.io/docs/install
- CLI: https://prettier.io/docs/cli
- VS Code extension: https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode
- Node.js runtime: https://nodejs.org/en/download/

The browser playground needs no installer. Its interactive interface could not be inspected through the research view. Install the official VS Code extension through Ctrl+Shift+X, or in PowerShell:

~~~powershell
code --install-extension esbenp.prettier-vscode
~~~

Use Shift+Alt+F to format the document; if prompted select Prettier through Format Document With. The code command requires VS Code's CLI on PATH. Prefer formatting an evidence copy.

## Windows and Ubuntu WSL2 local installation

In PowerShell:
~~~powershell
cd $HOME
mkdir js-formatting-lab
cd js-formatting-lab
npm init -y
npm install --save-dev --save-exact prettier
npx prettier --version
~~~

In Ubuntu WSL2:
~~~bash
mkdir -p ~/js-formatting-lab
cd ~/js-formatting-lab
npm init -y
npm install --save-dev --save-exact prettier
npx prettier --version
~~~

Use one environment, with supported Node and npm installed. --save-dev adds a development dependency; --save-exact pins the version in package.json. Retain package-lock.json; exclude node_modules from Git. The --version option verifies the installed tool.

## Original example

Save this trusted sample as input.js:
~~~javascript
function greet(name){return"Hello, "+name;}console.log(greet("Student"));
~~~

~~~powershell
npx prettier input.js
~~~

Expected readable representation:
~~~javascript
function greet(name) {
  return "Hello, " + name;
}
console.log(greet("Student"));
~~~

Expected runtime result: Hello, Student. The CLI normally prints formatting to stdout. npx prettier input.js --write overwrites the file; use only on a copy when analyzing evidence. Configuration example (.prettierrc):

~~~json
{
  "tabWidth": 2,
  "semi": true
}
~~~

tabWidth controls indentation width and semi controls semicolon formatting. Use npx prettier input.js --check to check consistency without rewriting.

## Troubleshooting and security

If the formatter is unavailable, verify extension ID, Node/npm and PATH. For PowerShell script-policy problems, use npm.cmd and npx.cmd. Syntax errors may indicate invalid input; preserve the original instead of altering it blindly. If formatting differs, review configuration and the installed version. Never execute unknown obfuscated scripts to format them. Prefer local tooling for private code and avoid submitting secrets to online services.

Use formatting to improve UniTrack and ProTrack-MX code reviews; it does not enforce authorization or secure credentials. No actual learner-side installation or execution claimed.
