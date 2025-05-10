# Open Canvas Interchange Format (OCIF)

**OCWG Candidate Recommendation, April 2025**

**This version:** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; https://spec.canvasprotocol.org/v0.6 \
**Latest version:** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; https://spec.canvasprotocol.org/v0.6 \
**Previous version:** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; https://spec.canvasprotocol.org/v0.4.0

**Feedback:** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; https://github.com/orgs/ocwg/discussions

**Editor:** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dr. Max Völkel ([ITMV](https://maxvoelkel.de))

**Authors (alphabetically):** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aaron Franke](https://github.com/aaronfranke/) (Godot Engine), \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Maikel van de Lisdonk](https://devhelpr.com) ([Code Flow Canvas](https://codeflowcanvas.io/)) \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Jess Martin](https://jessmart.in) ([sociotechnica](https://sociotechnica.org)) \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Orion Reed

Copyright © 2024, 2025 the Contributors to the Open Canvas Working Group (OCWG).

## Abstract

An interchange file format for canvas-based applications. Visual nodes, structural relations, assets, and schemas.

## Status of this Document

This document is a candidate recommendation (CR). The Open Canvas Working Group (OCWG) is inviting implementation feedback.

**Legal**:
Open Canvas Interchange Format (OCIF) v0.6 © 2025 by Open Canvas Working Group is licensed under CC BY-SA 4.0. To view a copy of this licence, visit https://creativecommons.org/licenses/by-sa/4.0/

## Document Conventions

- Types:
  This document defines a catalog of _OCIF types_, which are more precise than the generic JSON types.
  See [OCIF Types](#ocif-types) for a catalog.
- The terms _OCIF file_ and _OCIF document_ are used interchangeably.


### Table of Contents

<!-- TOC -->
* [Open Canvas Interchange Format (OCIF)](#open-canvas-interchange-format-ocif)
  * [Abstract](#abstract)
  * [Status of this Document](#status-of-this-document)
  * [Document Conventions](#document-conventions)
    * [Table of Contents](#table-of-contents)
* [Introduction](#introduction)
  * [Hello World Example](#hello-world-example)
* [File Structure](#file-structure)
  * [Canvas Extensions](#canvas-extensions)
    * [Canvas Viewport](#canvas-viewport)
* [Nodes](#nodes)
  * [Size and Resource](#size-and-resource)
  * ["Text Nodes"](#text-nodes)
  * ["Image Nodes"](#image-nodes)
  * [Root Node](#root-node)
    * [Nesting Canvases](#nesting-canvases)
      * [Partial Export](#partial-export)
* [Node Extensions](#node-extensions)
  * [Rectangle Extension](#rectangle-extension)
  * [Oval Extension](#oval-extension)
  * [Arrow Extension](#arrow-extension)
  * [Path Extension](#path-extension)
  * [Ports Node Extension](#ports-node-extension)
  * [Node Transforms Extension](#node-transforms-extension)
  * [Anchored Node Extension](#anchored-node-extension)
  * [Text Style Node Extension](#text-style-node-extension)
  * [Theme Node Extension](#theme-node-extension)
    * [Theme Selection](#theme-selection)
  * [Page Node Extension](#page-node-extension)
* [Relations](#relations)
* [Relation Extensions](#relation-extensions)
  * [Group Relation Extension](#group-relation-extension)
  * [Edge Relation Extension](#edge-relation-extension)
  * [Parent-Child Relation Extension](#parent-child-relation-extension)
  * [Hyperedge Relation Extension](#hyperedge-relation-extension)
* [Assets](#assets)
  * [Resources](#resources)
    * [Representation](#representation)
    * [Fallback](#fallback)
    * [Nodes as Resources](#nodes-as-resources)
      * [Semantics](#semantics)
  * [Schemas](#schemas)
    * [Built-in Schema Mappings](#built-in-schema-mappings)
* [Extensions](#extensions)
  * [Extension Mechanism](#extension-mechanism)
  * [Defining Extensions](#defining-extensions)
    * [How To Write an Extension Step-by-Step](#how-to-write-an-extension-step-by-step)
  * [Exporting Data with Extensions](#exporting-data-with-extensions)
  * [Handling Extension Data](#handling-extension-data)
* [OCIF Types](#ocif-types)
  * [Angle](#angle)
  * [Color](#color)
  * [ID](#id)
  * [MIME Type](#mime-type)
  * [Node](#node)
  * [Relation](#relation)
  * [Representation](#representation-1)
  * [Resource](#resource)
  * [Schema Entry](#schema-entry)
  * [Schema Name](#schema-name)
  * [URI](#uri)
  * [Vector](#vector)
* [Practical Recommendations](#practical-recommendations)
* [References](#references)
* [Appendix](#appendix)
  * [Built-in Schema Entries](#built-in-schema-entries)
  * [Examples](#examples)
    * [Node Extension: Circle](#node-extension-circle)
    * [Advanced Examples](#advanced-examples)
  * [OCWG URL Structure (Planned)](#ocwg-url-structure-planned)
  * [Syntax Conventions](#syntax-conventions)
  * [Changes](#changes)
    * [From v0.5 to v0.6](#from-v05-to-v06)
    * [From v0.4 to v0.5](#from-v04-to-v05)
    * [From v0.3 to v0.4](#from-v03-to-v04)
    * [From v0.2.1 to v0.3](#from-v021-to-v03)
    * [From v0.2.0 to v0.2.1](#from-v020-to-v021)
    * [From v0.1 to v0.2](#from-v01-to-v02)
<!-- TOC -->

# Introduction

This document describes the Open Canvas Interchange Format (OCIF), which allows canvas-applications to exchange their data.

**Other Documents** \
For more information about the goals and requirements considered for this spec, see the [Goals](../../design/goals.md), [Requirements](../../design/requirements.md) and [Design Decisions](../../design/design-decisions.md) documents.
**For practical advice on how to use OCIF, see the [OCIF Cookbook](../../cookbook.md).**

**Canvas** \
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

## Hello World Example

Given two nodes, a rectangle with the word "Berlin" and an oval with "Germany."
We let an arrow point from Berlin to Germany.
The arrow represents a relation of the kind "is capital of."

![Hello World Example image](./hello-world-example.png)

In OCIF, it looks like this (using JSON5 here):

```json5
{
  ocif: "https://canvasprotocol.org/ocif/v0.6",
  nodes: [
    {
      id: "berlin-node",
      position: [100, 100],
      size: [100, 50],
      resource: "berlin-res",
      /* a green rect with a 3 pixel wide black border line */
      data: [
        {
          type: "@ocif/node/rect",
          strokeWidth: 3,
          strokeColor: "#000000",
          fillColor: "#00FF00",
        },
      ],
    },
    {
      id: "germany-node",
      position: [300, 100],
      /* slightly bigger than Berlin */
      size: [100, 60],
      resource: "germany-res",
      /* a white rect with a 5 pixel wide red border line */
      data: [
        {
          type: "@ocif/node/oval",
          strokeWidth: 5,
          strokeColor: "#FF0000",
          fillColor: "#FFFFFF",
        },
      ],
    },
    {
      id: "arrow-1",
      data: [
        {
          type: "@ocif/node/arrow",
          strokeColor: "#000000",
          /* right side of Berlin */
          start: [200, 125],
          /* center of Germany */
          end: [350, 130],
          startMarker: "none",
          endMarker: "arrowhead",
          /* link to relation which is shown by this arrow */
          relation: "relation-1",
        },
      ],
    },
  ],
  relations: [
    {
      id: "relation-1",
      data: [
        {
          type: "@ocif/rel/edge",
          start: "berlin-node",
          end: "germany-node",
          /* WikiData 'is capital of'.
           We could also omit this or just put the string 'is capital of' here. */
          rel: "https://www.wikidata.org/wiki/Property:P1376",
          /* link back to the visual node representing this relation */
          node: "arrow-1",
        },
      ],
    },
  ],
  resources: [
    {
      id: "berlin-res",
      representations: [{ "mimeType": "text/plain", content: "Berlin" }],
    },
    {
      id: "germany-res",
      representations: [{ "mimeType": "text/plain", content: "Germany 🇩🇪" }],
    },
  ],
}
```
More examples can be found in the [cookbook](./../../cookbook.md).

# File Structure

The OCIF file is a JSON object with the following properties:

| Property    | JSON Type | OCIF Type                         | Required     | Contents                          |
|-------------|-----------|:----------------------------------|--------------|-----------------------------------|
| `ocif`      | `string`  | [URI](#uri)                       | **required** | The URI of the OCIF schema        |
| `rootNode`  | `string`  | [ID](#id)                         | optional     | A canvas root [node](#nodes)      |
| `data`      | `array`   | array of [Extension](#extensions) | optional     | Extended canvas data              |
| `nodes`     | `array`   | [Node](#node)[]                   | optional     | A list of [nodes](#nodes)         |
| `relations` | `array`   | [Relation](#relation)[]           | optional     | A list of [relations](#relations) |
| `resources` | `array`   | [Resource](#resource)[]           | optional     | A list of [resources](#resources) |
| `schemas`   | `array`   | [Schema Entry](#schema-entry)[]   | optional     | Declared [schemas](#schemas)      |

- **OCIF**: The _Open Canvas Interchange Format_ schema URI.
  - The URI SHOULD contain the version number of the schema, either as a version number or as a date (preferred).
  - Known versions:
    - `https://spec.canvasprotocol.org/v0.1` Retrospectively assigned URI for the first draft at https://github.com/ocwg/spec/blob/initial-draft/README.md
    - `https://spec.canvasprotocol.org/v0.2` This is a preliminary version, as described in this draft, for experiments
    - `https://spec.canvasprotocol.org/v0.3` This is the first stable version.

- **rootNode**: An optional [root node](#root-node) id. It MUST point to a node defined within the `nodes` array.

- **data**: Additional properties of the canvas.
  The canvas may have any number of extensions. Each extension is a JSON object with a `type` property.
  See [extensions](#extensions).

- **nodes**: A list of nodes on the canvas. See [Nodes](#nodes) for details.

- **relations**: A list of relations between nodes (and relations). See [Relations](#relations) for details.

- **resources**: A list of resources used by nodes. See [Resources](#resources) for details.

- **schemas**: A list of schema entries, which are used for relation types and extensions. See [Schemas](#schemas) for details.

JSON schema: [schema.json](schema.json)

**Example** \
A minimal valid OCIF file without any visible items, relations or assets.

```json
{
  "ocif": "https://canvasprotocol.org/ocif/v0.6"
}
```

**Example** \
A small OCIF file, with one node and one resource.
Visually, this should render as a box placed with the top-left corner at (100,100), an application-determined size, containing the text `Hello, World!`.

```json
{
  "ocif": "https://canvasprotocol.org/ocif/v0.6",
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
      "representations": [{ "mimeType": "text/plain", "content": "Hello, World!" }]
    }
  ]
}
```

## Canvas Extensions
The canvas itself, the whole OCIF document, is also an element that can be extended.

### Canvas Viewport
The initial _viewport_ of an OCIF file can be defined with this viewport extension.

- Name: `@ocif/canvas/viewport`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/viewport-canvas.json`

A viewport is a rectangle that defines at what part of a canvas the app should initially pan and zoom.
The viewport is defined relative to the canvas coordinate system, which is defined by its explicit or implicit [root node](#root-node).
A user's monitor and window sizing define an effective aspect-ratio.
The view should be centered within the available screen space.
The viewport should be shown as large as possible, while maintaining its defined aspect-ratio.
Thus, the effective rendered view might be showing more of the canvas on the top and bottom or on the left and right, than stated in the viewport.

NOTE: To achieve this, the application should calculate a zoom factor as min(canvas_width / viewport_width, canvas_height / viewport_height). The view should then be centered by calculating the top-left pan offset as x: (canvas_width - viewport_width * zoom) / 2 and y: (canvas_height - viewport_height * zoom) / 2.

| Property   | JSON Type | OCIF Type | Required     | Contents                                | Default     |
|------------|-----------|-----------|--------------|-----------------------------------------|-------------|
| `position` | `array`   | number[]  | **required** | Coordinate as (x,y) or (x,y,z).         | [0,0]       |
| `size`     | `array`   | number[]  | **required** | The size of the viewport per dimension. | `[100,100]` |

- **position**:
  The top-left corner of the viewport.

- **size**:
  The width and height (in 3D: also depth) of the viewport.


JSON schema: [viewport-canvas.json](extensions/viewport-canvas.json)


# Nodes

Nodes represent visual items on the canvas.
Conceptually, a node is a rectangle (bounding box) on the canvas, often displaying some content (resource).
A _Node_ is an `object` with the following properties:

| Property      | JSON Type | OCIF Type                         | Required     | Contents                            | Default     |
|---------------|-----------|-----------------------------------|--------------|-------------------------------------|-------------|
| `id`          | `string`  | [ID](#id)                         | **required** | A unique identifier for the node.   | n/a         |
| `position`    | `array`   | number[]                          | recommended  | Coordinate as (x,y) or (x,y,z).     | [0,0]       |
| `size`        | `array`   | number[]                          | recommended  | The size of the node per dimension. | `[100,100]` |
| `resource`    | `string`  | [ID](#id)                         | optional     | The resource to display             |             |
| `resourceFit` | `string`  | enum, see below                   | optional     | Fitting resource in item            | `contain`   |
| `data`        | `array`   | array of [Extension](#extensions) | optional     | Extended node data                  |             |
| `rotation`    | `number`  | [Angle](#angle)                   | optional     | +/- 360 degrees                     | `0`         |
| `relation`    | `string`  | [ID](#id)                         | optional     | ID of a [relation](#relation)       | n/a         |

NOTE: JSON numbers allow integer and floating-point values, so does OCIF.

- **id**: A unique identifier for the node. Must be unique within an OCIF file. See [ID](#ocif-types) type for details.

- **position**: The position of the node on the canvas.

  - Required are **x** (at position `0`) and **y** (at position `1`). Optional is **z** at position `2`.
  - The _coordinate system_ has the x-axis pointing to the right, the y-axis pointing down, and the z-axis pointing away from the screen. This is the same as in CSS, SVG, and most 2D and 3D graphics libraries. The origin is the top-left corner of the canvas.
  - The unit is logical pixels (as used in CSS for `px`).
  - The positioned point (to which the `position` refers) is the top-left corner of the node.
  - The position is global. The computation for this position -- a local position -- can _additionally_ be stated using the [node transforms](spec.md#node-transforms-extension) extension. When the OCIF file is generated, such computations should be applied and the resulting global position be written redundantly into this property.
  - The default for z-axis is `0` when importing 2D as 3D.
  - When importing 3D as 2D, the z-axis is ignored (but can be left as-is). When a position is changed, the z-axis CAN be set to 0. Yes, this implies that full round-tripping is not always possible.
  - Values on all three axes can be negative.

- **size**: The size of the node in dimensions. I.e., this is **x-axis** ("width" at position `0`), **y-axis** ("height" at position `1`), and **z-axis** ("depth" at position `2`).

  - Size might be omitted if a linked resource defines the size. E.g., raster images such as PNG and JPEG define their size in pixels. SVG can have a `viewbox` defined, but may also omit it. Text can be wrapped at any width, so a size property is clearly required. In general, a size property is really useful as a fall-back to display at least a kind of rectangle if the resource cannot be displayed as intended. Size can only be omitted if _all_ resource representations define a size.\
  - See also [Size and Resource](#size-and-resource)

- **data**: Additional properties of the node.
  A node may have any number of extensions. Each extension is a JSON object with a `type` property.
  See [extensions](#extensions).

- **resource**: A reference to a resource, which can be an image, video, or audio file. See [resources](#resources).

  - Resource can be empty, in which case a node is acting as a transform for other nodes.
  - Resource content is cropped/limited by the node boundaries. This is commonly called _clip children_. Only in this respect the resource content is a kind of child. In CSS, this is called `overflow: hidden`.

  - Resources can define ornamental borders, e.g., a rectangle has a rectangular border, or an [oval](#oval-extension) defines an oval border. The border itself is z-ordered in front of the resource content.

- **resourceFit**: Given a node with dimensions 100 (height) x 200 (width) and a bitmap image (e.g., a .png) with a size of 1000 x 1000.
  How should this image be displayed? We re-use some options from CSS ([object-fit](https://developer.mozilla.org/en-US/docs/Web/CSS/object-fit) property):

  - `none`: All pixels are displayed in the available space unscaled. The example would be cropped down to the 100 x 200 area top-left. No auto-centering.
  - `containX`: Scaled by keeping the aspect ratio, so that the image width matches the item width. This results in the image being displayed at a scale of `0.2`, so that it is 200 px wide and 200 px high.
  NOTE: This is called `keep-width` in Godot.
  Empty space may be visible above and below the image.
  Never crops the image.
  - `containY`: Scaled by keeping the aspect ratio, so that the image height matches the item height. This results in the image being displayed at a scale of `0.1`, so that it is 100 px high and 100 px wide. The image is now fully visible, but there are boxes of empty space left and right of the image.
  Never crops the image.
  NOTE: This is called `keep-height` in Godot.
  - `contain`: Scaled by keeping the aspect ratio of the image, so that the image fits into the item for both height and width.
  The image is auto-centered vertically and horizontally.
  Empty space left and right _or_ top and bottom might appear.
    Never crops the image.
  NOTE: This is identical to auto-selecting one of the two previous options.
  This is called 'keep aspect centered' in Godot.
  - `cover`: Scaled by keeping the aspect ratio of the image, so that the image fits into the item for one of height and width while the other dimension overlaps. The overlap is cropped away and not visible. The entire view area is filled.
  - `fill`: Aspect ratio is ignored and the image is simply stretched to match the width and height of the view box.
  - `tile`: If the image is larger than the viewport, it just gets cropped. If it is smaller, it gets repeated in both dimensions. CSS calls this `background-repeat: repeat`.

- **rotation**: The absolute, global 2D rotation of the node in degrees. The rotation center is the positioned point, i.e., top-left. The z-axis is not modified.

- **relation**:
  The ID of the relation defining the semantics of the visual node (e.g., an [arrow](#arrow-extension)).
  The [relation](#relation) SHOULD point back to this visual node using its `node` property. It MAY NOT point to another visual node.
  - Deletion semantics: If a visual node is deleted, which has a `relation` stated, that underlying relation should also be deleted.


## Size and Resource
Conceptually, a node has a position (top-left) and a size.
The node position is interpreted as the root of a local coordinate system.
The size of the node is interpreted in the global coordinate system.
This yields a rectangle (bounding box) acting as a clipping mask on the contents of the node.

A node may display a resource.
This resource may have an intrinsic size (bitmap image) or at least a given aspect-ratio (vector graphics without an explicit size) or have not stated its size (text or formatted text).
For text resources, the text settings (e.g., font size and line height) define how text is wrapped and displayed in the available space.

The `scale` factor can also be manually overwritten using the [node transforms](spec.md#node-transforms-extension).


## "Text Nodes"

There is no special text node in OCIF. Text is a kind of resource. A node can display a resource.
See [Resources](#resources) for details on text resources.

**Example:** A node showing "Hello, World!" as text.

```json
{
  "nodes": [
    {
      "id": "n1",
      "position": [300, 200],
      "resource": "r1"
    }
  ],
  "resources": [
    {
      "id": "r1",
      "representations": [
        {
          "mimeType": "text/plain",
          "content": "Hello, World!"
        }
      ]
    }
  ]
}
```

TIP: Additional node extensions (e.g. [Rectangle](#rectangle-extension)) can be used to "style" the text node, e.g., by adding a background color or a border.

## "Image Nodes"

There is no special image node in OCIF. An image is another kind of resource, which can be displayed by any node.

**Example:** A node showing an image.

```json
{
  "nodes": [
    {
      "id": "n1",
      "position": [300, 200],
      "resource": "r1"
    }
  ],
  "resources": [
    {
      "id": "r1",
      "representations": [
        {
          "mimeType": "image/png",
          "location": "https://example.com/image.png"
        }
      ]
    }
  ]
}
```

TIP: Additional node extensions can be used. E.g., an [Oval](#oval-extension) could be used to display the image cropped as a circle.


## Root Node
Every canvas has **one** defined or implied _root node_.
The root node itself SHOULD not be rendered, only its interior.
If no root node is defined, an implied root with ID `rootNode` is used.

All text styles are already defined as default values of optional properties in the [text style](spec.md#text-style-node-extension) extension. A root node can define these values to set custom standard values for a whole canvas.

The root node 'contains' all nodes that are not explicitly contained by another node.
"Contain" in this context refers to the [parent-child](spec.md#parent-child-relation-extension) relation.
The root node cannot be used as a child of another node within the same file, but instances may be child nodes in another file.
If a non-root node is not a child of another node, it is implicitly a child of the root node.

The `size` property of the root node effectively defines a canvas size, much like the [viewbox](https://www.w3.org/TR/SVGTiny12/coords.html#ViewBoxAttribute) of an SVG file. However, while a viewbox in SVG allows setting the root position, the coordinate system of an OCIF file always starts in the top-left corner at (0,0).

The root node represents the entire OCIF file, and it does not make sense for a node to have a transform relative to itself. Therefore, the `position` and `rotation` properties of the root node MUST NOT be set. If the root node has either of those properties set, the app should ignore their values and emit a warning.


### Nesting Canvases
A [node](#node) 'A' in a canvas (now called 'host canvas') may use a [resource](#resource) of type `application/ocif+json` (the [recommended IANA MIME type](#practical-recommendations) for OCIF files).
That resource defines another canvas (now called 'sub-canvas').
Per definition, the sub-canvas has an explicit or implicit [root node](#root-node).
From the perspective of the host canvas, node 'A' and the sub-canvas root node 'B' **are the same node**.

The properties of the **host** canvas node 'A' extend and overwrite the properties of the imported **child** canvas root node. This even overwrites the `id` property, so that the former 'B' is now 'A' in the host canvas.
NOTE: All OCIF definitions referring to node 'B' are now also interpreted as referring to 'A'.
This allows re-using existing canvases in different ways.
That is, the host node 'A' is interpreted as a JSON Merge Patch ([RFC 7396](https://www.rfc-editor.org/info/rfc7396)) document against the sub-canvas root node 'B'.

There is one exception: `data`-extension arrays are always _merged_ (see [extension mechanism](#extension-mechanism)):
Both arrays are appended, first the sub-canvas root nodes extensions, then the host nodes 'A' extensions.
Later entries overwrite earlier entries for the same `type` of extension.

#### Partial Export
NOTE: When exporting a node from a canvas (and all its child nodes per parent-child relation), that node should become the root node of the exported sub-canvas. For consistency, all effective values, which may be inherited, need to be copied onto the exported root node.


# Node Extensions
These are [extensions](spec.md#extensions) that can be added to nodes in an OCIF document.
To be placed inside the `data` `array`.


## Rectangle Extension

- Name: `@ocif/node/rect`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/rect-node.json`

A rectangle is a visual node [extension](#extensions), to define the visual appearance of a node as a rectangle.
A core node has already a position, size, rotation, scale.

| Property      | JSON Type | OCIF Type       | Required | Contents                 | Default   |
|---------------|-----------|-----------------|----------|--------------------------|-----------|
| `strokeWidth` | `number`  | number          | optional | The line width.          | `1`       |
| `strokeColor` | `string`  | [Color](#color) | optional | The color of the stroke. | `#FFFFFF` |
| `fillColor`   | `string`  | [Color](#color) | optional | The color of the fill.   | (none)    |

- **strokeWidth**:
  The line width in logical pixels. Default is `1`. Inspired from SVG `stroke-width`.
- **strokeColor**:
  The color of the stroke. Default is white (`#FFFFFF`). Inspired from SVG `stroke`.
- **fillColor**:
  The color of the fill. Default is none, meaning fully transparent and allowing clicks to pass through.

z-order: The stroke (`strokeWidth`, `strokeColor`) SHOULD be rendered "on top" of a resource, while the fill (`fillColor`) SHOULD be rendered "behind" the resource.
So a _fillColor_ can be used for a background color.

These properties are meant to customize the built-in default stroke of a canvas app.
I.e., if all shapes in a canvas app are red and a node is using the rectangle extension but defines no color, the node should be red as well. The defaults listed in the table are just examples and can be different in different canvas apps.

JSON schema: [rect-node.json](extensions/rect-node.json)

## Oval Extension

- Name: `@ocif/node/oval`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/oval-node.json`

An oval is a visual node extension, to define the visual appearance of a node as an oval.
An oval in a square bounding box is a circle.

An oval has the exact same properties as a [Rectangle](#rectangle-extension), just the rendering is different.
The oval shall be rendered as an ellipse, within the bounding box defined by the node's position and size.

JSON schema: [oval-node.json](extensions/oval-node.json)

## Arrow Extension

- Name: `@ocif/node/arrow`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/arrow-node.json`

An arrow is a visual node that connects two point coordinates.
It should be rendered as a straight line, with optional direction markers at the start and end.

| Property      | JSON Type | OCIF Type       | Required     | Contents                | Default   |
|---------------|-----------|-----------------|--------------|-------------------------|-----------|
| `strokeWidth` | `number`  | number          | optional     | The line width.         | `1`       |
| `strokeColor` | `string`  | [Color](#color) | optional     | The color of the arrow. | `#FFFFFF` |
| `start`       | `array`   | number[]        | **required** | The start point.        | n/a       |
| `end`         | `array`   | number[]        | **required** | The end point.          | n/a       |
| `startMarker` | `string`  | string          | optional     | Marker at the start.    | `none`    |
| `endMarker`   | `string`  | string          | optional     | Marker at the end.      | `none`    |

- **strokeWidth**:
  The line width in logical pixels. Default is `1`. Inspired from SVG `stroke-width`.

- **strokeColor**:
  The color of the arrow. Default is white (`#FFFFFF`). Inspired from SVG `stroke`.

- **start**:
  The start point of the arrow. The array contains the x and y coordinates. \
  The z-coordinate, if present, is used only in 3D canvas apps.

- **end**:
  The end point of the arrow. The array contains the x and y coordinates. \
  The z-coordinate, if present, is used only in 3D canvas apps.

- **startMarker**:
  The marker at the start of the arrow.
  Possible values are:

  - `none`: No special marker at the start. A flat line at the start.
  - `arrowhead`: An arrow head at the start. The arrow head points at the start point.

- **endMarker**:
  The marker at the end of the arrow.
  Possible values are:

  - `none`: No special marker at the end. A flat line end at the end.
  - `arrowhead`: An arrow head at the end. The arrow head points at the end point.

NOTE on **position** and **size**:
An arrow should only include a position if a [resource](#resource) is stated to represent this arrow.
The geometric properties (start and end) often suffice.


The markers allow representing four kinds of arrow:

| startMarker | endMarker | Visual              |
|-------------|-----------|---------------------|
| none        | none      | start `-------` end |
| none        | arrowhead | start `------>` end |
| arrowhead   | none      | start `<------` end |
| arrowhead   | arrowhead | start `<----->` end |

NOTE: Canvas apps can use any visual shape for the markers, as long as the direction is clear.

JSON schema: [arrow-node.json](extensions/arrow-node.json)

## Path Extension

- Name: `@ocif/node/path`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/path-node.json`

A path is a visual node extension, to define the visual appearance of a node as a path.
The rendering of resources inside a path is not defined by OCIF, but by the canvas app.

| Property      | JSON Type | OCIF Type       | Required     | Contents               | Default   |
|---------------|-----------|-----------------|--------------|------------------------|-----------|
| `strokeWidth` | `number`  | number          | optional     | The line width.        | `1`       |
| `strokeColor` | `string`  | [Color](#color) | optional     | The color of the path. | `#FFFFFF` |
| `fillColor`   | `string`  | [Color](#color) | optional     | The color of the fill. | `none`    |
| `path`        | `string`  | string          | **required** | The path data.         | n/a       |

- **strokeWidth**:
  The line width in logical pixels. Default is `1`. Inspired from SVG `stroke-width`.

- **strokeColor**:
  The color of the path. Default is white (`#FFFFFF`). Inspired from SVG `stroke`.

- **fillColor**:
  The color of the fill. Default is none / fully transparent. Applies only to closed or self-intersecting paths.

- **path**:
  The path data, like the SVG path data `d` attribute. The path data is a string, which can contain the following commands:
  - `M x y`: Move to position x, y
  - `L x y`: Line to position x, y
  - `C x1 y1 x2 y2 x y`: Cubic Bézier curve to x, y with control points x1, y1 and x2, y2
  - `Q x1 y1 x y`: Quadratic Bézier curve to x, y with control point x1, y1
  - `A rx ry x-axis-rotation large-arc-flag sweep-flag x y`: Arc to x, y with radii rx, ry, x-axis-rotation, large-arc-flag, sweep-flag
  - `Z`: Close the path
  - The starting point of the path is the top-left corner of the node, i.e., the positioned point.

NOTE: Canvas apps can simplify rendering of curves (cubic/quadratic bezier, arc) to straight lines.

JSON schema: [path-node.json](extensions/path-node.json)



## Ports Node Extension

- Name: `@ocif/node/ports`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/ports-node.json`

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







## Node Transforms Extension

- Name: `@ocif/node/transforms`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/transforms-node.json`

The node transform extension allows customizing the local coordinate system of a node relative to the parent coordinate system.
This is a concept commonly found in game engines and infinitely zoomable canvases.
For some cases, the [Anchored Node Extension](#anchored-node-extension) can be a better fit.

The default parent coordinate system is the global, canvas-wide coordinate system.
If the [parent-child-relation](#parent-child-relation-extension) is used, the defined parent node MUST be used as the one,
from which the reference coordinate system is used.

The transforms affect the local coordinate system, which is used to display resources (see [spec](spec.md#size-and-resource)) and child nodes. The child nodes have global coordinates, and the node transform extension can provide the "recipe" how to calculate the global positions of a node when, e.g., the parent has been moved, rotated, or scaled.

NOTE: If both a global position and a Node Transforms Extension are present, an importing application MUST treat the global position as the authoritative value for rendering. For interactive editing, if a parent node is modified, the application SHOULD recalculate and update the global position of its children using the offset from the Node Transforms Extension. If a conflict is detected on initial load, a warning SHOULD be issued, and the global position MUST be preferred.

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



## Anchored Node Extension

- Name: `@ocif/node/anchored`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/anchored-node.json`

This extension is mainly useful to split the space of one node into several auto-resized areas. For placing elements like in a vector-drawing application, but relative to the parent node, the [node transforms](#node-transforms-extension) is often a better tool.

- Relative positioning requires anchoring to a parent item.

- The parent position is interpreted as the root of a local coordinate system.
NOTE: Parent extensions such as node transforms may have altered the parent's coordinate system. In any case, the effective coordinate system of the parent after applying all extensions on it, is used.

- The parent size is added to the position and yields the coordinate of the _one_ unit.
This is (1,1) in 2D and (1,1,1) in 3D.

- Now nodes can be positioned relative to the parent using relative positions.

NOTE: The coordinates in [0,1]x[0,1] (or [0,1]x[0,1]x[0,1] in 3D) cover any position within the parent item.
These percentage-coordinates are now used to position the item.

| Property            | JSON Type                  | OCIF Type             | Required     | Contents            | Default         |
|---------------------|----------------------------|-----------------------|--------------|---------------------|-----------------|
| `topLeftAnchor`     | `number[2]` or `number[3]` | Percentage Coordinate | **optional** | Top left anchor     | [0,0] / [0,0,0] |
| `bottomRightAnchor` | `number[2]` or `number[3]` | Percentage Coordinate | **optional** | Bottom-right anchor | [1,1] / [1,1,1] |
| `topLeftOffset`     | `number[2]` or `number[3]` | Absolute offset       | **optional** | Top left offset     | [0,0] / [0,0,0] |
| `bottomRightOffset` | `number[2]` or `number[3]` | Absolute offset       | **optional** | Bottom-right offset | [0,0] / [0,0,0] |

The offsets are interpreted in the parent's coordinate system.

If only the top-left position is given, the bottom-right position defaults to [1,1] (or [1,1,1] in 3D) as specified in the default values. This means the node would be anchored at the specified top-left position, and would extend to the bottom-right of the parent.

JSON schema: [anchored-node.json](extensions/anchored-node.json)


## Text Style Node Extension

- Name: `@ocif/node/textstyle`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/textstyle-node.json`

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


## Theme Node Extension

- Name: `@ocif/node/theme`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/theme-node.json`

The theme node extension allows defining and selecting themes.
Defining themes works in a recursive way, by setting properties in a named theme.

Example for Using a Theme on the [Root Node](spec.md#root-node):
```json
{
  "data": [{ "type": "@ocif/node/theme",
    "dark": {
      "data": [{ "type": "@ocif/node/textstyle",
        "color": "#FFFFFF"
      }]
    },
    "light": {
      "data": [{ "type": "@ocif/node/textstyle",
        "color": "#000000"
      }]
    }
  }]
}

```
So the theme branches a node content into several possible worlds and defines any values, including those in extensions.

### Theme Selection
Theme selection could happen at the canvas level or at any node in a parent-child inheritance tree. Theme selection inherits downwards. So any node (including the root node) is a good place to select a theme.
We model this with a `select-theme` property in the same extension.
The default is selecting no theme, which ignores all theme definitions.
This default theme can also be selected explicitly further down the parent-child tree by stating `"select-theme": null`.

Example for Selecting a Theme on a Node:
```json
{
  "data": [
    {
      "type": "@ocif/node/theme",
      "select-theme": "dark"
    }
  ]
}
```


## Page Node Extension

- Name: `@ocif/node/page`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/page-node.json`

The page node extension allows marking a node as a _page_.
Several infinite canvas tools have a built-in page concept.

| Property     | JSON Type | OCIF Type | Required | Contents              | Default |
|--------------|-----------|-----------|----------|-----------------------|---------|
| `pageNumber` | `number`  | number    | optional | The page number.      | `1`     |
| `label`      | `string`  | string    | optional | A label for the page. | --      |

- **pageNumber**:
  Like in a book, pages can have a number. This number defines the order of pages, when listed. The first page should be numbered `1`.
- **label**:
  A label for the page. To be displayed, for example, in a special widget for selecting the current page.

NOTE: When combined with the _root node_ concept, the root node is usually _not_ a page. However, it may have a number of page nodes (nodes with the page node extension) as child nodes (as indicated by the parent-child relation).
















# Relations

Relations are used to indicate relationships between Nodes on the canvas.
They can also be used to indicate relationships between other relations.
Relations are generally not visible but rather conceptual.
If a relation should be visualized, it should have a corresponding Node.

Every relation has the following properties:

| Property | JSON Type | OCIF Type                | Required     | Contents                                             |
|----------|-----------|--------------------------|--------------|------------------------------------------------------|
| `id`     | `string`  | [ID](#id)                | **required** | A unique identifier for the relation.                |
| `data`   | `array`   | [Extension](#extensions) | optional     | Additional data for the relation.                    |
| `node`   | `string`  | [ID](#id)                | optional     | ID of a visual node, which represents this relation. |

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

- **node**:
  The ID of a node, which represents this relation visually.
  E.g., often an arrow shape is used to represent an [edge relation](#edge-relation-extension).
  - If a visual node is used to represent a relation, the visual node should point back via its `relation` to this relation ID.
  - Semantics: If a relation (e.g., arrow or group) is deleted, which points to a `node`, that node should also be deleted.

In the remainder of this section, the current list of relation extension types (also just called _relation types_) is explained.
In addition to the relation types defined here, anybody can define and use their own relation types.
If this is your first read of the spec, skip over the details of the relation types and come back to them later.




# Relation Extensions

## Group Relation Extension

- Name: `@ocif/rel/group`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/group-rel.json`

A group relation is a relation that groups nodes together.
Groups are known as "Groups" in most canvas apps,
"Groups" in Godot, and "Tags" in Unity.

A group has the following properties in its `data` object:

| Property        | JSON Type | OCIF Type   | Required     | Contents                    |
|-----------------|-----------|-------------|--------------|-----------------------------|
| `members`       | `array`   | [ID](#id)[] | **required** | IDs of members of the group |
| `cascadeDelete` | `boolean` | `boolean`   | **optional** | `true` or `false`           |

- **members**: A list of IDs of nodes or other groups that are part of the group.
  Resources cannot be part of a group.
- **cascadeDelete**: A boolean flag indicating if deleting the group should also delete all members of the group.
  If `true`, deleting the group will also delete all members of the group.
  If `false`, deleting the group will not delete its members.

**Example:** A group of 3 nodes with letters for names:

```json
{
  "id": "letter_named_nodes",
  "data": [
    {
      "type": "@ocif/rel/group",
      "members": [
        "A",
        "B",
        "C"
      ]
    }
  ]
}
```

- Groups can contain groups as members. Thus, all semantics apply recursively.
- When a group is deleted, if `"cascadeDelete"` is `true`, all members are deleted as well.
- When a group is 'ungrouped,' the group itself is deleted, but its members remain.
- When a member is deleted, it is removed from the group.

JSON schema: [group-rel.json](extensions/group-rel.json)

## Edge Relation Extension

- Name: `@ocif/rel/edge`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/edge-rel.json`

An edge relates two elements (nodes and/or relation, mixing types is allowed).
It supports directed and undirected (bi-) edges.

It has the following properties (in addition to standard [relation](#relation) properties):

| Property   | JSON Type | OCIF Type | Required     | Contents                  | Default |
|------------|-----------|:----------|--------------|---------------------------|:--------|
| `start`    | `string`  | [ID](#id) | **required** | ID of source element.     |         |
| `end`      | `string`  | [ID](#id) | **required** | ID of target element.     |         |
| `directed` | `boolean` |           | optional     | Is the edge directed?     | `true`  |
| `rel`      | `string`  |           | optional     | Represented relation type |         |
| `node`     | `string`  | [ID](#id) | optional     | ID of a visual node       |         |

- **start**: The ID of the source element.
- **end**: The ID of the target element.
- **directed**: A boolean flag indicating if the edge is directed. If `true`, the edge is directed from the source to the target. If `false`, the edge is undirected. Default is `true`.
- **rel**: The type of relation represented by the edge. This is optional but can be used to indicate the kind of relation between the source and target elements. Do not confuse with the `type` of the OCIF relation. This field allows representing an RDF triple (subject, predicate, object) as (start,rel,end).

JSON schema: [edge-rel.json](extensions/edge-rel.json)


## Parent-Child Relation Extension

- Name: `@ocif/rel/parent-child`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/parent-child-rel.json`

A parent-child relation models a strict hierarchical relationship between nodes or relations.
It can be used to model inheritance, containment, or other hierarchical relationships.

| Property        | JSON Type | OCIF Type        | Required     | Contents                                | Default |
|-----------------|-----------|------------------|--------------|-----------------------------------------|:--------|
| `parent`        | `string`  | [ID](spec.md#id) | optional     | ID of the parent.                       | empty   |
| `child`         | `string`  | [ID](spec.md#id) | **required** | ID of the child.                        |         |
| `inherit`       | `boolean` |                  | optional     | Inherit properties.                     | `false` |
| `cascadeDelete` | `boolean` |                  | optional     | Delete children when parent is deleted. | `true`  |

- **parent**: The ID of the parent node or relation. There MUST be only one parent per child.
    - If empty, the [root node of the canvas](spec.md#root-node) is the parent node. This is relevant for [node transforms](spec.md#node-transforms-extension).

- **child**: The ID of the child node or relation. A parent can have multiple children (expressed by multiple parent-child relations).

- **inherit**: A boolean flag indicating if the child should inherit properties from the parent. Default is `false`.

    - The Exact semantics of inheritance are defined by the application.
    - In general, when looking for JSON properties of a child and finding them undefined, an app should look for the same value in the parent.
      The chain of parents should be followed until a root is reached or a cycle is detected.

- **cascadeDelete**: A boolean flag indicating if the children should be deleted when the parent is deleted. Default it `true`.

Semantics:

- If a parent is deleted, all children, which inherit from the parent, SHOULD also be deleted. (see **cascadeDelete** property)

JSON schema: [parent-child-rel.json](extensions/parent-child-rel.json)




## Hyperedge Relation Extension

- Name: `@ocif/rel/hyperedge`
- URI: `https://spec.canvasprotocol.org/v0.6/extensions/hyperedge-rel.json`

A hyperedge is a relation that connects any number of nodes.
Hyperedges can also be used to model simple bi-edges.

Conceptually, a hyper-edge is an entity that has a number of _endpoints_.
For each endpoint, we allow defining the directionality of the connection.
The endpoints are explicitly defined as an ordered list, i.e., endpoints can be addressed by their position in the list.
Such a model allows representing all kinds of hyper-edges, even rather obscure ones.

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
- **rel**: See [Edge Relation](spec.md#edge-relation-extension)

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
- `out` (edge is going **out** from the hyper-edge),
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






# Assets

OCIF knows two kinds of assets, [resources](#resources) and [schemas](#schemas). Both are managed by similar mechanisms. Assets can be stored in three ways:

- **Inline**: The asset is stored directly in the OCIF file. It is referenced by its id.
- **External**: The asset is stored in a separate file, which is referenced by the OCIF file. A relative URI expresses the reference.
- **Remote**: The asset is stored on a remote server, which is referenced by the OCIF file. A URI is required as a reference.

## Resources

Resources are the hypermedia assets that nodes display.
They are stored separately from Nodes to allow for asset reuse and efficiency.
Additionally, nodes can be used as resources, too. See XXXX.

Resources can be referenced by nodes or relations.
They are stored in the `resources` property of the OCIF file.
Typical resources are, e.g., SVG images, text documents, or media files.

- Each entry in `resources` is an array of _representation_ objects.
- The order of representations is significant. The first representation is the default representation.
  Later representations can be used as fallbacks.

A resource is an `object` with the following properties:

| Property          | JSON Type | OCIF Type                           | Required     | Contents                        |
|-------------------|-----------|-------------------------------------|--------------|---------------------------------|
| `id`              | `string`  | [ID](#id)                           | **required** | Identifier of the resource      |
| `representations` | `array`   | [Representation](#representation)[] | **required** | Representations of the resource |

- **id**: A unique identifier for the resource. See [ID](#id) type for details.

- **representations**: A list of representations of the resource.

### Representation

Each _Representation_ object has the following properties:

| Property   | JSON Type | OCIF Type               | Required  | Contents                               |
|------------|-----------|-------------------------|-----------|----------------------------------------|
| `location` | `string`  | [URI](#uri)             | see below | The storage location for the resource. |
| `mimeType` | `string`  | [MIME Type](#mime-type) | see below | The IANA MIME Type of the resource.    |
| `content`  | `string`  |                         | see below | The content of the resource.           |

Either `content` or `location` MUST be present. If `content` is used, `location` must be left out and vice versa.

- **location**: The storage location for the resource.
  This can be a relative URI for an external resource or an absolute URI for a remote resource.
  - If a `data:` URI is used, the `content` and `mimeType` properties are implicitly defined already. Values in `content` and `mimeType` are ignored.
- **mimeType**: The IANA MIME Type of the resource. See [MIME Type](#mime-type) for details.
- **content**: The content of the resource.
  This is the actual data of the resource as a string.
  It can be base64-encoded.

**Summary** \
Valid resource representations are

|                 | `location`                      | `mimeType`                                                 | `content`          |
|:----------------|---------------------------------|------------------------------------------------------------|--------------------|
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
      "representations": [{ "mimeType": "image/svg+xml", "content": "<svg>...</svg>" }]
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
        { "mimeType": "image/svg+xml", "content": "<svg>...</svg>" },
        {
          "mimeType": "image/png",
          "location": "https://example.com/image.png"
        },
        { "mimeType": "text/plain", "content": "Plan of the maze" }
      ]
    }
  ]
}
```

### Nodes as Resources
Motivation: Using a node B as a resource in another node A can be seen as a form of _transclusion_.
In HTML, an `IFRAME` on page A can show a page B.
Similarly, by using another node as a resource, the importing node establishes another view (in CSS terms, a _view port_) on the canvas.
This allows a node B to be seen in multiple places on the canvas:
Once at the original location of B and additionally in n other places where n other nodes use node B as a resource.

Every node in an OCIF document is automatically available as a resource, too.
They are addressed by prefixing the node id with `#`.
Nodes need not be added to the `resources` array.
Implicitly, the following mapping can be assumed:

*Example* Node:

```json
{
  "nodes": [
    {
      "id": "berlin-node",
      "position": [100, 100],
      "size": [100, 50],
      "resource": "berlin-res",
      "data": [
        {
          "type": "@ocif/node/rect",
          "strokeWidth": 3,
          "strokeColor": "#000000",
          "fillColor": "#00FF00"
        }
      ]
    }
  ]
}
```
*Example*: Node generates this implicit resource (not explicitly present in the `resources` array)


```json5
{
  "resources": [
    {
      "id": "#berlin-node",
      "representations": [ {
          "mimeType": "application/ocif-node+json",
          "content": {
            "id": "berlin-node",
            "position": [100, 100],
            "size": [100, 50],
            "resource": "berlin-res",
            "data": [
              {
                "type": "@ocif/node/rect",
                "strokeWidth": 3,
                "strokeColor": "#000000",
                "fillColor": "#00FF00"
              }
            ]
          }
      } ]
    }
  ]
}
```
#### Semantics
If a node A contains a node B as its resource (we call this *importing*):

- Node A establishes a kind of 'viewport' onto node B.
- Technically, the app first 'renders' node B, e.g., into a bitmap or vector buffer.
The actual node B might or might not be visible on the canvas.
Other nodes might be placed on top of node B.
In any case, node B is rendered in isolation, only taking all of its (transitive) children into account.
The app should produce an internal representation taking node Bs size (via node Bs data and the resource of node B) into account.

- The resulting view (most commonly internally represented as a bitmap or vector buffer) is then treated like any other image bitmap or image vector resource: It has a size and some content.
This virtual resource is now rendered by all importing nodes, including node A:
Node A renders the resource, using all defined mechanisms, including node As `position`, `size` and `resourceFit`. Different from normal resources, here the intention is to create a live view (not a static image) into the canvas. Whenever the way B looks is changed, the other places where node B is imported should be updated, too.

Transclusions may not form 'loops', that is, a node MAY NOT directly or indirectly import itself. If such a loop is present, all stated imports of the loop MUST be ignored and a warning SHOULD be given.



## Schemas

A schema in this specification refers to a [JSON Schema](https://json-schema.org/draft/2020-12) 2020-12.

Schemas are used to define

- a whole OCIF document,
  - Due to the openness of OCIF, the JSON schema for the OCIF document cannot capture all possible [extensions](#extensions).
- the structure of [extensions](#extensions).

Schemas are stored either (1) inline in the `schemas` property of an OCIF document or (2) externally/remote. See [assets](#assets) for storage options. There is a list of [built-in schema entries](#built-in-schema-entries) which need neither to be mentioned in the `schemas` property or have their schemas included.

Each entry in the `schemas` array is an object with the following properties:

| Property   | JSON Type | OCIF Type                   | Required     | Contents                                 |
|------------|-----------|:----------------------------|--------------|------------------------------------------|
| `uri`      | `string`  | absolute [URI](#uri)        | **required** | Identifier (and location) of the schema  |
| `schema`   | `object`  |                             | optional     | JSON schema inline as a JSON object      |
| `location` | `string`  | [URI](#uri)                 | optional     | Override storage location for the schema |
| `name`     | `string`  | [Schema Name](#schema-name) | optional     | Optional shortname for a schema. "@..."  |

- **uri**: The URI of the schema. The URI SHOULD be absolute. Only for local testing or development, relative URIs MAY be used.

  - The URI SHOULD contain the version number of the schema, either as a version number or as a date.

- **schema**: The actual JSON schema as a JSON object. This is only required for inline schemas. If `schema` is used, `location` must be left out.

- **location**: The storage location for the schema.

  - For a schema stored inline, this property should be left out.
  - For a _remote_ schema, the `uri` property is used as a location. This field allows overriding the location with another URL. This is particularly useful for testing or development.
  - An _external_ schema uses a relative URI as a location. This is a relative path to the OCIF file.

- **name**: An optional short name for the schema. This defines an alias to the URI. It is useful for human-readable references to the schema. The name MUST start with a `@` character. Names SHOULD use the convention organisation name `/` type (`node` or `rel`) `/` schema name. Example name: `@example/node/circle` (not needed, use an [oval](#oval-extension) instead). Names MUST be unique within an OCIF file.
  - By convention, schema names do not contain a version number. However, if multiple versions of the same schema are used in a file, the version number MUST be appended to the name to distinguish between them. E.g. `@example/circle/1.0` and `@example/circle/1.1`.

A JSON schema file may contain more than one type definition (under the `$defs` property).
When referencing a schema URI, there are two options:

- `https://example.com/myschema.json` refers to a schema defining only one (main) type. Implicitly, the first type is addressed.
- `https://example.com/myschema.json#typename` is formally understood as a JSON pointer expression (`/$defs/` _typename_ ) , which refers to a specific type definition within the schema.

To summarize, these schema definitions are possible:

| Schema        | `uri`        | `schema`        | `location`                   | `name`   |
|---------------|--------------|-----------------|------------------------------|----------|
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
      "name": "@ocif/node/ports"
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
The syntax `{var}` denotes placeholders.
To simplify the use of OCIF, a set of built-in schema mappings is defined:

1. Any [Schema Name](#schema-name) of the form `@ocif/rel/{suffix}` maps to a schema [URI](#uri) `https://spec.canvasprotocol.org/v0.6/extensions/{suffix}-rel.json`.

2. A schema name of the form `@ocif/node/{suffix}` maps to a schema URI `https://spec.canvasprotocol.org/v0.6/extensions/{suffix}-node.json`.

Here `v0.6` is the current version identifier of the OCIF spec. Later OCIF specs will have different versions and thus different URIs.

Built-in Entries:

```json
{
  "schemas": [
    {
      "name": "@ocif/node/${ext-type}",
      "uri": "https://spec.canvasprotocol.org/v0.6/extensions/${ext-type}-node.json"
    },
    {
      "name": "@ocif/rel/${ext-type}",
      "uri": "https://spec.canvasprotocol.org/v0.6/extensions/${ext-type}-rel.json"
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
They allow adding custom data to **nodes**, **relations**, **resources**, and the whole **canvas**.

## Extension Mechanism
- An extension _is_ a JSON object (used as a "property bag") with one mandatory, reserved property: `type`.
  The extension can use all other property keys.
- Arbitrary, nested JSON structures are allowed.
- Extensions SHOULD define how the OCIF properties play together with the extension properties and ideally with other (known) extensions.
- Elements (nodes, relations, resources, canvas) can have multiple extensions within their `data` array.

| Property | JSON Type | OCIF Type                                  | Required     | Contents          |
|----------|-----------|:-------------------------------------------|--------------|-------------------|
| `type`   | `string`  | [Schema Name](#schema-name) or [URI](#uri) | **required** | Type of extension |

- **type**: The type of the extension. This is a URI or a simple name.
  If a name is used, that name must be present in the [schemas](#schemas) section, where it is mapped to a URI.

If an element uses multiple extensions of the same type (same `type` property), the JSON fragments of the objects are by default considered to override each other, as defined in JSON Merge Path RFC 7386.
As an example, if a node has these extensions in its `data` array:
```json
[
    {
      "type": "https://example.com/ns/ocif-node/my-extension/1.0",
      "fruit": "apple",
      "color": "blue"
    },
    {
      "type": "https://example.com/ns/ocif-node/my-extension/1.0",
      "color": "red",
      "city": "Karlsruhe"
    }
]
```
the OCIF-using app should treat this as if the file stated
```json
[
    {
      "type": "https://example.com/ns/ocif-node/my-extension/1.0",
      "fruit": "apple",
      "color": "red",
      "city": "Karlsruhe"
    }
]
```
This makes combining files by hand easier and uses the same mechanism as [parent-child inheritance](spec.md#parent-child-relation-extension) and [nested canvases](#nesting-canvases) (when merging host node and imported root node).

For an example of an extension, see the fictional [appendix](#appendix), [Node Extension: Circle](#node-extension-circle).
In practice, the `@ocif/node/oval` extension can be used.

## Defining Extensions

If you need to store some extra data at a node for your canvas app, and none of the existing extensions fit, you can define your own extension.

An extension MUST have a URI (as its ID) and a document describing the extension.

It SHOULD have a version number, as part of its URI.
It SHOULD have a proposed name and SHOULD have a JSON schema.

The proposed structure is to use a directory in a git repository.
The directory path should contain a name and version number.
Within the repo, there SHOULD be two files:

- README.md, which describes the extension.
- schema.json, which contains the JSON schema for the extension.
  - This schema MUST use the same URI as the extension.
  - It SHOULD have a `description` property, describing briefly the purpose of the extension.
  - It MAY have a `title`. If a title is used, it should match the proposed short name, e.g. `@ocif/node/oval` or `@ocif/node/ports/v0.6`.
  - If the extension is defined to extend just one kind of element (like all initial extensions), that kind of element SHOULD be part of the name (`node`,`relation`,`resource` or `canvas`).

As an example, look at the fictitious [Circle Extension](#node-extension-circle) in the appendix.

### How To Write an Extension Step-by-Step

- Define the properties of the extension. What data is added to a node or relation?
- Define the URI of the extension. Ideally, this is where you publish your JSON schema file.
- Write a text describing the intended semantics.
- Create a JSON schema that defines the structure of the extension data. Large language models are a great help here.

To publish an extension, a version number should be included.
It is good practice to use a directory structure that reflects the version number of the extension.
Within the directory, the text is usually stored as a Markdown file, which links to the JSON schema.

**Example for a file structure**

```
/1.0
  /README.md      <-- your documentation
  /schema.json    <-- your JSON schema
```

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
OCIF defines more precise types, e.g., _ID_ is a JSON string with additional semantic (must be unique within a document).
We also use the syntax `ID[]` to refer to a JSON `array`, in which each member is an _ID_.

Here is the catalog of types used throughout the document (in alphabetical order):

## Angle

A `number` that represents an angle between -360 and 360.
The angle is measured in degrees, with positive values (0,360] indicating a clockwise rotation and negative values [-360,0) indicating a counterclockwise rotation.
Numbers outside the range [-360, 360] SHOULD be normalized into the range by adding or subtracting 360 until the value is within the range.

## Color

A `string` that encodes a color. CSS knows many ways to define colors, other formats usually less.
As a minimum, the syntax `#010203` should be understood as marker (`#`), red channel (`01`), green channel (`02`), and blue channel (`03`). Each channel is a value in the range 0 to 255, encoded as hex (`00` to `ff`). Uppercase and lowercase letters are valid to use in hex color definitions, with no difference in interpretation.
A canvas app SHOULD also allow stating four channels, with the fourth channel the _alpha_ channel, which encodes (partial) transparency. Example: `#ed80e930` is "orchid" with ca. 19% transparency.
The color is expressed in the [sRGB](https://developer.mozilla.org/en-US/docs/Glossary/RGB) color space.

## ID

A `string` that represents a unique identifier.
It MAY NOT start with a hash-mark (#).

It may not contain control characters like a null byte (00), form feed, carriage return, backspace, and similar characters. In general, OCIF IDs should be [valid HTML IDs](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Global_attributes/id) and if possible even [valid CSS identifiers](https://www.w3.org/TR/CSS2/syndata.html#value-def-identifier): "In CSS, identifiers (including element names, classes, and IDs in selectors) can contain only the characters [a-zA-Z0-9] and ISO 10646 characters U+00A0 and higher, plus the hyphen (-) and the underscore (_); they cannot start with a digit, two hyphens, or a hyphen followed by a digit."

It must be unique among all IDs used in an OCIF document.
The ID space is shared among nodes, relations, and resources.

NOTE: An OCIF file itself can be used as a resource representation. Thus, a node can show a (then nested) other OCIF file. The ID uniqueness applies only within each OCIF file, not across document boundaries.

## Integer

A `number` that represents an integer value, while JSON numbers are normally all floating-point values.

## MIME Type

A `string` that represents the _MIME Type_ for a resource.
Typical examples in a canvas are `text/plain`, `text/html`, `image/svg+xml`, `image/png`, `image/jpeg`, `video/mp4`.
IANA content type registry: https://www.iana.org/assignments/media-types/media-types.xhtml

## Node

An `object` representing a visual [node](#nodes).

## Relation

An `object` representing a [relation](#relations).

## Representation

An `object` representing a [resource](#resources) representation.

## Resource

An `array` of [resource](#resources).

## Schema Entry

An `object` representing a [schema](#schemas) entry.
Schema entries assign schema _URIs_ to _Schema Names_.

## Schema Name

A `string` that represents the name of a schema.
It must be _defined_ in the [schemas](#schemas) section of an OCIF document as a `name` property.
It can be _used_ as `type` of relation, `type` of relation extension, or `type` of node extension.

## URI

A `string` that represents a Uniform Resource Identifier (URI) as defined in [RFC 3986](https://tools.ietf.org/html/rfc3986).

## Vector
The whole canvas is interpreted either as 2D or 3D.

- A 3D vector is represented using an `array` with three `number` in them, with `v[0]` as _x_, `v[1]` as _y_, and `v[2]` as _z_.
- A 2D vector is represented using an `array` with two `number` in them, with `v[0]` as _x_ and `v[1]` as _y_. In 2D, the z-axis coordinate SHOULD be used for relative z-index ordering of 2D shapes. An application MAY also ignore the z-axis. A 2D vector interpreted as 3D is auto-extend with z-axis set to `0`.

- Syntax shortcut: A vector given as a single number, e.g. `3` is auto-extended to apply uniformly to all dimensions, e.g., `[3,3,3]`. This is most useful for a `scale` factor.



# Practical Recommendations

- The proposed MIME-type for OCIF files is `application/ocif+json`.
<!-- IANA registration https://github.com/ocwg/spec/issues/13 -->

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
    - See [OCWG URL Structure](#ocwg-url-structure-planned) for a proposed URI structure for OCIF resources.

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

The materialized list of schema entries, as explained in [built-in schema mappings](#built-in-schema-mappings).
Note that the OCIF extensions have no version number of their own (in the short name).
They are versioned together with the OCIF spec.
The following block can be assumed to be present in every OCIF document.
It is also valid to additionally copy these schema entries in.

```json
{
  "schemas": [
    {
      "name": "@ocif/node/arrow",
      "uri": "https://spec.canvasprotocol.org/v0.6/extensions/arrow-node.json"
    },
    {
      "name": "@ocif/node/oval",
      "uri": "https://spec.canvasprotocol.org/v0.6/extensions/oval-node.json"
    },
    {
      "name": "@ocif/node/path",
      "uri": "https://spec.canvasprotocol.org/v0.6/extensions/path-node.json"
    },
    {
      "name": "@ocif/node/rect",
      "uri": "https://spec.canvasprotocol.org/v0.6/extensions/rect-node.json"
    },
    {
      "name": "@ocif/rel/edge",
      "uri": "https://spec.canvasprotocol.org/v0.6/extensions/edge-rel.json"
    },
    {
      "name": "@ocif/rel/group",
      "uri": "https://spec.canvasprotocol.org/v0.6/extensions/group-rel.json"
    },
    {
      "name": "@ocif/rel/hyperedge",
      "uri": "https://spec.canvasprotocol.org/v0.6/extensions/hyperedge-rel.json"
    },
    {
      "name": "@ocif/rel/parent-child",
      "uri": "https://spec.canvasprotocol.org/v0.6/extensions/parent-child-rel.json"
    },
    {
      "name": "@ocif/node/ports",
      "uri": "https://spec.canvasprotocol.org/v0.6/extensions/ports-node.json"
    }
  ]
}
```

## Examples

### Node Extension: Circle

This fictive example extension defines geometric circles. In reality, a circle in OCIF can be represented as an [oval](#oval-extension) with the same width and height.

- Schema: http://example.com/ns/ocif-node/circle/1.0
- Name: `@example/circle`
- Properties:

| Property | JSON Type | Required | Contents                    | Default |
|----------|-----------|----------|-----------------------------|--------:|
| `radius` | number    | optional | The circles radius in pixel |      10 |

- Semantics:
  - The `radius` property implies a `size`. I.e., a circle of radius _r_ implies a size of _2r_ x _2r_.

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
          "type": "@ocif/node/ports",
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

## OCWG URL Structure (Planned)

- `https://canvasprotocol.org` - info site
- `https://spec.canvasprotocol.org` - specification; REDIRECT to the latest version, e.g. `https://spec.canvasprotocol.org/v0.6/spec.md`
- `https://spec.canvasprotocol.org/v0.6/spec.md` - OCIF specification version; this is also its [URI](#uri). Links in the text to the schema.
- `https://spec.canvasprotocol.org/v0.6/schema.json` - General OCIF JSON schema
- Extension URIs (some selected exemplars):
  - `https://spec.canvasprotocol.org/v0.6/extensions/rect-node.json` - URI for the rectangle node extension
  - `https://spec.canvasprotocol.org/v0.6/extensions/edge-rel.json` - URI for the rectangle relation extension

## Syntax Conventions
- All JSON property names are camelCased. This makes it the easiest to name variables in a programming language.

## Changes


### From v0.5 to v0.6

**Specification Changes:**
- Merged _Core Extensions_ and _Extended Extensions_
- Added a `rootNode` property to allow choosing a single node as root. This helps for nesting canvases, which is now also documented.
- Added canvas-level extensions (`data` in OCIF document), such as the new _canvas viewport_ extension.
- Move _parent-child-relation_ to main extensions.
- Define how to read and combine multiple extensions of the same type
- Fix some typos
- Updated extension versioning (but not core extensions) to use explicit version numbers (e.g., `@ocif/node/ports/0.4.1`)
- Moved `@ocif/node/parent-child` from extensions to core
- Added _page node extension_.
- Clarification on fillColor
- Conflict Resolution for Node Transforms

### From v0.4 to v0.5

**Core Specification Changes:**
- Removed `node.scale` property - moved to `@ocif/node/transforms` extension
- Added `node.resource-fit` property for controlling resource display within nodes
- Added OCIF type `Vector` with support for 2D/3D vectors and scalar shortcuts
- Made `type` property required for all core node and relation extensions
- Made specific properties required in core extensions (e.g., `start`/`end` for arrows, `ports` for ports extension)

**Extension Changes:**
- Removed `@ocif/rel/set` relation - merged functionality into `@ocif/rel/group`
- Added `cascadeDelete` property to group relations
- Removed deprecated `@ocif/node/relative` extension - functionality moved to `@ocif/node/transforms`
- Added `@ocif/node/anchored` - percentage-based positioning relative to parent bounds
- Added `@ocif/node/textstyle` - font styling properties for text rendering
- Added `@ocif/node/transforms` - geometric transforms including scale, offset, and rotation


### From v0.3 to v0.4

- Changed @ocwg to @ocif
- Prefaced all version numbers with `v` as in `v0.4`
- Moved node `scale` property to [node transforms](spec.md#node-transforms-extension) extension.
- Changed from @ocwg (Open Canvas Working Group) to @ocif (Open Canvas Interchange Format) in schema names.
- Prefaced all version numbers with `v` as in `v0.4`
- Added release instructions

### From v0.2.1 to v0.3

- Added OCIF type [Color](#color)
- Renamed @ocif/rel/edge properties: `from` -> `start`, `to` -> `end`
- Added arrow node
- Split in core (for interoperability) and extensions (for interchange)
- Added JSON schemas
- Added default sizes for nodes
- Added node extensions for rectangle, oval, arrow, and path
- Documented text and image usage in nodes
- Clarified ID uniqueness
- Updated URI structure (less fancy, easier to implement)

### From v0.2.0 to v0.2.1

- Relation types and relation extensions merged into one. There is now a base relation, which has extensions.
- Node rotation center fixed.
- Schema object to a schema array, see [design decision](../../design/design-decisions.md#list-or-map).

### From v0.1 to v0.2

- Root property `schema_version` renamed to `ocif` -- this is simpler and serves as a kind of "magic" signature, i.e., a JSON document with an "ocif" property near the top is likely an OCIF file.
- Renamed node `properties` to `data` -- this is simpler and more generic.
- Relation property `name` renamed to `type`.
