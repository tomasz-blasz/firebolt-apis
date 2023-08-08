# AirPlay 2

Document Status: Draft

See [Firebolt Requirements Governance](../../governance.md) for more info.

| Contributor              | Organization   |
| --------=====----------- | -------------- |
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
- [3. Enabled](#3-enabled)
- [4. Connected](#4-connected)
- [5. Paired](#5-paired)
- [6. Player UI](#6-player-ui)
- [7. Settings UI](#7-settings-ui)
- [8. AirPlay Experience Provider](#8-airplay-experience-provider)
  - [8.1. Player](#81-player)
  - [8.2. Settings](#82-settings)
- [9. AirPlay Process Provider](#9-airplay-process-provider)
  - [9.1. Enabled](#91-enabled)

## 3. Enabled
AirPlay 2 manages it's own settings via Apple's AirPlay Luna App SDK.

Apple does not expose setters for any AirPlay settings, only getters, so the Firebolt API reflects that.

The `AirPlay` module **MUST** have an `enabled` boolean property that is `readonly`. This setting has a corresponding notification for when the value changes.

The `enabled` property **MUST** return the value from the `<INSERT AIRPLAY API HERE>` API.

## 4. Connected
The `AirPlay` module **MUST** have a `connected` boolean property that is `readonly`. This value has a corresponding notification for when the paired status changes.

The `connected` property **MUST** return whether an AirPlay device on the network is connected, e.g.:

- Screen mirroring
- Audio streaming
- Video streaming

## 5. Paired
The `AirPlay` module **MUST** have a `paired` boolean property that is `readonly`. This value has a corresponding notification for when the paired status changes.

The `paired` property **MUST** return whether the Firebolt device is paired to an AirPlay HomeKit cloud account.

## 6. Player UI
The `AirPlay` module **MUST** have a `player` method.

The `player` method **MUST** attempt to display the AirPlay App Setting UI, see [Player](#81-settings) below.

## 7. Settings UI
The `AirPlay` module **MUST** have a `settings` method.

The `settings` method **MUST** attempt to display the AirPlay App Setting UI, see [Settings](#82-settings) below.

## 8. AirPlay Experience Provider
The AirPlay App **MUST** include the `xrn:firebolt:capability:airplay:experience` capability in it's provided capabilities via the app manifest.

The AirPlay App **MUST** register a provider class for the `xrn:firebolt:capability:airplay:experience` capability as part of it's initialziation:

```typescript
import { AirPlay } from '@firebolt-js/sdk'

AirPlay.provide('xrn:firebolt:capability:airplay:experience', new CustomAirPlayExperienceProvider())
```

The `xrn:firebolt:capability:airplay:experience` capability interface is:

```typescript
interface AirPlayExperience {
    player(): Promise<void>
    settings(): Promise<void>
}
```

### 8.1. Player
The `AirPlayExperience` interface **MUST** have a `player` API for instructing the app to present the main player UX.

When the `player` API is called the AirPlay App **MUST** attempt to present the main player user experience and either succeed or report an error denoting a failure.

There are no parameters. The AirPlay Daemon will send more detailed info using Apple's opaque WebSocket protocol.

### 8.2. Settings
The `AirPlayExperience` interface **MUST** have a `settings` API for instructing the app to present the AirPlay settings UX.

When the `settings` API is called the AirPlay App **MUST** attempt to present the settings user experience and either succeed or report an error denoting a failure.

There are no parameters.

## 9. AirPlay Process Provider

The AirPlay Daemon **MUST** register as a provider for the `xrn:firebolt:capability:airplay:process` capability.

### 9.1. Enabled
The `xrn:firebolt:capability:airplay:process` capability **MUST** support a component providing a read-only `enabled` boolean property.

- enabled
- connected
- 

| method | params | result | 
| ------ | ------ | ------ |
| enabled | 