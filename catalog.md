# OCIF Extensions List

This document lists known OCIF extensions ([see OCIF spec](spec/v0.6/spec.md)) as service to the community.
Getting listed in this file is not a requirement for an extension to be used in an OCIF document.
Extensions can be defined within an OCIF document, without an external schema file.
It is common practice for reusable extensions to publish a JSON schema file and a README.
All this is described in the OCIF spec.

Listing in this file is not an endorsement of an extension.
This list is merely a convenience for developers and users to find extensions that might be useful for their use case.

Mandatory fields in this catalog:

- **URI**: The URI of the extension (its unique id).
- **Schema**: Link to JSON schema.
  - Can be omitted if URI points to a JSON schema.
- **Documentation**: A link to the documentation, describing the semantics and usage of the extension.

Optional fields:

- **Name**: The proposed short name of the extension.<!-- TOC -->
* [OCIF Extensions List](#ocif-extensions-list)
* [Node Extensions](#node-extensions)
  * [Arrow Node](#arrow-node)
  * [Oval Node](#oval-node)
  * [Path Node](#path-node)
  * [Rectangle Node](#rectangle-node)
  * [Ports Node](#ports-node)
  * [Anchored Node](#anchored-node)
  * [Text Style Node](#text-style-node)
  * [Transforms Node](#transforms-node)
* [Relation Extensions](#relation-extensions)
  * [Edge Relation](#edge-relation)
  * [Group Relation](#group-relation)
  * [Hyperedge Relation](#hyperedge-relation)
  * [Parent-Child Relation](#parent-child-relation)
* [Deprecated Extensions](#deprecated-extensions)
<!-- TOC -->peredge-relation)
  - [Parent-Child Relation](#parent-child-relation)
- [Deprecated Extensions](#deprecated-extensions)
<!-- TOC -->

# Node Extensions

## Arrow Node

- Name: `@ocif/node/arrow`
- URI: `https://spec.canvasprotocol.org/v0.5/core/arrow-node.json`
- Doc: [spec/v0.6/spec.md#arrow](spec/v0.6/spec.md#arrow-extension)
- Version: v0.5
- Author: Open Canvas Working Group

## Oval Node

- Name: `@ocif/node/oval`
- URI: `https://spec.canvasprotocol.org/v0.5/core/oval-node.json`
- Doc: [spec/v0.6/spec.md#oval](spec/v0.6/spec.md#oval-extension)
- Version: v0.5
- Author: Open Canvas Working Group

## Path Node

- Name: `@ocif/node/path`
- URI: `https://spec.canvasprotocol.org/v0.5/core/path-node.json`
- Doc: [spec/v0.6/spec.md#path](spec/v0.6/spec.md#path-extension)
- Version: v0.5
- Author: Open Canvas Working Group

## Rectangle Node

- Name: `@ocif/node/rect`
- URI: `https://spec.canvasprotocol.org/v0.5/core/rect-node.json`
- Doc: [spec/v0.6/spec.md#rectangle](spec/v0.6/spec.md#rectangle-extension)
- Version: v0.5
- Author: Open Canvas Working Group

## Ports Node

- Name: `@ocif/node/ports`
- URI: `https://spec.canvasprotocol.org/v0.5/extensions/ports-node.json`
- Doc: [spec/v0.6/spec.md#ports-node](spec/v0.6/spec.md#ports-node-extension)
- Version: v0.5
- Author: Open Canvas Working Group

## Anchored Node

- Name: `@ocif/node/anchored`
- URI: `https://spec.canvasprotocol.org/v0.5/extensions/anchored-node.json`
- Doc: [spec/v0.6/spec.md#anchored-node](spec/v0.6/spec.md#anchored-node-extension)
- Version: v0.5
- Author: Open Canvas Working Group

## Text Style Node

- Name: `@ocif/node/textstyle`
- URI: `https://spec.canvasprotocol.org/v0.5/extensions/textstyle-node.json`
- Doc: [spec/v0.6/spec.md#text-style-node](spec/v0.6/spec.md#text-style-node-extension)
- Version: v0.5
- Author: Open Canvas Working Group

## Transforms Node

- Name: `@ocif/node/transforms`
- URI: `https://spec.canvasprotocol.org/v0.5/extensions/transforms-node.json`
- Doc: [spec/v0.6/spec.md#transforms-node](spec/v0.6/spec.md#node-transforms-extension)
- Version: v0.5
- Author: Open Canvas Working Group

# Relation Extensions

## Edge Relation

- Name: `@ocif/rel/edge`
- URI: `https://spec.canvasprotocol.org/v0.5/core/edge-rel.json`
- Doc: [spec/v0.6/spec.md#edge-relation](spec/v0.6/spec.md#edge-relation-extension)
- Version: v0.5
- Author: Open Canvas Working Group

## Group Relation

- Name: `@ocif/rel/group`
- URI: `https://spec.canvasprotocol.org/v0.5/core/group-rel.json`
- Doc: [spec/v0.6/spec.md#group-relation](spec/v0.6/spec.md#group-relation-extension)
- Version: v0.5
- Author: Open Canvas Working Group

## Hyperedge Relation

- Name: `@ocif/rel/hyperedge`
- URI: `https://spec.canvasprotocol.org/v0.5/extensions/hyperedge-rel.json`
- Doc: [spec/v0.6/spec.md#hyperedge-relation](spec/v0.6/spec.md#hyperedge-relation-extension)
- Version: v0.5
- Author: Open Canvas Working Group

## Parent-Child Relation

- Name: `@ocif/rel/parent-child`
- URI: `https://spec.canvasprotocol.org/v0.5/extensions/parent-child-rel.json`
- Doc: [spec/v0.6/spec.md#parent-child-relation](spec/v0.6/spec.md#parent-child-relation-extension)
- Version: v0.5
- Author: Open Canvas Working Group

# Deprecated Extensions

The authors of these extensions have deprecated them. Often this means that the functionality has been replaced by a newer version of the extension.

- `https://spec.canvasprotocol.org/node/ports/0.2`
- `https://spec.canvasprotocol.org/rel/hyperedge/0.2`
- `https://spec.canvasprotocol.org/rel/parent-child/0.2`
- `https://spec.canvasprotocol.org/rel/relative/0.2`
- `@ocif/rel/set` - functionality merged into `@ocif/rel/group` in v0.5
- `@ocif/node/relative` - functionality moved to `@ocif/node/transforms` in v0.5
