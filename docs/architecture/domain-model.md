# Domain Model

## Entity relationships

```mermaid
erDiagram
    TEAM ||--o{ TEAM_MEMBERSHIP : has
    USER ||--o{ TEAM_MEMBERSHIP : holds
    TEAM ||--o{ PRODUCT : owns
    PRODUCT ||--o{ RELEASE : has
    RELEASE ||--o{ DEPLOYMENT : "deployed as"

    TEAM {
        uuid Id PK
        string Name "unique"
    }
    USER {
        uuid Id PK
        string ExternalSubject "unique, token sub claim"
        string Email
        string DisplayName
        bool IsActive
    }
    TEAM_MEMBERSHIP {
        uuid TeamId PK, FK
        uuid UserId PK, FK
        string Role "Manager | Member"
    }
    PRODUCT {
        uuid Id PK
        uuid TeamId FK
        string Name "unique per team"
    }
    RELEASE {
        uuid Id PK
        uuid ProductId FK
        string Version "unique per product"
        string Status
        uuid CreatedBy FK
        datetimeoffset CreatedAt
        bool IsDeleted
        uuid DeletedBy FK
        datetimeoffset DeletedAt
    }
    DEPLOYMENT {
        uuid Id PK
        uuid ReleaseId FK
        string Environment "Test | Production"
        string Status
        uuid DeployedBy FK
        datetimeoffset DeployedAt
    }
```

`OutboxMessage (Id, Type, Payload, CreatedAt, ProcessedAt)` is a technical table for the worker and is not part of the domain.

## Relationships

| Relationship | Cardinality | Notes |
|---|---|---|
| Team → Product | 1 : n | A product belongs to exactly one team |
| Team ↔ User | n : m via `TeamMembership` | Membership carries the role (`Manager` / `Member`) |
| Team → Manager | 1 : n (at least 1) | A team must always keep at least one manager |
| Product → Release | 1 : n | Version is unique per product |
| Release → Deployment | 1 : n | One release can be deployed to several environments over time |

Members belong to the **team**, not to a manager. Removing a manager does not affect the other members.

## Release status

```mermaid
stateDiagram-v2
    [*] --> Planned
    Planned --> InProgress
    InProgress --> Ready
    InProgress --> Cancelled
    Planned --> Cancelled
    Ready --> [*]
    Cancelled --> [*]
```

Release status describes the release's lifecycle. Where it runs is tracked by deployments.

## Deployment status

```mermaid
stateDiagram-v2
    [*] --> Pending
    Pending --> Succeeded
    Pending --> Failed
    Succeeded --> RolledBack : Manager or Global Admin only
    Failed --> [*]
    RolledBack --> [*]
```

## Domain rules

1. A release can be deployed to `Production` only if it has a `Succeeded` deployment to `Test`.
2. A release must be `Ready` before it can be deployed.
3. A deleted release cannot be deployed or updated.
4. Only a `Succeeded` deployment can be rolled back.
5. All timestamps are stored as UTC `DateTimeOffset`.
