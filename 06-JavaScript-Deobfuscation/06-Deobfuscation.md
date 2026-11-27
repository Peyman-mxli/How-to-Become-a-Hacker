# Section 06 — Deobfuscation

**Module:** JavaScript Deobfuscation | **Section:** 6/11 | **Date:** November 26, 2026

**Status:** Theory documented from learner-supplied HTB lesson. One mandatory flag question; live lab response and static flag reconstruction verified from learner terminal output; HTB answer acceptance pending. Displayed rewards +15 section and +30 question are not verified as earned. No tool installation or unknown-script execution claimed.

## 1. Beautification, deobfuscation and reverse engineering

Beautification restores spacing, indentation and line breaks. Deobfuscation attempts to recover concealed program behavior. Reverse engineering studies an existing program to understand how it operates. Original names and comments may not be recoverable.

Original compact example:
~~~javascript
function _0x1(a,b){return a*b}console.log(_0x1(10,5));
~~~
Beautified:
~~~javascript
function _0x1(a, b) {
    return a * b;
}
console.log(_0x1(10, 5));
~~~
Reconstructed with descriptive names:
~~~javascript
function multiply(first, second) {
    return first * second;
}
console.log(multiply(10, 5));
~~~
Expected output of each is 50; illustrative, not executed. The multiplication behavior is unchanged, but meaningful names improve understanding. Formatting alone does not reverse arbitrary obfuscation.

## 2. Browser Pretty Print

Firefox: open an authorized trusted page, press Ctrl+Shift+Z on Windows/Linux or F12 and select Debugger, locate secret.js, then use the Pretty Print control (curly braces) in the source pane. Firefox creates a formatted source view. Chrome: F12, Sources, select the script, Pretty Print. Exact layout may vary. No extra installation is needed for existing browsers. Elements/Inspector shows the live DOM, Sources/Debugger shows scripts, Network shows requests/responses, and Console permits interaction. Pretty Print changes presentation, not original semantics. For unknown scripts, prefer retrieving text without execution. See [browser source inspection guide](Tools/01-Browser-Source-Inspection.md).

Official Firefox documentation: https://firefox-source-docs.mozilla.org/devtools-user/debugger/how_to/open_the_debugger/index.html and https://firefox-source-docs.mozilla.org/devtools-user/debugger/how_to/pretty-print_a_minified_file/index.html.

## 3. Prettier

Free open-source formatter, not a general-purpose deobfuscator. Supports JavaScript, TypeScript, JSX, CSS, HTML and JSON. Online: https://prettier.io/playground/ ; releases https://github.com/prettier/prettier/releases ; npm https://www.npmjs.com/package/prettier ; VS Code https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode.

Install VS Code extension using:
~~~powershell
code --install-extension esbenp.prettier-vscode
~~~
Or open Extensions (Ctrl+Shift+X), search Prettier - Code formatter, verify extension identifier, and install. Use Shift+Alt+F or Format Document With.

Local reference in a separate disposable project:
~~~powershell
cd $HOME
mkdir js-formatting-lab
cd js-formatting-lab
npm init -y
npm install --save-dev --save-exact prettier
npx prettier --version
~~~
Save input.js:
~~~javascript
function greet(name){return"Hello, "+name;}console.log(greet("Student"));
~~~
~~~powershell
npx prettier input.js
~~~
Expected formatted representation:
~~~javascript
function greet(name) {
  return "Hello, " + name;
}
console.log(greet("Student"));
~~~
The CLI prints the formatted text; --write modifies the input file. Preserve original evidence and use a copy. See [Prettier guide](Tools/11-Prettier.md).

## 4. JavaScript Beautifier

js-beautify is a free open-source formatter that can also unpack some known packing formats and partially deobfuscate some scripts. It does not handle every custom obfuscator. Official website https://beautifier.io/ ; source https://github.com/beautifier/js-beautify ; releases https://github.com/beautifier/js-beautify/releases ; npm https://www.npmjs.com/package/js-beautify.

In the separate formatting project:
~~~powershell
npm install --save-dev js-beautify
npx js-beautify --version
~~~
Save sample.js:
~~~javascript
function add(a,b){return a+b;}console.log(add(20,30));
~~~
~~~powershell
npx js-beautify sample.js
npx js-beautify sample.js -o sample.beautified.js
~~~
Expected readable representation:
~~~javascript
function add(a, b) {
    return a + b;
}
console.log(add(20, 30));
~~~
Expected runtime output: 50. -o specifies a separate output file. See [JavaScript Beautifier guide](Tools/12-JavaScript-Beautifier.md).

## 5. UnPacker and manual reconstruction

Official historical browser utility: https://matthewfl.com/unPacker.html ; author explanation: https://matthewfl.com/19/old-blog/unpacker. The original page was reachable; standalone installer not verified. HTB instructs pasting trusted packed code and clicking UnPack, avoiding leading blank lines. The utility's handling of arbitrary scripts and privacy have not been audited. Prefer static local analysis for unknown scripts. See [UnPacker guide](Tools/13-UnPacker.md).

Packing typically stores program text and a substitution dictionary, with a reconstruction wrapper. The supplied lesson shows an eval(function(p,a,c,k,e,d) ...) pattern. In this particular example, p is packed text, a is radix argument 17, c is count 17, k is the dictionary, e is a helper reassigned to encode an index using toString(36), and d is a lookup object. These names are not universal. Values 10 through 16 are represented by a through g in the packed tokens. while(c--) traverses entries in descending order; split('|') separates dictionary entries; RegExp with a word boundary matches tokens; g selects global replacement; return p returns the reconstructed source. The outer eval executes that source.

