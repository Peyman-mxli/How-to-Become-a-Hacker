# Section 10 — Skills Assessment

**Module:** JavaScript Deobfuscation | **Section:** 10/11

**Status:** Assessment in progress. Five required questions; Questions 1–3 answers identified from learner terminal evidence and static reconstruction; submission acceptance pending. The previous Section 9 flag was recovered from learner terminal evidence; separate platform acceptance not supplied.

## Objective

Apply the module's source inspection, JavaScript analysis, deobfuscation, HTTP request and decoding concepts to an explicitly assigned training application. Distinguish observed behavior from hypotheses. The supplied assessment offers five questions worth 30 points each and a displayed section reward of 15; earned rewards are not confirmed.

## General methodology

| Stage | Skill | Evidence to collect |
| --- | --- | --- |
| 1 | Inspect HTML source | External script reference |
| 2 | Understand JavaScript behavior | Output or functionality, after safe analysis |
| 3 | Deobfuscation | Reconstructed variable |
| 4 | HTTP request analysis | Method, path, parameters and observed response |
| 5 | Encoding identification and request reproduction | Decoded data and final response |

Retrieve only the assigned page and inspect it as text. Example with a placeholder:
~~~bash
curl -fsS 'http://SERVER_IP:PORT/'
~~~
-f reports HTTP error status as failure, -s suppresses progress and -S restores error messages. cURL does not execute JavaScript. Inspect script elements and their src attributes. Resolve relative paths against the document base URL. For illustration, an independent HTML page might contain:
~~~html
<script src="example.js"></script>
~~~
This is not the assessment filename. Use the actual observed resource, not guesses.

Before considering execution, inspect the script as text and identify side effects. Do not execute unknown code in a normal browser or host. Use controlled analysis for trusted demonstrations and follow the assigned lab scope. Preserve original evidence; use copies for formatting. Recover data through static analysis where possible. Do not mistake a candidate encoding or an HTTP response for a confirmed vulnerability. Avoid unnecessary state-changing requests.

## References to previous study

- [Source Code](02-Source-Code.md)
- [Deobfuscation](06-Deobfuscation.md)
- [Code Analysis](07-Code-Analysis.md)
- [HTTP Requests](08-HTTP-Requests.md)
- [Decoding](09-Decoding.md)
- [cURL guide](Tools/15-cURL.md)
- [CyberChef guide](Tools/21-CyberChef.md)

## Assessment checkpoint

Question 1: actual HTML retrieved by learner using cURL, and the external JavaScript filename identified from the script src attribute. Exact filename redacted. Submission acceptance not separately confirmed. Questions 2–3: learner retrieved the script as text. Static dictionary substitution and literal concatenation recovered its console output and a function-local variable independently; empty dictionary entries preserve their tokens. No original wrapper execution was necessary. Question 4 next: analyze the recovered request and obtain the response from the assigned lab. Questions 4–5 pending. Public documentation records general methodology and progress only; no private lab addresses, actual filenames, flags, secret keys or assessment-specific answer sequence will be published. No execution or platform acceptance claimed.
