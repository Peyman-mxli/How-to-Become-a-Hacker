# Section 04 — Basic Obfuscation

**Module:** JavaScript Deobfuscation | **Section:** 4/11 | **Date:** November 26, 2026

**Status:** Theory and supplied demonstrations documented. No mandatory question in the learner-supplied lesson. +15 reward displayed; earned points and platform completion not verified. Original examples are illustrative; no learner-side execution or installation claimed.

## 1. Running JavaScript

The supplied HTB example is:

~~~javascript
console.log('HTB JavaScript Deobfuscation Module');
~~~

Expected output: HTB JavaScript Deobfuscation Module.

console is the logging/debugging object; log() is its output method; the single-quoted text is a string literal; parentheses contain arguments and the semicolon terminates the statement. Developers use logging to inspect values and troubleshoot. A REPL is a Read-Evaluate-Print-Loop: accept input, evaluate, display the result, repeat.

HTB demonstrates JSConsole at https://jsconsole.com/. Its official open-source MIT repository is https://github.com/remy/jsconsole. The hosted site was reachable, but its interactive interface could not be inspected through the research view. Open it, paste the trusted example, press Enter, and inspect the displayed output. For local execution, run node in a terminal, enter the same expression and use .exit to leave. An additional undefined may appear because console.log returns undefined. See [JSConsole guide](Tools/04-JSConsole.md). Never execute unknown scripts.

## 2. JavaScript minification

Minification reduces source size by removing unnecessary whitespace, line breaks and comments, optionally simplifying expressions, shortening identifiers and removing unreachable code. A single long line is common, not the complete definition. Output should preserve intended behavior, subject to testing. Benefits include smaller downloads, potentially improved loading, lower bandwidth and compact production assets. Reduced readability is incidental rather than a confidentiality guarantee. HTML and CSS can also be minified. The .min.js suffix is a naming convention, not a different language.

Original illustrative example (ASCII, without trailing newline):

~~~javascript
// Calculate the total price
function calculateTotal(price, quantity) {
    const total = price * quantity;

    return total;
}

console.log(calculateTotal(50, 3));
~~~

Illustrative equivalent:

~~~javascript
function calculateTotal(t,n){return t*n}console.log(calculateTotal(50,3));
~~~

Both should print 150. Original length 165 bytes and compact length 74 bytes, a 55.2% reduction; the transformation is not observed tool output. Preserve readable source and test both forms.

## 3. Online minifier and local alternative

HTB's https://javascript-minifier.com/ redirects to https://www.toptal.com/developers/javascript-minifier. The current page shows an input area, Minify, Clear and output/copy controls. No standalone installer was verified. Paste a non-sensitive example, select Minify, copy the output and save it as greet.min.js if desired.

~~~javascript
function greet(name) {
    return "Welcome, " + name;
}

console.log(greet("Student"));
~~~

Illustrative minified equivalent:

~~~javascript
function greet(e){return"Welcome, "+e}console.log(greet("Student"));
~~~

Expected output: Welcome, Student. Run node greet.min.js only for your own inspected example. The website advertises a POST API accepting source as an input parameter; do not transmit private source or credentials. See [online JavaScript Minifier guide](Tools/05-JavaScript-Minifier.md).

Terser is an additional free open-source local alternative, not a tool named by HTB in this section. It parses, compresses and mangles JavaScript. In a separate directory, with Node/npm installed:

~~~powershell
cd $HOME
mkdir js-minification-lab
cd js-minification-lab
npm init -y
npm install --save-dev terser
npx terser --version
~~~

Save the greet example in app.js.

~~~powershell
npx terser app.js -c -m -o app.min.js
node app.js
node app.min.js
~~~

-c enables compression; -m enables identifier mangling; -o specifies the output path. Both files should print Welcome, Student. No execution is claimed. See [Terser guide](Tools/06-Terser.md).

## 4. Packing

The learner-supplied lesson uses BeautifyTools (already documented separately) to transform the benign log statement into a packed JavaScript expression. Packing stores an encoded program representation and reconstructs it at runtime, often using a substitution dictionary. The supplied example has an eval wrapper and function(p,a,c,k,e,d), a pattern commonly associated with dictionary-based JavaScript packers. The signature is a clue rather than conclusive proof of the exact tool. Custom tools may differ.

