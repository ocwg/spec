# Open Canvas Initial Draft Spec

**NOTE:** This is a draft!

## Overview

- Trying to balance between visual and conceptual canvases. Nodes provide the core elements for the canvas. Relations provide rich support for creating conceptual relationships between nodes.
- Not trying to maintain backwards-compatibility with Obsidian's JSON Canvas.
- Aiming for extensibility with sensible fallbacks.
- Enable different canvases to work together without canvases needing to implement or even understand all aspects of the spec.

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
