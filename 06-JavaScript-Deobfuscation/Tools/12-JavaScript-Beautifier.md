# JavaScript Beautifier — js-beautify

## Purpose and architecture

js-beautify is a free open-source formatter for JavaScript, CSS and HTML. It reindents code and supports unpacking some recognized packer formats and partial deobfuscation. It is not a universal unpacker and does not guarantee recovery of original names or comments. Prefer static inspection of the output rather than execution.

## Official access and downloads

- Browser utility: https://beautifier.io/
- Source: https://github.com/beautifier/js-beautify
- Releases: https://github.com/beautifier/js-beautify/releases
- npm: https://www.npmjs.com/package/js-beautify
- Node.js: https://nodejs.org/en/download/

The browser utility requires no installer. Local npm installation is preferred for private source. Use the separate js-formatting-lab directory with Node and npm installed:
~~~powershell
npm install --save-dev js-beautify
npx js-beautify --version
~~~
The same commands work in Ubuntu WSL2. --save-dev registers a development dependency; npx invokes the installed CLI. If starting a new directory, run npm init -y first.

## Original example

Save sample.js:
~~~javascript
function add(a,b){return a+b;}console.log(add(20,30));
~~~
~~~powershell
npx js-beautify sample.js
npx js-beautify sample.js -o sample.beautified.js
~~~
-o selects a separate output path. Expected readable representation:
~~~javascript
function add(a, b) {
    return a + b;
}
console.log(add(20, 30));
~~~
Expected runtime result: 50. The example is illustrative. For online formatting, open the official site, paste only non-sensitive sample text, select the relevant format and inspect the output. Preserve the original.

## Configuration and troubleshooting

Use npx js-beautify --help to inspect supported options. A .jsbeautifyrc can set indentation and related preferences; example:
~~~json
{"indent_size": 4}
~~~
If output is unchanged, the source may already be formatted or the transformation unsupported. If input is invalid, preserve it and inspect the syntax. If npm fails, check the runtime, PATH, current directory and reported errors. For unknown scripts do not rely on a beautifier as a sandbox. A recognized wrapper may be unpacked, but custom layers require analysis.

## Project and security applications

Consistent formatting helps review UniTrack JavaScript and ProTrack-MX production assets. Keep source evidence unchanged, do not upload proprietary code or credentials, and enforce access server-side. No actual learner installation or tool output claimed.
