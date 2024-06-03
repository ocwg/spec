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
