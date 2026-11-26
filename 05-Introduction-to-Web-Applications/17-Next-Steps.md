# Section 17 — Next Steps

**Provider:** Hack The Box Academy  
**Status:** Final lesson theory studied and documented. No mandatory question appears in the supplied text. Module completion awaits platform confirmation; full Sections 4 and 5 remain documentation gaps.

## 1. Lesson objective

The course concludes with a recommendation to reinforce web application fundamentals through developing, operating and securing a small application. This connects frontend structure, presentation, behavior, web servers, backend logic, persistence, APIs and security. The supplied lesson presents a suggested roadmap, not an additional mandatory lab.

## 2. Architecture and responsibilities

```text
Browser (HTML, CSS, JavaScript)
          | HTTP / HTTPS
          v
Web server / reverse proxy (optional in a local development setup)
          |
          v
Application framework and request handlers
          | validation, authentication, authorization, business logic
          v
Database and other authorized services
```

| Component | Responsibility |
| --- | --- |
| HTML | Semantic page structure |
| CSS | Layout, presentation and responsive styles |
| JavaScript | Browser interaction and asynchronous requests |
| Web server | HTTP handling and forwarding or serving responses |
| Backend framework | Routing, request processing and application logic |
| API | Defined interface between components |
| Database | Persistent data storage and retrieval |
| Authentication | Verifying identity |
| Authorization | Enforcing allowed actions and resource access |
| Security controls | Protecting confidentiality, integrity and availability |

A browser executes frontend code; server-side logic must enforce permissions independently of frontend controls. A local Flask development server can serve a small learning app, while a production deployment requires suitable application-server infrastructure. A reverse proxy is optional, not a mandatory component of every application.

## 3. The nine-step roadmap from HTB

| Step | Recommended activity | Expected learning outcome |
| --- | --- | --- |
| 1 | Set up a VM with a web server | Understand server setup, network bindings and HTTP |
| 2 | Create an HTML page | Understand elements, forms and page structure |
| 3 | Design it with CSS | Understand selectors, layout and presentation |
| 4 | Add basic JavaScript functions | Understand events, DOM and client-side behavior |
| 5 | Program a small web application | Understand routes, validation and backend logic |
| 6 | Connect the application to a database | Understand persistence, queries and constraints |
| 7 | Experiment with APIs | Understand request methods, data formats and status codes |
| 8 | Test for vulnerabilities | Identify weaknesses in an owned, isolated application |
| 9 | Adjust code and configuration to fix weaknesses | Remediate, retest and prevent regressions |

The course recommends a virtual machine. For this learner, existing Ubuntu WSL2 or local development environments are useful alternatives for the basic development concepts, although WSL2 is not identical to a separately managed lab VM. No new VM, installation or practice exercise is mandatory.

## 4. Original reference example — a school notice application

The following fictional application illustrates the roadmap without altering existing projects. It stores public demonstration notices only; it is not a complete production service. The code is a reference example, not a new required assignment. Run only in an owned local environment.

### Backend: app.py

```python
import sqlite3
from pathlib import Path
from flask import Flask, jsonify

app = Flask(__name__)
DB = Path(__file__).with_name("notices.db")

def init_db():
    with sqlite3.connect(DB) as conn:
        conn.execute("""
            CREATE TABLE IF NOT EXISTS notices (
                id INTEGER PRIMARY KEY,
                title TEXT NOT NULL
            )
        """)
        conn.execute(
            "INSERT OR IGNORE INTO notices (id, title) VALUES (?, ?)",
            (1, "Demo mathematics notice")
        )

@app.get("/")
def home():
    return app.send_static_file("index.html")

@app.get("/api/notices/<int:notice_id>")
def get_notice(notice_id):
    with sqlite3.connect(DB) as conn:
        row = conn.execute(
            "SELECT id, title FROM notices WHERE id = ?",
            (notice_id,)
        ).fetchone()
    if row is None:
        return jsonify(error="Notice not found"), 404
    return jsonify(id=row[0], title=row[1]), 200

if __name__ == "__main__":
    init_db()
    app.run(host="127.0.0.1", port=5000, debug=False)
```

The Flask route converts the URL identifier into an integer. SQLite stores the demonstration record. The question mark is a SQL parameter placeholder; a one-element tuple supplies the value. fetchone retrieves one matching row or None. jsonify serializes the selected fields as JSON. A missing notice returns HTTP 404. The server binds to loopback and disables debug mode. Flask's built-in server is for development only. Real private records require authentication and object-level authorization; this example deliberately exposes only fictional public data.

### Frontend: static/index.html

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>School notice demo</title>
  <link rel="stylesheet" href="/static/style.css">
  <script src="/static/app.js" defer></script>
</head>
<body>
  <main>
    <h1>School notice</h1>
    <button id="load" type="button">Load notice</button>
    <p id="result" role="status"></p>
  </main>
