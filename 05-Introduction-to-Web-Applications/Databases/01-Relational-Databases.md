# Relational Database Systems — Technical Reference

Supplement to [Section 13](../13-Databases.md). These systems implement relational data and SQL with different dialects, deployment models and feature sets. No single product is universally fastest or cheapest.

## MySQL and MariaDB
MySQL is a client/server database; MariaDB originated as a MySQL fork. Both commonly use the MySQL protocol and InnoDB-family transactional storage. Compatibility is not complete. Common port: TCP 3306. MySQL Community Edition and MariaDB Community Server offer free software; enterprise and hosting costs vary. MySQL/MariaDB examples:
```sql
CREATE TABLE school_notice (
  id INT AUTO_INCREMENT PRIMARY KEY,
  message VARCHAR(255) NOT NULL
);
INSERT INTO school_notice (message) VALUES ('Class begins at 09:00');
SELECT id, message FROM school_notice;
```
AUTO_INCREMENT generates IDs. Use a dedicated schema and account. mysql --version or mariadb --version shows the client version, not necessarily the server version. SELECT VERSION() queries the connected server. Use authorized connections only; keep passwords out of shell history.

## PostgreSQL
Free, open-source object-relational DBMS; default TCP 5432. Supports constraints, MVCC (multiversion concurrency control), transactions, JSONB, extensibility, indexes and RLS (row-level security). Example:
```sql
CREATE TABLE school_notice (
  id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  message TEXT NOT NULL,
  published_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);
INSERT INTO school_notice(message) VALUES ('Exam on Friday') RETURNING id;
```
IDENTITY generates a value; TIMESTAMPTZ stores an instant displayed according to session time zone; DEFAULT supplies a timestamp; RETURNING retrieves generated values. psql --version identifies the client. Supabase uses PostgreSQL; apply policies by authenticated identity and role. RLS is not automatically a complete security design.

## SQLite
Embedded public-domain SQL engine, commonly a single file plus possible journal/WAL files; no independent server port. Convenient for zero-budget Python learning. Local illustrative example:
```python
import sqlite3
with sqlite3.connect(":memory:") as db:
    db.execute("PRAGMA foreign_keys = ON")
    db.execute("CREATE TABLE notices (id INTEGER PRIMARY KEY, text TEXT NOT NULL)")
    db.execute("INSERT INTO notices VALUES (?, ?)", (1, "Class notice"))
    print(db.execute("SELECT text FROM notices WHERE id = ?", (1,)).fetchone())
```
:memory: creates a transient database; connect opens a connection; execute accepts SQL and bound values; fetchone returns a row. Expected output: ('Class notice',). The connection context manages transaction completion, not automatic connection closure. Close explicitly when finished. SQLite is suitable for local examples, but concurrent write workloads and distributed architectures need careful design. Protect the database and journal files.

## Microsoft SQL Server
Microsoft RDBMS using T-SQL, commonly port 1433 for default TCP configurations (named instances can differ). Developer edition is for development/testing, Express has limits, and production licensing varies. Example:
```sql
CREATE TABLE dbo.Notices (
    id INT IDENTITY(1,1) PRIMARY KEY,
    message NVARCHAR(255) NOT NULL
);
INSERT INTO dbo.Notices(message) VALUES (N'Class notice');
SELECT id, message FROM dbo.Notices;
```
dbo is a schema; IDENTITY(1,1) starts at 1 and increments by 1; NVARCHAR supports Unicode; N marks a Unicode literal. SQL Server Management Studio is a client management tool, not the database engine. This relates to the learner's UPBC SQL coursework.

## Oracle Database
Enterprise RDBMS with SQL and PL/SQL procedural extensions; listener commonly TCP 1521. Licensing and free-edition constraints vary. Example for compatible Oracle versions:
```sql
CREATE TABLE school_notice (
  id NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  message VARCHAR2(255) NOT NULL
);
INSERT INTO school_notice(message) VALUES ('Class notice');
COMMIT;
```
NUMBER is numeric; VARCHAR2 is variable-length text; COMMIT finalizes transaction work. SQL*Plus and SQL Developer are client tools. Never assume Oracle compatibility with MySQL syntax.

## Amazon Aurora and Azure SQL
Managed relational cloud services. Aurora provides MySQL- or PostgreSQL-compatible editions; Azure SQL offers managed Microsoft SQL variants. Cloud management can simplify provisioning, backups and failover but does not eliminate authorization, network controls, monitoring or costs. Do not create paid resources for this lesson.

## Selection and security
For local Python learning, SQLite has minimal setup; for multi-user school systems, PostgreSQL is a strong candidate where appropriate. MySQL/MariaDB fit many PHP apps, SQL Server fits T-SQL coursework and Microsoft deployments, Oracle fits specific enterprise requirements. Use constraints, transactions, parameterized statements, least privilege, TLS for network links, tested backups, and private endpoints. Avoid assuming a port proves a product. Examples use fictional data and were not executed.

Official references: [MySQL](https://dev.mysql.com/doc/), [MariaDB](https://mariadb.com/docs/), [PostgreSQL](https://www.postgresql.org/docs/), [SQLite](https://www.sqlite.org/docs.html), [SQL Server](https://learn.microsoft.com/en-us/sql/), [Oracle](https://docs.oracle.com/en/database/), [Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html), [Azure SQL](https://learn.microsoft.com/en-us/azure/azure-sql/).
