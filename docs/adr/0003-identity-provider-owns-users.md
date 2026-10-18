# 3. The identity provider owns users and credentials

- **Status:** Accepted
- **Date:** 2026-10-18

## Context
User story US-02 requires Global Admins to "manage users". Building user registration, password storage and token issuing in the application is a security risk and out of scope for this project.

## Decision
- Users and credentials live in an OIDC identity provider: Keycloak for local and cluster development, Entra ID for real Azure. The application only depends on the OIDC standard; switching providers is configuration only.
- ShipLog keeps a local `User` record keyed by the token's `sub` claim, created on first login (just-in-time provisioning) and updated from token claims.
- "Manage users" in ShipLog means assigning team memberships and deactivating users.

## Consequences
- No password or token handling code in ShipLog.
- A user must log in once before they can be added to a team. Pre-provisioning through the identity provider's admin API is a possible future extension.
- Deactivation in ShipLog blocks writes in ShipLog only; disabling login is done in the identity provider.
