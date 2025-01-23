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
The OCIF core provides the building blocks which the working group found to be shared by many canvas apps. Technically, the OCIF core uses already the node and relation extension mechanism.

### Using OCIF Extensions
Usage of non-core extensions is technically almost identically to the core extensions. The only difference is the schema. Non-core extensions SHOULD provide their schema, either within the OCIF document or at the extension URI. Extensions can be defined and used in the same OCIF document, without any prior announcement or registration process. 

### Developing OCIF Extensions
An OCIF extension consists of two parts:

- a text describing the intended semantics, and
- a JSON schema that defines the structure of the extension data.

NOTE: Some extensions (e.g. ocif/node-set and ocif/node-group) have the exact same structure and differ only in semantics. The text describing what the extension does is the only difference between them. 

To publish an extensions, a version number should be included.
It is good practice to use a directory structure that reflects the version number of the extension.
Within the directory, the text is usually stored as a markdown file, which links to the JSON schema.

NOTE: The OCIF extensions document bundles several OCIF extensions into one document, but this might change in the future.
