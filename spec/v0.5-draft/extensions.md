# OCIF Extensions

Copyright © 2025 the Contributors to the Open Canvas Working Group (OCWG).

Versioning Policy: Currently, all extensions in this document are at version **0.4**.

## Abstract

This document describes extensions to the Open Canvas Interchange Format (OCIF) that are not part of the core specification. Extensions are optional and may be used to add additional functionality to OCIF documents.
See the [OCIF Core Specification](./spec) for the core features of OCIF.

## Status of this Document

This document is an editor's draft and has no official standing. It is a work in progress and may be updated, replaced, or obsoleted by other documents at any time.

**Legal**:
Open Canvas Interchange Format (OCIF) v0.5 © 2025 by Open Canvas Working Group is licensed under CC BY-SA 4.0. To view a copy of this licence, visit https://creativecommons.org/licenses/by-sa/4.0/

### Table of Contents

<!-- TOC -->
* [OCIF Extensions](#ocif-extensions)
  * [Abstract](#abstract)
  * [Status of this Document](#status-of-this-document)
    * [Table of Contents](#table-of-contents)
* [Node Extensions](#node-extensions)
  * [Ports Node](#ports-node)
  * [Node Transforms](#node-transforms)
  * [Anchored Node](#anchored-node)
  * [Text Style Node](#text-style-node)
* [Relation Extensions](#relation-extensions)
  * [Hyperedge Relation](#hyperedge-relation)
  * [Parent-Child Relation](#parent-child-relation)
  * [Changes](#changes)
    * [From v0.4 to v0.5](#from-v04-to-v05)
    * [Up to v0.4](#up-to-v04)
<!-- TOC -->

# Node Extensions

These are [extensions](spec.md#extensions) that can be added to nodes in an OCIF document.
To be placed inside the `data` `array`.

## Ports Node

- Name: `@ocif/node/ports`
- URI: `https://spec.canvasprotocol.org/v0.5/extensions/ports-node.json`

It provides the familiar concept of _ports_ to a node. A port is a point that allows geometrically controlling where, e.g., arrows are attached to a shape.

Any node can act as a port. The 'container' node uses the _Ports Extension_ to define which nodes it uses as ports.
The _Ports Extension_ has the following properties:

| Property | JSON Type | OCIF Type        | Required     | Contents                      | Default |
|----------|-----------|------------------|--------------|-------------------------------|---------|
| `ports`  | `array`   | [ID](spec.md#id) | **required** | IDs of nodes acting as ports. |         |

Each node SHOULD appear only in **one** ports array.
A port cannot be used by multiple nodes as a port.

**Example** \
A node (n1) with two ports (p1, p2).
Note that _p1_ and _p2_ are normal nodes.
It is the node _n1_ which uses _p1_ and _p2_ as its ports.
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







## Node Transforms

- Name: `@ocif/node/transform`
- URI: `https://spec.canvasprotocol.org/v0.5/ext/transform-node.json`

The node transform extension allows customizing the local coordinate system of a node relative to the parent coordinate system.
This is a concept commonly found in game engines and infinitely zoomable canvases.

The default parent coordinate system is the global, canvas-wide coordinate system.
If the [parent-child-relation](#parent-child-relation) is used, the defined parent node MUST be used as the one,
from which the reference coordinate system is used.

The transforms affect the local coordinate system, which is used to display resources (see [spec](spec.md#size-and-resource)) and child nodes. The child nodes have global coordinates, and the node transform extension can provide the "recipe" how to calculate the global positions of a node when, e.g., the parent has been moved, rotated, or scaled.

Transforms are chainable. For example, a node A may transform its coordinate system relative to the canvas. Node B may transform relative to the coordinate system of its parent A. Then node C transforms again relative to its parent B. The resulting scale, rotation, and offset computation requires computing first A, then B, then C.

| Property       | JSON Type                          | OCIF Type | Required     | Contents            | Default   |
|----------------|------------------------------------|-----------|--------------|---------------------|-----------|
| `scale`        | `number`, `number[2]`, `number[3]` | Vector    | **optional** | Scale factor        | `1`       |
| `rotation`     | `number`                           | Angle     | **optional** | Rotation in degrees | `0`       |
| `rotationAxis` | `number[3]`                        |           | **optional** | Rotation axis       | `[0,0,1]` |
| `offset`       | `number`, `number[2]`, `number[3]` | Vector    | **optional** | Offset              | `0`       |

- **scale**: A number-vector (floating-point) to override (set) the automatic scale factor of the node. This defines the scale of the local coordinate system. A larger scale SHOULD also affect font sizes. The scale factors are multiplied component-wise to the parent coordinate system.

    - NOTE: The scale factors cannot be computed from global positions alone.
      Scale factors provide additional state which influences interaction behaviour, e.g., an item drag-dropped into an item with a scale factor of less than 1 causes the item to shrink, when released.

- **rotation**: A number-vector (floating-point) to override (set) the rotation of the node.
  - This (relative, local) rotation is added to the rotation of the parent.
  - A single number around the axis defined in `rotationAxis`, in degrees in counter-clockwise direction.

- **rotationAxis**: The default is `(0,0,1)`. This is the [axis-angle notation](https://en.wikipedia.org/wiki/Axis%E2%80%93angle_representation). The default is to use the z-axis, which results in 'normal' 2D rotation in the x-y-plane.

- **offset**: A number-vector (floating-point). This vector is added to the parent position to result in the childs position. This is the 'recipe' how the global child positions have been computed.

  - Semantics: When the parent is moved in an app, the app SHOULD update the children's position accordingly. The offset remains unchanged, unless the child itself is moved. In that case, the offset and the position of the child should be adapted.

**Practical Advice**\
On import, the global positions can be used as-is.
For text rendering, the scale factors SHOULD be taken into account.
For interactive apps, the transforms allow to adapt on parent changes.
Furthermore, when zooming very large maps, position and size should be computed on the fly using node transforms, as global positions would become unstable due to numeric precision.


**Example:** A node with a scale factor:

```json
{
  "id": "node-with-image",
  "position": [100, 100],
  "size": [100, 200],
  "resource": "frog",
  "data": [
    { "type": "@ocif/node/transform",
      "scale": 0.5 }
  ]
}
```
JSON schema: [transform-node.json](extensions/transforms-node.json)



## Anchored Node

- Name: `@ocif/node/anchored`
- URI: `https://spec.canvasprotocol.org/v0.5/extensions/anchored-node.json`

Relative positioning requires anchoring to a parent item.
The parent position is interpreted as the root of a local coordinate system.
The parent size is added to the position and yields the coordindat of the _one_.
This is (1,1) in 2D and (1,1,1) in 3D.
Now nodes can be positioned relative to the parent using relative positions.
The coordinates in [0,1]x[0,1] (or [0,1]x[0,1]x[0,1] in 3D) cover any position within the parent item.
These percentage-coordinates are now used to position the item.

| Property              | JSON Type                  | OCIF Type             | Required     | Contents            | Default         |
|-----------------------|----------------------------|-----------------------|--------------|---------------------|-----------------|
| `topLeftPosition`     | `number[2]` or `number[3]` | Percentage Coordinate | **optional** | Top left anchor     | [0,0] / [0,0,0] |
| `bottomRightPosition` | `number[2]` or `number[3]` | Percentage Coordinate | **optional** | Bottom-right anchor | [1,1] / [1,1,1] |
| `topLeftOffset`       | `number[2]` or `number[3]` | Absolute offset       | **optional** | Top left offset     | [0,0] / [0,0,0] |
| `bottomRightOffset`   | `number[2]` or `number[3]` | Absolute offset       | **optional** | Bottom-right offset | [0,0] / [0,0,0] |

The offsets are interpreted in the global parents (#TODO or global?) coordinate system.

@@ What is the interpretation if the top-left is given and bottom-right is not?

JSON schema: [anchored-node.json](extensions/anchored-node.json)


## Text Style Node

- Name: `@ocif/node/textstyle`
- URI: `https://spec.canvasprotocol.org/v0.5/extensions/textstyle-node.json`

The text style extension allows setting common properties for rendering plain text and structured text (such as Markdown or AsciiDoc).


| Property     | JSON Type | OCIF Type       | Required | Contents    | Default      |
|--------------|-----------|-----------------|----------|-------------|--------------|
| `fontSizePx` | `number`  |                 | optional | Font size   | `12px`       |
| `fontFamily` | `string`  |                 | optional | Font family | `sans-serif` |
| `color`      | `string`  | Color           | optional | Text color  | `#000000`    |
| `align`      | `string`  | enum, see below | optional | Alignment   | `left`       |
| `bold`       | `boolean` |                 | optional | Bold text   | `false`      |
| `italic`     | `boolean` |                 | optional | Italic text | `false`      |

* **fontSize**: The font size in `px`, as used in CSS.
* **fontFamily**: The font family, as used in CSS.
* **color**: The text color. See [Color](spec.md#color).
* **align**: The text alignment. Possible values are `left`, `right`, `center`, `justify`.
* **bold**: A boolean flag indicating if the text should be bold.
* **italic**: A boolean flag indicating if the text should be italic.

JSON schema: [textstyle-node.json](extensions/textstyle-node.json)


# Relation Extensions

## Hyperedge Relation

- Name: `@ocif/rel/hyperedge`
- URI: `https://spec.canvasprotocol.org/v0.5/extensions/hyperedge-rel.json`

A hyperedge is a relation that connects any number of nodes.
Hyperedges can also be used to model simple bi-edges.

Conceptually, a hyper-edge is an entity that has a number of _endpoints_.
For each endpoint, we allow defining the directionality of the connection.
The endpoints are explicitly defined as an ordered list, i.e., endpoints can be addressed by their position in the list.
Such a model allows representing all kinds of hyper-edges, even rather obscure one.

A hyper-edge in OCIF has the following properties:

| Property    | JSON Type | OCIF Type | Required     | Contents                             | Default |
|-------------|-----------|-----------|--------------|--------------------------------------|--------:|
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
|-------------|-----------|------------------|--------------|------------------------------------------|---------|
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
- URI: `https://spec.canvasprotocol.org/v0.5/extensions/parent-child-rel.json`

A parent-child relation models a strict hierarchical relationship between nodes or relations.
It can be used to model inheritance, containment, or other hierarchical relationships.

| Property        | JSON Type | OCIF Type        | Required     | Contents                                | Default |
|-----------------|-----------|------------------|--------------|-----------------------------------------|:--------|
| `parent`        | `string`  | [ID](spec.md#id) | optional     | ID of the parent.                       | empty   |
| `child`         | `string`  | [ID](spec.md#id) | **required** | ID of the child.                        |         |
| `inherit`       | `boolean` |                  | optional     | Inherit properties.                     | `false` |
| `cascadeDelete` | `boolean` |                  | optional     | Delete children when parent is deleted. | `true`  |

- **parent**: The ID of the parent node or relation. There MUST be only one parent per child.
  - If empty, the canvas itself acts as the parent node. This is relevant for [node transforms](#node-transforms).

- **child**: The ID of the child node or relation. A parent can have multiple children (expressed my multiple parent-child relations).

- **inherit**: A boolean flag indicating if the child should inherit properties from the parent. Default is `false`.

  - The Exact semantics of inheritance are defined by the application.
  - In general, when looking for JSON properties of a child and finding them undefined, an app should look for the same value in the parent.
    The chain of parents should be followed until a root is reached or a cycle is detected.

- **cascadeDelete**: A boolean flag indiciating if the children should be deleted when the parent is deleted. Default it `true`.

Semantics:

- If a parent is deleted, all children, which inherit from the parent, SHOULD also be deleted. (see **cascadeDelete** property)

JSON schema: [parent-child-rel.json](extensions/parent-child-rel.json)


## Changes

### From v0.4 to v0.5
- "Relative Node" has been replaced by "Node Transforms"
- "Anchored Node" has been added

### Up to v0.4
- Simplified language from "Ports Extension" to "Ports Node", from "Relative Constraints Extension" to "Relative Node."
- Moved "freehand-node" to issues https://github.com/ocwg/spec/issues/14
- 2025-01-21: Initial version of the document.
