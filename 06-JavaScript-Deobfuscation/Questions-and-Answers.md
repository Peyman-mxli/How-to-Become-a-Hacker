# Module 06 — Questions and Answers

**Module:** JavaScript Deobfuscation | **Provider:** HTB Academy | **Tier:** 0 (free)

This reference records the nine mandatory questions encountered by the learner, in paraphrased form, with the corresponding answers and explanations from the study session. Read the linked lessons before consulting the answers. Values describe the observed training instance; future instances may differ. Temporary target addresses and credentials are not included.

HTB's [publication guidelines](https://help.hackthebox.com/en/articles/5188925-streaming-writeups-walkthrough-guidelines) explicitly permit publication of Tier 0 module solutions. The [official certification curriculum](https://academy.hackthebox.com/preview/certifications/htb-certified-web-exploitation-specialist) identifies JavaScript Deobfuscation as Tier 0, and the [Academy FAQ](https://academy.hackthebox.com/faq) identifies Tier 0 as free. This permission is specific to eligible content; do not generalize it to paid modules or certification exams.

## Section 2 — Source Code

**Question 1:** Find the flag hidden in the webpage source.

**Answer:**
```text
HTB{4lw4y5_r34d_7h3_50urc3}
```

**Explanation:** The flag was observed in an HTML comment in the response retrieved with cURL. HTML comments are not rendered as ordinary visible page content but are accessible to the client. See [Source Code](02-Source-Code.md).

## Section 6 — Deobfuscation

**Question 1:** Deobfuscate the provided JavaScript and identify the flag.

**Answer:**
```text
HTB{1_4m_7h3_53r14l_g3n3r470r!}
```

**Explanation:** Static substitution of the packing dictionary reconstructed the flag without executing the original wrapper. See [Deobfuscation](06-Deobfuscation.md).

## Section 8 — HTTP Requests

**Question 1:** Reproduce the HTTP request described by the JavaScript and report the response.

**Answer:**
```text
N2gxNV8xNV9hX3MzY3IzN19tMzU1NGcz
```

**Explanation:** The learner observed this response in the terminal. The requested answer is the original response, not its decoded representation. See [HTTP Requests](08-HTTP-Requests.md).

## Section 9 — Decoding

**Question 1:** Decode the previous response and follow the exercise's request instructions to recover the flag.

**Answer:**
```text
HTB{ju57_4n07h3r_r4nd0m_53r14l}
```

**Explanation:** The earlier Base64 response represents the ASCII string `7h15_15_a_s3cr37_m3554g3`. The final flag was observed in the learner's terminal. See [Decoding](09-Decoding.md).

## Section 10 — Skills Assessment

### Question 1 — JavaScript filename

**Question:** Identify the JavaScript resource referenced by the assigned webpage.

**Answer:**
```text
api.min.js
```

**Explanation:** The filename was found in the HTML script element's src attribute.

### Question 2 — JavaScript output

**Question:** Determine the output produced by the JavaScript code.

**Answer:**
```text
HTB{j4v45cr1p7_3num3r4710n_15_k3y}
```

**Explanation:** Static analysis reconstructed the concatenated string passed to the console output function. Executing unknown code was not necessary.

### Question 3 — Reconstructed flag variable

**Question:** Recover the flag stored in the deobfuscated JavaScript variable.

**Answer:**
```text
HTB{n3v3r_run_0bfu5c473d_c0d3!}
```

**Explanation:** The function-local variable and console output are different strings. Static reconstruction recovered the variable value. The initial submission of a hexadecimal response was rejected; the corrected flag was accepted, as confirmed by the learner.

### Question 4 — HTTP response

**Question:** Reproduce the request identified in the script and report the original response.

**Answer:**
```text
4150495f70336e5f37333537316e365f31355f66756e
```

**Explanation:** The response is a 44-character hexadecimal representation of 22 bytes. The original response, not its decoded text, was accepted by HTB (learner confirmed).

### Question 5 — Final flag

**Question:** Decode the prior response and follow the assigned request instructions to obtain the final flag.

**Answer:**
```text
HTB{r34dy_70_h4ck_my_w4y_1n_2_HTB}
```

**Explanation:** Hexadecimal decoding reconstructs `API_p3n_73571n6_15_fun`. The final flag was observed in the learner's terminal output. The trailing shell prompt was not part of the answer. Question 5 submission acceptance was not separately supplied before the learner advanced to Section 11.

See [Skills Assessment](10-Skills-Assessment.md) for the study methodology.

## Evidence and status

All nine answers were recovered from the study record. Questions are paraphrased rather than represented as verbatim HTB text. Section 10 Questions 3 and 4 were explicitly confirmed accepted; other individual submissions were not separately confirmed. The learner reached Section 11 Summary, but the module completion screen has not yet been supplied. No private lab address, cookie or account credential is included.

Sections 1, 3, 4, 5, 7 and 11 had no mandatory questions in the supplied lessons.
