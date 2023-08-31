# AirPlay 2

Document Status: Draft

See [Firebolt Requirements Governance](../../governance.md) for more info.

| Contributor              | Organization   |
| ------------------------ | -------------- |
| Ramasamy Thalavay Pillai | Comcast        |
| Tim Dibben               | Sky            |
| Jeremy LaCivita          | Comcast        |

## 1. Overview
This document describes the requirements for supporting AirPlay 2.0 on Firebolt devices, typically TVs.

Apple provides two components that must be integrated for AirPlay support:

- AirPlay Luna App SDK
- AirPlay Bonjour Daemon

These components communicate via an opaque WebSocket protocol designed by Apple.

Outside of the WebSocket, there are several points of integration between Apple's components and Firebolt, which are covered here.

This document is written using the [IETF Best Common Practice 14](https://www.rfc-editor.org/rfc/rfc2119.txt), specifically:

The key words "**MUST**", "**MUST NOT**", "**REQUIRED**", "**SHALL**", "**SHALL NOT**", "**SHOULD**", "**SHOULD NOT**", "**RECOMMENDED**", "**NOT RECOMMENDED**", "**MAY**", and "**OPTIONAL**" in this document are to be interpreted as described in [BCP 14](https://www.rfc-editor.org/rfc/rfc2119.txt) [RFC2119] [RFC8174] when, and only when, they appear in all capitals, as shown here.

## 2. Table of Contents
- [1. Overview](#1-overview)
- [2. Table of Contents](#2-table-of-contents)
- [3. AirPlay APIs](#3-airplay-apis)
  - [3.1. Enabled](#31-enabled)
  - [3.2. Paired](#32-paired)
  - [3.3. State](#33-state)
- [4. AirPlay Process Provider](#4-airplay-process-provider)
  - [4.1. Enabled](#41-enabled)
  - [4.2. Paired](#42-paired)
- [5. AirPlay Experience Provider](#5-airplay-experience-provider)
  - [5.1. State](#51-state)
  - [5.2. Player](#52-player)
  - [5.3. Settings](#53-settings)

## 3. AirPlay APIs

### 3.1. Enabled
AirPlay 2 manages it's own settings via Apple's AirPlay Luna App SDK.

Apple does not expose setters for any AirPlay settings, only getters, so the Firebolt API reflects that.

The `AirPlay` module **MUST** have an `enabled` boolean property that is `readonly`. This setting has a corresponding notification for when the value changes.

The `enabled` property **MUST** return the value from the `<INSERT AIRPLAY API HERE>` API.

### 3.2. Paired
The `AirPlay` module **MUST** have a `paired` boolean property that is `readonly`. This value has a corresponding notification for when the paired status changes.

The `paired` property **MUST** return whether the Firebolt device is paired to an AirPlay HomeKit cloud account.

### 3.3. State
The `AirPlay` module **MUST** have a `state` string property that is `readonly`. This value has a corresponding notification for when the AirPlay App state changes.

The `paired` property **MUST** return whether the Firebolt device is paired to an AirPlay HomeKit cloud account.

## 4. AirPlay Process Provider

The AirPlay Daemon **MUST** register as a provider for the `xrn:firebolt:capability:airplay:process` capability.

```typescript
interface AirPlayProcess {
  enabled(): Promise<boolean>
  onEnabledChanged(): Promise<boolean>
  paired(): Promise<boolean>
  onPairedChanged(): Promise<boolean>
  launch(): Promise<void>
  background(): Promise<void>
  close(): Promise<void>
}
```

### 4.1. Enabled
The `AirPlayProcess` interface **MUST** have an `enabled` boolean property API that represents whether AirPlay is currently enabled.

There are no parameters.

### 4.2. Paired
The `AirPlayProcess` interface **MUST** have an `paired` boolean property API that represents whether AirPlay is currently paired.

There are no parameters.

## 5. AirPlay Experience Provider
The AirPlay App **MUST** include the `xrn:firebolt:capability:airplay:experience` capability in it's provided capabilities via the app manifest.

The AirPlay App **MUST** register a provider class for the `xrn:firebolt:capability:airplay:experience` capability as part of it's initialziation:

```typescript
import { AirPlay } from '@firebolt-js/sdk'

AirPlay.provide('xrn:firebolt:capability:airplay:experience', new CustomAirPlayExperienceProvider())
```

The `xrn:firebolt:capability:airplay:experience` capability interface is:

```typescript
enum AirPlayState = "audio" | "curtain" | "video" | "screen" | "settings"

interface AirPlayExperience {
    state(): Promise<AirPlayState>
    onStateChanged(): Promise<AirPlayState>
}
```

### 5.1. State
The `AirPlayExperience` interface **MUST** have a `state` string property API that represents the current state of the UX.

The `state` **MUST** be one of the following values:

- `"audio"`
- `"curtain"`
- `"video"`
- `"screen"`
- `"settings"`

There are no parameters.

### 5.2. Player
The `AirPlayExperience` interface **MUST** have a `player` API for instructing the app to present the main player UX.

When the `player` API is called the AirPlay App **MUST** attempt to present the main player user experience and either succeed or report an error denoting a failure.

There are no parameters. The AirPlay Daemon will send more detailed info using Apple's opaque WebSocket protocol.

### 5.3. Settings
The `AirPlayExperience` interface **MUST** have a `settings` API for instructing the app to present the AirPlay settings UX.

When the `settings` API is called the AirPlay App **MUST** attempt to present the settings user experience and either succeed or report an error denoting a failure.

There are no parameters.
