# vita-ingest Tinfoil Config

Public Tinfoil deployment manifest for the debug/staging `vita-ingest` enclave.

This repository intentionally contains no secret values. Secret names in
`tinfoil-config.yml` must be populated in the Tinfoil dashboard before deploy.

`vita-ingest` is not a private-AI brain. It only handles ingestion surfaces:

- wearable OAuth and fetch for Oura, Whoop, and Withings
- raw lab extraction and normalization
- inert `410 disabled_by_private_ai_cutover` compatibility responses for legacy private-AI proxy routes

Private AI chat, memory, protocol generation, supplement parsing, condition
suggestion, and research orchestration remain owned by `vita-agent`.

## Current Debug Image

```text
ghcr.io/vitadao/vita-ingest:sha-5a37f06@sha256:f48f0e25b09d4a5fb88c708dea2f6b22d80217d600fc5de8ffe0126d371b1c23
```

## Deploy Notes

1. Create the Tinfoil debug service from this public repo.
2. Add all secrets listed in `tinfoil-config.yml` in the Tinfoil dashboard.
3. After Tinfoil creates the debug URL, update `TINFOIL_PUBLIC_URL` in
   `tinfoil-config.yml` to that exact URL and redeploy.
4. Register the same callback URLs in the wearable vendor dashboards.
5. Keep `DEFAULT_FRONTEND_URL` and `ALLOWED_REDIRECT_ORIGINS` scoped to debug
   and staging app hosts so OAuth callbacks return to the app that started the
   connection.

## Current Debug OAuth Callback URLs

The wearable vendor dashboards must match these URLs exactly for the current
debug `vita-ingest` container. Do not add trailing slashes or query params.

```text
Oura:
https://vitadaovita-ingest-tinfoil-config.debug.vitality-now.containers.tinfoil.dev/api/wearable/oura/callback

WHOOP:
https://vitadaovita-ingest-tinfoil-config.debug.vitality-now.containers.tinfoil.dev/api/wearable/whoop/callback

Withings:
https://vitadaovita-ingest-tinfoil-config.debug.vitality-now.containers.tinfoil.dev/api/wearable/withings/callback
```

Use separate OAuth apps/client IDs for debug/staging and production when the
production non-debug `vita-ingest` URL exists.

## Current Debug Frontend Redirect Origins

After vendor OAuth succeeds, `vita-ingest` redirects the browser back only to
allowlisted app origins encoded in the OAuth `state` value.

```text
DEFAULT_FRONTEND_URL=https://staging-app.vitadao.com
ALLOWED_REDIRECT_ORIGINS=https://app.vitadao.com,https://staging-app.vitadao.com,http://localhost:8080,http://localhost:5173,http://localhost:3000
```
