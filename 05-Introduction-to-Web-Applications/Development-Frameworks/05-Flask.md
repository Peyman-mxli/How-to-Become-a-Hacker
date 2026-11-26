# Flask — Python Web Framework

Flask is a free BSD-licensed lightweight WSGI framework. WSGI defines communication between compatible Python applications and application servers. Flask supplies routes, request/response objects, Jinja templates, sessions and extension integration. It does not automatically provide a database ORM or every authentication feature. Blueprints group routes; application factories construct app instances; extensions may add ORM, CSRF and other functionality.

## Original local API
```python
from flask import Flask, jsonify, request

app = Flask(__name__)

@app.get("/api/notices/<int:notice_id>")
def get_notice(notice_id):
    if notice_id != 42:
        return jsonify(error="Not found"), 404
    return jsonify(id=42, content="Class notice")
```
Flask(__name__) constructs an app; decorator registers GET; int converts path data; jsonify builds JSON; tuple return sets status. request.args reads query data, request.form parsed forms, request.get_json parses JSON. Validate types and content. For an existing local Flask environment with app.py:
```bash
python -m flask --app app routes
python -m flask --app app run --host 127.0.0.1 --port 5000
curl -i http://127.0.0.1:5000/api/notices/42
```
--app selects app module; routes lists endpoints; run starts the development server; --host binds loopback; --port selects the socket. Expected example response is 200 JSON. Use a production WSGI server for deployment.

## Security and UniTrack
Validate session identity, enforce roles and object ownership on every protected route, apply CSRF protection, use parameterized queries, validate uploads, avoid debug mode in production, set secure cookie properties and protect secret keys. Flask's signed default session cookie is not encrypted; do not store sensitive data in it. UniTrack uses Flask, but no changes were made to its implementation. [Official docs](https://flask.palletsprojects.com/).