</body>
</html>
```

HTML defines a heading, button and status paragraph. defer executes the script after parsing the document. role=status exposes updates to assistive technology.

### Presentation: static/style.css

```css
body { font-family: system-ui, sans-serif; margin: 2rem; }
main { max-width: 40rem; margin: auto; }
button { padding: 0.5rem 1rem; cursor: pointer; }
```

CSS controls typography, content width and button spacing.

### Browser logic: static/app.js

```javascript
const result = document.getElementById("result");
document.getElementById("load").addEventListener("click", async () => {
  try {
    const response = await fetch("/api/notices/1");
    if (!response.ok) throw new Error("Request failed");
    const notice = await response.json();
    result.textContent = notice.title;
  } catch {
    result.textContent = "Unable to load notice.";
  }
});
```

addEventListener connects a button click to an asynchronous function. fetch issues a same-origin GET request. response.ok indicates a 2xx status. response.json parses JSON. textContent inserts text rather than interpreting the title as HTML. try/catch handles errors.

### Optional local setup and usage

In a new disposable project directory, save the four files with the paths above. In Ubuntu WSL2:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install Flask
python app.py
```

If venv support is missing, install Ubuntu's python3-venv package. The -m flag executes a module; source activates the isolated environment; pip installs Flask inside it. These are optional instructions, not executed commands. Use supported dependency versions and pin them for a reproducible project.

In a second terminal:

```bash
curl -i http://127.0.0.1:5000/api/notices/1
curl -i http://127.0.0.1:5000/api/notices/999
```

Expected illustrative results: HTTP 200 with JSON containing id=1 and the demo title, then HTTP 404 with JSON error for the missing record. -i includes response headers. Open http://127.0.0.1:5000/ in a browser and click Load notice. Stop the development server with Ctrl+C. No live test or installation has been performed.

## 5. Secure development and verification

| Threat | Control | Safe verification in an owned environment |
| --- | --- | --- |
| Sensitive data exposure | Minimize returned fields; keep secrets server-side | Inspect fictional API output |
| HTML injection / XSS | Context-appropriate encoding; textContent for text | Confirm ordinary text is displayed literally |
| CSRF | CSRF protection for state-changing cookie-authenticated routes | Check rejection of missing/invalid CSRF tokens |
| Broken authentication | Secure hashing, session management and rate limits | Use controlled test accounts |
| Broken access control | Server-side role, tenant and object checks | Compare allowed and denied fictional accounts |
| SQL injection | Bound query parameters | Verify ordinary names with apostrophes work |
| Command injection | Avoid shell calls; structured arguments where needed | Review process invocation code |
| Unsafe file upload | Validate decoded content, limits, private storage | Use benign valid and invalid fixtures |
| Known dependencies | Inventory, vendor advisories, supported patches | Compare installed versions and run regression tests |

The notice demo illustrates parameterization and text-only rendering but does not implement full authentication, authorization, CSRF protection or upload handling. Do not mistake illustrative controls for a production audit. A secure development cycle is design -> implement -> review -> test -> remediate -> retest -> maintain.

## 6. Relationship to current projects

UniTrack already demonstrates a Flask application with frontend pages, data persistence, QR access control, visitor workflows and user roles. Review server-side permission enforcement, secure sessions, CSRF for state-changing operations, upload validation, and safe database queries. ProTrack-MX already demonstrates React frontend components, Supabase/PostgreSQL services, authentication, real-time functionality and database access policies. Review row-level security, group isolation, private messages, grades and storage permissions. These are study recommendations, not newly discovered defects or changes to either project.

## 7. Recommended next modules

The supplied HTB lesson recommends Web Requests and JavaScript Deobfuscation for extending web fundamentals; Hacking WordPress and SQL Injection Fundamentals for web security; and authorized Easy HTB machines for applying learned skills. Web Requests is already completed and documented (8/8). The suggested next module in this learner's study sequence is JavaScript Deobfuscation, subject to the learner's Academy path and availability. Do not assume module availability, required cubes or prerequisites without checking the platform. Only attack explicitly authorized HTB labs and in-scope assets.

## 8. Module recap and documentation status

Sections 1–3 and 6–17 are fully documented, totaling 15/17 supplied full lessons. Sections 4 and 5 have supplementary notes for HTML image elements and CSS text alignment but their full lesson texts have not been supplied. Four preceding modules contain 66 completed documented sections. The overall count is 81/83 full lessons documented across these five modules, or approximately 97.59%. Reaching the final section is not independently verified platform completion. Await the learner's completion screen or share preview before marking this module complete. Section 16 answer acceptance was not separately shown. Section 17 has no required question in the supplied text.

## References

- [HTB Academy — Introduction to Web Applications](https://academy.hackthebox.com/course/preview/introduction-to-web-applications)
- [HTB Academy](https://academy.hackthebox.com/)
- [Hack The Box](https://www.hackthebox.com/)
- [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web)
- [Flask documentation](https://flask.palletsprojects.com/)
- [Python sqlite3](https://docs.python.org/3/library/sqlite3.html)
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/)

Original reference example is educational and has not been executed. No new optional practice is required and no project changes have been made.
