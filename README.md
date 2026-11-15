# Lingo Rider JavaScript SDK

Official JavaScript/TypeScript SDK for Lingo Rider.

> Lingo Rider keeps its technical name `toterslokalise` in packages, modules, classes and settings.

Lingo Rider lets applications fetch versioned translations over the air (OTA), switch locales at runtime, and continue working from cached or bundled translations when the network is unavailable.

## Features

- OTA translation updates
- Versioned releases
- Runtime locale switching
- Synchronous translation lookup
- Offline last-known-good cache
- Namespace support
- Pluralization
- Placeholder formatting
- SHA-256 artifact verification
- Same-origin protection
- TypeScript support

## Installation

```bash
npm install @totersapp/toterslokalise-sdk
```

### Releasing

Bump the version without npm's `v` tag prefix, then push the tag; the Release
workflow tests and publishes it to npmjs.com:

```bash
npm version 1.2.2 --tag-version-prefix=""
git push origin main 1.2.2
```

## Quick Start

```ts
import { createClient, MemoryCacheAdapter } from '@totersapp/toterslokalise-sdk';

const client = createClient({
  baseUrl: 'https://arcade.stage.toters-api.com/toters-lokalise/api',
  token: 'gl_sdk_your_public_read_token',
  projectId: 'your-project-id',
  environment: 'production',
  locale: 'en',
  cache: new MemoryCacheAdapter(),
});

await client.initialize();

const title = client.t('welcome', { namespace: 'common', fallback: 'Welcome' });
```

## Switching Locale

```ts
await client.setLocale('ar');

const title = client.t('welcome', { namespace: 'common', fallback: 'Welcome' });
```

## OTA and Offline Behavior

Lingo Rider validates OTA manifests and translation artifacts before replacing cached translations.

The SDK verifies project and environment scope, protocol version, artifact origin, artifact size, and SHA-256 checksum.

If an update fails, the existing last-known-good translations remain available. Translation lookup itself does not perform network requests.

## Build-only namespaces

Namespaces marked **Build only** in Lingo Rider are left out of OTA releases.
Download them in your CI/CD pipeline (one `<namespace>.json` per namespace, from
`GET /api/v1/projects/{projectId}/translations?environment=production&namespace=<name>&format=json`;
ready-made Bitbucket and GitHub Actions steps are on the Developer Hub) and bundle them
with the app. Lookups fall back to these bundled strings.

```ts
import { TotersLokaliseClient, bundledFromExports } from '@totersapp/toterslokalise-sdk';
import checkout from './TotersLokalise/checkout.json';

const client = new TotersLokaliseClient({ /* … */ bundled: bundledFromExports({ checkout }) });
```

## Requirements

- Modern browsers with Fetch API support
- Modern Node.js runtimes with Fetch API support
- TypeScript declarations are included

## Security

Use a Lingo Rider public SDK credential beginning with `gl_sdk_`.

Public SDK credentials are designed for client applications. Never embed administrative or private server credentials in browser or mobile applications.

## Documentation

Visit https://arcade.stage.toters-api.com/toters-lokalise/ for more information.

## Status

This SDK implements Lingo Rider OTA protocol v1.
