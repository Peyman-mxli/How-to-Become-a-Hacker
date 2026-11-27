# Section 05 — Advanced Obfuscation

**Module:** JavaScript Deobfuscation | **Section:** 5/11 | **Date:** November 26, 2026

**Status:** Theory and supplied benign string-array analysis documented. No mandatory question appears in the supplied lesson. Both encoded strings independently verified by static data decoding; the supplied script itself was not executed. The second example is truncated. No learner installation, execution, earned reward or platform completion claimed.

## 1. Basic and advanced representations

The previous lesson's dictionary retained readable strings, such as Module, Deobfuscation, JavaScript, HTB, log and console. More complex transformations store strings in encoded form and recover them at runtime. Obfuscation increases analysis effort rather than preventing analysis entirely.

Original illustrative code:

~~~javascript
console.log("Hello");
~~~

Basic representation:

~~~javascript
const words = ["log", "Hello"];
console[words[0]](words[1]);
~~~

Encoded data illustration:

~~~javascript
const words = ["bG9n", "SGVsbG8="];
~~~

The last snippet is only data, not a complete equivalent program.

## 2. Obfuscator.io

The official hosted site has free standard obfuscation and additional commercial features; its current UI differs from the old HTB screenshot. The related open-source local javascript-obfuscator package is free. Official links: https://obfuscator.io/ ; https://obfuscator.io/docs/options ; https://obfuscator.io/docs/basic-obfuscation/string-array ; https://github.com/javascript-obfuscator/javascript-obfuscator ; https://github.com/javascript-obfuscator/javascript-obfuscator/releases ; https://www.npmjs.com/package/javascript-obfuscator.

| Setting | Meaning |
| --- | --- |
| Compact | Compact formatting |
| String Array | Extract string literals into an array |
| String Array Encoding | Change representation of stored strings |
| Base64 | Reversible binary-to-text encoding |
| Source Map | Map generated locations to original source |
| Identifier transformation | Change variable/function names |

HTB's supplied demonstration selects Base64 string-array encoding for a harmless console log. We analyze the supplied output rather than configure stronger concealment or execute unknown scripts. See [Obfuscator.io guide](Tools/07-Obfuscator-IO.md) and the existing [local tool guide](Tools/02-JavaScript-Obfuscator.md).

## 3. Base64

Base64 represents binary data using printable characters; typical applications include email attachments, data URLs, HTTP Basic authentication representation and data exchange. It requires no secret key and provides no confidentiality.

| Original ASCII text | Standard Base64 |
| --- | --- |
| Hello | SGVsbG8= |
| World | V29ybGQ= |
| log | bG9n |
| HTB | SFRC |

The standard alphabet contains uppercase A-Z, lowercase a-z, digits 0-9, plus and slash. Padding uses equals. Base64 converts groups of three bytes into four six-bit character indices; the final group may use padding. The interactive chat illustration encoded ASCII strings locally; these examples preserve its substantive result. Do not embed secrets in encoded browser code.

## 4. Static analysis of the supplied example

The learner-supplied sample has an encoded array, rotation helper, accessor, decoder and cache followed by a console call. No whole script execution is required to interpret this benign example.

~~~javascript
var _0x1ec6 = [
    'Bg9N',
    'sfrciePHDMfty3jPChqGrgvVyMz1C2nHDgLVBIbnB2r1Bgu='
];
~~~

Identifiers such as _0x1ec6, _0x13249d, _0x14f83b and _0x14f8 are names, not hexadecimal numbers.

| Hex literal | Decimal |
| --- | --- |
| 0x0 | 0 |
| 0x1 | 1 |
| 0x4 | 4 |
| 0x10 | 16 |
| 0x40 | 64 |
| 0xb4 | 180 |
| 0xff | 255 |

### Rotation

array.push(array.shift()) removes the first entry and appends it at the end. In an original illustrative array [A,B,C,D], one rotation gives [B,C,D,A]; four restore the original order. In the supplied script, 0xb4 is 180; the pre-increment followed by a pre-decrement loop performs 180 rotations, which restores the order of the two entries.

### Custom Base64 alphabet and observed verification

Standard alphabet (equals is padding):
~~~text
ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=
~~~
Supplied decoder alphabet:
~~~text
abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789+/=
~~~

The two letter groups exchange positions. Static verification performed by the assistant on the two data strings only, using the equivalent standard alphabet after swapping letter case; no JavaScript was executed. Results:

| Supplied value | Verified text |
| --- | --- |
| Bg9N | log |
| sfrciePHDMfty3jPChqGrgvVyMz1C2nHDgLVBIbnB2r1Bgu= | HTB JavaScript Deobfuscation Module |

These are the supplied course demonstration strings, not an assessment flag or secret.

### Decoder and cache

