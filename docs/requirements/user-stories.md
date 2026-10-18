# User Stories

ShipLog tracks products, their releases, and where those releases are deployed. Access is organised around **teams**.

## Roles

| Role | Scope | Where it lives |
|---|---|---|
| Global Admin | Whole system | Identity provider (role claim in the token) |
| Team Manager | One team | ShipLog database (`TeamMembership`) |
| Team Member | One team | ShipLog database (`TeamMembership`) |
| Authenticated user | Whole system, read-only | Any user with a valid token |

A user can hold different roles in different teams (e.g. Manager of Team A, Member of Team B).

---

## Epic 1 — Teams and users

### US-01 — Create teams
As a **Global Admin**, I want to create release teams, so that products and people can be organised by ownership.

**Acceptance criteria**
- Given I am a Global Admin, when I create a team with a unique name, then the team is created and I can assign its first manager.
- Given a team name already exists, when I create a team with the same name, then I get `409 Conflict`.
- Given I am not a Global Admin, when I try to create a team, then I get `403 Forbidden`.

### US-02 — Manage users
As a **Global Admin**, I want to manage users, so that the right people have access.

**Acceptance criteria**
- Given I am a Global Admin, when I add a user to any team as Manager or Member, then the membership is created.
- Given I am a Global Admin, when I deactivate a user, then that user can no longer perform any write action, and their past actions remain visible.
- Users are created in the identity provider, not in ShipLog. ShipLog creates its local user record on first login.

### US-03 — Manage my team's members
As a **Team Manager**, I want to add and remove users in my team, so that my team's access stays current.

**Acceptance criteria**
- Given I manage Team A, when I add a user to Team A as Member or Manager, then the membership is created.
- Given I manage Team A, when I try to change memberships of Team B, then I get `403 Forbidden`.
- Given I am the last manager of Team A, when I try to remove myself or demote myself, then the request is rejected (a team must keep at least one manager).

### US-04 — First login
As a **user**, when I log in for the first time, I want ShipLog to recognise me, so that I can be added to teams.

**Acceptance criteria**
- Given a valid token for an unknown subject, when I call the API, then a local user record is created from the token claims (`sub`, email, name).
- Given an existing user, when their email or name changes in the identity provider, then the local record is updated on next login.

---

## Epic 2 — Products

### US-05 — Create products
As a **Global Admin** or **Team Manager**, I want to create products for a team, so that releases can be tracked per product.

**Acceptance criteria**
- Given I am a Global Admin, when I create a product for any team, then it is created.
- Given I manage Team A, when I create a product for Team A, then it is created.
- Given I manage Team A, when I create a product for Team B, then I get `403 Forbidden`.
- Given a product name already exists in the same team, then I get `409 Conflict`. The same name in a different team is allowed.

---

## Epic 3 — Releases

### US-06 — Manage releases as Global Admin
As a **Global Admin**, I want to add, update and delete releases for any team, so that I can correct mistakes anywhere.

**Acceptance criteria**
- Given I am a Global Admin, when I create, update or delete a release for any product, then the change is applied.
- Deleting a release is a soft delete (see ADR-0005).

### US-07 — Manage releases as Team Manager
As a **Team Manager**, I want to add, update and delete releases for my team's products, so that I can run my team's release process.

**Acceptance criteria**
- Given I manage Team A, when I create, update or delete a release for a Team A product, then the change is applied.
- Given I manage Team A, when I do the same for a Team B product, then I get `403 Forbidden`.
- Given a version already exists for the product, when I create a release with the same version, then I get `409 Conflict`.

### US-08 — Manage releases as Team Member
As a **Team Member**, I want to add and update releases for my team's products, so that I can record my work.

**Acceptance criteria**
- Given I am a member of Team A, when I create or update a release for a Team A product, then the change is applied.
- Given I am a member of Team A, when I try to delete a release, then I get `403 Forbidden`.

### US-09 — View releases
As an **authenticated user**, I want to view all teams, products and releases, so that I know what is deployed where.

**Acceptance criteria**
- Given I am logged in, when I list releases for any product, then I see them, paged and filterable by status.
- Soft-deleted releases are not shown.
- Given I am not logged in, then I get `401 Unauthorized`.

---

## Epic 4 — Deployments *(depends on ADR-0004)*

### US-10 — Record deployments
As a **Team Member** or **Team Manager**, I want to record a deployment of a release to an environment, so that everyone can see where each release is running.

**Acceptance criteria**
- Given a release of my team's product, when I record a deployment to `Test`, then a deployment with status `Pending` is created.
- Given a release has no `Succeeded` deployment to `Test`, when I record a deployment to `Production`, then the request is rejected with a validation error.
- A deleted release cannot be deployed.

### US-11 — Initiate rollback
As a **Team Manager**, I want to mark a deployment as rolled back, so that the release history reflects reality.

**Acceptance criteria**
- Given I manage Team A and a Team A deployment is `Succeeded`, when I initiate rollback, then its status becomes `RolledBack`.
- Given I am a Team Member, when I initiate rollback, then I get `403 Forbidden`.
- Given a deployment is not `Succeeded`, when anyone initiates rollback, then the request is rejected.

---

## Out of scope (MVP)

- Creating users or resetting passwords from ShipLog (done in the identity provider)
- Email/Slack notifications to real recipients (the worker only processes and logs events)
- Multi-tenancy
- API versioning
