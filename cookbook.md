# OCIF Cookbook

**Open Canvas Interchange Format (OCIF) Cookbook**

A collection of examples and best practices for using OCIF. It is intended to help developers and designers to understand how to use OCIF in their applications.

## Canvas App Users

Your canvas app might have either an OCIF export option or an OCIF import option. It might even use OCIF as its native format.
You can export OCIF from one canvas app and import it back into another canvas app. This way, you can share your work with others or use it in different apps.

OCIF defines no mechanism how the file is transported.
If your canvas app does not support OCIF, consider opening a ticket with the developers to request support for OCIF. Or be the developer yourself and use the OCIF documentation to implement support for OCIF in your favorite app.

## Canvas App Developers

If you are developing a canvas app, consider supporting OCIF as an import and export format. This way, users can share their work with others or use it in different apps.

### Using OCIF Core

The OCIF core provides the building blocks which the working group found to be shared by many canvas apps. Technically, the OCIF core also uses the node and relation extension mechanism.

### Using OCIF Extensions

Usage of non-core extensions is technically almost identical to the core extensions. The only difference is the schema. Non-core extensions SHOULD provide their schema, either within the OCIF document or at the extension URI. Extensions can be defined and used in the same OCIF document, without any prior announcement or registration process.

### Transclusion

**Goal**  
One visual node shows the [resource](spec/v0.4/spec.md#resources) of another visual node.
This concept is also known as _transclusion_.

We have two roles:

- An _original_ node -- just any existing visual node, and
- a _holder_ node -- the node showing the content of the original node.

**Solution**  
We use a [parent-child relation](spec/v0.4/extensions.md#parent-child-relation) to indicate that the holder node is a child of the original node.
The original node (the parent) is included in the holder node (the child).
Or in other words: The child inherits the properties (the resource) of the parent.

**Example: A shows content of B**  
Given two nodes A and B, we want node A to show the content (resource) of B, but at another position on the canvas.
This is a special case of the parent-child relation, where the child inherits the _resource_ property of the parent.
We use the `inherit` flag to indicate that the child should inherit the properties of the parent.
Node A could additionally specify a different size, if it wants to show the content of node B in a different size.

```json
{
  "nodes": [
    {
      "id": "A",
      "position": [100, 100]
    },
    {
      "id": "B",
      "position": [300, 200],
      "size": [20, 20],
      "resource": "r1"
    }
  ],
  "relations": [
    {
      "type": "@ocif/rel/parent-child",
      "parent": "B",
      "child": "A",
      "inherit": true
    }
  ],
  "resources": [
    {
      "id": "r1",
      "representations": [
        {
          "mime-type": "text/plain",
          "content": "Hello, World!"
        }
      ]
    }
  ]
}
```

### Developing OCIF Extensions

OCIF extensions ([see spec](spec.md#)) allow to define custom "property bags" for visual nodes or relations.
If the built-in types (technically also using the extension mechanism) are not enough, you should define your own extension.
Keep in mind that nodes and relations can use several extensions at the same time, so only the missing pieces need to be defined in your extension.
The spec defines how to define custom extensions.
