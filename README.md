# NORG Edge Router — Bunny Edge Scripting

This repo holds NORG's edge router for sites served through **bunny.net**. It installs on **your own** pull zone and runs in your Bunny account. NORG never receives your credentials.

**Install guide, configuration and troubleshooting: [`bunny/README.md`](bunny/README.md)**

> **On Cloudflare?** Use the [Cloudflare Worker](https://github.com/norgai/norg-edge-mirror-cloudflare-deploy) instead. Other platforms are listed under [Other platforms](#other-platforms).

Current version: **0.2.2**, set by `EDGE_SCRIPT_VERSION` in `bunny/src/lib/config.js`.

---

## What it does

| Visitor | What they get |
|---|---|
| A person in a browser | Your site, unchanged |
| Googlebot, Bingbot and other search crawlers | Your site, unchanged |
| An AI agent, on a page NORG has rendered | The NORG render, at your own URL, with no redirect |
| An AI agent, on a page not rendered yet | Your page stripped to its facts, while NORG renders it |

A Bunny middleware script runs on a cache miss only, so the installer keeps your HTML out of the pull zone's cache. Read [The cache is the hazard](bunny/README.md#the-cache-is-the-hazard) before installing. When `EDGE_HOSTNAME` is set, an edge rule also tells your origin the public hostname in `X-Norg-Public-Host`.

---

## Layout

```
bunny/
  src/index.js             the only file that touches the Bunny SDK
  src/router.js            the request pipeline
  src/lib/                 Bunny adapters: config, origin, request
  dist/edge-router-bunny.js  committed bundle, which install.mjs uploads
  install.mjs              the installer
  build.mjs                builds dist/
  tests/                   node:test suites
core/                      provider-neutral router logic
workers/, aws/, src/, wrangler.jsonc, README.shopify.md
                           files from other platforms, carried over from the original repo
```

The Bunny code imports `core/` and `workers/lib/`, so those have to stay. The other platforms' files here are older copies, not their releases.

## Development

```bash
node --test bunny/tests/*.test.mjs   # nothing to install
node bunny/build.mjs                 # rebuild dist/edge-router-bunny.js
```

The tests rebuild the bundle and fail on any difference, so commit `dist/` with any source change.

## Releasing

1. Bump `EDGE_SCRIPT_VERSION` in `bunny/src/lib/config.js`, rebuild, and run the tests.
2. Tag `bunny-vX.Y.Z` and publish a GitHub release with `edge-router-bunny.js` attached.
3. In content-craft, update `bunny_pin.py` and `EDGE_WORKER_VERSION_BUNNY`.

---

## Other platforms

| Platform | Where it lives |
|---|---|
| **Bunny Edge Scripting** | This repo. Install guide: [`bunny/README.md`](bunny/README.md) |
| **Cloudflare** | [`norg-edge-mirror-cloudflare-deploy`](https://github.com/norgai/norg-edge-mirror-cloudflare-deploy) |
| **Next.js and Vercel** | [`norg-edge-mirror-nextjs-deploy`](https://github.com/norgai/norg-edge-mirror-nextjs-deploy) |
| **AWS CloudFront** | [`norg-edge-mirror-cloudfront-deploy`](https://github.com/norgai/norg-edge-mirror-cloudfront-deploy) |
| **Fastly Compute** | [`norg-edge-mirror-fastly-deploy`](https://github.com/norgai/norg-edge-mirror-fastly-deploy) |

## Support

For questions about your install, your Site ID or Site Key, contact your NORG account contact.
