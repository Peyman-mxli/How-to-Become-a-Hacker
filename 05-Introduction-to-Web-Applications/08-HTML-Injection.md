# Lesson 08/17 — HTML Injection

## Learning objectives
Understand HTML injection, identify unsafe rendering sinks, distinguish transient and stored injection, predict rendered output, and apply context-appropriate defenses.

## What is HTML injection?
HTML injection occurs when untrusted input is interpreted as HTML markup rather than displayed as data. A vulnerable interface can allow unintended changes to the document structure, text formatting, links, styles or forms. Potential consequences include misleading page content, defacement and harm to user trust. Injected active content may also lead to cross-site scripting (XSS), depending on the rendering context and browser controls. HTML injection alone does not necessarily establish script execution.

## Sources, sinks and rendering
A source is where untrusted data enters an application, such as a prompt, form, URL or previously stored comment. A sink is an operation that renders or otherwise processes the data. Assignment to innerHTML is an HTML-parsing sink. Concatenation does not escape or sanitize input.

An original local-only demonstration, using harmless formatting:

~~~html
<label for="name">Display name</label>
<input id="name">
<button id="show" type="button">Display</button>
<p id="output"></p>
<script>
  document.getElementById("show").addEventListener("click", () => {
    const name = document.getElementById("name").value;
    document.getElementById("output").innerHTML = "Welcome, " + name;
  });
</script>
~~~

Illustrative input: `<strong>Demo Student</strong>`.

Expected rendering: Welcome, followed by bold Demo Student. The browser creates a strong element rather than showing literal angle brackets. This example is deliberately unsafe to illustrate the flaw in an owned local file. Do not deploy it. No execution was performed.

## Transient and stored HTML injection
| Type | Explanation |
| --- | --- |
| Client-side/DOM-based | Browser code directly inserts untrusted data as HTML; no server persistence is necessary. |
| Reflected | A server returns user-controlled markup in an immediate response. |
| Stored | Submitted content is persisted and subsequently rendered unsafely to one or more users. |

The lesson's prompt example is a transient DOM-based case; reloading its page resets the modified DOM. That is not a universal property of HTML injection: stored content may persist.

## Impact and scope
Unintended markup can misrepresent application content, add misleading links, alter layout, or introduce deceptive forms. These risks are assessed only within owned or explicitly authorized environments. The demonstration does not require collecting credentials, interacting with real users, or sending information to external services. Use harmless formatting and synthetic data as proof.

## Defenses
Input validation checks whether submitted data satisfies business rules. Output encoding ensures data is interpreted as data in its destination context. Sanitization removes disallowed markup when rich HTML is intentionally supported. All are useful, but validation alone is not a reliable substitute for safe rendering.

Correct the example by assigning plain text instead:

~~~javascript
document.getElementById("output").textContent = "Welcome, " + name;
~~~

Expected result for the same input: the literal characters <strong>Demo Student</strong> are displayed instead of a bold element.

| Defense | Purpose |
| --- | --- |
| textContent | Insert untrusted plain text without parsing markup |
| Context-appropriate output encoding | Prevent data from becoming interpreted syntax |
| Maintained HTML sanitizer | Allow approved rich-text elements when necessary |
| Server-side validation | Enforce types, lengths and business rules |
| Client-side validation | Improve input feedback and constrain client-only flows |
| Content Security Policy | Reduce certain consequences as defense in depth |

Do not use a home-grown regular expression as a general HTML sanitizer. A sanitizer must be appropriate to the target context and maintained. CSP does not repair an unsafe sink. Avoid unnecessarily using innerHTML, outerHTML and document.write with untrusted data.

## Original example: safe student comments
A hypothetical school dashboard should render a student's plain-text comment using textContent or the framework's ordinary escaped interpolation. If rich text is a business requirement, apply an appropriate maintained sanitizer and server-side policies, and verify that allowed formatting cannot introduce executable or misleading content. This is a design example, not a verified finding in UniTrack or ProTrack-MX.

## Mandatory question reasoning
The learner's supplied code concatenates a fixed text prefix with user-controlled HTML before assigning it to innerHTML. To predict the rendered output, identify the fixed prefix, parse the supplied anchor element, and distinguish its href attribute from its visible link label. The rendered sentence consists of the prefix and the label; the destination URL is not ordinary displayed text. This answer was derived from the supplied source, not from a live browser test. The exact restricted exercise answer is not reproduced here.

## Verification
Section 8 theory and question reasoning documented. Platform answer acceptance pending. No target spawned or browser test performed in this session for Section 8. Section 7 practical evidence was previously verified; the learner advanced to Section 8, but individual Section 7 acceptance was not separately shown. Full Sections 4–5 remain awaiting source material.

## References
- [HTB Academy](https://academy.hackthebox.com/app/module/75)
- [MDN — Element.innerHTML](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML)
- [MDN — Node.textContent](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent)
- [OWASP — Cross Site Scripting Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
- [OWASP — DOM based XSS Prevention](https://cheatsheetseries.owasp.org/cheatsheets/DOM_based_XSS_Prevention_Cheat_Sheet.html)
- [OWASP — Input Validation](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html)

Independent English notes with original examples, technical clarifications and sanitized assessment reasoning.
