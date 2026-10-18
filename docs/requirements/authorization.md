# Authorization

## Model

- **Global role** — `GlobalAdmin` is assigned in the identity provider and arrives as a role claim in the access token.
- **Team roles** — `Manager` and `Member` are stored in ShipLog's database (`TeamMembership`). They are checked per request against the team that owns the resource.
- **Read access** — every authenticated user can read all teams, products and releases. Only writes are scoped.

Authorization rule for any team-owned resource:

```
allowed = user.IsGlobalAdmin
       OR user has the required role in the resource's owning team
```

## Permission matrix

| Action | Global Admin | Team Manager | Team Member | Other authenticated users |
|---|---|---|---|---|
| Create teams | ✅ | ❌ | ❌ | ❌ |
| Deactivate users | ✅ | ❌ | ❌ | ❌ |
| Add/remove team members | ✅ any team | ✅ own team | ❌ | ❌ |
| Create products | ✅ any team | ✅ own team | ❌ | ❌ |
| Create/update releases | ✅ any team | ✅ own team | ✅ own team | ❌ |
| Delete releases (soft) | ✅ any team | ✅ own team | ❌ | ❌ |
| Record deployments | ✅ any team | ✅ own team | ✅ own team | ❌ |
| Initiate rollback | ✅ any team | ✅ own team | ❌ | ❌ |
| View teams, products, releases | ✅ | ✅ | ✅ | ✅ |

## Invariants

- A team must always have **at least one Manager**. Removing or demoting the last manager is rejected.
- A **deactivated** user is denied every write action, regardless of memberships.
- A user has **at most one role per team** (Manager or Member).

## Implementation notes

- Role-only checks (e.g. "create teams") use named policies: `.RequireAuthorization("GlobalAdmin")`.
- Team-scoped checks are **resource-based**: load the resource, resolve its owning team, then call `IAuthorizationService.AuthorizeAsync(user, team, requirement)` with a requirement such as `TeamRoleRequirement(Manager)`.
- Denied writes return `403 Forbidden`; missing or invalid tokens return `401 Unauthorized`. Do not return `404` to hide existence, since all users can read everything anyway.
