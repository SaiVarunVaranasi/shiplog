# 2. Team roles are stored in the application database

- **Status:** Accepted
- **Date:** 2026-10-18

## Context
The initial sketch modelled Global Admin, Release Manager and Release Team Member as three user roles. But Manager and Member only make sense **per team**: a user can manage Team A and be a member of Team B. A global role claim in the token cannot express this.

## Decision
- `GlobalAdmin` is a role in the identity provider and is carried in the access token.
- Team roles (`Manager`, `Member`) are stored in ShipLog as `TeamMembership(TeamId, UserId, Role)`.
- A team can have one or more managers and must always keep at least one.
- Members belong to the team, not to a manager.
- Team-scoped writes use resource-based authorization: Global Admin, or the required role in the resource's owning team.

## Consequences
- A user can hold different roles in different teams.
- Team role changes take effect immediately, without re-issuing tokens.
- Authorization requires a database lookup per write request. Acceptable at this scale; can be cached later.
- The "last manager" invariant must be enforced in the domain and covered by tests.
