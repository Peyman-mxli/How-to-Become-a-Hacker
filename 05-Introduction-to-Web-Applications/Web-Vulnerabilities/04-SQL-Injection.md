# SQL Injection — Defensive Study Guide

SQL injection occurs when input is interpreted as SQL syntax rather than data. It can affect SELECT, INSERT, UPDATE and DELETE operations. Potential outcomes include authentication bypass, reading or modifying data and, depending on engine configuration and privileges, wider system impact. Stacked statements and OS execution are not universally available. An SQL error alone does not establish exploitability.

## Original safe SQLite example
```python
def find_notice(conn, search):
    if not isinstance(search, str) or len(search) > 100:
        raise ValueError("Invalid search")
    return conn.execute(
        "SELECT id, content FROM notices WHERE content LIKE ?",
        ("%" + search + "%",)
    ).fetchall()
```
The question mark is a bound parameter; the one-element tuple supplies its value; fetchall returns matching rows. LIKE wildcards are data with pattern semantics: escape them explicitly if a literal search is required. Use pagination for large datasets. This public-notice illustration assumes a properly configured database; protected records also require scoped authorization.

## Safe PHP pattern
```php
$stmt = $conn->prepare("SELECT username FROM users WHERE username = ?");
$stmt->bind_param("s", $username);
$stmt->execute();
```
prepare defines the statement; bind_param binds a string value; execute runs the operation. Never concatenate client-controlled identifiers into SQL: use a fixed mapping of approved columns or operations. ORMs can still be unsafe when raw query strings are assembled from user input.

## Defense and verification
Use parameterized queries, allowlisted dynamic identifiers, least-privileged accounts, private error logs, safe output encoding and authorization checks. Passwords require adaptive hashing, not plain-text comparison. Test with fictional inputs containing ordinary apostrophes (such as O'Connor), expected empty results and unauthorized account scopes. Do not expose errors, credentials or test data. UniTrack should use safe ORM/driver operations; Supabase-backed ProTrack-MX should enforce RLS and avoid unsafe dynamic SQL in functions.

References: [OWASP SQL Injection Prevention](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html), [Python sqlite3](https://docs.python.org/3/library/sqlite3.html), [PHP prepared statements](https://www.php.net/manual/en/mysqli.quickstart.prepared-statements.php). Examples not executed.
