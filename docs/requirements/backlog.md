# Backlog — Deferred Requirements

Gaps found in a stakeholder review on 2026-10-18. **Not part of the MVP.** The MVP is scoped around the platform learning objectives; revisit these once the platform phases are done.

Suggested priority is in brackets: **[Next]** first candidates after MVP, **[Later]** stretch, **[Decide]** needs only a decision or ADR.

## Integration and data

| # | Item | Notes | Priority |
|---|---|---|---|
| B-01 | **Pipeline integration** | CI/CD reports releases and deployments via the API. Needs a machine identity (client credentials flow), scoped to a team. Dogfooding idea: ShipLog's own pipeline reports to ShipLog. | [Next] |
| B-02 | **Release content** | Release notes, git commit SHA, pipeline run ID, container image tag. | [Next] |
| B-03 | **Current-state view** | Latest successful deployment per product per environment ("what is in Production right now?"). | [Next] |

## Process and governance

| # | Item | Notes | Priority |
|---|---|---|---|
| B-04 | **Production approval (four-eyes)** | Production deployment requested by one person, approved by a manager who is not the requester. | [Later] |
| B-05 | **Emergency override** | Deploy to Production without a successful Test deployment. Manager only, mandatory reason, flagged in history. | [Later] |
| B-06 | **Audit log** | State transitions and membership changes with actor, timestamp, old and new values. Relevant for ISO audits. | [Later] |
| B-07 | **Orphaned teams** | What happens when the last manager is deactivated or leaves. | [Later] |
| B-08 | **Team and product lifecycle** | Move a product to another team; archive or dissolve a team and decide what happens to its products. | [Later] |

## Insights

| # | Item | Notes | Priority |
|---|---|---|---|
| B-09 | **DORA metrics** | Deployment frequency, lead time, change failure rate (rollbacks ÷ deployments), time to restore. Mostly derivable from existing data. | [Later] |

## Compliance and non-functional

| # | Item | Notes | Priority |
|---|---|---|---|
| B-10 | **GDPR: retention and erasure** | User names and emails are personal data. Common approach: pseudonymise the user record, keep IDs in history. | [Decide] |
| B-11 | **Non-functional requirements** | Expected users and load, response time targets, availability target, backup RPO/RTO. Needed as the basis for SLOs in the monitoring phase. | [Decide] |

## Open domain questions

| # | Question | Suggested default | Priority |
|---|---|---|---|
| B-12 | Version format: free text or validated SemVer? How are versions sorted? | Validated SemVer | [Decide] |
| B-13 | Environments: fixed (Test, Production) or configurable per product? | Fixed for MVP | [Decide] |
| B-14 | Can a failed deployment to Test be retried? | Yes | [Decide] |
| B-15 | Does a planned release have a target date? | Optional field | [Decide] |
