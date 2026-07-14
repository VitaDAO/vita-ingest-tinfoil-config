# vita-ingest Tinfoil Config

Public Tinfoil deployment manifest for the production `vita-ingest` enclave.

This repository intentionally contains no secret values. Secret names in
`tinfoil-config.yml` must be populated in the Tinfoil dashboard before deploy.

`vita-ingest` is not a private-AI brain. It only handles ingestion surfaces:

- wearable OAuth and fetch for Oura, Whoop, and Withings
- raw lab extraction and normalization

Private AI chat, memory, protocol generation, supplement parsing, condition
suggestion, and research orchestration remain owned by `vita-agent`.

## Current Image

```text
ghcr.io/vitadao/vita-ingest:sha-47a04ab@sha256:efe8155604cbbbe0d918bd0316a89f839e9a42ef439cfd368d3c3c8631a945da
```

## Deploy Notes

This repository is the production attestation source consumed by the app's
`SecureClient`. Never publish a staging or debug config as a release here:
the latest release defines the measurement production clients trust. Use a
separate config repository for staging.

1. Create or update the non-debug Tinfoil service from this public repo.
2. Add all secrets listed in `tinfoil-config.yml` in the Tinfoil dashboard.
3. After Tinfoil creates the production URL, update `TINFOIL_PUBLIC_URL` in
   `tinfoil-config.yml` to that exact URL and redeploy.
4. Register the same callback URLs in the wearable vendor dashboards.
5. Keep `DEFAULT_FRONTEND_URL` and `ALLOWED_REDIRECT_ORIGINS` scoped to
   `app.vitadao.com` and the staging host used for production-shaped tests.
6. Optional: set `SENTRY_DSN` in the Tinfoil dashboard to enable scrubbed error
   monitoring once the deployed vita-ingest image supports the Sentry runtime
   hook. Do not commit the DSN value into this public repo.

## Monitoring

`SENTRY_ENVIRONMENT=production` and zero trace sampling are configured in the
public manifest. `SENTRY_DSN` is a Tinfoil secret. vita-ingest events must stay
metadata-only: no lab file text, wearable payloads, OAuth tokens, cookies,
request bodies, or Supabase service-role details should be sent to Sentry.

## Production OAuth Callback URLs

The wearable vendor dashboards must match these URLs exactly for the current
production `vita-ingest` container. Do not add trailing slashes or query params.

```text
Oura:
https://vita-ingest.vitality-now.containers.tinfoil.dev/api/wearable/oura/callback

WHOOP:
https://vita-ingest.vitality-now.containers.tinfoil.dev/api/wearable/whoop/callback

Withings:
https://vita-ingest.vitality-now.containers.tinfoil.dev/api/wearable/withings/callback
```

Use separate OAuth apps/client IDs for debug/staging and production.

## Production Frontend Redirect Origins

After vendor OAuth succeeds, `vita-ingest` redirects the browser back only to
allowlisted app origins encoded in the OAuth `state` value.

```text
DEFAULT_FRONTEND_URL=https://app.vitadao.com
ALLOWED_REDIRECT_ORIGINS=https://app.vitadao.com,https://staging-app.vitadao.com
```

## Exposed Routes

Production `vita-ingest` exposes only:

- `/health`
- `/api/parse-lab-results`
- `/api/summarize-screening-report`
- wearable OAuth and fetch routes under `/api/wearable/{oura,whoop,withings}/...`

Legacy private-AI proxy routes are intentionally not exposed by this manifest.
