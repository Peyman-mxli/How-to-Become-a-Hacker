# Obfuscator.io — Browser Tool and String Array Analysis

## Definition and architecture

Obfuscator.io offers a hosted JavaScript transformation interface, with free standard obfuscation and separate commercial offerings. The related open-source javascript-obfuscator package supports local source transformation. The basic architecture is source parsing, syntax-tree transformations, and output generation. String-array representations may include an accessor and runtime decoder. The current hosted interface differs from HTB's historical screenshot.

## Official access and downloads

- https://obfuscator.io/
- https://obfuscator.io/docs/options
- https://obfuscator.io/docs/basic-obfuscation/string-array
- https://github.com/javascript-obfuscator/javascript-obfuscator/releases
- https://www.npmjs.com/package/javascript-obfuscator
- https://nodejs.org/en/download/

The hosted tool needs a browser and no standalone installer. The local package is free; follow the existing [local installation guide](02-JavaScript-Obfuscator.md) for Windows/WSL2, Node/npm, CLI basics and troubleshooting. No new installation is necessary. The vendor describes standard browser processing as local, but this has not been independently audited; never submit private code or secrets to an online service.

## Settings and example

Compact controls formatting; String Array moves string literals into an array; String Array Encoding changes their representation; source maps link generated to original locations; identifier transformations change names. Base64 is reversible, not encryption. HTB's supplied benign example uses the Base64 setting and this source:

~~~javascript
console.log('HTB JavaScript Deobfuscation Module');
~~~

Expected output: HTB JavaScript Deobfuscation Module. The supplied result contains a string array and runtime decoder. See the [numbered lesson](../05-Advanced-Obfuscation.md) for verified static analysis. The current tool output may differ by version; no hosted transformation was performed for this guide.

Original harmless data representation:

~~~javascript
const words = ["bG9n", "SGVsbG8="];
~~~

These are standard Base64 strings for log and Hello. This array is data, not a complete program. Base64 is not a confidentiality control. Keep original code and test generated assets; do not execute unknown generated scripts.

## Troubleshooting

If the UI differs from HTB screenshots, consult the official options reference. If hosted access fails, use the local package for ordinary owned code. For a local npm failure, inspect Node version, network access and installation errors. If output behaves differently, preserve the source and do not deploy it. Measure performance and bundle size. Source maps are useful for debugging but may expose original source.

## Security and project application

UniTrack and ProTrack-MX should keep secrets and authoritative access checks server-side; browser code remains inspectable. Do not depend on obfuscation for confidentiality or access control. This guide covers the supplied demonstration and defensive analysis, not detection-evasion configurations. No tool execution or additional HTB completion claimed.
