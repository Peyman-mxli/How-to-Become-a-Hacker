# 06 — JavaScript Deobfuscation

**Provider:** Hack The Box Academy | **Difficulty:** Easy
**Started:** November 26, 2026 (learner local date)
**Status:** In progress — 9/11 full lesson notes; 0/11 platform sections confirmed in this module.

Recommended prerequisite: Web Requests (completed).

## Lessons

1. [Introduction](01-Introduction.md) — theory documented and expanded from the learner-supplied HTB Section 1; no mandatory question; platform completion pending.
2. [Source Code](02-Source-Code.md) — theory documented; mandatory flag retrieval verified from learner terminal output; submission acceptance pending.
3. [Code Obfuscation](03-Code-Obfuscation.md) — theory documented; no mandatory question in supplied text.
4. [Basic Obfuscation](04-Basic-Obfuscation.md) — theory, supplied examples and static reconstruction documented; no mandatory question.
5. [Advanced Obfuscation](05-Advanced-Obfuscation.md) — theory and static decoding of supplied benign strings verified; no mandatory question in supplied text.
6. [Deobfuscation](06-Deobfuscation.md) — theory and tool guides documented; mandatory flag reconstruction verified from learner terminal output; submission acceptance pending.
7. [Code Analysis](07-Code-Analysis.md) — static analysis, XMLHttpRequest and HTTP request behavior documented; no mandatory question.
8. [HTTP Requests](08-HTTP-Requests.md) — theory and cURL reference documented; mandatory POST response verified from learner terminal output; submission acceptance pending.
9. [Decoding](09-Decoding.md) — theory and strict Base64 decoding verified; final POST flag verified from learner terminal output; submission acceptance pending.
10. Skills Assessment — pending.
11. Summary — pending.

## Supplemental guides

- [Browser source inspection, DevTools and cURL](Tools/01-Browser-Source-Inspection.md) — setup, architecture, commands, examples, troubleshooting and defensive practices.

- [JavaScript Obfuscator reference](Tools/02-JavaScript-Obfuscator.md) — official download links, Windows/WSL2 installation, architecture, harmless example and troubleshooting.
- [BeautifyTools JavaScript Obfuscator](Tools/03-BeautifyTools-JavaScript-Obfuscator.md) — official online access, controls, example and troubleshooting; no standalone installer verified.

- [JSConsole](Tools/04-JSConsole.md) — official access, REPL, Node alternative and troubleshooting.
- [JavaScript Minifier](Tools/05-JavaScript-Minifier.md) — current Toptal redirect, usage and privacy.
- [Terser](Tools/06-Terser.md) — supplemental local minification, Windows/WSL2 setup and examples.

- [Obfuscator.io](Tools/07-Obfuscator-IO.md) — official access, string array settings and analysis.
- [JSFuck](Tools/08-JSFuck.md) — source downloads and harmless coercion examples.
- [JJEncode](Tools/09-JJEncode.md) — historical symbol-based encoding and author reference.
- [AAEncode](Tools/10-AAEncode.md) — historical emoticon-style encoding and author reference.

- [Prettier](Tools/11-Prettier.md) — official downloads, VS Code extension, npm and formatting examples.
- [JavaScript Beautifier](Tools/12-JavaScript-Beautifier.md) — online and local formatting, recognized unpacking formats.
- [UnPacker](Tools/13-UnPacker.md) — historical tool, manual dictionary example and safe analysis.

- [XMLHttpRequest (XHR)](Tools/14-XMLHttpRequest.md) — browser API, official references, local example, lifecycle, configuration and troubleshooting.

- [cURL](Tools/15-cURL.md) — official downloads, Windows/WSL2 installation, HTTP methods, local Flask demo, examples and troubleshooting.

- [Base64](Tools/16-Base64.md) — GNU utility and exact byte conversion.
- [xxd](Tools/17-xxd.md) — hexadecimal dumps and reversal.
- [GNU tr / ROT13](Tools/18-tr-ROT13.md) — character translation.
- [ROT13 online](Tools/19-ROT13-Online.md) — browser reference and local alternative.
- [Cipher Identifier](Tools/20-Cipher-Identifier.md) — candidate recognition and limitations.
- [CyberChef](Tools/21-CyberChef.md) — supplemental open-source decoder.

## Learning objectives

Locate JavaScript in web applications, distinguish obfuscation from encoding, encryption and minification, reconstruct readable behavior, analyze code safely, decode data and understand related HTTP requests. Apply defensive findings to owned or expressly authorized systems.

## Current checkpoint

Section 1 introduction documented with original examples, analysis terminology, static-first methodology, existing free tools and defensive recommendations for UniTrack and ProTrack-MX. No lab or new installation performed. Learner supplied the actual Section 1 text and +15 reward indicator; no mandatory question or exercise is present. The reward is not yet verified as earned. Learner advanced to Section 2; Section 1 completion not separately confirmed. Section 2 theory documented, including HTML, CSS, JavaScript, source inspection, sensitive comments and original examples. Mandatory flag retrieved from an HTML comment in the assigned page, as verified in learner terminal output. Sanitized extraction methodology documented; submission acceptance not separately confirmed. Learner advanced to Section 3. Section 3 theory documented, including obfuscator architecture, dictionary representations, runtime compilation, IDS/IPS, security limitations and a supplemental local tool reference. No mandatory question or tool execution in supplied section. Learner advanced to Section 4; Section 3 completion not separately confirmed. Section 4 theory, manual analysis of the supplied benign packed example, minification and three individual tool guides documented. No mandatory question, tool installation or live execution verified. Learner advanced to Section 5; Section 4 completion not separately confirmed. Section 5 theory, static decoding of both supplied benign strings, coercion and four individual tool guides documented. Truncated JSF sample not fully validated. No mandatory question or learner-side tool execution verified. Learner advanced to Section 6; Section 5 completion not separately confirmed. Section 6 theory, manual unpacking, XMLHttpRequest and three individual tool guides documented. Mandatory flag reconstructed from actual secret.js response and dictionary, as verified from learner terminal output. Sanitized methodology documented; no wrapper execution or POST request performed. Submission acceptance not separately confirmed; learner advanced to Section 7. Section 7 theory and individual XMLHttpRequest guide documented. The supplied function describes an asynchronous POST without explicit body; server functionality and vulnerability status remain unverified. No mandatory question or live POST request performed. Learner advanced to Section 8; Section 7 completion not separately confirmed. Section 8 theory, empty POST versus POST data and a dedicated cURL guide documented. Mandatory POST response from /serial.php verified from learner terminal output. A 32-character Base64-like response was received; exact value redacted. Submission acceptance pending; no HTTP status captured or vulnerability established. Learner advanced to Section 9 Decoding; separate Section 8 acceptance not supplied. Section 9 theory and individual Base64, xxd, tr, ROT13, Cipher Identifier and supplemental CyberChef guides documented. Strict Base64 decoding of the observed Section 8 response verified; exact values redacted. Mandatory POST with decoded serial performed by learner; returned assessment flag verified from terminal evidence. Exact values redacted. Submission acceptance pending. Next: Section 10 Skills Assessment after submission. Across the portfolio: five completed modules, 83 confirmed platform sections, 90 full lesson notes; two prior partial lessons remain documentation gaps.

Follow the [study workflow](../STUDY-WORKFLOW.md). Supplements will be linked as relevant technologies are introduced. Do not publish private lab identifiers, credentials or restricted answers.

Source: [Official HTB module preview](https://academy.hackthebox.com/course/preview/javascript-deobfuscation).
