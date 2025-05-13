# How To Edit The Spec
<!-- TOC -->
* [How To Edit The Spec](#how-to-edit-the-spec)
  * [Release Instructions](#release-instructions)
  * [How To Typeset Issues](#how-to-typeset-issues)
<!-- TOC -->


- All URIs should have the same, consistent structure
- Property tables should follow these conventions
    - **required** is always bold, other entries are not
    - JSON types are set in `monospace`
    - OCIF types are linked to their definition
    - All examples start with `**Example:**`
    - Order of columns is always: Property, JSON Type, OCIF Type, Required, Contents, Default
        - Empty columns can be omitted

## Release Instructions

When creating a new version of the spec:

1. `cp` the current version's directory (`/spec/vX.X`) to the next version number with `-draft` appended
2. Merge the new directory to the `main` branch.
3. Create a new branch named `vX.X-draft`.
4. Create pull requests against the new `vX.X-draft` folder until satisfied with release. These will have nice, small diffs that just highlight the major changes.
    - Alternatively, you can leave the new directory on a branch and create pull requests against that branch until it's ready to be merged.
5. Open a pull request to update all of the "current" version pointers in the `spec` repo:
    - rename the `vX.X-draft` folder to `vX.X`
    - update `/public/_redirects` line 2 to point spec.canvasprotocol.org to:
      ```
      / https://github.com/ocwg/spec/blob/main/spec/vX.X/spec.md 302
      ```
    - Excluding the `/spec` directory, find and replace the previous version with the new version (i.e., replace `v0.4.1` with `v0.4.1.1`).
        - That will update the Cookbook, Catalog, Examples, and README.md
6. Update the version numbers on the [website](https://github.com/ocwg/canvasprotocol.org/blob/main/index.html).
7. Consider notifying people in Discord and sending a Newsletter update.

## How To Typeset Issues
- Issues are temporary TODOs, which should be resolved before the final version. The `@@` makes them easy to search in an editor.

**Issue Example:**

- [ ] @@ This is an issue


