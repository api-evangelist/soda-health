---
name: Issue a scoped Evermore docs magic link
description: Implementation-Manager flow to mint a scoped, time-boxed magic-link access token for a customer to reach the Evermore Docs Portal.
api: openapi/soda-health-docs-portal-openapi-original.json
operations:
  - docs_login_api_docs_login_post
  - docs_magic_link_api_docs_magic_link_post
  - docs_verify_api_docs_verify_post
---

# Issue a scoped docs magic link

Base URL: `https://docs.evermoreoutcomes.com`.

## Steps

1. **Authenticate as an Implementation Manager.** Establish a bearer session
   (`docs_login_api_docs_login_post` — `POST /api/docs/login`, or OIDC SSO).
2. **Mint the link.** Call `docs_magic_link_api_docs_magic_link_post`
   (`POST /api/docs/magic-link`) with a `MagicLinkRequest`:
   - `customer_name` (required): display name embedded in the token.
   - `scope`: `all` | `folder` | `page` (default `all`).
   - `folder_id`: Confluence label — required when `scope=folder`.
   - `page_id`: Confluence page ID — required when `scope=page`.
   - `expires_in_minutes`: default `43200` (30d); presets `129600` (90d),
     `5256000` (~10y). Out-of-range values clamp to the 30-day default.
3. **Hand off / verify.** The customer opens the link; the token is validated by
   `docs_verify_api_docs_verify_post` (`POST /api/docs/verify`).

## Rules

- Auth: HTTP Bearer. Scope is carried explicitly on the token; `email` and
  `client_types` are not part of the request.
- A malformed request (e.g. `scope=folder` without `folder_id`) returns `422`.
- Choose the narrowest scope and shortest reasonable expiry for the use case.