The supplied benign payload string is:

~~~text
5.4('3 2 1 0');
~~~

The dictionary from the supplied example is:

~~~text
Module|Deobfuscation|JavaScript|HTB|log|console
~~~

split('|') produces an array. Its zero-based indices resolve as follows:

| Index | Word |
| --- | --- |
| 0 | Module |
| 1 | Deobfuscation |
| 2 | JavaScript |
| 3 | HTB |
| 4 | log |
| 5 | console |

Manual static substitution yields:

~~~javascript
console.log('HTB JavaScript Deobfuscation Module');
~~~

The supplied wrapper takes p (packed text), a (radix/base parameter, 6 in the call but not used in the shown simplified function body), c (count, 6), k (dictionary), e (helper parameter reassigned to a token-mapping function) and d (lookup object used in a compatibility branch). These names are not a universal API. The while(c--) loop counts down; if(k[c]) checks whether an entry exists; replace and RegExp perform substitutions in the packed text. In the displayed compatibility branch the dictionary is adapted to a lookup callback. The outer eval executes the reconstructed string as code. The original text contains escaped quotes inside a string; the displayed payload above is its decoded string content. The backslash in a regular-expression string is escaped; the resulting word boundary matches token boundaries. The 'g' flag selects global matching. Do not run an unknown wrapper, since eval can execute arbitrary JavaScript. Static inspection of this supplied benign example is sufficient.

The lesson's screenshots show the intended same console message before and after packing. We did not run the supplied packer or reproduce its exact output. Unlike minification, packing can introduce a larger wrapper and runtime overhead. The recognizable words in the dictionary remain visible, demonstrating that packing is not encryption.

## 5. Original dictionary interpretation

~~~javascript
const words = ["Ready", "System"];
console.log(words[1] + " " + words[0]);
~~~

Expected output: System Ready. This is a harmless array lookup rather than an executable packer. Index 1 selects System and index 0 selects Ready. Inspect the lookup without running unfamiliar code.

## 6. Comparison and security

| Technique | Primary goal | Typical properties |
| --- | --- | --- |
| Minification | Reduce file size | Compact source, optional identifier shortening |
| Packing | Store/reconstruct code representation | Wrapper, runtime reconstruction, often dictionary |
| Obfuscation | Reduce readability | Various transformations |
| Encryption | Confidentiality | Cryptographic algorithm and key |

These are overlapping but distinct concepts. Formatting can make minified code easier to read but cannot necessarily restore original comments or names. Packing may leave visible strings; neither packing nor minification provides authorization or guarantees secrecy. An eval wrapper is a reason for caution, not proof of malware. CSP may restrict eval in browser applications; avoid unnecessary dynamic execution.

## 7. Project application

UniTrack (Flask): keep QR and visitor-pass validation, role checks and sensitive secrets on the server; use normal asset minification only for delivery optimization. ProTrack-MX (React/Vite/Supabase): production bundling and minification can improve delivery; browser-delivered assets remain inspectable. Keep service-role keys server-side, enforce RLS and backend authorization for student records, grades and messages, and deliberately manage source maps. Preserve original source and test generated assets. These are recommendations, not findings from a new audit.

## 8. References and checkpoint

- [HTB JavaScript Deobfuscation](https://academy.hackthebox.com/course/preview/javascript-deobfuscation)
- [JSConsole](https://jsconsole.com/)
- [JSConsole official repository](https://github.com/remy/jsconsole)
- [Toptal JavaScript Minifier](https://www.toptal.com/developers/javascript-minifier)
- [BeautifyTools guide](Tools/03-BeautifyTools-JavaScript-Obfuscator.md)
- [Terser CLI documentation](https://terser.org/docs/cli-usage/)
- [Node.js downloads](https://nodejs.org/en/download/)
- [MDN eval](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/eval)

No mandatory question appears in supplied Section 4. Learner advanced to Section 4, without separate confirmation of Section 3 completion. Next: Section 5 Advanced Obfuscation. No installation, live execution, or earned points verified.
