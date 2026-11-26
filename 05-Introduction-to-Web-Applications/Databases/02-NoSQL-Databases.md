# NoSQL Databases — Storage Models and Products

Supplement to [Section 13](../13-Databases.md). NoSQL is a broad category. Data modeling, query features and guarantees depend on the specific engine. Flexible schemas still require validation and access control.

## Key-value: Valkey, Redis and DynamoDB
A key maps to a value. Values may be bytes, strings or richer structures supported by the engine; JSON is optional. Valkey is a free BSD-licensed key-value server; Redis licensing varies by release. Both are commonly used for cache, rate limits and temporary state. Example on an owned local instance:
```text
SET notice:101 "Class starts at 09:00" EX 300
GET notice:101
TTL notice:101
```
SET stores a value; EX sets expiry in seconds; GET retrieves it; TTL returns remaining lifetime. Expected illustrative GET: Class starts at 09:00. Do not store authoritative attendance solely in an expiring cache. DynamoDB is a managed AWS key-value/document system with partition keys and optional sort keys; costs and access patterns matter.

## Document: MongoDB, CouchDB and Firestore
Documents contain structured fields, nested objects and arrays. MongoDB uses BSON; a collection groups documents and _id identifies them. MongoDB Community Server is SSPL source-available, not OSI-approved open source. CouchDB uses JSON documents and HTTP APIs and is Apache-licensed. Firestore is managed. Example MongoDB shell operations using fictional data:
```javascript
db.notices.insertOne({ title: "Exam", group: "1A", active: true })
db.notices.find({ group: "1A", active: true }, { title: 1, _id: 0 })
```
insertOne adds one document; find uses a filter and projection; 1 includes title; 0 excludes _id. Never accept raw filter objects from untrusted clients; build allowed filters with validated scalar values. Validate document shape and index the intended query fields.

## Wide-column: Apache Cassandra
Apache-licensed distributed wide-column system using CQL (Cassandra Query Language). Partition keys determine data placement; clustering columns determine ordering within partitions. Model tables for known queries; avoid unbounded partitions. Replication and tunable consistency are important. Fault tolerance refers to node/network failures, not automatic correction of wrong data values.
```sql
CREATE TABLE notices_by_group (
  group_id text,
  notice_id int,
  content text,
  PRIMARY KEY (group_id, notice_id)
);
SELECT content FROM notices_by_group WHERE group_id = '1A';
```
Run only within a configured keyspace on a local owned instance. group_id is the partition key; notice_id is a clustering column. No joins are implied. The example returns matching stored notices if inserted. Use appropriate consistency levels and replication configuration.

## Graph: Neo4j
Graph databases represent entities as nodes and relationships as edges. Neo4j uses Cypher and has community/enterprise offerings. Example in a disposable local graph:
```cypher
CREATE (s:Student {id: 1, name: 'Demo'})
CREATE (c:Course {id: 10, name: 'Math'})
CREATE (s)-[:ENROLLED_IN]->(c)
```
```cypher
MATCH (s:Student)-[:ENROLLED_IN]->(c:Course)
RETURN s.name, c.name
```
Labels classify nodes; properties store values; ENROLLED_IN names an edge; MATCH traverses a pattern. Use parameterized Cypher, constraints and access controls. Relational junction tables can represent the same enrollment facts; graph traversal is a different query model.

## Search engine: Elasticsearch
Distributed search and analytics engine for indexed documents, often supporting full-text search and operational logs. Inverted indexes associate terms with documents; analyzers tokenize/normalize text; shards divide data, replicas provide redundancy. Do not confuse it with a transactional system of record. Licensing differs by release and distribution; eligible free source portions have AGPLv3 as an option. Example query body for an owned index:
```json
{"query":{"match":{"content":"mathematics"}}}
```
match performs analyzed text search. Do not send student private data or tokens to a public search endpoint. Authenticate requests and restrict index permissions.

## Choosing a model
Use a relational system for structured school enrollment/grades where constraints and joins are important; consider key-value caching for temporary state; documents for variable content; wide-column for large query-driven distributed datasets; graph for relationship-heavy traversal; search engine for full-text discovery. These are options, not changes to UniTrack or ProTrack-MX.

## Security and references
Validate data type and allowed query operators, authenticate clients, enforce least privilege, restrict network listeners, use TLS, protect backups, and avoid public administrative ports. NoSQL injection and broken access control remain possible. All examples are illustrative and not executed.

[Valkey](https://valkey.io/), [Redis](https://redis.io/docs/), [MongoDB](https://www.mongodb.com/docs/), [MongoDB SSPL](https://www.mongodb.com/legal/licensing/server-side-public-license/faq), [CouchDB](https://docs.couchdb.org/), [Cassandra](https://cassandra.apache.org/doc/latest/), [Neo4j](https://neo4j.com/docs/), [Elasticsearch](https://www.elastic.co/docs/), [Elastic licensing](https://www.elastic.co/pricing/faq/licensing), [DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html).
