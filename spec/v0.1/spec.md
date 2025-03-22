# Open Canvas Initial Draft Spec

> **This DEPRECATED version:** \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; https://spec.canvasprotocol.org/v0.1 \
> **Latest version:** \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; https://spec.canvasprotocol.org/v0.2 \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; See [spec 0.2](spec-0.2.md) in this repo.

⚒️ Work on the initial spec is underway in the [Initial Draft Spec pull request](https://github.com/ocwg/spec/pull/1).

📚 Read more at [canvasprotocol.org](https://canvasprotocol.org).

The purpose of this doc is to provide a plain-language description of the specification and walk through key concepts of the Open Canvas format.

## Motivation and Design Goals

The goal of the Open Canvas format is to enable interoperability between different [infinite canvas tools](https://infinitecanvas.tools) by being a simple, compact "transmission" format.

Open Canvas has been designed to meet the following goals:

- **Balance between visual and conceptual canvases.** Nodes provide the core elements for the canvas. Relations provide rich support for creating conceptual relationships between nodes.
- **Extensibility.** Open Canvas is fully extensibile using custom-defined schemas.
- **Runtime-independence.** Enable different canvases to work together without canvases needing to implement or even understand all aspects of the spec.

Out of scope:

- **Backwards compatibility with Obsidian's JSON Canvas.** JSON Canvas isn't designed to support the full gamut of functionality supported by Open Canvas.

## Structure: Nodes, Relations, Resources, Schemas

Conceptually, an infinite canvas can be thought of as `nodes` and `relations`. `nodes` represent all of the visual information on the canvas, while `relations` represent the invisible conceptual relationships between nodes.

In order to enable asset re-use, Open Canvas distinguishes between a `node` with a location on the canvas and the `resource` that the `node` displays.

The structure of an Open Canvas JSON consists of `nodes`, `relations`, and `resources`, with a version number that defines the shape of the top-level structure and the shapes of the base classes. The following is a valid, though empty Open Canvas file:

```json
{
  "schema_version": "1.0", // this could be implied
  "nodes": [],
  "relations": [],
  "resources": [],
  "schemas": []
}
```

### Nodes

All visible items on the canvas are referred to as Nodes.

**Node Base Class**

All Nodes must implement the following properties:

```ts
type Node = {
  id: string;
  position: [number, number] | [number, number, number]; // x,y or x,y,z
  size?: [number, number] | [number, number, number]; // w,h or w,h,d
  rotation?: number; // +/- 360 degrees; if not included, defaults to 0
  scale?: number; // defaults to 0
  resource?: string; // id of a resource
  properties?: [
    {
      schema: string; // name of a schema listed in schemas
      schema_version: string;
      [key: string]: any;
    }
  ];
  fallback?: string;
};
```

### Relations

Relations are used to indicate relationships between Nodes on the canvas. Relations are generally not visible, but rather conceptual. If a relation should be visualized, it should have a corresponding Node.

**Relation Base class**

```ts
type Relation = {
  id: string;
  name: string;
  properties?: [
    {
      schema: string;
      schema_version: string;
      [key: string]: any;
    }
  ];
};
```

### Resources

Resources are the hypermedia assets that Nodes display. They are stored separately from Nodes to allow for asset reuse and efficiency.

**Resoruce Base class**

```ts
type Resource = {
  id: string;
  uri: string;
  mimeType?: string;
};
```

- [ ] What happens if `id`s collide? Should we just use array indexes instead?

The `uri` for a resource can either be:

- embedded in the file itself
- a local file relative to the open canvas file
- a remote URL

**Embedded in the file**

```json
{
  "resources": {
    [
      "id": "a-rectangle",
      "uri": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFCAYAAACNbyblAAAACXBIWXMAAAsTAAALEwEAmpwYAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAfSURBVHgBpcixEQAACAIxdP8FfxrtOTpSZoCTWQVlPm0NA5S6X7P2AAAAAElFTkSuQmCC", // 5x5 white png
      "mimeType": "image/png"
    ]
  }
}
```

- [ ] is `mimeType` duplicative?
- [ ] what if `mimeType` and the mimeType specified in `data:` differ?

**Local file relative to the Open Canvas file**

```json
{
  "resources": {
    [
      "id": "a-rectangle",
      "uri": "./a-rectangle.png",
      "mimeType": "image/png"
    ]
  }
}
```

**A remote URL**

```json
{
  "resources": {
    [
      "id": "a-rectangle",
      "uri": "https://somesite.com/a-rectangle.png",
      "mimeType": "image/png"
    ]
  }
}
```

- [ ] what if `mimeType` and the mimeType specified in the http response differ?

## Extensibility

One major challenge of an interchange format for infinite canvases is that no two canvases are exactly alike. There are canvases built for informal whiteboarding, formal diagramming, quick visual sketches, node-and-wire programming, and many, many other use cases. Each of these canvases have radically different feature sets. How can we enable these canvases to work together?

The core of the Open Canvas specification is extensibility and fallbacks. In the case of one canvas supporting a feature that another canvas doesn't support, we need to enable three things:

1. Canvases need to be able to read and write to an open canvas file without implementing all of the features of all other canvases and also extend the format with features unique to their canvas without breaking other canvases or changing the main specification.
2. Canvases that lack a feature need to preserve the data for features that they don't implement.
3. Canvases need to be able to optionally display reasonable fallbacks for features that they don't implement.

Put simply: how do we design the specification to be extensible by individual canvas tools and robust against unknown features?

### Schemas

Schemas are used to define the structure of Nodes and Relations. They provide extensibility by supporting different properties for Nodes and Relations.

Schemas are included inline in the file. In the future, there may be a way to externalize schemas to a remote schema registry.

Schemas have a version number to support schema evolution.

### Extensibility through preserving the property bag

In order to support interchange beteween canvases when features don't overlap, canvases need to preserve nodes and relations that it doesn't support.

In practice, that looks something like this:

1. Canvas A supporting Feature X creates a canvas with a Feature X node in it and exports it as an Open Canvas file.
2. Canvas B, which does not support Feature X, opens the Open Canvas file, and some edits are made to the canvas.
3. Canvas B exports the canvas to an Open Canvas file. The nodes for Feature X should still be in the Open Canvas file, unchanged.

NOTE: This is perhaps the most onerous part of supporting Open Canvas; implementing canvases have to store data that they don't need or recognize.

### Extensibility through fallbacks

The base node type supports an optional `fallback` property as a string. If a canvas wishes to visualize a node that it doesn't implement, the canvas can render a fallback element on the canvas at the element's x,y position using the string.

### Extensibility through schema extension

Schemas can extend one another.

When extending, properties are simply merged to establish the full schema. This allows extension with meaningful fallbacks: if an implementer doesn't support the extended schema, but does support the base schema, they can render the node using a simpler representation.

- [ ] "merging properties" is probably a bad idea - let the implementer choose which property to use rather than using this inheritance-like behavior

Schemas are defined in JSONSchema format.

```json
"schemas": [
  "@ocwg/text": { /* JSONSchema format */ }
]
```

For example, a `@tldraw/arrow` schema could extend a `@ocwg/arrow` schema, adding properties that are only applicable to TLDraw arrow.

- [ ] Flesh out this example

## Core Node Schemas

These are the node schemas included in the core `@ocwg` namespace.

### Text

Text Nodes are used to display text on the canvas.

### Arrows

### Examples

Here are a bunch of example Nodes:

```json
{
  "nodes": [
    {
      "id": "someShapeID11321",
      "position": [10, 10],
      "properties": [{
        "schema": "@ocwg/text",
        "schema_version": "1.0",
        "text": "Hello, world!",
        "color": "#000000"
      }]
    },
    {
      "id": "2435234634t324t3245",
      "position": [10, 10],
      "size": [200, 150],
      "properties": [{
        "schema": "@ocwg/text",
        "schema_version": "1.0",
        "mime_type": "text/json",
        "uri": "data://9823fy9283hf2i3yf082ohu3fo2hufi2uh4fk234f8youhi",
      }]
    },
    {
      "id": "oajefioajef83r8y3ehf",
      "position": [10, 10],
      "size": [200, 150],
      "rotation": 90, /* +/-360 degrees */
      "properties": [{
        "schema": "@ocwg/rectangle",
        "schema_version": "1.0",
        "text": "Hello, world!",
        "stroke_color": "red",
        "stroke_weight": ,
        "fill_color": ,
      }]
    },
    {
      "id": "someShapeID124135",
      "position": [10, 10],
      "size": [200, 150],
      "properties": [{
        "schema": "@stately/typed-rectangle",
        "schema_version": "3.1",
        "type": "rectangle",
      }]
    },
    {
      "id": "someShapeID535",
      "position": [10, 10],
      "properties": [{
        "schema": "@tldraw/arrow",
        "schema_version": "1.0",
        "start": {
          "position": [1, 1],
          "terminal": "",
          "connected_to": "someShapeID124135",
        },
        "end": {
          "position": [10, 10],
          "terminal": "",
          "connected_to": "someShapeID124135",
        },
        "bend": 200,
      }]
    }
  ]
}
```

## Core Relation Schemas

These are the relation schemas included in the core `@ocwg` namespace.

### Sets

Sets can be used to model groups, frames, and many other things. Their basic representation is a collection of Nodes or Relations.

Here's an example:

```json
{
  "id": "setRelationID1234",
  "name": "group a",
  "properties": [
    {
      "schema": "@ocwg/set",
      "schema_version": "1.0",
      "members": ["someID", "someOtherID"]
    }
  ]
}
```

- `members` can be Nodes or Relations.

### Edges

Edge Relations are used to connect two Nodes or Relations together. Edge Relations are not the same as Nodes that are Arrows. Relations may not be visible on the canvas.

Here's an example:

```json
{
  "id": "edgeRelationID1234",
  "name": "a named edge",
  "properties": [
    {
      "schema": "@ocwg/edge",
      "schema_version": "1.0",
      "from": "someNodeID124135",
      "to": "someNodeID11321"
    }
  ]
}
```

- `from` and `to` can be Nodes or Relations.

### Hyper-edges

Hyper-edges represent many-to-many relationships.

```json
{
  "id": "hyperEdgeRelation1234",
  "name": "a named hyperedge",
  "properties": [
    {
      "schema": "@ocwg/hyperedge",
      "schema_version": "1.0",
      "from": ["someShapeID124135", "someShapeID11321"],
      "to": ["someShapeID124135", "someShapeID11321"]
    }
  ]
}
```

- The `from[]` and `to[]` arrays can contain Nodes or Relations.
