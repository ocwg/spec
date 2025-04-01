# OCIF Extensions

Copyright © 2025 the Contributors to the Open Canvas Working Group (OCWG).

Versioning Policy: Currently, all extensions in this document are at version **0.4**.

## Abstract

This document describes extensions to the Open Canvas Interchange Format (OCIF) that are not part of the core specification. Extensions are optional and may be used to add additional functionality to OCIF documents.
See the [OCIF Core Specification](./spec) for the core features of OCIF.

## Status of this Document

This document is an editor's draft and has no official standing. It is a work in progress and may be updated, replaced, or obsoleted by other documents at any time.

**Legal**:
Open Canvas Interchange Format (OCIF) v0.4 © 2025 by Open Canvas Working Group is licensed under CC BY-SA 4.0. To view a copy of this licence, visit https://creativecommons.org/licenses/by-sa/4.0/

### Table of Contents

<!-- TOC -->

- [OCIF Extensions](#ocif-extensions)
  - [Abstract](#abstract)
  - [Status of this Document](#status-of-this-document)
    - [Table of Contents](#table-of-contents)
- [Node Extensions](#node-extensions)
  - [Ports Node](#ports-node)
  - [Relative Node](#relative-node)
- [Relation Extensions](#relation-extensions)
  - [Hyperedge Relation](#hyperedge-relation)
  - [Parent-Child Relation](#parent-child-relation)
- [Changes](#changes)
<!-- TOC -->

# Node Extensions

These are [extension](spec.md#extensions) that can be added to nodes in an OCIF document.
To be placed inside the `data` `array`.

## Ports Node

- Name: `@ocif/node/ports`
- URI: `https://spec.canvasprotocol.org/v0.4/extensions/ports-node.json`

It provides the familiar concept of _ports_ to a node. A port is a point, which allows geometrically controlling where, e.g., arrows are attached to a shape.

Any node can act as a port. The 'container' node uses the _Ports Extension_ to define which nodes it uses as ports.
The _Ports Extension_ has the following properties:

| Property | JSON Type | OCIF Type        | Required     | Contents                      | Default |
| -------- | --------- | ---------------- | ------------ | ----------------------------- | ------- |
| `ports`  | `array`   | [ID](spec.md#id) | **required** | IDs of nodes acting as ports. |         |

Each node SHOULD appear only in **one** ports array.
A port cannot be used by multiple nodes as a port.

**Example** \
A node (n1) with two ports (p1, p2).
Note that p1 and p2 are normal nodes.
It's node n1 which uses p1 and p2 as its ports.
An arrow can now start at node p1 (which is a port of n1) and end at node n2 (which is not a port in this example).

```json
{
  "nodes": [
    {
      "id": "n1",
      "position": [100, 100],
      "size": [100, 100],
      "data": [
        {
          "type": "@ocif/node/ports",
          "ports": ["p1", "p2"]
        }
      ]
    },
    {
      "id": "p1",
      "position": [200, 200]
    },
    {
      "id": "p2",
      "position": [100, 200]
    },
    {
      "id": "n2",
      "position": [800, 800]
    }
  ]
}
```

JSON schema: [ports-node.json](extensions/ports-node.json)

## Relative Node

- Name: `@ocif/node/relative`
- URI: `https://spec.canvasprotocol.org/v0.4/extensions/relative-node.json`

Relative constraints are used to define, e.g., the relative positioning of nodes.

The node on which this extension is placed is the _target node_.
It defines a _source_ node, which is used as a reference for the relative positioning.

| Property   | JSON Type | OCIF Type              | Required     | Contents                         | Default |
| ---------- | --------- | ---------------------- | ------------ | -------------------------------- | ------- |
| `source`   | `string`  | [ID](spec.md#id)       | **required** | ID of the source node.           |         |
| `position` | `array`   | number[]               | optional     | Relative position of the target. | `[0,0]` |
| `rotation` | `number`  | [Angle](spec.md#angle) | optional     | Relative angle of the target.    | `0`     |

- **source**: The ID of the source node, which is used as a reference for the relative positioning.
- **position**: The relative position of the target node to the source node.
  The numbers given in the array are vector-added to the position of the source node.
- **rotation**: The relative rotation of the target node to the source node. The rotation is added to the rotation of the source node.

JSON schema: [relative-node.json](extensions/relative-node.json)

# Relation Extensions

## Hyperedge Relation

- Name: `@ocif/rel/hyperedge`
- URI: `https://spec.canvasprotocol.org/v0.4/extensions/hyperedge-rel.json`

A hyperedge is a relation, which connects any number of nodes.
Hyperedges can also be used to model simple bi-edges.

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
- **rel**: See [Edge Relation](spec.md#edge-relation)

**Endpoint** \
Each endpoint is an object with the following properties:

| Property    | JSON Type | OCIF Type        | Required     | Contents                                 | Default |
| ----------- | --------- | ---------------- | ------------ | ---------------------------------------- | ------- |
| `id`        | `string`  | [ID](spec.md#id) | **required** | ID of attached entity (node or relation) |         |
| `direction` | `string`  | Direction        | optional     | Direction of the connection.             | `undir` |
| `weight`    | `number`  |                  | optional     | Weight of the edge                       | `1.0`   |

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
  "type": "@ocif/rel/hyperedge",
  "endpoints": [
    { "id": "n1", "direction": "in" },
    { "id": "n2", "direction": "in" },
    { "id": "n3", "direction": "out" }
  ]
}
```

JSON schema: [hyperedge-rel.json](extensions/hyperedge-rel.json)

## Parent-Child Relation

- Name: `@ocif/rel/parent-child`
- URI: `https://spec.canvasprotocol.org/v0.4/extensions/parent-child-rel.json`

A parent-child relation models a hierarchical relationship between nodes.
It can be used to model inheritance, containment, or other hierarchical relationships.

| Property  | JSON Type | OCIF Type        | Required     | Contents               | Default |
| --------- | --------- | ---------------- | ------------ | ---------------------- | :------ |
| `parent`  | `string`  | [ID](spec.md#id) | **required** | ID of the parent node. |         |
| `child`   | `string`  | [ID](spec.md#id) | **required** | ID of the child node.  |         |
| `inherit` | `boolean` |                  | optional     | Inherit properties.    | `false` |

- **parent**: The ID of the parent node. There SHOULD be only one parent per child.

- **child**: The ID of the child node. A parent can have multiple children (expressed my multiple parent-child relations).

- **inherit**: A boolean flag indicating if the child should inherit properties from the parent. Default is `false`.
  - The Exact semantics of inheritance are defined by the application.
  - In general, when looking for JSON properties of a node and finding them undefined, an app should look for the same value in the parent node.
    The chain of parents should be followed until a root is reached or a cycle is detected.

Semantics:

- If a parent is deleted, all children, which inherit from the parent, SHOULD also be deleted.

JSON schema: [parent-child-rel.json](extensions/parent-child-rel.json)

# Changes

- Simplified language from "Ports Extension" to "Ports Node", from "Relative Constraints Extension" to "Relative Node."
- Moved "freehand-node" to issues https://github.com/ocwg/spec/issues/14
- 2025-01-21: Initial version of the document.
