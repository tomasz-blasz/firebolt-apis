# App Navigation

Document Status: Working Draft

See [Firebolt Requirements Governance](../../governance.md) for more info.

| Contributor    | Organization   |
| -------------- | -------------- |
| Jeremy LaCivita            | Comcast            |

## 1. Overview
This document describes the Firebolt requirements that enable users to discover and navigate to an app's content from outside of the app, e.g. the main device experience, or via a voice assistant.

To faciliate navigating to discovered content, there are two operations needed: receiving navigation instructions from the platform, e.g. "deep links," and sending navigation instructions to from one app, to another app in the same app family, e.g. one app publisher might have separate apps for movies and music, and want to link between them to facilitate deeper discovery of content.

The key words "**MUST**", "**MUST NOT**", "**REQUIRED**", "**SHALL**", "**SHALL NOT**", "**SHOULD**", "**SHOULD NOT**", "**RECOMMENDED**", "**NOT RECOMMENDED**", "**MAY**", and "**OPTIONAL**" in this document are to be interpreted as described in [BCP 14](https://www.rfc-editor.org/rfc/rfc2119.txt) [RFC2119] [RFC8174] when, and only when, they appear in all capitals, as shown here.

## 2. Table of Contents
- [1. Overview](#1-overview)
- [2. Table of Contents](#2-table-of-contents)
- [3. Navigate To Events](#3-navigate-to-events)
- [Launch Method](#launch-method)
- [Reserved App Ids](#reserved-app-ids)
  - [3.3. Firebolt Method Templates](#33-firebolt-method-templates)
- [4. Example Section](#4-example-section)
  - [4.1. Example Feature](#41-example-feature)

## 3. Navigate To Events
The `Discovery` module **MUST** have a `navigateTo` notification that sends Navigation Intents to the App.



## Launch Method
The `Discovery` module **MUST** have a `launch` method that allows an app to launch another app in the same app family.

The `launch` method **MUST** have a required `appId` string parameter.

The `launch` method **MUST** have an optional `intent` object parameter which is a Navigation Intent.

An app **MUST** have permission to the `use` role of the `xrn:firebolt:capability:lifecycle:launch` capability in order to invoke the `launch` method.

When the `launch` method is successfully called, the platform **MUST** check if the provided `appId` resolves to an app that shares the same `appPublisherId` as the app calling `launch`. If so then the platform **MUST** attempt to launch the app denoted by `appId`. If not, then the call **MUST** return an error.

TODO: Do we want this API limited by:

- Same App Publisher and each app's friendlyId in app manifest
- A list of friendlyIds -> distP ids in the Manifest
- User Grants and

## Reserved App Ids
All Firebolt APIs exposed for building Firebolt Apps **MUST** be exposed as JSON-RPC methods on a WebSocket accessible to the device, typically running locally.

Parameters and return values for all APIs **MUST** be described using JSON-Schema schemas.

Methods **MUST** be grouped into “modules” or “packages” of functionality.

The JSON-RPC method name of any method **MUST** follow the template:

```
<module>.<method>
```

e.g.

```
lifecycle.ready
```

JSON-RPC method names are case sensitive.

Methods **MUST** have at least one capability used, managed, or provided by the method.

Methods **MAY** require the use of more than one capability, but this means that the app must have permission to all of them. In order to enable App permissions to be evaluated in an isolated layer, separate from the method implementation itself, a Firebolt method **MUST NOT** be specified to add or remove fields based on the caller's permissions.

The words used in method and parameter names **SHOULD** be used as consistently as possible across the Firebolt API surface. See the [Firebolt API Glossary](./glossary.md) for words that Firebolt uses and how they are used.

### 3.3. Firebolt Method Templates
Methods **SHOULD** consider using the existing Firebolt method tags, in order to have a level of consistency across APIs.

If a Firebolt method is specified such that it requires a non-existant template, then a new Requirements Specification **MUST** be written and referenced by the specification that inspired it. Method templates **MUST** be designed with re-use in mind.

## 4. Example Section
A section describes group of closely related features. Many specifications have only one section, however, more complicated specifications may have many. The first paragraph of a section is typically a non-normative introduction to that section, and therefor does not contain any formal requirements.

### 4.1. Example Feature
Each feature under a section will have it's own heading. Non-normative introductions to features are not typically needed, as the reader is ready to get into requirements at this point. It is recommended that all Feature headings under each Section contain only sentences or short paragraphs with formal requirements, e.g. MUST, SHOULD, MAY, MUST NOT, SHOULD NOT, etc. These sentences should be separated by blank lines for readability, e.g.:

This requirement **MUST** be satisifed.

This requirement **SHOULD** be satisfied.

This requirement **MUST** be satisfied. The requirement **MUST** be satisifed in this particular way.
