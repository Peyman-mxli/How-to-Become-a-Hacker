# Terser — Local JavaScript Minifier

## Definition and architecture

Terser is an open-source JavaScript parser, compressor and mangler with a CLI and API. It parses syntax, optionally applies compression and identifier mangling, then prints compact JavaScript. A source map can associate generated locations with original source. Minification differs from packing and encryption. Terser is supplemental to the supplied HTB lesson, not a named HTB tool in Section 4.

## Official downloads and documentation

- https://github.com/terser/terser
- https://github.com/terser/terser/releases
- https://www.npmjs.com/package/terser
- https://terser.org/docs/cli-usage/
- https://nodejs.org/en/download/

Use Node.js and npm in a disposable project. Install Node LTS if needed from its official download page (Windows MSI or official Linux instructions).

## Windows 11 PowerShell

~~~powershell
node --version
npm --version
cd $HOME
mkdir js-minification-lab
cd js-minification-lab
npm init -y
npm install --save-dev terser
npx terser --version
~~~

## Ubuntu WSL2

~~~bash
node --version
npm --version
mkdir -p ~/js-minification-lab
cd ~/js-minification-lab
npm init -y
npm install --save-dev terser
npx terser --version
~~~

Use one environment; Windows and WSL installations are separate. npm init -y creates package metadata; --save-dev registers a development dependency; npx invokes the local executable. Keep package-lock.json for reproducibility, but do not commit node_modules.

## Original example

Save as app.js:

~~~javascript
function greet(name) {
    return "Welcome, " + name;
}

console.log(greet("Student"));
~~~

~~~powershell
npx terser app.js -c -m -o app.min.js
node app.js
node app.min.js
~~~

-c enables compression; -m enables identifier mangling; -o specifies the output path. Both files should print Welcome, Student. Exact generated text depends on configuration/version. The original remains separate. No execution was performed for this guide.

## Configuration and troubleshooting

Use npx terser --help and the official CLI documentation. Source maps are useful for debugging, but published maps may reveal original code. Do not place secrets in browser source. Avoid property mangling without compatibility tests; external code may rely on exact names. Preserve required license notices. If npm fails, check runtime version, network and reported error. If PowerShell blocks npm.ps1, use npm.cmd and npx.cmd. If input is missing, check working directory. If semantics differ, preserve readable input and do not deploy the output. Compare output and bundle size; test production builds.

## Project application

UniTrack can minify browser assets while keeping authorization server-side. ProTrack-MX may use production minification through its existing build pipeline; do not assume a specific minifier is configured without checking. Supabase RLS and backend authorization remain necessary. Prefer local processing for private source and never use minification as a security boundary.

References: https://terser.org/docs/cli-usage/ ; https://github.com/terser/terser ; https://nodejs.org/en/download/
