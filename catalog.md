# OCIF Extensions List
This document lists known OCIF extensions ([see OCIF spec 0.3](spec/0.3/spec.md)). 
Listing in this file is not required for an extension to be used in an OCIF document.
Note the extensions can even be defined within an OCIF document, without an external schema file.
All this is described in the OCIF spec.

Listing in this file is not an endorsement of an extension.
This list is merely a convenience for developers and users to find extensions that might be useful for their use case.

Mandatory fields:
- **URI**: The URI of the extension (its unique id). 
- **Schema**: Link to JSON schema. 
    - Can be omitted if URI points to a JSON schema.
- **Documentation**: A link to the documentation, describing the semantics and usage of the extension. 
 
Optional fields: 
- **Name**: The proposed short name of the extension.
- **Version**: The version of the extension. It is already part of the URI.
- **Author**: The author of the extension.

The List:
<!-- TOC -->
- [OCIF Extensions List](#ocif-extensions-list)
- [Node Extensions](#node-extensions)
  - [Arrow Node](#arrow-node)
  - [Oval Node](#oval-node)
  - [Path Node](#path-node)
  - [Rectangle Node](#rectangle-node)
  - [Ports Node](#ports-node)
- [Relation Extensions](#relation-extensions)
  - [Edge Relation](#edge-relation)
  - [Group Relation](#group-relation)
  - [Set Relation](#set-relation)
  - [Relative Nodes Relation](#relative-nodes-relation)
  - [Hyperedge Relation](#hyperedge-relation)
  - [Parent-Child Relation](#parent-child-relation)
- [Deprecated Extensions](#deprecated-extensions)
<!-- TOC -->

# Node Extensions

## Arrow Node
- Name: `@ocif/node/arrow`
- URI: `https://spec.canvasprotocol.org/0.3/core/arrow-node.json`
- Doc: [spec/0.3/spec.md#arrow](spec/0.3/spec.md#arrow)
- Version: 0.3
- Author: Open Canvas Working Group

## Oval Node
- Name: `@ocif/node/oval`
- URI: `https://spec.canvasprotocol.org/0.3/core/oval-node.json`
- Doc: [spec/0.3/spec.md#oval](spec/0.3/spec.md#oval)
- Version: 0.3
- Author: Open Canvas Working Group

## Path Node
- Name: `@ocif/node/path`
- URI: `https://spec.canvasprotocol.org/0.3/core/path-node.json`
- Doc: [spec/0.3/spec.md#path](spec/0.3/spec.md#path)
- Version: 0.3
- Author: Open Canvas Working Group

## Rectangle Node
- Name: `@ocif/node/rect`
- URI: `https://spec.canvasprotocol.org/0.3/core/rect-node.json`
- Doc: [spec/0.3/spec.md#rectanlge](spec/0.3/spec.md#rectangle)
- Version: 0.3
- Author: Open Canvas Working Group

## Ports Node
- Name: `@ocif/node/ports`
- URI: `https://spec.canvasprotocol.org/0.3/extensions/ports-node.json`
- Doc: [spec/0.3/extensions.md#ports-node](spec/0.3/extensions.md#ports-node)
- Version: 0.3
- Author: Open Canvas Working Group


# Relation Extensions

## Edge Relation
- Name: `@ocif/rel/edge`
- URI: `https://spec.canvasprotocol.org/0.3/core/edge-rel.json`
- Doc: [spec/0.3/spec.md#edge-relation](spec/0.3/spec.md#edge-relation)
- Version: 0.3
- Author: Open Canvas Working Group

## Group Relation
- Name: `@ocif/rel/group`
- URI: `https://spec.canvasprotocol.org/0.3/core/group-rel.json`
- Doc: [spec/0.3/spec.md#group-relation](spec/0.3/spec.md#group-relation)
- Version: 0.3
- Author: Open Canvas Working Group

## Set Relation
- Name: `@ocif/rel/set`
- URI: `https://spec.canvasprotocol.org/0.3/core/set-rel.json`
- Doc: [spec/0.3/spec.md#set-relation](spec/0.3/spec.md#set-relation)
- Version: 0.3
- Author: Open Canvas Working Group 

## Relative Nodes Relation
- Name: `@ocif/node/relative`
- URI: `https://spec.canvasprotocol.org/0.3/extensions/relative-node.json`
- Doc: [spec/0.3/extensions.md#relative-node](spec/0.3/extensions.md#relative-node)
- Version: 0.3
- Author: Open Canvas Working Group

## Hyperedge Relation
- Name: `@ocif/rel/hyperedge`
- URI: `https://spec.canvasprotocol.org/0.3/extensions/hyperedge-rel.json`
- Doc: [spec/0.3/extensions.md#hyperedge-relation](spec/0.3/extensions.md#hyperedge-relation)
- Version: 0.3
- Author: Open Canvas Working Group

## Parent-Child Relation
- Name: `@ocif/rel/parent-child`
- URI: `https://spec.canvasprotocol.org/0.3/extensions/parent-child-rel.json`
- Doc: [spec/0.3/extensions.md#parent-child-relation](spec/0.3/extensions.md#parent-child-relation)
- Version: 0.3
- Author: Open Canvas Working Group



# Deprecated Extensions
The authors of these extensions have deprecated them. Often this means that the functionality has been replaced by a newer version of the extension. 

- `https://spec.canvasprotocol.org/node/ports/0.2`
- `https://spec.canvasprotocol.org/rel/hyperedge/0.2`
- `https://spec.canvasprotocol.org/rel/parent-child/0.2`
- `https://spec.canvasprotocol.org/rel/relative/0.2`

