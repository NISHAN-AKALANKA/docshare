# Docshare ERD

The Google Sheet acts as the database. Each tab below is a table.

```mermaid
erDiagram
  USERS {
    string username PK
    string password
    string displayName
    string role
    boolean active
  }

  INCOMING {
    string id PK
    string ref
    date dr
    datetime du
    string by FK
    string from
    string to
    string subj
    string desc
    string fileId
    string fileName
    string fileType
    string fileUrl
    string status
    string note
    datetime deAt
  }

  OUTGOING {
    string id PK
    string ref
    date ds
    datetime du
    string by FK
    string from
    string to
    string subj
    string desc
    string fileId
    string fileName
    string fileType
    string fileUrl
  }

  ASSIGNMENTS {
    string letterId FK
    string username FK
    string displayName
    string status
    datetime seen
  }

  NOTIFICATIONS {
    string id PK
    string username FK
    string title
    string message
    string letterId FK
    string ref
    string type
    boolean read
    datetime createdAt
  }

  SETTINGS {
    string key PK
    string value
  }

  USERS ||--o{ INCOMING : uploads
  USERS ||--o{ OUTGOING : uploads
  INCOMING ||--o{ ASSIGNMENTS : has
  USERS ||--o{ ASSIGNMENTS : receives
  USERS ||--o{ NOTIFICATIONS : receives
  INCOMING ||--o{ NOTIFICATIONS : triggers
  SETTINGS ||--|| SETTINGS : stores_counters
```

## Reference Number

The `ref` field is the office/document reference shown in the app.

When uploading an incoming or outgoing letter:

- enter an existing manual reference if the document already has one
- leave it blank to use the generated reference, such as `DEO_Batt_26_05_26_In_01`

## Attachments

Files are stored in Google Drive. The Sheet stores:

- `fileId`
- `fileName`
- `fileType`
- `fileUrl`

The binary file itself is not stored in the Sheet.
