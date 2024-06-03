# Open Canvas Initial Draft Spec

**NOTE:** This is a draft!

## Overview

- Trying to balance between visual and conceptual canvases. Nodes provide the core elements for the canvas. Relations provide rich support for creating conceptual relationships between nodes.
- Not trying to maintain backwards-compatibility with Obsidian's JSON Canvas.
- Aiming for extensibility via schemas.
- Enable different canvases to work together without canvases needing to implement or even understand all aspects of the spec.

### Structure

The JSON structure includes `nodes`, `relations`, and `schemas`, and a version number that defines the shape of the top-level structure and the shapes of the base classes. The following is a valid, though empty open canvas file:

```json
{
  "schema_version": "1.0",
  "nodes": {},
  "relations": {},
  "schemas": {}
}
```

## Nodes

All visible items on the canvas are considered Nodes.

### Node Base class

All Nodes must implement the following properties:

```ts
type Node = {
  id: string;
  schema: string;
  schema_version: string;
  x: number;
  y: number;
  z?: number;
  properties?: {
    [key: string]: any;
  };
};
```

### Text

Text Nodes are used to display text on the canvas.

### Assets

Asset Nodes are used to represent files or other assets. They can be embedded or reference external sources. We use `mime-type` to describe the type of the asset and `uri` to describe the location of the asset.

### Arrows

### Examples

Here are a bunch of example Nodes:

```json
{
  "nodes": {
    "someShapeID11321": {
      "schema": "@ocwg/text",
      "schema_version": "1.0",
      "x": 10,
      "y": 10,
      "properties": {
        "text": "Hello, world!",
        "color": "#000000"
      }
    },
    "2435234634t324t3245": {
      "schema": "@ocwg/asset",
      "schema_version": "1.0",
      "x": 10,
      "y": 10,
      "properties": {
        "w": 200,
        "h": 150,
        "mime_type": "text/json",
        "uri": "data://9823fy9283hf2i3yf082ohu3fo2hufi2uh4fk234f8youhi",
      }
    },
    "oajefioajef83r8y3ehf": {
      "schema": "@ocwg/rectangle",
      "schema_version": "1.0",
      "x": 10,
      "y": 10,
      "properties": {
        "width": 200,
        "height": 150,
        "text": "Hello, world!",
        "stroke_color": "red",
        "stroke_weight": ,
        "fill_color": ,
        "rotation": 90 /* +/-360 degrees */
      }
    },
    "someShapeID124135": {
      "schema": "@stately/typed-rectangle",
      "schema_version": "3.1",
      "x": 10,
      "y": 10,
      "properties": {
        "type": "rectangle",
        "width": 100,
        "height": 200,
      }
    },
    "someShapeID535": {
      "schema": "@tldraw/arrow",
      "schema_version": "1.0",
      "x": 10,
      "y": 10,
      "properties": {
        "start": {
          "x": 1,
          "y": 1,
          "terminal": "",
          "connected_to": "someShapeID124135",
        },
        "end": {
          "x": 100,
          "y": 100,
          "terminal": "",
          "connected_to": "someShapeID124135",
        },
        "bend": 200,
      }
    }
  },
}
```

## Relations

Relations are used to connect Nodes together.

### Relation Base class

```ts
type Relation = {
  id: string;
  schema: string;
  schema_version: string;
  name: string;
  properties?: {
    [key: string]: any;
  };
};
```

### Sets, Edges, and Hyper-edges

**Sets**

Sets can be used to model groups, frames, and many other things. Their basic representation is a collection of Nodes or Relations.

Here's an example:

```json
"setRelationID1234": {
  "schema": "@ocwg/set",
  "schema_version": "1.0",
  "members": ["someID", "someOtherID"],
  "name": "group a"
}
```

- `members` can be Nodes or Relations.

**Edges**

Edge Relations are used to connect two Nodes or Relations together. Edge Relations are not the same as Nodes that are Arrows. Relations may not be visible on the canvas.

Here's an example:

```json
"edgeRelationID1234": {
  "schema": "@ocwg/edge",
  "schema_version": "1.0",
  "from": "someNodeID124135",
  "to": "someNodeID11321",
  "name": "a named edge",
}
```

- `from` and `to` can be Nodes or Relations.

**Hyper-edges**

Hyper-edges represent many-to-many relationships.

```json
"hyperEdgeRelation1234": {
  "schema": "@ocwg/hyperedge",
  "schema_version": "1.0",
  "from": [
    "someShapeID124135",
    "someShapeID11321"
  ],
  "to": [
    "someShapeID124135",
    "someShapeID11321"
  ],
  "name": "a named hyperedge",
}
```

- The `from[]` and `to[]` arrays can contain Nodes or Relations.

## Schemas

Schemas are used to define the structure of Nodes and Relations. They provide extensibility by supporting different properties for Nodes and Relations.

Schemas are included inline in the file. In the future, there may be a way to externalize schemas to a remote store.

Schemas have a version number to support schema evolution.

Those who wish to implement the base specification should aim to support all schemas in the `@ocwg/*` namespace.

Schemas can extend one another. For example, a `@tldraw/arrow` schema could extend a `@ocwg/arrow` schema. When extending, properties are simply merged to establish the full schema. This allows extension with sensible fallbacks: if an implementer doesn't support the extended schema, they can still render the base schema.

Schemas are defined in JSONSchema format.

```json
"schemas": [
  "@ocwg/text": { /* JSONSchema format */ }
]
```
