---
title: Firebolt APIs
---

[![semantic-release: conventional](https://img.shields.io/badge/semantic--release-conventional-e10079?logo=semantic-release)](https://github.com/semantic-release/semantic-release)

# Firebolt APIs

Firebolt APIs are defined by [OpenRPC schemas](https://spec.open-rpc.org).

The schemas are used to generate SDK and Documentation artifacts.

# Quick-Start

To generate OpenRPC and SDK artifacts, simply execute the following commands

```
npm install
npm run dist
```

This will:

- Identify each [SDK](./src/sdks/) and its configuration ([Example configuration](src/sdks/core/sdk.config.json))
- Generate each SDK's final OpenRPC using the [OpenRPC Templates](./src/openrpc/)
  - The OpenRPC will be created in the SDK's "dist" directory. Ex: "./src/sdks/core/dist"
- Use each SDK's final OpenRPC to generate the SDK using firebolt-openRPC's SDK Templates
  - The SDK code will be created in the SDK's "build" directory. Ex: "./src/sdks/core/build"
  - Note: Only the Javascript SDK is generated as of this writing
- Run Unit Test cases against the generated SDKs to ensure proper functionality
