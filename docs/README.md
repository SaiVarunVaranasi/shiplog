# ShipLog Documentation

| Section | Contents |
|---|---|
| [User stories](requirements/user-stories.md) | Roles, epics, stories with acceptance criteria, out of scope |
| [Authorization](requirements/authorization.md) | Permission matrix, invariants, implementation notes |
| [System overview](architecture/system-overview.md) | Components, request flow, environment terminology |
| [Domain model](architecture/domain-model.md) | ER diagram, relationships, status state machines, domain rules |

## Architecture Decision Records

| # | Decision | Status |
|---|---|---|
| [0001](adr/0001-record-architecture-decisions.md) | Record architecture decisions | Accepted |
| [0002](adr/0002-team-roles-stored-in-application-database.md) | Team roles are stored in the application database | Accepted |
| [0003](adr/0003-identity-provider-owns-users.md) | The identity provider owns users and credentials | Accepted |
| [0004](adr/0004-deployments-tracked-per-environment.md) | Deployments are tracked per environment in the domain | Accepted |
| [0005](adr/0005-soft-delete-for-releases.md) | Releases are soft-deleted | Accepted |
| [0006](adr/0006-read-access-for-all-authenticated-users.md) | All authenticated users can read; writes are team-scoped | Accepted |
