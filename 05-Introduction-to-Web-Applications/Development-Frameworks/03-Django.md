# Django — Python Web Framework

Django is a BSD-licensed framework with ORM, templates, form validation, authentication, administration, routing, migrations and middleware. Its model-template-view pattern separates data models, presentation and request logic. A project contains settings and URL configuration; an app is a modular feature. settings.py configures the project, urls.py maps routes, views.py defines handlers, models.py defines ORM models, and migrations/ stores schema changes.

## Original view and URL
views.py:
```python
from django.http import JsonResponse

def notice(request, notice_id):
    if request.method != "GET":
        response = JsonResponse({"error": "Method not allowed"}, status=405)
        response["Allow"] = "GET"
        return response
    if notice_id != 42:
        return JsonResponse({"error": "Not found"}, status=404)
    return JsonResponse({"id": 42, "content": "Class notice"})
```
urls.py:
```python
from django.urls import path
from . import views

urlpatterns = [
    path("api/notices/<int:notice_id>/", views.notice),
]
```
path maps URL and converter; view receives the request and integer identifier; JsonResponse serializes dictionaries; status sets the HTTP code. Include the app URLs in the project if needed.

## Commands in an existing project
```bash
python -m django --version
python manage.py check
python manage.py showmigrations
```
-m runs a module; manage.py is the project command interface; check validates configuration; showmigrations reports migration state. runserver is for development, not production.

## Security
Use ORM parameterization, CSRF middleware, permission checks, secure cookies, protected admin, DEBUG=False and configured ALLOWED_HOSTS in production. Avoid exposing SECRET_KEY or raw database credentials. Django offers more integrated features than Flask but does not automatically enforce every object-level permission. A potential alternative for future Python projects; do not rewrite UniTrack. [Official docs](https://docs.djangoproject.com/).
