# Section 13 — Databases

**Provider:** Hack The Box Academy  
**Status:** Theory documented; required conceptual question verified from official Firebase documentation; platform acceptance pending.

## 1. Purpose and architecture

A database is an organized collection of persistent information; a DBMS is software that manages it. A relational DBMS (RDBMS) implements relational concepts. A typical application receives a request, validates identity and permissions, executes a database operation through a driver or API, and returns a representation. Databases may run locally, on another server or as managed services. Binary assets can be stored as BLOBs or in object storage with metadata and references in the database. Passwords must be stored as salted, adaptive password hashes, not plaintext.

Selection criteria include query patterns, latency, throughput, storage, scaling, consistency, availability, operational complexity, security and total cost. An index can improve reads but consumes space and adds write overhead. Replication maintains copies; backups enable recovery and must be tested. Replication is not a backup.

## 2. Relational data

Tables have rows (records), columns (attributes) and data types. A primary key uniquely identifies a row; a foreign key references a candidate key and enforces referential integrity when configured. A composite key contains multiple columns. UNIQUE, NOT NULL, CHECK and DEFAULT express additional constraints (details vary by engine). A schema describes the logical structure, not just the relationships; in some systems it is also a namespace. An ERD depicts entities and relationships. One-to-one, one-to-many and many-to-many relationships are common. A junction table models many-to-many relationships.

Original portable-style example, with explicitly assigned integer IDs:

```sql
CREATE TABLE users (
  id INTEGER PRIMARY KEY,
  username VARCHAR(50) NOT NULL UNIQUE,
  first_name VARCHAR(100) NOT NULL
);
CREATE TABLE posts (
  id INTEGER PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id),
  content TEXT NOT NULL
);
INSERT INTO users VALUES (1, 'demo_teacher', 'Sara');
INSERT INTO posts VALUES (101, 1, 'Mathematics assignment');

SELECT u.username, p.content
FROM users AS u
INNER JOIN posts AS p ON u.id = p.user_id;
```

CREATE TABLE defines structure; INSERT adds a row; SELECT retrieves data; AS assigns aliases; INNER JOIN returns matched rows; ON specifies the relationship. Expected illustrative output: demo_teacher | Mathematics assignment. SQLite requires PRAGMA foreign_keys = ON per connection for foreign-key enforcement; type and length enforcement differ by engine. SQL Server may need modern VARCHAR(MAX) or NVARCHAR(MAX) instead of TEXT. This is an illustrative model, not a migration of either project.

## 3. SQL commands and transactions

| Command | Function |
| --- | --- |
| SELECT | Read |
| INSERT | Create row |
| UPDATE | Modify rows |
| DELETE | Remove rows |
| CREATE / ALTER / DROP | Define or change objects |
| GRANT / REVOKE | Manage privileges where supported |
| COMMIT / ROLLBACK | Finalize or undo transaction work |

CRUD means Create, Read, Update, Delete. A WHERE clause restricts matching rows. UPDATE or DELETE without WHERE can affect every row. Use parameterized queries and scoped permissions.

ACID: Atomicity (all-or-nothing), Consistency (preserve declared invariants), Isolation (control concurrent interactions), Durability (committed data survives relevant failures). These properties depend on engine and configuration and are not exclusive to SQL databases. Normalization reduces avoidable duplication and update anomalies. Denormalization deliberately duplicates data for a specific access pattern and requires consistency management.

## 4. Relational products

MySQL and MariaDB are client/server relational systems commonly used with PHP. PostgreSQL is extensible and supports advanced types, transactions and row-level security. SQLite is embedded, file-based and convenient for local projects. Microsoft SQL Server (MSSQL) supports T-SQL and multiple editions. Oracle Database supports enterprise workloads and PL/SQL; licensing varies. Aurora is a managed AWS relational service compatible with MySQL or PostgreSQL variants; Azure SQL is a managed Microsoft SQL offering. Free software does not guarantee free hosting. See [relational systems guide](Databases/01-Relational-Databases.md).

## 5. Non-relational data (NoSQL)

NoSQL is a family of non-relational models, not a single query language. The course emphasizes flexibility. Technical clarification: NoSQL systems can have keys, indexes, validation schemas, relationships and transactions; wide-column engines even have tables and primary keys. Flexible schema does not mean no structure. NoSQL does not automatically outperform SQL; select a model for the workload.

| Model | Storage approach | Example |
| --- | --- | --- |
| Key-value | Unique key maps to value | Valkey, Redis |
| Document | Structured documents with identifiers and fields | MongoDB, CouchDB, Firestore |
| Wide-column | Partitioned rows and clustering columns, query-driven design | Cassandra |
| Graph | Nodes and relationships/edges | Neo4j |

Original key-value example:
```json
{
  "notice:101": {"date": "2026-11-26", "content": "Class begins at 09:00"},
  "notice:102": {"date": "2026-11-27", "content": "Bring your notebook"}
}
```
A key identifies a value; JSON is a serialization format, not a database. XML is another encoding; neither is required for key-value stores. Document stores can query fields and maintain indexes; MongoDB uses BSON (binary serialization of JSON-like documents). Wide-column stores partition data for known access patterns. Graph stores traverse relationships. Elasticsearch is a distributed search and analytics engine using indexed documents; it is often used alongside a system of record. DynamoDB is a managed key-value/document service. See [NoSQL guide](Databases/02-NoSQL-Databases.md).

Licensing clarification: MongoDB Community Server is SSPL-licensed (source-available, not OSI-approved open source). Elasticsearch licensing depends on edition and source/distribution; Elastic offers AGPLv3 for eligible free source portions alongside other licenses. Check terms for the specific release. Valkey is a BSD-licensed alternative for local key-value learning.

