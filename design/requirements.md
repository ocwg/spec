# Requirements
These are requirements and goals which we defined for a canvas interchange format.

<!-- TOC -->
* [Requirements](#requirements)
  * [Offline](#offline)
  * [Dynamic](#dynamic)
  * [Graceful Degradation](#graceful-degradation)
  * [Extensible](#extensible)
  * [Human Friendly](#human-friendly)
  * [Re-use](#re-use)
  * [Precise](#precise)
  * [Visual Fidelity](#visual-fidelity)
<!-- TOC -->

## Offline
The format should allow to be opened in an offline scenario, i.e. all
content needs to be present locally.

## Dynamic
We don’t want to force a canvas to contain only offline content. Some
resource are (a) dynamic (e.g. a weather forecast), or (b) large (e.g.
videos) so that downloading is not always the best strategy.

## Graceful Degradation
We want fall-backs for difficult content types. In email, HTML mails
usually have a plain-text fall-back.

## Extensible
We want a number of apps, including those not known at time of writing
this spec, be able to use the export format to round-trip data. Thus,

- an app may write arbitrary data into the export (given it adheres to
  some rules)
- an app may not alter or delete data written be another app (unless the
  whole node is deleted)

## Human Friendly
We want to make it easy for humans to inspect and understand an export
files' content. And, if possible, make changes on the source file.

## Re-use
When possible, we want to re-use existing standards and mechanisms, in
order to not reinvent the wheel.

## Precise
We want the spec to clearly define which OCWG files are valid, which are
not, and especially, how to interpret the valid ones.

For numeric precision, which becomes relevant in an "infinite" zoom-able
canvas, see [13](https://github.com/orgs/ocwg/discussions/13).

Text rendering and container dimensions have a complex interplay. See
[5](https://github.com/orgs/ocwg/discussions/5).

## Visual Fidelity
Although we don’t strive for 100% visual fidelity (see
[11](https://github.com/orgs/ocwg/discussions/11)), there are some areas
we need to look at:

- More Relevant
  - text rendering (e.g. different browsers have different default line
    heights; installed fonts differ, hyphenation differs, text direction,
    Unicode, …)
  - transparency & z-Index: What is in effect visible and whats hidden?
- Minor
  - color spaces?
  - path stroking (rendering a path is not 100% defined)

