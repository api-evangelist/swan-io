# Swan (swan-io)

Swan is a European embedded-banking / Banking-as-a-Service (BaaS) platform, headquartered in France, that lets companies embed accounts, IBANs, cards, and SEPA payments into their own products. Swan holds an Electronic Money Institution (EMI) license.

## Access model (read this first)

- **Transport is GraphQL, not REST.** Swan exposes a single GraphQL API. You send HTTPS `POST` requests with a GraphQL query or mutation body. There is no REST resource API. The primary machine-readable artifact in this repo is the GraphQL SDL at [`graphql/swan-io.graphql`](graphql/swan-io.graphql).
- **Two GraphQL endpoints per environment:**
  - **Partner** (`https://api.swan.io/live-partner/graphql`) — requires an authenticated session / access token.
  - **Unauthenticated** (`https://api.swan.io/live-unauthenticated/graphql`) — used for the onboarding process before a user has authenticated.
  - Sandbox mirrors: `https://api.swan.io/sandbox-partner/graphql`, `https://api.swan.io/sandbox-partner-admin/graphql`, `https://api.swan.io/sandbox-unauthenticated/graphql`.
- **Auth is OAuth2 + Bearer.** Use the **client credentials** flow for server-to-server access and the **authorization code** flow for user access tokens. Send the token as `Authorization: Bearer $ACCESS_TOKEN`. Get a Client ID / Client Secret from the Swan dashboard under **Developers > API**. Token exchange is at `https://oauth.swan.io/oauth2`.
- **Backend-only.** Swan blocks API calls made directly from a frontend; all calls must come from your backend.
- **Partner / Project scoping + consent.** Access is scoped to a partner **Project**. Sensitive mutations (adding memberships, issuing cards, initiating payments) return a **consent** with a Swan-hosted URL that the user must approve (Strong Customer Authentication) before the operation runs.
- **Sandbox vs Live are isolated.** Access tokens, user credentials, and URLs do not carry across environments — "test in Sandbox, build in Live."
- **Rate limit:** 2,000 requests every 5 minutes per IP.
- **Events:** delivered via HTTP **webhooks** — there is no documented GraphQL subscription or WebSocket (`wss://`) surface.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/swan-io/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/swan-io/refs/heads/main/apis.yml)

## Tags

- Embedded Banking
- Banking as a Service
- BaaS
- Payments
- Accounts
- Cards
- IBAN
- SEPA
- Europe
- France
- Fintech
- GraphQL

## Timestamps

- **Created:** 2026-07-12
- **Modified:** 2026-07-12

## APIs

The surface below is one GraphQL API grouped into product areas. Every entry points at the same GraphQL SDL and collections; the base URL is the GraphQL endpoint (POST), not a REST route.

### Swan Accounts API

Create and manage bank accounts and their account holders - open accounts, read balances and status, and access the IBAN and BIC distributed to each account.

