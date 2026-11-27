# JSFuck (JSF) — JavaScript Type Coercion

## Definition and architecture

JSFuck is an educational esoteric JavaScript style using six characters: []()!+. It relies on JavaScript type conversion, indexing and expression semantics. The output can be much larger than ordinary source, and evaluation can be expensive. It is not encryption or a security boundary.

## Official access and download

- https://jsfuck.com/
- https://github.com/aemkei/jsfuck
- https://www.npmjs.com/package/jsfuck

The hosted demo requires no installation. Download the upstream repository via GitHub Code > Download ZIP or clone its source for inspection:

~~~bash
git clone https://github.com/aemkei/jsfuck.git
~~~

git clone copies the repository to a local directory; Git downloads: https://git-scm.com/downloads. Node.js downloads for trusted local expressions: https://nodejs.org/en/download/. Do not execute unknown encoder output; no installation required for the lesson.

## Original harmless examples

| Expression | Result |
| --- | --- |
| +[] | 0 |
| ![] | false |
| !![] | true |
| +!![] | 1 |
| ![]+[] | "false" |
| !![]+[] | "true" |
| [][[]] | undefined |
| [][[]]+[] | "undefined" |

The expression (![]+[])[+[]] yields f: negation of a truthy array gives false, concatenation with an empty array produces the string false, and numeric conversion of the empty array gives index zero. The expression (!![]+[])[+[]] similarly yields t. These are bounded language examples, not executable payload encodings.

For a trusted local demonstration, open Node using node, type a simple expression above, read its result, and exit with .exit. The HTB long sample is truncated and cannot be fully validated; the lesson reports the full code prints the original message. No whole-script execution or generated output claimed.

## Troubleshooting and defensive application

If a full sample contains ...SNIP..., do not attempt to execute it; obtain an authorized complete sample for static analysis. Distinguish strings, Booleans, numbers and arrays; JavaScript conversion depends on operator and operand types. Symbol-heavy code is not by itself proof of maliciousness. Use static analysis and correlate with other evidence. Do not use these techniques to evade filters or security monitoring. UniTrack and ProTrack-MX should use safe DOM operations and backend authorization, not keyword blacklists.

References: https://jsfuck.com/ ; https://github.com/aemkei/jsfuck ; https://developer.mozilla.org/en-US/docs/Glossary/Type_coercion
