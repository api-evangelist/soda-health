---
name: Browse Soda Health / Evermore product documentation
description: Authenticate with a scoped token, then list, fetch, and search the Evermore Docs Portal documentation corpus.
api: openapi/soda-health-docs-portal-openapi-original.json
operations:
  - docs_verify_api_docs_verify_post
  - docs_folders_api_docs_folders_get
  - docs_inventory_api_docs_inventory_get
  - docs_page_api_docs_pages__page_id__get
  - docs_search_api_docs_search_get
---

# Browse Soda Health / Evermore documentation

Base URL: `https://docs.evermoreoutcomes.com`. All `/api/docs/*` calls require
`Authorization: Bearer <token>`.

## Steps

1. **Get a token.** Obtain a scoped magic-link token from an Evermore
   Implementation Manager, or complete OIDC SSO via `GET /docs/auth/login`.
   Optionally validate it with `docs_verify_api_docs_verify_post`
   (`POST /api/docs/verify`).
2. **Discover structure.** Call `docs_folders_api_docs_folders_get`
   (`GET /api/docs/folders`) for the folder tree, or
   `docs_inventory_api_docs_inventory_get` (`GET /api/docs/inventory`) for the
   full page inventory. The token's scope (`all` / `folder` / `page`) limits what
   is returned.
3. **Read a page.** Call `docs_page_api_docs_pages__page_id__get`
   (`GET /api/docs/pages/{page_id}`) with a Confluence page ID from the inventory.
4. **Search.** Call `docs_search_api_docs_search_get` (`GET /api/docs/search`)
   with a query to find relevant pages.

## Rules

- Auth: HTTP Bearer only. A missing/invalid token returns `401 {"detail": "Authentication required"}`.
- Bad parameters return `422` with a `detail[]` list — fix the fields in `loc` and retry.
- No idempotency key and no pagination are documented; treat GETs as safe/repeatable.
