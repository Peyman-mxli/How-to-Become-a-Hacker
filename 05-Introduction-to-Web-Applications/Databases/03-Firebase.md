# Firebase Databases — Realtime Database and Cloud Firestore

Supplement to [Section 13](../13-Databases.md). Firebase is a Google-managed application development platform, not a single database engine. Realtime Database is a cloud-hosted JSON tree; Firestore is a document/collection database. Google's [official comparison](https://firebase.google.com/docs/database/rtdb-vs-firestore) classifies both as NoSQL.

## Data modeling

Realtime Database illustrative tree:
```json
{
  "users": {
    "demo-user": {"displayName": "Demo Student"}
  },
  "notices": {
    "101": {"content": "Class starts at 09:00"}
  }
}
```
Each path addresses a node; subscriptions can receive changes. Avoid excessive nesting and broad data reads. Firestore uses documents inside collections, with optional subcollections. A document may contain typed fields, maps and arrays. Example path: notices/101; fields: content and publishedAt. Indexes and query restrictions differ from relational SQL joins.

## Access and security
Firebase Authentication establishes identity; Security Rules determine allowed client reads and writes. A client SDK is not a trusted authorization boundary. Admin/server SDKs have separate privileged access semantics. Example Realtime Database rules for a deliberately limited profile-only demo:
```json
{
  "rules": {
    ".read": false,
    ".write": false,
    "users": {
      "$uid": {
        ".read": "auth != null && auth.uid == $uid",
        ".write": false
      }
    }
  }
}
```
Root denies access by default. $uid is a wildcard path key; auth is the authenticated user context; auth.uid is the authenticated identity; equality restricts reading to the corresponding user's node. This example grants no client writes and is not a full application policy. Rules are not filters; design paths and queries for permitted access. Test rules using the Local Emulator Suite before deployment, if needed. Avoid public test-mode rules, exposed privileged keys and sensitive data in broadly readable nodes. Firebase project configuration is not equivalent to a privileged server credential.

## Comparison with Supabase
Firebase Realtime Database and Firestore use non-relational models; Supabase's database is PostgreSQL. Firebase Security Rules and PostgreSQL RLS are distinct mechanisms. Both require proper authentication, authorization, data validation and isolation. ProTrack-MX already uses Supabase; no migration is proposed.

## Cost and verification
Managed services may offer limited no-cost usage but quotas and paid features vary. No account or cloud resource is necessary for this conceptual lesson. Examples were not executed. References: [Realtime Database](https://firebase.google.com/docs/database/), [JSON tree structure](https://firebase.google.com/docs/database/web/structure-data), [Firestore data model](https://firebase.google.com/docs/firestore/data-model), [Security Rules](https://firebase.google.com/docs/database/security), [Local Emulator Suite](https://firebase.google.com/docs/emulator-suite).
