> The following is a sample template for a fictional `AirplaneMode` module. Copy and modify as needed for future specifications.

# AirplaneMode Requirements

Document Status: Candidate Specification

See [Firebolt Requirements Governance](../../governance.md) for more info.

| Contributor    | Organization |
| -------------- | ------------ |
| Erlich Bachman | Aviato       |

## 1. Overview

This is an overview of the requirements for `AirplaneMode`.  It should provide a high-level description of the requirements and delve into various use-cases of the proposed APIs and how they are expected to be used by the target audience.

> The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119.txt) when, and only when, they appear in all capitals, as shown here.

### 1.1. User Stories

#### 1.1.1. As an OTT App Developer

I want to:

- Check the status of airplane mode

#### 1.1.2. As a First-Party App Developer

I want to:

- Check the status of airplane mode
- Enable airplane mode with a set duration
- Disable airplane mode

## 2. Table of Contents

- [1. Overview](#1-overview)
  - [1.1. User Stories](#11-user-stories)
    - [1.1.1. As an OTT App Developer](#111-as-an-ott-app-developer)
    - [1.1.2. As a First-Party App Developer](#112-as-a-first-party-app-developer)
- [2. Table of Contents](#2-table-of-contents)
- [3. Constants and Schemas](#3-constants-and-schemas)
  - [3.1. State](#31-state)
- [4. AirplaneMode Module](#4-airplanemode-module)
  - [4.1. Status](#41-status)
  - [4.2. Enable](#42-enable)
  - [4.3. Disable](#43-disable)

## 3. Constants and Schemas

The following describes the various constants and object schemas that are used within this module.

### 3.1. State

The `SharedSchemas` module **MUST** have a `State` enumeration of the following `string` values:

| Name       | Description                    |
| ---------- | ------------------------------ |
| `disabled` | Airplane mode is disabled      |
| `enabled`  | Airplane mode is enabled       |
| `unknown`  | Airplane mode state is unknown |

## 4. AirplaneMode Module

Provide a detailed overview of the module being proposed. It should include a detailed description of its purpose and intended uses, followed by a breakdown of each method being proposed.

### 4.1. Status

The `AirplaneMode` module **MUST** have a `status` API that returns an object describing the status of airplane mode.

The API response **MUST** be an object with the following properties:

| Name               | Description                                           |
| ------------------ | ----------------------------------------------------- |
| `bluetoothEnabled` | Whether bluetooth is enabled. Type: `boolean`         |
| `expiry`           | Timestamp when airplane mode expires. Type: `integer` |
| `state`            | State of airplane mode. Type: `AirplaneMode.State`    |
| `wifiEnabled`      | Whether wifi is enabled. Type: `boolean`              |

This API **MUST** have an accompanying event to notify listeners when any of these properties have changed.

Access to this API requires `uses` privilege to the `xrn:firebolt:capability:airplanemode:info` capability.

```javascript
AirplaneMode.getStatus()
//> {
//>   "bluetoothEnabled": false,
//>   "expiry": 1718910281,
//>   "state": "enabled",
//>   "wifiEnabled": false,
//> }
```

### 4.2. Enable

The `AirplaneMode` module **MUST** have an `enable` API that enables airplane mode.

This API **MUST** return a `boolean` denoting whether or not airplane mode was successfully enabled.

This API **MUST** have a required `integer` parameter denoting the desired duration of airplane mode, in minutes.

Access to this API requires `manages` privilege to the `xrn:firebolt:capability:airplanemode:status` capability.

```javascript
AirplaneMode.enable(600)
//> true
```

### 4.3. Disable

The `AirplaneMode` module **MUST** have a `disable` API that disables airplane mode.

This API **MUST** return a `boolean` denoting whether or not airplane mode was successfully disabled.

Access to this API requires `manages` privilege to the `xrn:firebolt:capability:airplanemode:status` capability.

```javascript
AirplaneMode.disable()
//> true
```
