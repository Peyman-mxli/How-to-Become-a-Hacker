# Lesson 06/17 — JavaScript

## Learning objectives
Understand JavaScript execution in browsers and on servers, script loading, DOM manipulation, event-driven interfaces, asynchronous requests and common frontend libraries and frameworks.

## JavaScript and execution environments
HTML defines document structure; CSS describes presentation; JavaScript implements behavior. Browsers execute JavaScript through engines such as V8 (Chromium), SpiderMonkey (Firefox) and JavaScriptCore (Safari). Node.js is a JavaScript runtime outside the browser, useful for server-side applications and development tools. Browser APIs such as document are not automatically available in Node.js. JavaScript also supports mobile development through appropriate frameworks.

## Loading scripts
Inline scripts are embedded within script elements. External scripts are loaded from a resource specified by src. The lesson shows type="text/javascript"; modern HTML normally does not require this attribute for classic JavaScript.

Original example:
~~~html
<button id="check" type="button">Check attendance</button>
<p id="status" aria-live="polite"></p>
<script src="./attendance.js" defer></script>
~~~

src identifies a script URL, ./ refers to the current directory, and defer lets the external classic script run after document parsing. External files improve organization but third-party scripts must be trusted and managed carefully.

## DOM and events
The Document Object Model (DOM) represents the parsed document as objects. getElementById locates an element by ID, returning null if it is absent. innerHTML interprets strings as HTML; textContent inserts ordinary text. Prefer textContent when displaying untrusted text.

Original attendance.js:
~~~javascript
const button = document.getElementById("check");
const status = document.getElementById("status");

button.addEventListener("click", () => {
    status.textContent = "Attendance demo ready";
});
~~~

The const declarations bind references to DOM elements. addEventListener registers a click handler. The arrow function runs when the event occurs and changes the paragraph content. This code is illustrative, not an actual attendance check or authentication system.

## Usage and asynchronous requests
JavaScript can update content without reloading a whole page, process input, perform calculations, render interfaces, and coordinate animations with CSS. Browser engines execute local code without contacting a server for every UI update. Network-dependent operations still require the relevant backend. CSS itself supports many advanced animations; JavaScript is useful for programmatic and state-dependent animation.

AJAX originally means Asynchronous JavaScript and XML; modern applications often exchange JSON instead of XML. Fetch and XMLHttpRequest are browser APIs that can perform these requests. Asynchronous processing allows the UI to remain responsive while waiting for network results.

Original example for an owned local school application with a hypothetical same-origin endpoint:
~~~javascript
async function loadSummary() {
    const response = await fetch("/api/demo/summary");
    if (!response.ok) {
        throw new Error("Request failed: " + response.status);
    }
    const data = await response.json();
    document.getElementById("status").textContent =
        "Demo records: " + String(data.count);
}

loadSummary().catch(() => {
    document.getElementById("status").textContent = "Unable to load summary";
});
~~~

fetch sends a GET by default; await waits for the promise to resolve without blocking the entire browser UI; response.ok indicates a successful HTTP status (200–299); response.json parses a JSON response; catch handles failures. Illustrative response: {"count": 12}. No endpoint was called. A real API must authenticate and authorize requests independently of the UI.

## Frameworks and libraries
| Technology | Classification | Typical purpose |
| --- | --- | --- |
| Angular | Application framework | Structured applications, commonly using TypeScript |
| React | UI library | Component-based user interfaces |
| Vue | Progressive UI framework | Reactive components and application interfaces |
| jQuery | JavaScript library | DOM manipulation, events and AJAX |

The lesson groups them as frameworks in a broad sense; React and jQuery are more precisely libraries. TypeScript adds static type checking and is compiled to JavaScript. Frameworks do not automatically create secure authentication or authorization. Node.js is a runtime, not a frontend framework. Express is an example of a backend framework for Node.js.

## Security considerations
Client-delivered JavaScript is inspectable, even when minified. Do not embed private API keys, passwords or privileged service credentials in browser bundles. Client-side input checks improve usability but do not replace server validation and authorization. Prefer safe DOM APIs such as textContent for plain text and context-appropriate sanitization when HTML is necessary. Review dependencies and apply an appropriate Content Security Policy (CSP); use integrity checks for suitable externally hosted static resources. Browser same-origin policy and CORS regulate cross-origin access but are not substitutes for API authorization.

## Project connection
A React application such as ProTrack-MX can update UI state after an API response without a full page reload. A Flask application such as UniTrack can use browser JavaScript for dashboard updates while the backend checks roles and object-level access. These are conceptual applications, not a new inspection or modification of the projects.

## Verification
Section 6 theory studied and documented. No mandatory question appears in the supplied excerpt; the question area was not included, so requirement status is not independently confirmed. Optional JSFiddle experimentation skipped. All original examples are illustrative and unexecuted. Full Sections 4–5 are still awaiting their source content.

## References
- [HTB Academy](https://academy.hackthebox.com/app/module/75)
- [MDN JavaScript guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)
- [MDN DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)
- [MDN Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
- [Node.js](https://nodejs.org/en/about)
- [React](https://react.dev/)
- [Angular](https://angular.dev/)
- [Vue](https://vuejs.org/)
- [jQuery](https://jquery.com/)
- [OWASP DOM-based XSS prevention](https://cheatsheetseries.owasp.org/cheatsheets/DOM_based_XSS_Prevention_Cheat_Sheet.html)

Independent English study notes with original examples and labeled technical clarifications.
