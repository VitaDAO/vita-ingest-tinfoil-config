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
ghcr.io/vitadao/vita-ingest:sha-5355d10@sha256:f427774620ea1a5f059d0b89aef4126a31497612bd2c36dced9b3fd5eb7dc42a
```

## Deploy Notes

1. Create the Tinfoil debug service from this public repo.
2. Add all secrets listed in `tinfoil-config.yml` in the Tinfoil dashboard.
3. After Tinfoil creates the debug URL, update `TINFOIL_PUBLIC_URL` in
   `tinfoil-config.yml` to that exact URL and redeploy.
4. Register the same callback URLs in the wearable vendor dashboards.

