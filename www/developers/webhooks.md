---
title: Webhooks Service API
---

# Webhooks Service API

The `webhooks/` Node.js service listens for GitHub webhooks, runs `git pull`, builds affected VuePress sites, and purges Cloudflare cache for updated pages.

## Configuration

Create a `config.json` in the process working directory (same directory you run `node index.js` from):

```json
{
  "port": 9000,
  "secret": "<github_webhook_secret>",
  "sites": {
    "www": "rikka.app",
    "appops": "appops.rikka.app",
    "shizuku": "shizuku.rikka.app",
    "storage_redirect": "sr.rikka.app"
  },
  "cf_email": "<cloudflare_email>",
  "cf_key": "<cloudflare_api_key>",
  "cf_zone_id": "<cloudflare_zone_id>"
}
```

Ensure the process current working directory contains `config.json` because `index.js` loads it with `require(`${process.cwd()}/config.json`)`.

## HTTP Interface

- **Method**: `POST`
- **Path**: `/` (root)
- **Headers (production)**: `X-Hub-Signature: sha1=<HMAC>`
- **Body**: GitHub push event JSON
- **Auth**: HMAC-SHA1 signature over raw body with `secret` when `NODE_ENV=production`. Requests with bad signature return `403`.

### Request Example (GitHub push payload excerpt)

```json
{
  "commits": [
    {
      "added": ["www/README.md"],
      "removed": [],
      "modified": ["www/.vuepress/config.js"]
    }
  ]
}
```

### Response

The server streams logs and ends with code after `git pull` finishes. Build and purge steps continue asynchronously.

## Processing Flow

1. Validate signature in production.
2. Parse payload and compute target sites using top-level path segments in `added/removed/modified`.
3. `git pull` the repository.
4. For each affected site:
   - Build with VuePress programmatic API (`vuepress.createApp({ sourceDir })`).
   - Collect built file paths under `.vuepress/dist` and convert to `https://<host>/<path>` using `sites` mapping.
   - Purge the list of URLs in batches (up to 30 per request) via Cloudflare API.

## Public Functions

- `buildVuePress(sourcePath: string): Promise<void>`
  - Builds a VuePress site at `sourcePath`.
- `purgeCloudflareCache(urls: string[]): Promise<void>`
  - Purges a list of URLs using Cloudflare API, batching 30 per request.
- `collectTargets(body: PushPayload): Set<string>`
  - Computes target sites from GitHub push payload by first path segment.
- `collectFiles(parent: string, files: string[]): void`
  - Recursively enumerates files under `parent`.
- `collectUrls(target: string, path: string): Set<string>`
  - Builds absolute URLs for purging from built files for `target`.

## Run Locally

```bash
cd webhooks
npm install
npm run start-debug
# or production
npm run start-production
```

Set `NODE_ENV=production` to enforce signature validation.

## Security Notes

- Keep `secret`, Cloudflare `cf_key` secure and out of version control.
- Restrict network access to the webhook server or place behind a reverse proxy.

