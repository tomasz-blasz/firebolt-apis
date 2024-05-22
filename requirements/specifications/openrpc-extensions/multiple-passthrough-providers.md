# Multiple Pass-through Providers

Document Status: Working Draft

See [Firebolt Requirements Governance](../../governance.md) for more info.

| Contributor     | Organization   |
|-----------------|----------------|
| Jeremy LaCivita | Comcast        |
| Kevin Pearson   | Comcast        |
| Yuri Pasquali   | Sky            |

## 1. Overview
This document describes how multiple Firebolt Apps can provide the same capability, which may be used by another Firebolt App, with the platform as a permission broker that passes the requests and responses to each app without feature-specific logic.

This document further extends the [App Pass-through Firebolt OpenRPC extension](./app-passthrough-apis.md) with aggregation functionality so that more than one app can provide the same capability.

This document is written using the [IETF Best Common Practice 14](https://www.rfc-editor.org/rfc/rfc2119.txt) and should include the following summary in the Overview section:

The key words "**MUST**", "**MUST NOT**", "**REQUIRED**", "**SHALL**", "**SHALL NOT**", "**SHOULD**", "**SHOULD NOT**", "**RECOMMENDED**", "**NOT RECOMMENDED**", "**MAY**", and "**OPTIONAL**" in this document are to be interpreted as described in [BCP 14](https://www.rfc-editor.org/rfc/rfc2119.txt) [RFC2119] [RFC8174] when, and only when, they appear in all capitals, as shown here.

## 2. Table of Contents
- [1. Overview](#1-overview)
- [2. Table of Contents](#2-table-of-contents)
  - [3.2. Multiple Providers Extension](#32-multiple-providers-extension)
  - [4.2. Direct pass-through](#42-direct-pass-through)
  - [4.3. Aggregated pass-through results](#43-aggregated-pass-through-results)


### 3.2. Multiple Providers Extension
Firebolt OpenRPC **MUST** support a `string` `x-multiple-providers` extension property on the `capabilities` tag that denotes a single method request may be provided by multiple apps on the device registering for the specified provider API, e.g.:

```json
{
    "methods": [
        {
            "name": "Content.search",
            "tags": [
                {
                    "name": "capabilities",
                    "x-provided-by": "Discover.onRequestSearch",
                    "x-multiple-providers": true,
                    "x-uses": [
                        "xrn:firebolt:capability:discovery:search"
                    ]
                }
            ]
        }
    ]
}
```

Setting `x-multiple-providers` to `true` means that all available apps that can provide the capability **MUST** be called and their results aggregated into an array for the final result.

A platform method with `x-multiple-providers` set to `true` **MUST** have an `array` result type.

### 4.2. Direct pass-through
A direct pass-through is where a single app provides a single response to a single request by another app.

This section only applies to app provider methods that do not have an `event` tag and do not have the `x-multiple-providers` extension set to `true`.

... See [App Passthrough APIs](./app-passthrough-apis.md) for more info

### 4.3. Aggregated pass-through results
An aggregated pass-through is where many apps provide responses to a single request by another app. The results are aggregated inside of an array.

This section only applies to app provider methods that do not have an `event` tag and do have the `x-multiple-providers` extension set to `true`.

The platform method result schema **MUST** have a type of `array`.

The platform method result schema **MUST** have an `items` sub-schema that either:

> Matches the `x-response` schema on the provider method so that the result can be added to the final array.
>
> or
>
> Has a property that matches the `x-response` schema on the provider method so that the result can be composed
> and added to the final array.

The platform **MUST** call the provider method from each [candidate app](#7-provider-candidates) and aggregated all of the results into an array.

If the platform method result `items` schema matches the `x-response` schema on the provider method then each provier value **MUST** be used as-is.

Otherwise if the platform method result `items` schema has a property that matches the `x-response` schema on the provider method then each provider value **MUST** be composed into an object under the corresponding property name and the platform **MUST** apply any [result transformations](#9-result-transformations) to the composed result.

If some providers time out, the providers that did not time out **MUST** be returned, with the remaining providers omitted.

Time out durations are out of scope for this document, and are specified on a per feature basis.