Original independent dictionary exercise:
~~~text
2 0(){1("Demo");}
~~~
Dictionary: 0 = greet, 1 = console.log, 2 = function.
Recovered source:
~~~javascript
function greet(){console.log("Demo");}
~~~
Calling greet would print Demo; the declaration alone does not print. This is manual substitution, not actual packer output.

The learner-supplied lesson contains a demonstration flag-like string; it is intentionally omitted, and it is not assumed to be the answer from the current assigned lab. The non-secret portion of the reconstructed function shown in HTB is:
~~~javascript
function generateSerial() {
    // assessment-like data omitted
    var xhr = new XMLHttpRequest;
    var url = "/serial.php";
    xhr.open("POST", url, true);
    xhr.send(null);
}
~~~
XMLHttpRequest is a browser API for HTTP requests. new creates an instance; open sets method POST, URL and asynchronous mode (true); send(null) sends without an explicit body. Defining the function does not call it. This lesson requires recovering a flag from source, not triggering the endpoint. The supplied code is a demonstration, not observed output from the active target.

HTB mentions changing the outer eval to console.log to print the wrapper's returned source. This still executes the wrapper and is not a safe general sandbox; unknown code may perform side effects before returning. Prefer manual dictionary reconstruction or a verified static parser, and do not execute unknown scripts in the normal browser or host.

## 6. Reverse engineering

Automated tools can recognize known formats but may fail on custom or layered transformations. Preserve the original, identify structure, format a copy, locate encoded values, trace data and control flow, reconstruct meaningful behavior, and document evidence/uncertainty. The supplied lesson refers to HTB Secure Coding 101 for further study; do not start another module now. No optional lab required.

## 7. Project applications

UniTrack: formatting aids QR and visitor UI code review, but validation, roles and access records must be enforced by Flask. ProTrack-MX: readable source and consistent formatting aid React/Vite maintenance; Supabase RLS and backend authorization protect grades, messages and records. Browser source must not contain secrets. These are architectural recommendations, not findings from an audit.

## 8. Mandatory exercise methodology

Question: recover the flag by deobfuscating secret.js from the assigned HTB lab. Start by retrieving the actual resource as text:
~~~bash
curl -fsS 'http://SERVER_IP:PORT/secret.js'
~~~
-f fails on HTTP error statuses, -s suppresses progress and ordinary error messages, -S restores errors with -s. Replace placeholder with the active assigned host and port. cURL retrieves response text and does not execute JavaScript. Inspect the returned wrapper, payload and dictionary; reconstruct the relevant string using evidence. Do not assume the lesson demonstration flag is the active lab answer. Do not send a POST to the demonstrated endpoint because the question does not require it. If curl fails, verify the lab timer, exact address and authorized connectivity. The actual lab response and static reconstruction are now verified from learner-supplied terminal output; submission acceptance is not yet verified. Public notes omit the target address and assessment answer.

## References

- https://academy.hackthebox.com/course/preview/javascript-deobfuscation
- https://prettier.io/docs/install
- https://prettier.io/docs/cli
- https://github.com/beautifier/js-beautify
- https://matthewfl.com/unPacker.html
- https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/eval

Learner advanced to Section 6; Section 5 completion not separately confirmed. Next action: learner submits the reconstructed flag and confirms acceptance.

## 9. Verified practical exercise evidence

The learner supplied the actual terminal response from the assigned secret.js resource after retrieving it with cURL in Ubuntu WSL2. It contained a dictionary-based wrapper with 17 entries. The assessment-specific string is deliberately redacted from public documentation; no target address is published.

Observed packed program structure (token representation):

```text
g 4(){0 5="6{7!}";0 1=8 a();0 2="/9.c";1.d("e",2,f);1.b(3)}
```

The dictionary is split on the pipe character using split('|'). Indices are zero-based and the wrapper uses toString(36) for the tokens. The observed mapping, sanitized:

| Decimal index | Base-36 token | Dictionary value |
| --- | --- | --- |
| 0 | 0 | var |
| 1 | 1 | xhr |
| 2 | 2 | url |
| 3 | 3 | null |
| 4 | 4 | generateSerial |
| 5 | 5 | flag |
| 6 | 6 | HTB |
| 7 | 7 | [ASSESSMENT VALUE REDACTED] |
| 8 | 8 | new |
| 9 | 9 | serial |
| 10 | a | XMLHttpRequest |
| 11 | b | send |
| 12 | c | php |
| 13 | d | open |
| 14 | e | POST |
| 15 | f | true |
| 16 | g | function |

The packed declaration uses tokens 0, 5, 6 and 7. The punctuation in the packed text is literal: braces and the exclamation mark must be retained when extracting the actual value. The dictionary entry at index 7 is leetspeak (digits substitute for letters); its precise text and decoded message are excluded from the public repository. Static substitution of the observed tokens established the exact flag in the private study conversation. No wrapper execution or online submission of code was required.

Reconstructed function, with assessment value redacted:

```javascript
function generateSerial() {
    var flag = "[ASSESSMENT FLAG REDACTED]";
    var xhr = new XMLHttpRequest();
    var url = "/serial.php";
    xhr.open("POST", url, true);
    xhr.send(null);
}
```

The original declaration uses var; the reconstruction adds formatting and equivalent constructor parentheses. The function prepares an asynchronous POST request to /serial.php without an explicit body, if called. No POST request was necessary or performed for this assessment. A declaration does not execute its function body.

Security conclusion: client-delivered obfuscated JavaScript remains inspectable. Do not store actual secrets in it; enforce privileged operations on the server. The flag is intentionally included for this training exercise. Source retrieval and manual reconstruction are verified from the supplied evidence; acceptance, reward and platform completion remain pending.
