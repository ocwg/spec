# Open Canvas Interchange Format (OCIF)

**OCWG Working Draft, 3 December 2024**

**This version:** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; https://spec.canvasprotocol.org/v0.2 \
**Latest version:** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; https://spec.canvasprotocol.org/v0.2 \
**Previous version:** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; https://github.com/ocwg/spec/blob/initial-draft/README.md \
**Feedback:** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; https://github.com/orgs/ocwg/discussions \
**Editor:** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dr. Max Völkel, ITMV https://maxvoelkel.de,

Copyright © 2024 the Contributors to the Open Canvas Working Group (OCWG).

## Abstract

An interchange file format for canvas-based applications. Visual nodes, structural relations, assets, and schemas.

## Status of this Document

This document is an editor's draft and has no official standing. It is a work in progress and may be updated, replaced, or obsoleted by other documents at any time.

**Legal**:
Open Canvas Interchange Format (OCIF) v0.1 © 2024 by Open Canvas Working Group is licensed under CC BY-SA 4.0. To view a copy of this licence, visit https://creativecommons.org/licenses/by-sa/4.0/

## Document Conventions

- Types:
  This document defines a catalog of _OCIF types_, which are more precise than the generic JSON types.
  See [OCIF Types](#ocif-types) for a catalog.
- The terms _OCIF file_ and _OCIF document_ are used interchangeably.

- Issues are temporary TODOs, which should be resolved before the final version. The `@@` makes them easy to search in an editor.

**Issue Example:**

- [ ] @@ This is an issue

### Table of Contents

<!-- TOC -->

- [Open Canvas Interchange Format (OCIF)](#open-canvas-interchange-format-ocif)
  - [Abstract](#abstract)
  - [Status of this Document](#status-of-this-document)
  - [Document Conventions](#document-conventions)
    - [Table of Contents](#table-of-contents)
- [Introduction](#introduction)
- [File Structure](#file-structure)
- [Nodes](#nodes)
  - [Ports Extension](#ports-extension)
  - [Relative Constraints Extension](#relative-constraints-extension)
- [Relations](#relations)
  - [Set Relation](#set-relation)
  - [Edge Relation](#edge-relation)
  - [Hyperedge Relation](#hyperedge-relation)
  - [Group Relation](#group-relation)
  - [Parent-Child Relation](#parent-child-relation)
- [Assets](#assets)
  - [Resources](#resources)
    - [Representation](#representation)
    - [Fallback](#fallback)
  - [Schemas](#schemas)
    - [Built-in Schema Mappings](#built-in-schema-mappings)
- [Extensions](#extensions)
  - [Defining Extensions](#defining-extensions)
  - [Exporting Data with Extensions](#exporting-data-with-extensions)
  - [Handling Extension Data](#handling-extension-data)
- [OCIF Types](#ocif-types)
  _ [Angle](#angle)
  _ [ID](#id)
  _ [MIME Type](#mime-type)
  _ [Node](#node)
  _ [Relation](#relation)
  _ [Representation](#representation-1)
  _ [Resource](#resource)
  _ [Schema Entry](#schema-entry)
  _ [Schema Name](#schema-name)
  _ [URI](#uri)
- [Practical Recommendations](#practical-recommendations)
- [References](#references)
- [Appendix](#appendix)
  - [Built-in Schema Entries](#built-in-schema-entries)
  - [Examples](#examples)
    - [Node Extension: Circle](#node-extension-circle)
    - [Advanced Examples](#advanced-examples)
  - [OCWG URL Structure](#ocwg-url-structure)
  - [Changes](#changes)
    - [From v0.1 to v0.2](#from-v01-to-v02)
    - [From v0.2.0 to v0.2.1](#from-v020-to-v021)
- [TODO @@](#todo-)
  - [Discuss](#discuss)
  - [LATER](#later)
  - [Notes to the Editor](#notes-to-the-editor)
  <!-- TOC -->

# Introduction

This document describes the Open Canvas Interchange Format (OCIF), which allows canvas-applications to exchange their data.
A canvas in this context is a spatial view, on which visual items are placed.
Often, these items have been placed and sized manually.

There is no formal definition of _(infinite) canvas applications_.
The following references describe the concept:

- https://infinitecanvas.tools/
- https://lucid.co/techblog/2023/08/25/design-for-canvas-based-applications

The goal is to allow different canvas apps to display a canvas exported from other apps, even edit it,
and open again in the first app, without data loss.

In this spec, we define a canvas as consisting of three main parts:

- **[Nodes](#nodes)**: Visual items, placed on the canvas.
- **[Relations](#relations)**: Logical connections between visual items (and other relations).
- **[Resources](#resources)**: Content, such as text, vector drawings, raster images, videos, or audio files.

To make sub-formats explicit, OCIF uses JSON schemas, kept in a fourth part:

- **[Schemas](#schemas)**: Definitions of the structure of nodes and relations.

# File Structure

The OCIF file is a JSON object with the following properties:

| Property    | JSON Type | OCIF Type                       | Required     | Contents                          |
| ----------- | --------- | :------------------------------ | ------------ | --------------------------------- |
| `ocif`      | `string`  | [URI](#uri)                     | **required** | The URI of the OCIF schema        |
| `nodes`     | `array`   | [Node](#node)[]                 | optional     | A list of [nodes](#nodes)         |
| `relations` | `array`   | [Relation](#relation)[]         | optional     | A list of [relations](#relations) |
| `resources` | `array`   | [Resource](#resource)[]         | optional     | A list of [resources](#resources) |
| `schemas`   | `array`   | [Schema Entry](#schema-entry)[] | optional     | Declared [schemas](#schemas)      |

- **OCIF**: The _Open Canvas Interchange Format_ schema URI.
  - The URI SHOULD contain the version number of the schema, either as a version number or as a date (preferred).
  - Known versions:
    - `https://spec.canvasprotocol.org/v0.1` Retrospectively assigned URI for the first draft at https://github.com/ocwg/spec/blob/initial-draft/README.md
    - `https://spec.canvasprotocol.org/v0.2` This is a preliminary version, as described in this draft, for experiments
    - `https://spec.canvasprotocol.org/1.0` This is the first stable version, not yet released
- **nodes**: A list of nodes on the canvas. See [Nodes](#nodes) for details.
- **relations**: A list of relations between nodes (and relations). See [Relations](#relations) for details.
- **resources**: A list of resources used by nodes. See [Resources](#resources) for details.
- **schemas**: A list of schema entries, which are used for relation types and extensions. See [Schemas](#schemas) for details.

**Example** \
A minimal OCIF file, no visible items

```json
{
  "ocif": "https://canvasprotocol.org/ocif/0.1"
}
```

**Example** \
A small OCIF file, with one node and one resource

```json
{
  "ocif": "https://canvasprotocol.org/ocif/0.1",
  "nodes": [
    {
      "id": "n1",
      "position": [100, 100],
      "resource": "r1"
    }
  ],
  "resources": [
    {
      "id": "r1",
      "representations": [
        { "mime-type": "text/plain", "content": "Hello, World!" }
      ]
    }
  ]
}
```

# Nodes

Nodes represent visual items on the canvas.
Conceptually, a node is a rectangle on the canvas, often displaying some content (resource).
A _Node_ is an `object` with the following properties:

| Property   | JSON Type | OCIF Type          | Required     | Contents                            | Default   |
| ---------- | --------- | ------------------ | ------------ | ----------------------------------- | --------- |
| `id`       | `string`  | [ID](#id)          | **required** | A unique identifier for the node.   | n/a       |
| `position` | `array`   | number[]           | recommended  | Coordinate as (x,y) or (x,y,z).     | [0,0]     |
| `size`     | `array`   | number[]           | recommended  | The size of the node per dimension. | n/a       |
| `resource` | `string`  | [ID](#id)          | optional     | The resource to display             |           |
| `data`     | `array`   | array of Extension | optional     | Extended node data                  |           |
| `rotation` | `number`  | [Angle](#angle)    | optional     | +/- 360 degrees                     | `0`       |
| `scale`    | `array`   | number[]           | optional     | Scale factors to resize nodes       | `[1,1,1]` |

- **id**: A unique identifier for the node. Must be unique within an OCIF file. See [ID](#ocif-types) type for details.

- **position**: The position of the node on the canvas.

  - Required are **x** (at position `0`) and **y** (at position `1`). Optional is **z** at position `2`.
  - The _coordinate system_ has the x-axis pointing to the right, the y-axis pointing down, and the z-axis pointing away from the screen. This is the same as in CSS, SVG, and most 2D and 3D graphics libraries. The origin is the top-left corner of the canvas.
  - The unit is logical pixels (as used in CSS for `px`).
  - The positioned point (to which the `position` refers) is the top-left corner of the node.
  - The default for z-axis is 0, when importing 2D as 3D.
  - When importing 3D as 2D, the z-axis is ignored (but can be left as-is). When a position is changed, the z-axis CAN be set to 0. Yes, this implies that full round-tripping is not always possible.

- **size**: The size of the node in dimensions. I.e. this is **x-axis** ("width" at position `0`), **y-axis** ("height" at position `1`), and **z-axis** ("depth" at position `2`).

  - Size might be omitted if a linked resource defines the size. E.g., raster images such as PNG an JPEG define their size in pixels. SVG can have a `viewbox` defined, but may also omit it. Text can be wrapped at any width, so a size property is clearly required. In general, a size property is really useful as a fall-back to display at least a kind of rectangle, if the resource cannot be displayed as intended. Size can only be omitted if _all_ resource representations define a size.

- **data**: Additional properties of the node.
  A node may have any number of extensions. Each extension is a JSON object with a `type` property.
  See [extensions](#extensions).

- **resource**: A reference to a resource, which can be an image, video, or audio file. See [resources](#resources).

  - Resource can be empty, in which case a node is acting as a transform for other nodes.

- **rotation**: The 2D rotation of the node in degrees. The rotation center is the positioned point, i.e., top-left. The z-Axis is not modified.

- **scale**: Allows to re-scale a given node. This is particularly useful if a [parent-child](#parent-child-relation)

## Ports Extension

This is an [extension](#extensions) for a node. To be placed inside the `data` `array`. It provides the familiar concept of _ports_ to a node. A port is a point, which allows geometrically controlling where, e.g. arrows are attached to a shape.

- Name: `@ocwg/node/ports`
- URI: `https://spec.canvasprotocol.org/node/ports/0.2`

Any node can act as a port. The 'container' node uses the _Ports Extension_ to define which nodes it uses as ports.
The _Ports Extension_ has the following properties:

| Property | JSON Type | OCIF Type | Required     | Contents                      | Default |
| -------- | --------- | --------- | ------------ | ----------------------------- | ------- |
| `ports`  | `array`   | [ID](#id) | **required** | IDs of nodes acting as ports. |         |

**Example** \
A node (n1) with two ports (p1, p2)

```json
{
  "nodes": [
    {
      "id": "n1",
      "position": [100, 100],
      "size": [100, 100],
      "data": [
        {
          "type": "@ocwg/node/ports",
          "ports": ["n2", "n3"]
        }
      ]
    },
    {
      "id": "p1",
      "position": [200, 200]
    },
    {
      "id": "p2",
      "position": [300, 300]
    }
  ]
}
```

## Relative Constraints Extension

Relative constraints are used to define, e.g., the relative positioning of nodes.

- Name: `@ocwg/node/relative`
- URI: `https://spec.canvasprotocol.org/rel/relative/0.2`

The node on which this extension is placed is the _target node_.
It defines a _source_ node, which is used as a reference for the relative positioning.

| Property   | JSON Type | OCIF Type       | Required     | Contents                         | Default |
| ---------- | --------- | --------------- | ------------ | -------------------------------- | ------- |
| `source`   | `string`  | [ID](#id)       | **required** | ID of the source node.           |         |
| `position` | `array`   | number[]        | optional     | Relative position of the target. | `[0,0]` |
| `rotation` | `number`  | [Angle](#angle) | optional     | Relative angle of the target.    | `0`     |

- **source**: The ID of the source node, which is used as a reference for the relative positioning.
- **position**: The relative position of the target node to the source node.
  The numbers given in the array are vector-added to the position of the source node.
- **rotation**: The relative rotation of the target node to the source node. The rotation is added to the rotation of the source node.

# Relations

Relations are used to indicate relationships between Nodes on the canvas.
They can also be used to indicate relationships between other relations.
Relations are generally not visible, but rather conceptual.
If a relation should be visualized, it should have a corresponding Node.

Every relation has the following properties:

| Property | JSON Type | OCIF Type                | Required     | Contents                              |
| -------- | --------- | ------------------------ | ------------ | ------------------------------------- |
| `id`     | `string`  | [ID](#id)                | **required** | A unique identifier for the relation. |
| `data`   | `array`   | [Extension](#extensions) | optional     | Additional data for the relation.     |

Similar to nodes, there is a built-in base relation, which can use extensions.
Contrary to nodes, the base extension has no pre-defined properties except the `id` and `data` properties.
Thus, relations are very flexible.

- **id**:
  A unique identifier for the relation.
  Must be unique within an OCIF file.
  See [ID](#ocif-types) type for details.

- **data**:
  Additional data for the relation.
  Each array entry is an _extension object_, which is the same for nodes and relations.
  See [extensions](#extensions).

In the remainder of this section, the current list of relation extension types (also just called _relation types_) is explained.
In addition to the relation types defined here, anybody can define and use their own relation types.
If this is your first read of the spec, skip over the details of the relation types and come back to them later.

## Set Relation

A set relation is a relation, which groups nodes together.

- **URI**: `https://spec.canvasprotocol.org/rel/set/0.2`
- **Name**: `@ocwg/rel/set`

A set has the following properties in its `data` object:

| Property  | JSON Type | OCIF Type   | Required     | Contents                  |
| --------- | --------- | ----------- | ------------ | ------------------------- |
| `members` | `array`   | [ID](#id)[] | **required** | IDs of members of the set |

- **members**: A list of IDs of nodes or resources that are part of the set.
  Resources cannot be part of a set.

**Example:** A set relation with three members:

```json
{
  "type": "@ocwg/set",
  "members": ["n1", "n2", "n3"]
}
```

## Edge Relation

An edge relates two elements (nodes and/or relation, mixing types is allowed).
It supports directed and undirected bi-edges.

- URI: `https://spec.canvasprotocol.org/rel/edge/0.2`
- Name: `@ocwg/rel/edge`

It has the following properties:

| Property   | JSON Type | OCIF Type | Required     | Contents                  | Default |
| ---------- | --------- | :-------- | ------------ | ------------------------- | :------ |
| `from`     | `string`  | [ID](#id) | **required** | ID of source element.     |         |
| `to`       | `string`  | [ID](#id) | **required** | ID of target element.     |         |
| `directed` | `boolean` |           | optional     | Is the edge directed?     | `true`  |
| `rel`      | `string`  |           | optional     | Represented relation type |         |

- **from**: The ID of the source element.
- **to**: The ID of the target element.
- **directed**: A boolean flag indicating if the edge is directed. If `true`, the edge is directed from the source to the target. If `false`, the edge is undirected. Default is `true`.
- **rel**: The type of relation represented by the edge. This is optional but can be used to indicate the kind of relation between the source and target elements. Do not confuse with the `type` of the OCIF relation. This field allows representing an RDF triple (subject,predicate,object) as (from,rel,to).

## Hyperedge Relation

A hyperedge is a relation, which connects any number of nodes.
Hyperedges can also be used to model simple bi-edges.

- Name: `@ocwg/rel/hyperedge`
- URI: `https://spec.canvasprotocol.org/rel/hyperedge/0.2`

Conceptually, a hyper-edge is an entity, which has a number of _endpoints_.
For each endpoint, we allow defining the directionality of the connection.
The endpoints are explicitly defined as an ordered list, i.e., endpoints can be addressed by their position in the list.
Such a model allows representing all kinds of hyper-edges, even rather obscure one.

A hyper-edge in OCIF has the following properties:

| Property    | JSON Type | OCIF Type | Required     | Contents                             | Default |
| ----------- | --------- | --------- | ------------ | ------------------------------------ | ------: |
| `endpoints` | `array`   | Endpoint  | **required** | List of endpoints of the hyper-edge. |         |
| `weight`    | `number`  |           | optional     | Weight of the edge                   |   `1.0` |
| `rel`       | `string`  |           | optional     | Represented relation type            |         |

- **endpoints**: See below.
- **weight**: A floating-point number, which can be used to model the strength of the connection, as a whole. More general than endpoint-specific weights, and often sufficient.
<!--
Edge weight is a common requirement, and no extensions are needed for this simple property
-->
- **rel**: See [Edge Relation](#edge-relation)

**Endpoint** \
Each endpoint is an object with the following properties:

| Property    | JSON Type | OCIF Type | Required     | Contents                                 | Default |
| ----------- | --------- | --------- | ------------ | ---------------------------------------- | ------- |
| `id`        | `string`  | [ID](#id) | **required** | ID of attached entity (node or relation) |         |
| `direction` | `string`  | Direction | optional     | Direction of the connection.             | `undir` |
| `weight`    | `number`  |           | optional     | Weight of the edge                       | `1.0`   |

- **id**: states which node (or relation) is attached to the edge
- **direction**: See below
- **weight**: A floating-point number, which can be used to model the strength of the connection, for this endpoint.
<!--
Edge weight is a common requirement, and no extensions are needed for this simple property
-->

**Direction** \
An enum with three values:

- `in` (edge is going **into** the hyper-edge),
- `out` (edge is going **out** from thy hyper-edge),
- `undir` (edge is attached **undirected**). This is the default.

This allows representing cases such as:

- An edge with only one endpoint
- An edge with no endpoints
- An edge with only incoming or only outgoing endpoints.

**Example** \
An hyperedge relation connecting two nodes as input (n1,n2) with one node as output (n3).

```json
{
  "type": "@ocwg/rel/hyperedge",
  "endpoints": [
    { "id": "n1", "direction": "in" },
    { "id": "n2", "direction": "in" },
    { "id": "n3", "direction": "out" }
  ]
}
```

## Group Relation

A group relation is a relation, which groups nodes together.
It implies stronger semantics than a [set relation](#set-relation).

- Name: `@ocwg/rel/group`
- URI: `https://spec.canvasprotocol.org/rel/group/0.2`

A group is modeled as a relation with a list of its members.

| Property  | JSON Type | OCIF Type   | Required     | Contents                  |
| --------- | --------- | ----------- | ------------ | ------------------------- |
| `members` | `array`   | [ID](#id)[] | **required** | IDs of members of the set |

- **members**: A list of IDs of nodes (or relations, such as other groups) that are part of the group.

**Semantics**

- Groups can contain groups as members. Thus, all semantics apply recursively.
- When a group is deleted, all members are deleted as well.
- When a group is 'ungrouped,' the group itself is deleted, but its members remain.
- When a member is deleted, it is removed from the group.

## Parent-Child Relation

A parent-child relation models a hierarchical relationship between nodes.
It can be used to model inheritance, containment, or other hierarchical relationships.

- Name: `@ocwg/rel/parent-child`
- URI: `https://spec.canvasprotocol.org/rel/parent-child/0.2`

| Property  | JSON Type | OCIF Type | Required     | Contents               | Default |
| --------- | --------- | --------- | ------------ | ---------------------- | :------ |
| `parent`  | `string`  | [ID](#id) | **required** | ID of the parent node. |         |
| `child`   | `string`  | [ID](#id) | **required** | ID of the child node.  |         |
| `ìnherit` | `boolean` |           | optional     | Inherit properties.    | `false` |

- **parent**: The ID of the parent node. There SHOULD be only one parent per child.
- **child**: The ID of the child node. A parent can have multiple children (expressed my multiple parent-child relations).
- **inherit**: A boolean flag indicating if the child should inherit properties from the parent. Default is `false`.
  - The Exact semantics of inheritance are defined by the application.
  - In general, when looking for JSON properties of a node and finding them undefined, an app should look for the same value in the parent node.
    This chain of parents should be followed until root is reached or a cycle is detected.

# Assets

OCIF knows two kinds of assets, [resources](#resources) and [schemas](#schemas). Both are managed by similar mechanisms. Assets can be stored in three ways:

- **Inline**: The asset is stored directly in the OCIF file. It is referenced by its id.
- **External**: The asset is stored in a separate file, which is referenced by the OCIF file. A relative URI expresses the reference.
- **Remote**: The asset is stored on a remote server, which is referenced by the OCIF file. A URI is required as a reference.

## Resources

Resources are the hypermedia assets that nodes display.
They are stored separately from Nodes to allow for asset reuse and efficiency.

Resources can be referenced by nodes or relations.
They are stored in the `resources` property of the OCIF file.
Typical resources are, e.g., SVG images, text documents, or media files.

- Each entry in `resources` is an array of _representation_ objects.
- The order of representations is significant. The first representation is the default representation.
  Later representations can be used as fallbacks.

A resource is an `object` with the following properties:

| Property | JSON Type | OCIF Type | Required     | Contents                   |
| -------- | --------- | --------- | ------------ | -------------------------- |
| `id`     | `string`  | [ID](#id) | **required** | Identifier of the resource |

- **id**: A unique identifier for the resource. See [ID](#id) type for details.

### Representation

Each _Representation_ object has the following properties:

| Property    | JSON Type | OCIF Type               | Required  | Contents                               |
| ----------- | --------- | ----------------------- | --------- | -------------------------------------- |
| `location`  | `string`  | [URI](#uri)             | see below | The storage location for the resource. |
| `mime-type` | `string`  | [MIME Type](#mime-type) | see below | The IANA MIME Type of the resource.    |
| `content`   | `string`  |                         | see below | The content of the resource.           |

Either `content` or `location` MUST be present. If `content` is used, `location` must be left out and vice versa.

- **location**: The storage location for the resource.
  This can be a relative URI for an external resource or an absolute URI for a remote resource.
  - If a `data:` URI is used, the `content` and `mime-type` properties are implicitly defined already. Values in `content` and `mime-type` are ignored.
- **mime-type**: The IANA MIME Type of the resource. See [MIME Type](#mime-type) for details.
- **content**: The content of the resource.
  This is the actual data of the resource as a string.
  Can be base64-encoded.

**Summary** \
Valid resource representations are

|                 | `location`                      | `mime-type`                                                | `content`          |
| :-------------- | ------------------------------- | ---------------------------------------------------------- | ------------------ |
| Inline text     | Ignored, `content` is set       | E..g. `text/plain` or `image/svg+xml`                      | Text/SVG as string |
| Inline binary   | Ignored, `content` is set       | E.g. `image/png`                                           | Base64             |
| Remote          | `https://example.com/sunny.png` | Optional; obtained from HTTP response                      | Ignored            |
| External        | `images/sunny.png`              | Recommended; only guessable from file extension or content | Ignored            |
| Remote data URI | `data:image/png;base64,...`     | Ignored; present in URI                                    | Ignored            |

**Example:** A resource stored inline:

```json
{
  "resources": [
    {
      "id": "r1",
      "representations": [
        { "mime-type": "image/svg+xml", "content": "<svg>...</svg>" }
      ]
    }
  ]
}
```

### Fallback

**Example**: A resource with a fallback representation.

- The first representation is an SVG image, stored inline.
- The second representation is a remotely stored PNG image. If SVG content cannot be rendered by the application, the PNG can be used.
- The third representation is a text representation of the resource. This can be used for accessibility or indexing purposes.

```json
{
  "resources": [
    {
      "id": "r1",
      "representations": [
        { "mime-type": "image/svg+xml", "content": "<svg>...</svg>" },
        {
          "mime-type": "image/png",
          "location": "https://example.com/image.png"
        },
        { "mime-type": "text/plain", "content": "Plan of the maze" }
      ]
    }
  ]
}
```

## Schemas

A schema in this specification refers to a [JSON Schema](https://json-schema.org/draft/2020-12) 2020-12.

Schemas are used to define

- a whole OCIF document,
  - Due to the openness of OCIF, the JSON schema for the OCIF document cannot capture all possible extensions.
- the structure of [extensions](#extensions).

Schemas are stored either inline in the `schemas` property of an OCIF document or externally/remote. See [assets](#assets) for storage options.

- [ ] @@ Provide custom tooling for schema validation

Each entry in the `schemas` array is an object with the following properties:

| Property   | JSON Type | OCIF Type                   | Required     | Contents                                 |
| ---------- | --------- | :-------------------------- | ------------ | ---------------------------------------- |
| `uri`      | `string`  | absolute [URI](#uri)        | **required** | Identifier (and location) of the schema  |
| `schema`   | `object`  |                             | optional     | JSON schema inline as a JSON object      |
| `location` | `string`  | [URI](#uri)                 | optional     | Override storage location for the schema |
| `name`     | `string`  | [Schema Name](#schema-name) | optional     | Optional shortname for a schema. "@..."  |

- **uri**: The URI of the schema. The URI is usually absolute. Only for local testing or development, relative URIs are allowed.

  - The URI SHOULD contain the version number of the schema, either as a version number or as a date.

- **schema**: The actual JSON schema as a JSON object. This is only required for inline schemas. If `schema` is used, `location` must be left out.

- **location**: The storage location for the schema.

  - For a schema stored inline, this property should be left out.
  - For a _remote_ schema, the `uri` property is used as a location. This field allows overriding the location with another URL. This is particularly useful for testing or development.
  - An _external_ schema uses a relative URI as a location. This is a relative path to the OCIF file.

- **name**: An optional short name for the schema. This defines an alias to the URI. It is useful for human-readable references to the schema. The name MUST start with a `@` character. Names SHOULD use the convention organisation name `/` schema name. Example name: `@ocwg/circle`. Names MUST be unique within an OCIF file.
  - By convention, schema names do not contain a version number. However, if multiple versions of the same schema are used in a file, the version number MUST be appended to the name, to distinguish between them. E.g. `@example/circle/1.0` and `@example/circle/1.1`.

To summarize, these schema definitions are possible:

| Schema        | `uri`        | `schema`        | `location`                   | `name`   |
| ------------- | ------------ | --------------- | ---------------------------- | -------- |
| Inline Schema | **required** | the JSON schema | --                           | optional |
| External      | **required** | --              | relative path                | optional |
| Remote        | **required** | --              | -- (URI is used)             | optional |
| Remote        | **required** | --              | absolute URI (overrides URI) | optional |

By defining a mapping of URIs to names, the OCIF file becomes more readable and easier to maintain.

**Example** \
A schema array with two schemas:

```json
{
  "schemas": [
    {
      "uri": "https://spec.canvasprotocol.org/node/ports/0.2",
      "name": "@ocwg/node/ports"
    },
    {
      "uri": "https://example.com/ns/ocif-node/circle/1.0",
      "location": "schemas/circle.json",
      "name": "@example/circle"
    }
  ]
}
```

### Built-in Schema Mappings

To simplify the use of OCIF, a set of built-in schema mappings is defined.

Any [Schema Name](#schema-name) of the form
`@ocwg/rel/`_suffix_ maps to a schema [URI](#uri)
`https://spec.canvasprotocol.org/rel/` _suffix_ `/0.2`

Here `0.2` is the current version of the OCIF spec. Later OCIF specs will have different versions and thus different URIs.

Built-in Entries, where the syntax `{var}` denotes placeholders.

```json
{
  "schemas": [
    {
      "name": "@ocwg/node/{ext-type}",
      "uri": "https://spec.canvasprotocol.org/node/{ext-type}/0.2"
    },
    {
      "name": "@ocwg/rel/{ext-type}",
      "uri": "https://spec.canvasprotocol.org/rel/{ext-type}/0.2"
    }
  ]
}
```

These mappings SHOULD be materialized into the OCIF JSON schema.

# Extensions

No two canvas applications are alike:
There are apps for informal whiteboarding, formal diagramming, quick visual sketches, node-and-wire programming, and many other use cases.
Each of these apps has radically different feature sets.
Extensions are an integral part of OCIF.
They allow adding custom data to nodes, relations, and resources.

- An extension is a JSON object (used as a "property bag") with one mandatory property: `type`.
  Thus, `type` is a reserved property key.
  All other property keys can be used by the extension.
- Arbitrary, nested JSON structures are allowed.
- Extensions SHOULD define how the base properties play together with the extension properties and with other (known) extensions.
- Nodes and relations can have multiple extensions within their `data` array.
- Each extension is an object with a `type` property.

| Property | JSON Type | OCIF Type                                  | Required     | Contents          |
| -------- | --------- | :----------------------------------------- | ------------ | ----------------- |
| `type`   | `string`  | [Schema Name](#schema-name) or [URI](#uri) | **required** | Type of extension |

- **type**: The type of the extension. This is a URI or a simple name.
  If a name is used, that name must be present in the [schemas](#schemas) section, where it is mapped to a URI.

For an example of an extension, see the [appendix](#appendix), [Node Extension: Circle](#node-extension-circle).

## Defining Extensions

An extensions MUST have a URI (as its ID) and a document describing the extension.

It SHOULD have a version number, as part of its URI.
SHOULD have a proposed name, and SHOULD have a JSON schema.

The proposed structure is to use a directory in a git repository.
The directory path should contain a name and version number.
Within the repo, there SHOULD be two files:

- README.md, which describes the extension.
- schema.json, which contains the JSON schema for the extension.
  - This schema MUST use the same URI as the extension.
  - It SHOULD have a `description` property, describing briefly the purpose of the extension.

As an example, look at the [Circle Extension](#node-extension-circle) in the appendix.

## Exporting Data with Extensions

When exporting an OCIF file using extensions, the application SHOULD use inline or external schemas for the extensions.
Remote schemas CAN be used to save space in the OCIF file.

## Handling Extension Data

To support interchange between canvases when features don't overlap,
canvas apps need to preserve nodes and relations that they don't support:

- Canvas A supporting Feature X creates a canvas with a Feature X node in it and exports it as OCIF.
- Canvas B, which does not support Feature X, opens the OCIF file, and some edits are made to the canvas.
- Canvas B exports the canvas to an OCIF file. The nodes for Feature X should still be in the OCIF file, unchanged.

Vital parts of the OCIF format are modelled as extensions.
In the following sections, extensions defined within this specification are listed.

# OCIF Types

The _JSON types_ are just: `object`, `array`, `string`, `number`, `boolean`, `null`.
OCIF defines more precise types, e.g. _ID_ is a JSON string with additional semantic (must be unique within a document).
We also use the syntax `ID[]` to refer to a JSON `array`, in which each member is an _ID_.

Here is the catalog of types used throughout the document:

#### Angle

A `number` that represents an angle in degrees, from -360 to 360.
The angle is measured in degrees, with positive values indicating a clockwise rotation and negative values indicating a counterclockwise rotation.
Numbers outside the range of -360 to 360 are allowed, but they are normalized to the range by adding or subtracting 360 until the value is within the range.

#### ID

A `string` that represents a unique identifier.
It must be unique among all IDs used in an OCIF document.
The ID space is shared among nodes, relations, and resources.

#### MIME Type

A `string` that represents the _MIME Type_ for a resource.
Typical examples in a canvas are `text/plain`, `text/html`, `image/svg+xml`, `image/png`, `image/jpeg`, `video/mp4`.
IANA content type registry: https://www.iana.org/assignments/media-types/media-types.xhtml

#### Node

An `object` representing a visual [node](#nodes).

#### Relation

An `object` representing a [relation](#relations).

#### Representation

An `object` representing a [resource](#resources) representation.

#### Resource

An `array` of [resource](#resources).

#### Schema Entry

An `object` representing a [schema](#schemas) entry.
Schema entries assign schema _URIs_ to _Schema Names_.

#### Schema Name

A `string` that represents the name of a schema.
It must be _defined_ in the [schemas](#schemas) section of an OCIF document as a `name` property.
It can be _used_ as `type` of relation, `type` of relation extension, or `type` of node extension.

#### URI

A `string` that represents a Uniform Resource Identifier (URI) as defined in [RFC 3986](https://tools.ietf.org/html/rfc3986).

# Practical Recommendations

- The proposed MIME-type for OCIF files is `application/ocif+json`.

- [ ] IANA registration

- The recommended file extension for OCIF files is `.ocif.json`.
  This launches JSON-aware applications by default on most systems.
  The extension `.ocif` is also allowed.

- Parsing:

  - If [IDs](#id) collide, the first defined ID should be used.
    This is a simple rule, which allows for deterministic behavior.
    A warning SHOULD be emitted.

- Schema hosting:

  - A schema MUST have a [URI](#uri) as its identifier.
  - A schema SHOULD be hosted at its URI.
    - [purl.org](https://purl.archive.org/) provides a free service for stable, resolvable URIs. This requires URIs to start with `purl.org`.
  - A schema can solely exist in an OCIF file, in the [schemas](#schemas) entry. This is useful for private schemas or for testing.

  - **Recommendation**: As a good practice, "Cool URIs" (see [references](#references)) should provide services for humans and machines. Given a request to `https://example.com/schema`, the server can decide based on the HTTP `Accept`-header:

    - `application/json` -> Send JSON schema via a redirect to, e.g. `https://example.com/schema.json`
    - `text/html` -> Send a human-readable HTML page via a redirect to, e.g. `https://example.com/schema.html`.
    - See [OCWG URI Structure](#ocwg-url-structure) for a proposed URI structure for OCIF resources.

  - Versioning: Note that relation types have a version and extensions to a relation type have another version themselves.

# References

- https://www.canvasprotocol.org/ (OCWG homepage)
- https://jsoncanvas.org/ (the initial spark leading to the creation of the OCWG)
- https://github.com/orgs/ocwg/discussions (the work)
- The [big sheet](https://docs.google.com/spreadsheets/d/1XbD_WEhO2c-T21EkA6U546tpGf786itpwXdR3dmdZIA/edit?gid=199619473#gid=199619473) an analysis of features of existing canvas apps
- https://github.com/ocwg/spec/blob/initial-draft/README.md (initial spec draft)

- [Cool URIs for the Semantic Web](https://www.w3.org/TR/cooluris/) by Leo Sauermann and Richard Cyganiak, 2008. This document provides general advice on how to create URIs for the Semantic Web.
- [Cool URIs for FAIR Knowledge Graphs](https://arxiv.org/abs/2407.09237), Andreas Thalhammer, 2024. This provides up-to-date practical advice, replacing some advice given in the [Cool URIs](https://www.w3.org/TR/cooluris/) document.

# Appendix

## Built-in Schema Entries

The materialized list of schema entries, as explained in [built-in schema mappings](#built-in-schema-mappings):

```json
{
  "@ocwg/node/ports/0.2": {
    "uri": "https://spec.canvasprotocol.org/node/ports/0.2"
  },
  "@ocwg/node/relative/0.2": {
    "uri": "https://spec.canvasprotocol.org/node/relative/0.2"
  },
  "@ocwg/rel/edge/0.2": {
    "uri": "https://spec.canvasprotocol.org/rel/edge/0.2"
  },
  "@ocwg/rel/group/0.2": {
    "uri": "https://spec.canvasprotocol.org/rel/group/0.2"
  },
  "@ocwg/rel/hyperedge/0.2": {
    "uri": "https://spec.canvasprotocol.org/rel/hyperedge/0.2"
  },
  "@ocwg/rel/parent-child/0.2": {
    "uri": "https://spec.canvasprotocol.org/rel/parent-child/0.2"
  },
  "@ocwg/rel/set/0.1": { "uri": "https://spec.canvasprotocol.org/rel/set/0.1" }
}
```

## Examples

### Node Extension: Circle

This extension defines geometric circles.

- Schema: http://example.com/ns/ocif-node/circle/1.0
- Name: `@example/circle`
- Properties:

| Property | JSON Type | Required | Contents                    | Default |
| -------- | --------- | -------- | --------------------------- | ------: |
| `radius` | number    | optional | The circles radius in pixel |      10 |

- Semantics:
  - The `radius` property implies a `size`. I.e. a circle of radius _r_ implies a size of _2r_ x _2r_.

**Example** \
A circle node with a radius of 10 pixels:

```json
{
  "type": "@example/circle",
  "radius": 10
}
```

**Example** \
A node, using the circle extension, with a radius of 20 pixels:

```json
{
  "nodes": [
    {
      "id": "n1",
      "position": [10, 80],
      "size": [40, 40],
      "data": [
        {
          "type": "@example/circle",
          "radius": 20
        }
      ]
    }
  ]
}
```

### Advanced Examples

**Example** \
A node using multiple extensions.
A circle has a port at the geometric "top" position.

```json
{
  "nodes": [
    {
      "id": "n1",
      "position": [10, 80],
      "size": [40, 40],
      "data": [
        {
          "type": "@example/circle",
          "radius": 20
        },
        {
          "type": "@ocwg/node/ports",
          "ports": ["p1"]
        }
      ]
    },
    {
      "id": "p1",
      "position": [30, 80]
    }
  ]
}
```

## OCWG URL Structure

- `https://canvasprotocol.org` - info site

- `https://spec.canvasprotocol.org` - specification; REDIRECT to the latest version, e.g. `https://spec.canvasprotocol.org/v0.2`
- `https://spec.canvasprotocol.org/v0.2` - OCIF specification version; this is also its [URI](#uri)
  - `Accept='application/json'` -> REDIRECT to `https://spec.canvasprotocol.org/v0.2/schema.json`
  - `Accept='text/html'` -> REDIRECT to `https://spec.canvasprotocol.org/v0.2/spec.md`
- `https://spec.canvasprotocol.org/v0.2/schema.json` - General OCIF JSON schema
- `https://spec.canvasprotocol.org/v0.2/spec.html` - General OCIF specification as markdown file

- `https://spec.canvasprotocol.org/node/ports/0.2` - the _ports_ extension for nodes in version 0.2; this is also its [URI](#uri)
- `https://spec.canvasprotocol.org/node/ports/0.2/schema.json` - the JSON schema of the _ports_ extension for nodes in version 0.2
- `https://spec.canvasprotocol.org/spec.html#node-ports/0.2/schema.md` - the Markdown document for the _ports_ extension for nodes in version 0.2

- `https://spec.canvasprotocol.org/rel/group/0.2` - the _group_ relation type in version 0.2; this is also its [URI](#uri)
- `https://spec.canvasprotocol.org/rel/group/0.2/schema.json` - the JSON schema of the _group_ relation type in version 0.2
- `https://spec.canvasprotocol.org/rel/group/0.2/schema.md` - the Markdown document for the _group_ relation type in version 0.2

- [ ] @@ Can this setup be done using GitHub pages?

## Changes

### From v0.1 to v0.2

- Root property `schema_version` renamed to `ocif` -- this is simpler and serves as a kind of "magic" signature, i.e., a JSON document with an "ocif" property near the top is likely an OCIF file.
- Renamed node `properties` to `data` -- this is simpler and more generic.
- Relation property `name` renamed to `type`.

### From v0.2.0 to v0.2.1

- Relation types and relation extensions merged into one. There is now a base relation, which has extensions.
- Node rotation center fixed.
- Schema object to a schema array, see [design decision](../design/ocwg-design-decisions.md#list-or-map).

---

# TODO @@

- [ ] Update JSON schemas. We need
  - ocif-02.json
  - node-ports-02.json
  - node-relative-02.json
  - rel-edge-02.json
  - rel-group-02.json
  - rel-hyperedge-02.json
  - rel-parent-child-02.json
  - rel-set-02.json
- [ ] more pictures (see /design)
- [ ] more examples

## Discuss

- [ ] node id uniqueness when nesting canvases

## LATER

- [ ] add top level extension data for different canvas apps
- [ ] Have a wikipedia article defining the "canvas app" -> https://github.com/orgs/ocwg/discussions/39
  - [ ] link article in [introduction](#introduction)
- [ ] tldraw as a running example -> full mapping defined in another file -> https://github.com/orgs/ocwg/discussions/40
- [ ] Add a layer relation type (what is the difference to group?)

## Notes to the Editor

- All URIs should have the same, consistent structure
- Property tables should follow these conventions
  - **required** is always bold, other entries are not
  - JSON types are set in `monospace`
  - OCIF types are linked to their definition
  - All examples start with `**Example:**`
  - Order of columns is always: Property, JSON Type, OCIF Type, Required, Contents, Default
    - Empty columns can be omitted
