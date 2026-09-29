# Vita Ingest staging Tinfoil config

This branch contains the staging manifest for the debug `staging-vita-ingest`
container. Production stays on the `main` branch of this same repository.

This repository intentionally contains no secret values. Secret names in
`tinfoil-config.yml` must be populated in the Tinfoil dashboard before deploy.

- Production tags use `vX.Y.Z` and remain normal releases.
- Staging tags use `staging-vX.Y.Z` and remain prereleases.
- Both tags may pin the same immutable Docker image while using different
  environment settings and secret names.

`vita-ingest` is not a private-AI brain. It only handles ingestion surfaces:

- wearable OAuth and fetch for Oura, Whoop, and Withings
- raw lab extraction and normalization

Private AI chat, memory, protocol generation, supplement parsing, condition
suggestion, and research orchestration remain owned by `vita-agent`.

## Current Image

```text
ghcr.io/vitadao/vita-ingest:sha-a2b6f4a@sha256:22a50ed8a67e15485c4e464d62c791e586251169ff7e7ef3c7acde647ac3494a
```

## Deploy Notes

Create lightweight `staging-v*` tags only from this branch. The workflow marks
them as prereleases, so they cannot replace the normal production release.
Attach only the staging secret names declared in `tinfoil-config.yml`.

## Monitoring

`SENTRY_ENVIRONMENT=staging` and zero trace sampling are configured in this
manifest. `SENTRY_DSN` is a Tinfoil secret. vita-ingest events must stay
metadata-only: no lab file text, wearable payloads, OAuth tokens, cookies,
request bodies, or Supabase service-role details should be sent to Sentry.

PostHog receives only the closed `server_request_completed` schema with coarse
status, result, and duration fields. Health checks, HEAD requests, and CORS
preflights are excluded. The project key is mounted from the Tinfoil vault;
no account identifier, route, request body, IP, or health data is captured.

## Staging OAuth callback URLs

The wearable vendor dashboards must match these URLs exactly for the current
staging `vita-ingest` container. Do not add trailing slashes or query params.

```text
Oura:
https://staging-vita-ingest.debug.vitality-now.containers.tinfoil.dev/api/wearable/oura/callback

WHOOP:
https://staging-vita-ingest.debug.vitality-now.containers.tinfoil.dev/api/wearable/whoop/callback

Withings:
https://staging-vita-ingest.debug.vitality-now.containers.tinfoil.dev/api/wearable/withings/callback
```

Use separate OAuth apps/client IDs for debug/staging and production.

## Staging frontend redirect origins

After vendor OAuth succeeds, `vita-ingest` redirects the browser back only to
allowlisted app origins encoded in the OAuth `state` value.

```text
DEFAULT_FRONTEND_URL=https://staging-app.vitadao.com
ALLOWED_REDIRECT_ORIGINS=https://staging-app.vitadao.com
```

## Exposed Routes

Staging `vita-ingest` exposes only:

- `/health`
- `/api/parse-lab-results`
- `/api/summarize-screening-report`
- wearable OAuth and fetch routes under `/api/wearable/{oura,whoop,withings}/...`

Legacy private-AI proxy routes are intentionally not exposed by this manifest.
