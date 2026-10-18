# 5. Releases are soft-deleted

- **Status:** Accepted
- **Date:** 2026-10-18

## Context
Release history is audit-relevant: who released what, and when. Hard deletes lose that history.

## Decision
- Deleting a release sets `IsDeleted`, `DeletedBy` and `DeletedAt` instead of removing the row.
- Deleted releases are excluded from queries by an EF Core global query filter.
- Deleted releases cannot be updated or deployed.
- Releases and deployments store `CreatedBy` / `DeployedBy` from the authenticated user.

## Consequences
- Full audit trail of changes.
- Unique constraints (e.g. version per product) must consider deleted rows. Decision: a deleted version cannot be reused.
- Queries that need deleted rows must call `IgnoreQueryFilters()` explicitly.