| Operation | Purpose |
| --- | --- |
| charAt | Retrieve a character at an index |
| indexOf | Find value position |
| replace | Replace matching text |
| String.fromCharCode | Construct characters from UTF-16 code units |
| charCodeAt | Retrieve a UTF-16 code unit |
| toString(0x10) | Convert a number to hexadecimal text |
| slice(-0x2) | Extract final two characters |
| decodeURIComponent | Decode percent-encoded URI components |
| undefined | Undefined value |
| !![] | Boolean true from a truthy empty array |
| Cache object | Reuse previously decoded values |

The decoder uses character lookup and bitwise operations to reconstruct bytes, converts them to percent escapes, and decodes them as text. The final accessor call selects the method and message:

~~~javascript
console[_0x14f8('0x0')](_0x14f8('0x1'));
~~~

Recovered equivalent:
~~~javascript
console.log('HTB JavaScript Deobfuscation Module');
~~~

Expected output: HTB JavaScript Deobfuscation Module. This conclusion is supported by static data verification, not execution of the original wrapper.

## 5. JSFuck (JSF)

JSFuck is an esoteric educational JavaScript style using six characters: square brackets, parentheses, exclamation mark and plus: []()!+. It uses JavaScript coercion (conversion between types), array indexing and language semantics.

| Expression | Value | Type |
| --- | --- | --- |
| [] | empty array | object |
| +[] | 0 | number |
| ![] | false | boolean |
| !![] | true | boolean |
| +!![] | 1 | number |
| ![]+[] | "false" | string |
| !![]+[] | "true" | string |
| [][[]] | undefined | undefined |
| [][[]]+[] | "undefined" | string |

Harmless example: (![]+[])[+[]] gives f because an array is truthy, negation gives false, concatenation with an empty array gives "false", +[] gives 0, and indexing retrieves its first character. Official website https://jsfuck.com/ ; source https://github.com/aemkei/jsfuck ; npm https://www.npmjs.com/package/jsfuck. Source download through GitHub Code > Download ZIP or git clone https://github.com/aemkei/jsfuck.git. See [JSFuck guide](Tools/08-JSFuck.md).

The second supplied HTB sample contains ...SNIP... and cannot be fully reconstructed or validated as provided. The lesson reports the full example prints the original message. Do not execute unknown generated expressions. The form alone is not evidence of malicious intent.

## 6. JJEncode and AAEncode

Both are historical JavaScript encoding styles by Yosuke Hasegawa. JJEncode produces symbol-heavy expressions; AAEncode uses Japanese-style emoticon-like expressions. They exploit JavaScript syntax and type conversion; they do not provide encryption or authorization. The author explicitly cautions that JJEncode is recognizable, readily decoded and may have browser compatibility issues. Official original browser demos: https://utf-8.jp/public/jjencode.html and https://utf-8.jp/public/aaencode.html ; author index https://utf-8.jp/. No standalone installer verified. See individual [JJEncode](Tools/09-JJEncode.md) and [AAEncode](Tools/10-AAEncode.md) guides for source access, recognition, examples and limitations.

## 7. Performance and security

Complex representations can expand bundle size, add decoder work and parsing overhead, increase memory use and reduce maintainability. Cache may avoid repeated work but does not eliminate all overhead. No measured slowdown is claimed. The supplied material mentions use against filters; defensive implication: simple keyword blocking alone is inadequate. Use context-aware output encoding, safe DOM APIs, appropriate CSP, backend authorization and evidence-based monitoring; do not attempt to bypass restrictions. Never execute unknown scripts on a normal host or browser console.

## 8. Project applications

UniTrack: browser QR and visitor UI may be inspectable; server must validate QR, expiration, identity and roles. ProTrack-MX: Vite client variables and bundles are inspectable; privileged Supabase keys must stay server-side, with RLS and backend authorization for grades, private messages and student records. Ordinary minification may aid delivery; aggressive obfuscation is not a security boundary and may harm performance. Preserve source and test builds. These are architectural recommendations, not audit findings.

## 9. References and checkpoint

- [HTB JavaScript Deobfuscation](https://academy.hackthebox.com/course/preview/javascript-deobfuscation)
- [Obfuscator.io options](https://obfuscator.io/docs/options)
- [String array documentation](https://obfuscator.io/docs/basic-obfuscation/string-array)
- [JSFuck source](https://github.com/aemkei/jsfuck)
- [JJEncode original](https://utf-8.jp/public/jjencode.html)
- [AAEncode original](https://utf-8.jp/public/aaencode.html)
- [MDN type coercion](https://developer.mozilla.org/en-US/docs/Glossary/Type_coercion)
- [MDN Base64](https://developer.mozilla.org/en-US/docs/Glossary/Base64)

Learner advanced to Section 5; Section 4 completion not separately confirmed. No mandatory question in supplied Section 5. Next: Section 6 Deobfuscation. No new platform section credit claimed.
