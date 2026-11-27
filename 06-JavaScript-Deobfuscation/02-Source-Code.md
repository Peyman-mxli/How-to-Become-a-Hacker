# Section 02 — Source Code

**Module:** JavaScript Deobfuscation | **Section:** 2/11 | **Date:** November 26, 2026

**Status:** Theory documented from the supplied HTB lesson. One mandatory flag question; assigned target exists but response and flag have not yet been supplied. Section reward +15 and question reward +30 are displayed, not verified as earned. All examples are independently written and illustrative.

## 1. Frontend architecture

HTML (HyperText Markup Language, .html) defines page structure, headings, paragraphs, buttons and forms. CSS (Cascading Style Sheets, .css) controls appearance, colors, fonts, spacing and layout. JavaScript (.js) implements calculations, events, interaction and network requests. A browser receives resources and renders the interface. The frontend is inspectable; server-side source such as Flask/Python, PHP, Node backend logic, database queries and private environment variables is not normally delivered. Never treat data sent to a browser as secret.

## 2. HTML structure
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Serial Generator</title>
</head>
<body>
    <h1>Serial Generator</h1>
    <p>Generate your serial number.</p>
    <button>Generate</button>
</body>
</html>
```

| Element | Purpose |
| --- | --- |
| DOCTYPE html | Declares modern HTML |
| html | Root element |
| lang="en" | Declares English document language |
| head | Metadata and resource references |
| title | Browser tab title |
| body | Page content |
| h1 | Main heading |
| p | Paragraph |
| button | Interactive button |

A button does not automatically generate serial numbers; logic or form submission is required.

## 3. Source inspection methods

- Ctrl+U opens View Page Source in Chrome or Firefox, revealing original HTML rather than just the rendered page.
- F12 opens developer tools; Chrome Elements or Firefox Inspector displays the live DOM, including modifications made by JavaScript.
- Enter `view-source:http://127.0.0.1:5000/` in a supported browser for a local page source view.
- Run `curl http://127.0.0.1:5000/` in Ubuntu WSL2 to retrieve the HTTP response body without executing JavaScript. A local server must be running.

| Capability | View Source | DevTools | cURL |
| --- | --- | --- | --- |
| Original HTML | Yes | Not necessarily | Yes, if returned by server |
| Live DOM | No | Yes | No |
| JavaScript execution | No in source view | Available in page context | No |
| Network monitoring | No | Yes | Individual requests |
| HTTP response headers | No | Yes | With options |

The DOM (Document Object Model) is the browser's structured representation of a document; it can differ from original HTML due to parsing and script changes. DevTools Sources (Chrome) or Debugger (Firefox) shows script resources. Network shows requested URLs, response codes, headers and response bodies. Use source/text inspection for unknown scripts rather than executing them.

## 4. HTML comments and sensitive data exposure

HTML comments use `<!-- comment -->`. They are not normally rendered, but are visible to recipients of HTML. Original example:
```html
<h1>Administration Portal</h1>
<!-- Internal test credential: DEMO-ONLY -->
<p>Please sign in.</p>
```

The credential is fictional. Comments must not contain real passwords, tokens, private keys or sensitive internal details. This connects to Module 05 Section 7 Sensitive Data Exposure. Remove sensitive comments before deployment, keep credentials server-side, review production files, and revoke or rotate exposed secrets rather than merely deleting the comment.

## 5. CSS: internal and external styles

Internal CSS is placed in a style element; external CSS is loaded with a link element, normally in head. Inline CSS also exists via the style attribute.
```html
<style>
    *, html {
        margin: 0;
        padding: 0;
        border: 0;
    }
    h1 { font-size: 144px; }
    p { font-size: 64px; }
</style>
```

The universal selector * matches elements; html selects the root element. A comma groups selectors. The declaration block contains property:value pairs separated by semicolons. margin is outer spacing, padding inner spacing, border the element border. font-size sets text size; px is a CSS pixel unit. h1 and p are element selectors. The reset is a design choice, not a security measure. The CSS box model consists of content, padding, border and margin from innermost to outermost.

```html
<head>
    <link rel="stylesheet" href="style.css">
</head>
```

link references an external resource; rel="stylesheet" declares its relationship, href specifies its URL. Relative URLs resolve against the document base URL. Browser may request the stylesheet separately.

Original external stylesheet example, style.css:
```css
body { background-color: #f5f5f5; font-family: Arial, sans-serif; }
h1 { color: #174ea6; }
.generate { padding: 12px; }
```

