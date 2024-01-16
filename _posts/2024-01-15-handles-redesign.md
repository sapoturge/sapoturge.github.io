---
layout: post
title: "Minor Feature Redesign: Handles"
date: 2024-01-15
---

Last time (now almost a year ago), I planned a major feature, split into
three implementation steps: add Handles, port to Gtk4, implement the
actual feature.

It didn't turn out that way.

As planned, I started implementing the Handle class. In doing so, I
quickly ran into issues, most notably that the existing "handles" were
often computed properties, and that having an external class be the
official source of truth made maintaining the relationships between
difficult. I decided that I would be able to make it work without a Handle
class and moved on to porting to Gtk4.

Porting to Gtk4 actually went pretty well. I did lose drag-and-drop in the element list (there are open issues on the Gtk4 repository requesting it,
but apparently adding it corrently is really hard and they don't want to
do it), but the up and down buttons still support moving elements into and
out of open groups, so the functionality is still present, if less
convenient. The biggest problem with this is the statement on my last post: "Fix the many bugs introduced by this change".

I'm sure that **Froggum** is still full of bugs. However, in the last
semester, in which I used **Froggum** to help design models for my
Computer Gnaphics class, it worked well enough. So, now, finally, I have
decided to merge the feature-gtk4 branch and start working on a new
design for Handles.

### The Problem

In order to have the status bar, described fully in the last post, I need
to keep track of a selected point, its position, the names of all parent
elements, and its transformed position in all parent frames, and I need to
be able to update the point later in any of these frames. I also need to
add context menu items that appear when right-clicking on a handle, but
that affect the entire Element.

### The Solution

Add three new Handle classes.

These will be a base class and two child classes, allowing the child
classes to be used interchangeably.

Unlike the previous design where Handles stored the actual location of the
point, these Handles will sit on top of the existing properties. They will
be created in the click handler, with a new object created for each parent
element. In addition to the base point property, the handles will be given
a name and a transform. These will be used to calculate the effective
location of the handle, and to identify that point in the status bar.

At the root of the stack is the BaseHandle class. This handle directly
wraps a point property of the containing object. Context menu options can also be given to these handles when they are created.

All other levels of the stack use use the TransformedHandle class. These
wrap another Handle (BaseHandle or TransformedHandle), alogn with a
transformation and a title. Context menu options are requested from the
wrapped handle, eventaully reaching the BaseHandle. Point lookups are
done by passing the coordinates through the transform.

Checklist:
 * Add Handle class
 * Add point and options properties to Handle
 * Add BaseHandle subclass
 * Allow BaseHandles to hold ContextOptions
 * Create and return stack of Handles in clicked method
 * Add TransformedHandle subclass
 * Allow creating TransformedHandles from a Handle, Transform, and name
 * Forward context options through TransformedHandles
 * Calculate point locations in getters and setters
 * Check for clicking on handles in Element.clicked
 * Remove Element.check_controls
 * Track selected handle
 * When dragging points, update the Handle's point property
 * Ensure undoing moving points works
 * Add a Split Loop option to points in Polygons

As always, this is subject to change as I implement it. After this, I will
add the status bar, as described in the previous post, so I probably won't
make a new post for that. After that, there's only one major feature left.




