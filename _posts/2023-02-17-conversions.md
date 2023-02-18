---
layout: post
title: Polygons, Polylines, and Conversions
date: 2023-02-17
---

It turns out I forgot about two "minor" feature I should add before the two
major features I mentioned in the previous post.


## Feature 1: Polylines and Polygons

Polylines and Polygons currently have a set number of "segments". To allow
changing this, I will add a new `LinearSegment` class that allows splitting
itself to increase the number of segments in a Polyline or Polygon.

### Design in **Froggum**

 * The existing `Segment` class will be refactored into a base class
   `Segment` and a subclass `PathSegment`. All functionality related to
   arcs and curves will only be in `PathSegment`, and that is the class
   that will be used directly by Paths. The `clicked` method will continue
   to use `Segment`.

 * A new class `LinearSegment` will be created subclassing `Segment` that
   will be used by Polylines and Polygons.

    * The `options` method will return an option to Split, which replaces
      the segment with two segments divided in the center of the original.
      This can be undone.

### Checklist

 * Refactor `Segment` into `PathSegment`
 * Have `Path` now use `PathSegment`
 * Create base class `Segment`
 * Create class `LinearSegment`
 * Have `Polygon` use a closed loop of `LinearSegments`
 * Have `Polyline` use a sequence of `LinearSegments`
 * Ensure `Polygons` and `Polylines` can still be clicked
 * Add option to "Split Segment"
 * Make "Split Segment" undoable
 * Make moving points on `LinearSegment` undoable
 * Fix the bugs this will introduce
 * ... Anything else I think of during development

## Feature 2: Element Conversions

Most elements (not yet Lines and Polylines) have equivalent Path versions.
It would be useful in many cases to allow converting elements to a more
general format.

## Design in **Froggum**

The following context menu options will be added:

 * `Line`:

   * Convert to Polyline

 * `Polyline`:

   * Close Loop

     This adds a `LinearSegment` between the end points of the line and
     creates a Polygon from the new closed loop.

 * `Polygon`: 

   * Convert to Path

 * `Rectangle`:

   * Convert to Polygon

     This option only exists when not rounded.

   * Convert to Path

     This option only exists when rounded.

 * `Ellipse`:

   * Convert to Path

     Arc segments are difficult, so this will replace the element with one
     of the following:

     * A `Path` with a single Arc segment
     * A `Path` with two Arc segments, divided along the x axis
     * A `Path` with two Arc segments, divided along the x axis, with
       degenerate Line segments between them
     * Something else that results in an elliptical path

 * `Circle`:

   * Convert to Ellipse

All of these options replace the element with an equivalent element of a
more general type. Only the most specific more general element type is an
option, so converting a `Circle` to a `Path`, for example, requires
converting to an `Ellipse` first.

Converting an element keeps the transform, fill, and stroke of the
original.

All of these actions can be undone.

### Checklist

 * Add "Convert to Polyline" option to Lines
 * Add "Close Loop" option to Polylines
 * Add "Convert to Path" option to Polygons
 * Add "Convert to Polygon" option to unrounded Rectangles
 * Add "Convert to Path" option to rounded Rectangles
 * Add "Convert to Path" option to Ellipses
 * Decide what the "Convert to Path" option on Ellipses should do
 * Add "Convert to Ellipse" option to Circles
 * Make all conversions undoable
 * Fix the bugs this will introduce
 * ... Anything else I think of during development