body and h1 select elements; .generate selects a class. CSS comments use /* comment */. CSS hiding (display: none) does not enforce authorization; hidden interface elements and downloaded CSS can be inspected.

## 6. JavaScript: internal and external scripts

Internal scripts are enclosed by script tags; an external script uses src:
```html
<script>
    console.log("Page loaded");
</script>
```
```html
<script src="secret.js"></script>
```

src identifies a script resource URL, relative or absolute. The browser normally fetches and executes the script according to its script type and loading attributes. defer can delay external classic-script execution until HTML parsing is finished; type="module" selects JavaScript module semantics. Clicking a linked script in source view opens its resource for inspection.

Original example using an external local file:
```html
<button id="generate">Generate</button>
<p id="result"></p>
<script src="app.js" defer></script>
```
```javascript
const button = document.getElementById("generate");
button.addEventListener("click", () => {
    document.getElementById("result").textContent = "DEMO-123";
});
```

const declares a constant binding; document represents the current HTML document; getElementById retrieves an element by id; addEventListener registers a click handler; the arrow function runs on a click; textContent assigns plain text to the result element. Expected display after clicking: DEMO-123. This is a harmless demonstration, not a secure serial generator or observed lab output. Real privileged serial issuance must be authorized and validated server-side.

## 7. Identifying obfuscated scripts

The supplied HTB lesson shows a linked secret.js resource with a dense eval(function(p,a,c,k,e,d) ...) pattern. This resembles a JavaScript packer wrapper but the truncated excerpt is not enough to identify the exact tool or behavior conclusively. eval evaluates a string as JavaScript code; for a trusted illustrative expression, eval("2 + 3") produces 5. Avoid eval with untrusted input; the anonymous function and short parameters are part of the wrapper. split('|') splits a string at pipe delimiters into an array. For example, `"HTML|CSS|JavaScript".split("|")` returns `["HTML", "CSS", "JavaScript"]`. Obfuscation is deliberate reduction of readability, not proof of malware. Avoid eval and do not execute unknown scripts to reveal their behavior. Static formatting, inspection and later deobfuscation methods are preferred.

## 8. Source analysis in existing projects

UniTrack (Flask): inspect HTML and browser JS for unintended credentials, comments and internal data. QR and visitor-pass validation, user roles, session checks and entry/exit recording belong on the backend. A hidden button does not enforce authorization.

ProTrack-MX (React, Vite, Supabase): browser bundles and CSS are inspectable. VITE_ client variables are public; privileged service-role/secret keys must stay on the server. Use RLS and backend authorization for grades, messages and student records. Review production source maps and artifacts for exposure. These are architectural recommendations, not findings from an audit.

## 9. Mandatory HTB question: evidence-based methodology

The learner is asked to find a secret flag by repeating the source inspection technique on the assigned HTB target. Only the expressly assigned lab is in scope. Start by examining the HTML response without executing any script:
```bash
curl -sS 'http://SERVER_IP:PORT/'
```

Replace the placeholder with the active assigned target. curl retrieves the HTTP response body; -s suppresses the progress meter and routine errors, -S restores error messages when used with -s; the URL specifies HTTP, host, port and root path. Inspect the returned HTML for comments, flag-like strings and linked resources. If nothing relevant is visible, inspect referenced files in scope with the same static approach. Do not assume the flag is in a comment or secret.js until actual evidence is available. A successful HTTP response alone is not proof of a flag. If connection fails, verify the target timer, network access and assigned lab address; do not scan other targets.

The exact target address and restricted answer are deliberately omitted from the public repository. No response, flag, accepted submission or lab execution has yet been verified. Update this file with sanitized methodology after the learner supplies output. Next action: learner runs the initial cURL command and provides the result.

## 10. Official references

- [HTB JavaScript Deobfuscation](https://academy.hackthebox.com/course/preview/javascript-deobfuscation)
- [MDN HTML](https://developer.mozilla.org/en-US/docs/Web/HTML)
- [MDN CSS](https://developer.mozilla.org/en-US/docs/Web/CSS)
- [MDN JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
- [MDN script element](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/script)
- [MDN eval](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/eval)
- [Chrome DevTools](https://developer.chrome.com/docs/devtools/)
- [Firefox Developer Tools](https://firefox-source-docs.mozilla.org/devtools-user/)
- [cURL manual](https://curl.se/docs/manpage.html)

See the [Browser Source Inspection guide](Tools/01-Browser-Source-Inspection.md) for installation, architecture, workflows and troubleshooting. No optional lab required.
