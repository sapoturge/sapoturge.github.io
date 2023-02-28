---
layout: post
title: "Major Feature: Status Bar"
date: 2023-02-27
---

Frequently, I want to know exactly where a point is. I also often know
exactly where a point should be and want to put it there. This was
particularly noticeable while making the eyes on the **Froggum** icons,
which needed to be symmetrical. I had to count spaces, which is not the
easiest thing to do.

## The Solution

I want to add a status bar to the bottom of the screen. This will show
the coordinates of the selected point, in every reference frame (not
always the same when elements or groups are transformed). It will also
be directly editable, so you can put points exactly where you want them.

## Implementation

### Part 1: Handles

In order to show information on the selected handle, that handle needs to
be selectable. The first part of this major feature will be converting
every draggable control to use a new Handle class that wraps a Point.
This should be easier to share across Segments than the current method of
syncronizing property updates, but it will make drawing slightly harder.

A Handle out argument will be added to the `clicked` method on Elements.
This will simplify some checking, as the `check_controls` method will
become redundant, and dragging will always use the internal Point on a
clicked Handle rather than trying to use the `reference` attribute on a
Path. Handles will also have an `options` method, allowing point-specific
context menu options like "Split Loop" to convert from Polygons to
Polylines.

Objects holding Handles can connect to the `notify` signal to know when
to update.

new class Handle: Object, Undoable

 * property `Point point`

   This holds the location of the handle, in local coordinates.

 * method `options ()`

   As a proof of concept, I will add a "Split Loop" option to Polygons.
   More options will come in later updates.

 * method `void begin (string prop)`
 * method `void finish (string prop)`

   These save the point and add it to the command stack, just like all
   other Undoables.

Checklist:

 * Add Handle class
 * Make all Elements, Transforms, and Patterns use Handles for all control
   points
 * Add a Handle out parameter to the `clicked` method on Elements, and
   similar methods on sub-items.
 * Move the `check_controls` method implementation into the `clicked`
   method. For elements, this will check internally if handles should be
   checked (when the element is selected), and will pass this on to
   sub-items.
 * Make drag checks use the Handle argument from `clicked`.
 * Add options from Handles to the context menu.
 * Add option to split Polygons.
 * Make all Handle manipulations undoable.
 * Fix bugs
 * Anything else I think of during development

### Part 1.5: Port to Gtk4

This is mostly independent of Handles, but it will allow a few useful
changes for the status bar. Specifically, it has the EditableLabel widget,
and a better method of storing Trees.

Checklist:

 * Use version 7 of the elementary flatpak sdk
 * Use gtk4 instead of gtk3
 * Fix the many bugs introduced by this change.

### Part 2: The Status Bar

This will be a simple line of text running along the bottom of the screen.
I will decide during development whether it should go under or next to
the sidebar. 

The status bar will always start with the name of the file.

When no handle is selected, the status bar will say "No handle selected."

When a handle is selected, the status bar will list all groups containing
the selected element, then the selected element, then the name of the
sub-item (if present), then a name for the handle. These will be separated
by small arrows indicating the hierarchy. After all of these, there will be
editable labels specifying the handle's x and y coordinates.

When one of the labels is opened for editing, the other label will also
open for editing. When one finishes editing, the other will also close,
unless the other was then focused, in which case both will remain in
editing mode. The handle's position will update as the user types, but will
not add a command until both labels are deselected.

Example:
```
bobby.svg > Group > Body: Fill: Gradient Stop (15, 3)
```

```
bobby.svg > No handle selected.
```

Checklist:

 * Add a StatusBar widget
 * Add the StatusBar to the bottom of the EditorView
 * Detect when a handle is selected
 * Find the path to the selected handle
 * Put the path to the selected handle in the StatusBar
 * Put the handle's coordinates in the StatusBar
 * Make the handle's coordinates in the StatusBar editable (together)
 * Update the handle's coordinates in the Statusbar when dragging in the
   Viewport
 * Update the handle's coordinates in the Viewport when editing them in
   the StatusBar
 * Make the StatusBar add an undoable command after both entries have been
   defocused
 * Fix bugs
 * Anything else I think of during development

### Part 3: Interactive Transformed Coordinates

When using transforms, the local coordinate is not always the same as the
final visible coordinate. Usually the final visible coordinate is the one
that needs to be changed, but **Froggum** only allows changing the local
coordinate. This will be further improved in the next major feature, but
first, in the status bar, each group will list the handle's coordinate 
with every lower transform applied. Each coordinate will be separately
editable, with all becoming editable at the same time.

Example:

```
bobby.svg (5, 5) > Group (3, 3) > Body: Fill: Stop 2 (13, 3)
```

Checklist:
 * Find the transformed position of the handle for each transformation
 * Add editable coordinate controls to the StatusBar
 * Transform the edited coordinates to set them on the Handle
 * Add an undo command after finishing editing any coordinate, unless
   another coordinate is focused immediately instead
 * Fix bugs
 * Anything else I think of during development

This will be followed by a few more minor features, including adding more
context menu options to Handles, then the final major feature. (If anyone
cares to guess, Part 3 is a direct preparation for the next major feature.)
Release 0.3 is nearly ready.
