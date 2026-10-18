# 6. All authenticated users can read; writes are team-scoped

- **Status:** Accepted
- **Date:** 2026-10-18

## Context
The user stories define who may change data but not who may view it. Release visibility across teams is useful (e.g. dependencies between products), and the data is not sensitive.

## Decision
- Every authenticated user can read all teams, products, releases and deployments.
- All writes are restricted per the permission matrix in `docs/requirements/authorization.md`.
- Team Managers can create products for their own team; Global Admins for any team.

## Consequences
- Simpler queries: no per-team filtering on reads.
- Denied writes return `403`, not `404`, since resources are visible anyway.
- If confidential products are needed later, a per-team visibility flag would supersede this ADR.