## 6. Database access from PHP

The course uses mysqli, PHP's MySQL interface. A database account with suitable permissions connects using host, username, password and optional database name. localhost denotes the local host (mysqli may use a Unix socket on Unix); 127.0.0.1 specifies IPv4 loopback. A driver translates application calls to a database protocol. CREATE DATABASE requires elevated database privileges and belongs in controlled provisioning, not normal request handlers.

Illustrative provisioning command (administrator only, not needed for this lesson):
```sql
CREATE DATABASE school_demo;
```

A vulnerable pattern in the course inserts raw POST data directly into an SQL string. This can let untrusted data alter the intended query structure. The safe alternative separates query syntax and data with prepared statements. The following PHP example assumes an existing database and a public-safe directory-search endpoint or a caller whose authentication and authorization have already been validated:

```php
<?php
mysqli_report(MYSQLI_REPORT_ERROR | MYSQLI_REPORT_STRICT);
$conn = new mysqli(
    "127.0.0.1",
    getenv("DB_USER"),
    getenv("DB_PASSWORD"),
    "school_demo"
);
$conn->set_charset("utf8mb4");

$searchInput = $_POST["findUser"] ?? "";
if (!is_string($searchInput) || strlen($searchInput) > 100) {
    http_response_code(400);
    exit("Invalid search");
}

$pattern = "%" . $searchInput . "%";
$stmt = $conn->prepare(
    "SELECT username FROM users WHERE username LIKE ?"
);
$stmt->bind_param("s", $pattern);
$stmt->execute();
$stmt->bind_result($username);

while ($stmt->fetch()) {
    echo htmlspecialchars($username, ENT_QUOTES | ENT_SUBSTITUTE, "UTF-8");
    echo "<br>";
}
$stmt->close();
$conn->close();
```

mysqli_report enables exception-style error reporting; configure generic production errors and private logs. getenv obtains environment variables; set_charset selects encoding; POST is PHP's parsed form data; ?? supplies a fallback; strlen checks byte length. LIKE uses % for zero or more characters and _ for one character; escape wildcards if literal matching is required. prepare defines SQL with a placeholder; bind_param("s", ...) binds a string; execute runs the prepared operation; bind_result assigns selected values; fetch iterates rows. htmlspecialchars safely encodes text for HTML output; ENT_QUOTES includes both quote types; ENT_SUBSTITUTE replaces invalid sequences. close releases resources. Parameterization prevents value-based SQL injection, but does not replace authorization, allowlisting dynamic identifiers or contextual output encoding. Never hardcode real credentials or log secrets. Do not return password hashes to clients.

## 7. Original Python example for UniTrack

```python
import sqlite3

def find_user(conn, username):
    return conn.execute(
        "SELECT id, username FROM users WHERE username = ?",
        (username,)
    ).fetchone()
```
The question mark is SQLite's value placeholder; the trailing comma creates a one-element tuple; fetchone returns one row or None. Other drivers use different placeholders. Use the project's configured ORM/driver, enforce caller permissions before querying and restrict output fields. This code is illustrative and not a change to UniTrack.

## 8. ProTrack-MX and Firebase

Supabase uses PostgreSQL as its relational database. Row-level security (RLS) policies determine which rows a caller can access; authentication establishes identity, and authorization limits permitted operations. Do not expose privileged service-role keys or rely on UI role checks. Apply tenant/group boundaries, transaction controls and backup/restore tests.

Firebase Realtime Database stores a JSON tree and synchronizes data changes; Cloud Firestore organizes documents in collections. Google's official comparison classifies both under the non-relational database family. They are managed services, not substitutes for access rules. See [Firebase guide](Databases/03-Firebase.md).

## 9. Security

| Risk | Defensive control |
| --- | --- |
| SQL injection | Parameterized queries, safe identifier allowlists |
| NoSQL injection | Validate types and shapes; avoid client-controlled operators |
| Broken access control | Server-side authorization, scoped queries, RLS |
| Exposed database port | Private networking, firewall, TLS |
| Plaintext passwords | Salted adaptive hashes (Argon2id/scrypt as appropriate) |
| Overprivileged account | Least privilege, separate provisioning identity |
| XSS via stored content | Context-aware output encoding |
| Data loss | Tested backups and recovery |
| Race conditions | Transactions, constraints and concurrency controls |
| Secret leakage | Secure secret storage and rotation, private logs |

Only test owned or explicitly authorized environments. Do not publish sensitive data, credentials or assessment flags.

## 10. Required question and verification

The required conceptual question asks for the broad database category of Google's Firebase Database. The category is established by the vendor's official comparison of Realtime Database and Cloud Firestore. No live target, account, installation or Pwnbox is necessary. The terminology is explained in the general lesson and Firebase supplement; no separate restricted submission answer is recorded. Acceptance remains pending.

## References

- [HTB Academy](https://academy.hackthebox.com/app/module/75)
- [PostgreSQL documentation](https://www.postgresql.org/docs/)
- [SQLite foreign keys](https://www.sqlite.org/foreignkeys.html)
- [PHP mysqli prepared statements](https://www.php.net/manual/en/mysqli.quickstart.prepared-statements.php)
- [OWASP SQL injection prevention](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)
- [Firebase database comparison](https://firebase.google.com/docs/database/rtdb-vs-firestore)
- [MongoDB SSPL](https://www.mongodb.com/legal/licensing/server-side-public-license/faq)
- [Elastic licensing](https://www.elastic.co/pricing/faq/licensing)
- [Supabase RLS](https://supabase.com/docs/guides/database/postgres/row-level-security)

All examples are original and illustrative, not executed or verified against a deployed application.
