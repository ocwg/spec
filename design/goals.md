# Goals
The high-level goals of the OCWG interchange format (OCIF).

We want to allow a number of apps, including some not known at time of writing this spec, to be able to use the format to interchange data.
There are several scenarios, which we want to support:

- Allow an application A to export data,
- Let application B import it,
- Let application B see the data, which reasonably resembles what a user in A saw (goal: visual resemblance),
- Let application B make changes to the data (i.e. add/removed/change things), export it again,
- Let application A import the data, and have its special data still be there (goal: round-tripping).

## Visual Resemblance
Data can be authored in any application, exported in OCIF, imported in another compliant application, and should visually resemble the original data. 

## Round-Tripping
An app A can export to OCIF and embed its native data structures within, so that any app B can work without, re-export, and allow app A to reconstruct its native data structures. 

- If a node has been deleted in app B, it should also be deleted for app A. 
- If a node has been added in B, no native data for app A can be present. However, app A can import the node and add some defaults.

## Useful out of the Box
Balance Extensibility and Usefulness
On one extreme, we could define an ultra-extensible format for nodes devoid any built-in structure or semantics, delegating all this to extensions.
On the other hand, we could try to specify the super-set of all structures present in relevant apps. This would be a huge effort and likely to fail.

Therefore, we must carefully balance these two forces and strive for a simple, extensible, but out-of-the-box useful format. 

## Canvas Apps
To state our goals, we need to define, what a canvas app is.
Obviously, an application with the notion of an (infinite?) canvas is a canvas app. 
So a canvas app is characterized by 

- a visual (typically 2D) workspace, where nodes can be placed and connected.
- Nodes are often rectangles, but can be of other shapes. 
- Nodes can contain text, scribbles, images, or other media.
- Node can be connected by arrows or other connectors.

However, there are also a lot of slightly more specialized apps, which should also be able to profit from OCIF:
- A knowledge management tool (Hi Obsidian!)
- A freeform drawing tool (Hi Excalidraw!)
- A 3D modeling tool (Hi Aaron! Hi glTF!)
- A flow-based programming tool (Hi Maikel!)
- A mind map tool
- A (semantic) wiki 
- A graph database / knowledge graph visualizer

Less clearly in focus (although not impossible to export to OCIF) are: 
- A game level editor
- A CAD tool
- A UML tool
- A music notation tool
- A text editor
- A spreadsheet
- A video editor
- A photo/raster graphics editor

To make it even more clear, here is a list of apps known to us, which in our view are canvas apps:
* Draw.io
* Excalidraw
* Heptabase
* infinitymaps.io
* Kinopio
* Miro
* Muse
* Nodebook.io
* Notion
* Obsidian
* Roam
* TLDraw
* Zwibbler
* yEd

And here is another list of apps inspected by us, to understand the space:
* Blender Nodes	
* ReactFlow
* Kosmik
* Stately
* Everyone Draw
* Sage-3
* Bezi
* Dgrm
* Vizio
* GraphViz DOT
* Breadcrumbs
 

 More are listed https://infinitecanvas.tools/ 
