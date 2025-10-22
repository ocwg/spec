# OCIF (Open Canvas Interchange Format) Spec

## ⚒️ Status: Seeking Implementation Feedback
The [spec](./spec/v0.6/spec.md) is in the *"Candidate Recommendation"* stage, which means it has been widely reviewed and satisfies OCWG's technical requirements. We are currently gathering implementation experience.

We welcome any feedback and implementations.

📚 Read more at [canvasprotocol.org](https://canvasprotocol.org).

## Release Process

When releasing a new version of the OCIF specification, follow these steps:

### 1. Prepare the Release

See [detailed technical release instructions](./spec/v0.5/how-to-spec.md#release-instructions) for the complete Git workflow, including:
- Creating and managing draft versions
- Updating `public/_redirects` to point spec.canvasprotocol.org to the new version
- Updating version numbers across Cookbook, Catalog, Examples, and README

Quick checklist:
- [ ] Follow the technical release steps in how-to-spec.md
- [ ] Create a git tag for the release (e.g., `v0.6`)
- [ ] Push the tag to GitHub: `git push origin v0.6`

### 2. Update Website

- [ ] Update [canvasprotocol.org](https://canvasprotocol.org) to list the new version
  - Note: The current website does not list version numbers, consider if this should change

### 3. Announce on GitHub

- [ ] Create a new announcement post in [GitHub Discussions](https://github.com/orgs/ocwg/discussions)
  - Use the Announcements category
  - Title format: "OCIF [version] Candidate Recommendation"
  - Include: version number, status, key features/changes, call to action for implementers
  - Reference [example 0.5 announcement](https://github.com/orgs/ocwg/discussions/57)

### 4. Send Email Announcement

- [ ] Draft email announcement via [Buttondown](https://buttondown.com/ocwg)
  - Subject format: "OCIF [version] and Candidate Recommendation"
  - Include TL;DR summary
  - Link to the GitHub Discussion post
  - Link to https://spec.canvasprotocol.org
  - Review [past announcements](https://buttondown.com/ocwg/archive/) for tone and format

### 5. Social Media & Community

- [ ] Post in OCWG Discord #general channel
  - Link to GitHub Discussion post
  - Link to https://spec.canvasprotocol.org
  - Brief summary of key changes

- [ ] Post to Twitter and Bluesky (@jessmartin or designated account)
  - Link to GitHub Discussion post
  - Link to https://spec.canvasprotocol.org
  - Use consistent messaging with other channels

### 6. Post-Release

- [ ] Create a new draft branch for the next version
- [ ] Update README to reflect "seeking implementation feedback" status if applicable
- [ ] Monitor GitHub Discussions and Discord for questions and feedback