- **Human URL:** [https://docs.swan.io/api/services/accounts](https://docs.swan.io/api/services/accounts)
- **Base URL:** `https://api.swan.io/live-partner/graphql`

#### Tags

- Accounts
- Account Holders
- IBAN

#### Properties

- [Documentation](https://docs.swan.io/developers/)
- [API Reference](https://api-reference.swan.io/)
- [GraphQL](graphql/swan-io.graphql) — [GraphQL SDL](https://spec.graphql.org/)
- [Postman Collection](collections/swan-io.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/swan-io.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Swan Account Memberships API

Bind users to accounts and manage their permissions - `addAccountMemberships` (up to 200 with one consent), `suspendAccountMembership`, and `resumeAccountMembership`.

- **Human URL:** [https://docs.swan.io/api/services/account-memberships](https://docs.swan.io/api/services/account-memberships)
- **Base URL:** `https://api.swan.io/live-partner/graphql`

#### Tags

- Account Memberships
- Permissions
- Users

#### Properties

- [Documentation](https://docs.swan.io/developers/)
- [API Reference](https://api-reference.swan.io/mutations/resume-account-membership/)
- [GraphQL](graphql/swan-io.graphql) — [GraphQL SDL](https://spec.graphql.org/)
- [Postman Collection](collections/swan-io.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/swan-io.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Swan Cards API

Issue and manage virtual and physical payment cards. `addCards` adds up to 200 cards (including physical printing) with a single consent; further mutations activate, suspend, and cancel cards and set spending limits.

- **Human URL:** [https://docs.swan.io/api/services/cards](https://docs.swan.io/api/services/cards)
- **Base URL:** `https://api.swan.io/live-partner/graphql`

#### Tags

- Cards
- Card Issuing
- Payments

#### Properties

- [Documentation](https://docs.swan.io/developers/)
- [API Reference](https://api-reference.swan.io/)
- [GraphQL](graphql/swan-io.graphql) — [GraphQL SDL](https://spec.graphql.org/)
- [Postman Collection](collections/swan-io.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/swan-io.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Swan Payments API

Initiate and track SEPA payments. `initiateCreditTransfers` sends SEPA Credit Transfers, including SEPA Instant via the `isInstant` flag; direct-debit mutations set up mandates and collect SEPA Direct Debits.

- **Human URL:** [https://docs.swan.io/api/services/payments](https://docs.swan.io/api/services/payments)
- **Base URL:** `https://api.swan.io/live-partner/graphql`

#### Tags

- Payments
- SEPA
- Credit Transfer
- Direct Debit

#### Properties

- [Documentation](https://docs.swan.io/developers/)
- [API Reference](https://api-reference.swan.io/)
- [GraphQL](graphql/swan-io.graphql) — [GraphQL SDL](https://spec.graphql.org/)
- [Postman Collection](collections/swan-io.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/swan-io.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Swan Transactions API

Read the transaction ledger for an account - list and filter transactions through Relay-style connections, inspecting status (Pending, Booked, Rejected, Canceled), amounts, counterparties, and the payment or card that produced them.

- **Human URL:** [https://docs.swan.io/api/services/transactions](https://docs.swan.io/api/services/transactions)
- **Base URL:** `https://api.swan.io/live-partner/graphql`

#### Tags

- Transactions
- Statements
- Ledger

#### Properties

- [Documentation](https://docs.swan.io/developers/)
- [API Reference](https://api-reference.swan.io/)
- [GraphQL](graphql/swan-io.graphql) — [GraphQL SDL](https://spec.graphql.org/)
- [Postman Collection](collections/swan-io.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/swan-io.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Swan Onboarding API

Onboard individual and company account holders before they have a session. Started against the **Unauthenticated** endpoint, onboarding mutations create and update an onboarding, attach supporting documents, and finalize it - triggering KYC/KYB and, on approval, IBAN distribution.

- **Human URL:** [https://docs.swan.io/api/services/onboarding](https://docs.swan.io/api/services/onboarding)
- **Base URL:** `https://api.swan.io/live-unauthenticated/graphql`

#### Tags

- Onboarding
- KYC
- Account Opening

#### Properties

- [Documentation](https://docs.swan.io/developers/)
- [API Reference](https://api-reference.swan.io/)
- [GraphQL](graphql/swan-io.graphql) — [GraphQL SDL](https://spec.graphql.org/)
- [Postman Collection](collections/swan-io.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/swan-io.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Swan Users and Consents API

Read users and drive Swan's Strong Customer Authentication consent flow. Sensitive mutations return a consent that the user must approve via a Swan-hosted consent URL before the operation executes.

- **Human URL:** [https://docs.swan.io/api/consent](https://docs.swan.io/api/consent)
- **Base URL:** `https://api.swan.io/live-partner/graphql`

#### Tags

- Users
- Consents
- Authorization

#### Properties

- [Documentation](https://docs.swan.io/developers/)
- [API Reference](https://api-reference.swan.io/)
- [GraphQL](graphql/swan-io.graphql) — [GraphQL SDL](https://spec.graphql.org/)
- [Postman Collection](collections/swan-io.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/swan-io.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [Domain Security](security/swan-io-domain-security.yml)
- [Authentication](authentication/swan-io-authentication.yml)
- [GitHub Organization](https://github.com/swan-io)
- [LinkedIn](https://www.linkedin.com/company/swanio)
- [Website](https://www.swan.io)
- [Documentation](https://docs.swan.io)
- [Plans](plans/swan-io-plans-pricing.yml)
- [Rate Limits](rate-limits/swan-io-rate-limits.yml)
- [Fin Ops](finops/swan-io-finops.yml)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
