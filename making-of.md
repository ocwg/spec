Making Of
=========


## Markdown

This document is written in Markdown, in the _GitHub-flavored_ variant.

- https://github.github.com/gfm/




## Other Specs as Inspiration for Structuring this Document

- https://microsoft.github.io/language-server-protocol/specifications/lsp/3.17/specification/[]
via Maikel in https://github.com/orgs/ocwg/discussions/32



## W3C Tools

- Sample spec (outline) 
    - https://www.w3.org/StyleSheets/TR/2016/README (HTML)
    - https://w3c-ccg.github.io/markdown-to-spec/ (Markdown)

- Converter from Markdown to ReSpec https://github.com/w3c-ccg/markdown-to-spec
    - Bikeshed: https://github.com/speced/bikeshed

# Editor
- IntelliJ with https://plugins.jetbrains.com/plugin/14683-gfma

In IntelliJ, the markdown preview produces HTML, which allows this snippet of CSS to be used to style markdown checkboxes (a valid GitHub flavored Markdown extension). Just copy it into the spec.md file, while you work on it.

```css
<!-- hack to render TODO items as issues -->
<style>
div > :has(input[type="checkbox"]) { 
  background-color: #433; 
  border-left: 8px solid orange; 
  padding-left: 3em;
}
div > :has(input[type="checkbox"]):before {
    content: "ISSUES";
    font-family: "Comic Sans MS",cursive;
    color: white;
    font-weight: bold;
    text-align: center;
    width: 100%;
    display: block;
    margin-right: 2ex;
    float: right;
}
input[type="checkbox"] {
   margin-left: -1em;
}
</style>
```
