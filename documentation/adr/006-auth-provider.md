# ADR-006: Authentication and authorization provider

## Status
Accepted (2026-04-28)

## Context
We need a central authentication and authorization provider so operators can use single sign-on (SSO) across internal tools and, where practical, extend the same identity story toward Linux hosts.

The solution must balance resource use on our thin-client class hardware, breadth of integrations (OIDC/SAML/LDAP as needed), and operational complexity for a small team.

## Options considered

### Option A: Authentik

+ Broad protocol support and integrations suitable for a heterogenous tool stack
+ Includes LDAP-related capabilities useful for legacy or non-OIDC systems
+ Strong GUI for policy and application onboarding
- Higher baseline RAM use (on the order of gigabytes at steady state in typical installs), which matters on 8 GB nodes

### Option B: Authelia

+ Lower resource requirements than full-featured identity suites
+ Straightforward model for reverse-proxy-oriented authentication
- Narrower integration surface for complex enterprise-style scenarios
- Less “platform identity” depth if we outgrow proxy-centric patterns

### Option C: Keycloak

+ Very mature feature set and widespread enterprise adoption
+ Rich protocol coverage and extension points
- Operational complexity and resource footprint are high for our current scale
- Heavier day-two burden than we want while the platform is still stabilizing

## Decision

We chose **Authentik** as the identity provider.

Despite its heavier resource profile, it best matches our need for SSO across tools plus pragmatic LDAP-style compatibility, with an administration experience we can sustain while the service catalog grows.

## Consequences

- Positive: one place to model users, groups, and application access for internal services
- Positive: LDAP capabilities reduce dead-ends when a component cannot speak OIDC natively
- Negative: must budget RAM and placement so Authentik does not starve workload nodes
- Negative: identity becomes a critical dependency; backups, upgrades, and monitoring must be first-class

## References

- [Authentik documentation](https://docs.goauthentik.io/)
- [Authelia documentation](https://www.authelia.com/)
- [Keycloak documentation](https://www.keycloak.org/documentation)
- ADR-002: Infrastructure shape for service orchestration
