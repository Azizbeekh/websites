---
title: Developers Overview
---

# Developers Overview

Welcome to the developer documentation for the Rikka Apps websites repo.

- This site is built with VuePress 1.x.
- Sub-sites live under the repository root: `www`, `appops`, `shizuku`, `storage_redirect`.
- A webhook service in `webhooks/` rebuilds sites and purges Cloudflare cache after pushes.

What you'll find here:

- Project structure and local development
- Webhook API reference and setup
- Reusable Vue components available in themes

## Local development

Install dependencies at the repo root:

```bash
npm install
```

Run any site locally:

```bash
npm run www:dev
# or
npm run appops:dev
npm run shizuku:dev
npm run sr:dev
```

Build for production:

```bash
npm run www:build
```

Directory overview:

- `www/`: Main landing site and knowledge base
- `appops/`, `shizuku/`, `storage_redirect/`: Product sites
- `webhooks/`: Node service handling GitHub webhooks to build and purge cache

