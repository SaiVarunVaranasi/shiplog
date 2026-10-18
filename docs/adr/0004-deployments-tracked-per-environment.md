# 4. Deployments are tracked per environment in the domain

- **Status:** Accepted
- **Date:** 2026-10-18

## Context
The requirements mention two environments: Test and Production. This could mean where ShipLog itself runs, or where the releases it tracks are deployed. For a release tracker, the second is the core business value.

## Decision
- Add a `Deployment(ReleaseId, Environment, Status, DeployedBy, DeployedAt)` entity with environments `Test` and `Production`.
- Promotion rule: a release can be deployed to Production only after a `Succeeded` deployment to Test.
- Rollback is a deployment status (`Succeeded` → `RolledBack`), initiated by Team Managers or Global Admins only.
- To avoid confusion, the infrastructure uses the term **platform stage** (`dev`, `prod`) for where ShipLog runs.

## Consequences
- Release status describes the lifecycle; deployment status describes what runs where.
- The promotion rule and rollback permissions need unit and integration tests.
- If the timebox runs short, deployments are the first feature to defer.
