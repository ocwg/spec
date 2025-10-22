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

See the [Release Process section in the README](../../../README.md#release-process) for the complete release checklist, including communication and announcement steps.

### Technical Release Workflow

When creating a new version of the spec:

1. **Create draft version**: `cp` the current version's directory (`/spec/vX.X`) to the next version number with `-draft` appended
2. **Merge to main**: Merge the new directory to the `main` branch
3. **Create draft branch**: Create a new branch named `vX.X-draft`
4. **Iterate on draft**: Create pull requests against the new `vX.X-draft` folder until satisfied with release. These will have nice, small diffs that just highlight the major changes
    - Alternatively, you can leave the new directory on a branch and create pull requests against that branch until it's ready to be merged

5. **Finalize release**: Open a pull request to update all of the "current" version pointers in the `spec` repo:
    - Rename the `vX.X-draft` folder to `vX.X`
    - Update `/public/_redirects` line 2 to point spec.canvasprotocol.org to:
      ```
      / https://github.com/ocwg/spec/blob/main/spec/vX.X/spec.md 302
      ```
    - Excluding the `/spec` directory, find and replace the previous version with the new version (e.g., replace `v0.5` with `v0.6`)
        - This will update the Cookbook, Catalog, Examples, and README.md

6. **Create git tag**: After merging, create and push a git tag for the release (e.g., `git tag v0.6 && git push origin v0.6`)

7. **Update website**: Update the version numbers on the [canvasprotocol.org website](https://github.com/ocwg/canvasprotocol.org/blob/main/index.html)

8. **Announce**: Follow the communication steps in the [README Release Process](../../../README.md#release-process) to notify the community via GitHub Discussions, Buttondown newsletter, Discord, and social media

## How To Typeset Issues
- Issues are temporary TODOs, which should be resolved before the final version. The `@@` makes them easy to search in an editor.

**Issue Example:**

- [ ] @@ This is an issue
