# Style Guide for Firebolt Specifications

| Contributor     | Organization |
| --------------- | ------------ |
| Jeremy LaCivita | Comcast      |
| Joe Martin      | Comcast      |

## 1. Overview

This document is a style guide for **Firebolt Requirements Specifications**.

The intention of this guide is to assist stakeholders in creating new specifications for the Firebolt project.  Following the recommendations and guidelines in this document will result in specifications that are more complete, correct, clear, concise, and consistent.

> The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119.txt) when, and only when, they appear in all capitals, as shown here.

## 2. Table of Contents

- [1. Overview](#1-overview)
- [2. Table of Contents](#2-table-of-contents)
- [3. General Style Requirements](#3-general-style-requirements)
- [4. File Structure](#4-file-structure)
- [5. Document Structure](#5-document-structure)
- [4. Modules](#4-modules)
  - [4.1. Naming](#41-naming)
- [4. Methods](#4-methods)
  - [4.1. Descriptions](#41-descriptions)
  - [4.1. Naming](#41-naming-1)
  - [4.1. Tags](#41-tags)
  - [4.1. Capabilities](#41-capabilities)
  - [4.1. Example Code](#41-example-code)
  - [4.1. Constants and Enumerations](#41-constants-and-enumerations)
- [5. Abbreviations \& Acronyms](#5-abbreviations--acronyms)
  - [3.1. General Style Requirements](#31-general-style-requirements)
- [10. Overview Section](#10-overview-section)
  - [1.1. User Stories](#11-user-stories)
- [2. Table of Contents](#2-table-of-contents-1)
- [3. Constants and Schemas](#3-constants-and-schemas)
- [3. Specification Style Requirements](#3-specification-style-requirements)
  - [3.2. Firebolt Style Requirements](#32-firebolt-style-requirements)
  - [3.3. Firebolt Method Templates](#33-firebolt-method-templates)
    - [3.3.1 Method Tags](#331-method-tags)
- [4. Example Section](#4-example-section)
  - [4.1. Example Feature](#41-example-feature)

## 3. General Style Requirements

## 4. File Structure

All specification documents must be written in Markdown.

## 5. Document Structure
## 4. Modules
### 4.1. Naming

## 4. Methods
### 4.1. Descriptions
### 4.1. Naming
### 4.1. Tags
### 4.1. Capabilities
### 4.1. Example Code
### 4.1. Constants and Enumerations
## 5. Abbreviations & Acronyms










### 3.1. General Style Requirements
All headers **MUST** be numbered, and have the parent header as the prefix, separated with '.'

Module and method names, as well as constants **MUST** be in monospace font, e.g. the `Foo` module **MUST** have a `bar` method that returns `true`. Specs should use JavaScript notation for any code examples if the spec is not targeting another specific language binding, e.g. a spec about Event listeners in C++ would use C++ syntax.

String constants and values **MUST** be wrapped in quotes for clarity, e.g. `"Hello World"`.


###########

## 10. Overview Section

The overview section is a non-normative or informative introduction to the contents and subject matter of the document. This is included to introduce the reader to the overall problem, solution, and scope. No formal requirements will be included here, as it will often be skipped by readers that are already familiar with the subject matter.

Overviews can be as long or short as appropriate for the subject matter, and should have a target audience ranging from technical product managers to engineering teams that may be testing, implementing, or integrating with the functionality described in the document.

The overview must contain the following summary in the overview section:




### 1.1. User Stories

User stories should describe the various uses of the proposed APIs and their targeted base, such as OTT customers or third-party app developers.

## 2. Table of Contents

The table of contents should include  links to all headers in the document, except for the top-level header (i.e. `# Requirements Style Guide`). It is recommended to use a Markdown plugin in your IDE (such as `Markdown All in One` for VSCode) to automatically generate this based on markdown headers ranging from level `two` and up.


## 3. Constants and Schemas

This section describes any shared constants, enumerations, or object schemas used by methods within the module.

Each set of values **MUST** be in its own numbered section.

While simple unordered lists are acceptable, values **SHOULD** be provided in table format, with each value having a detailed description.


## 3. Specification Style Requirements
The following section describes the high-level style requirements for Firebolt Specifications.


### 3.2. Firebolt Style Requirements
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

Methods **MUST** have at least one capability: `used`, `managed`, or `provided` by the method.

Methods **MAY** require the use of more than one capability, but this means that the app must have permission to all of them. In order to enable App permissions to be evaluated in an isolated layer, separate from the method implementation itself, a Firebolt method **MUST NOT** be specified to add or remove fields based on the caller's permissions.

The words used in method and parameter names **SHOULD** be used as consistently as possible across the Firebolt API surface. See the [Firebolt API Glossary](./glossary.md) for words that Firebolt uses and how they are used.

### 3.3. Firebolt Method Templates
Firebolt uses method templates in order to code-generate consistent APIs.

Methods **SHOULD** consider using the existing Firebolt method tags for a level of consistency across APIs.

If a Firebolt method is specified such that it requires a non-existant template, then a new Requirements Specification **MUST** be written and referenced by the specification that inspired it. Method templates **MUST** be designed with re-use in mind.

#### 3.3.1 Method Tags
The Firebolt OpenRPC tools can auto-generate additional methods based on tags assigned to an editorially-defined method.

For example, methods marked with the `property` tag only need to have the `getter` editorially defined.  The code generation suite will auto-generate the `setter` and `subscriber` OpenRPC methods in various SDK languages using templates.

Using method tags enables both consistent APIs (all properties have a recongnizable pattern) and consistent SDK implementation, reducing code that needs to be tested.

The following table describes the currently supported tags and their behavior:

| Tag                  | Description                                                                                                   |
| -------------------- | ------------------------------------------------------------------------------------------------------------- |
| `event`              | Method is an `event`; generates no additional methods                                                         |
| `property:immutable` | Method is a `getter` whose value won't change during runtime; generates no additional methods                 |
| `property:readonly`  | Method is a `getter` whose value may change during runtime; generates an additional `event` method            |
| `property`           | Method is a `getter` whose value may change during runtime; generates additional `setter` and `event` methods |

## 4. Example Section
A section describes group of closely related features. Many specifications have only one section, however, more complicated specifications may have many. The first paragraph of a section is typically a non-normative introduction to that section, and therefor does not contain any formal requirements.

### 4.1. Example Feature
Each feature under a section will have it's own heading. Non-normative introductions to features are not typically needed, as the reader is ready to get into requirements at this point. It is recommended that all Feature headings under each Section contain only sentences or short paragraphs with formal requirements, e.g. MUST, SHOULD, MAY, MUST NOT, SHOULD NOT, etc. These sentences should be separated by blank lines for readability, e.g.:

This requirement **MUST** be satisifed.

This requirement **SHOULD** be satisfied.

This requirement **MUST** be satisfied. The requirement **MUST** be satisifed in this particular way.
