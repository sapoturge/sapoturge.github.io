---
title: Layout updates
date: 25-11-15
layout: post
---

Steps 1 and 2 from my roadmap are done. On to step 3!

A few months ago, I had a chance to show **Froggum** to another developer. He had several
suggestions for improving the layout and discoverability of the features in **Froggum**.

There are a lot of them, but most of them are just adding new ways to use existing functionality, so
they shouldn't take too long. I also have access to a computer with elementary OS again, so testing
won't be the bottleneck this time.

## Pure Layout Changes

The screen when opening **Froggum** for the first time has only enough space for the buttons. This
is too little space and looks cramped. After resizing the window, **Froggum** remembers how big it
should be, but the first impression needs to be fixed.

The dialogs for editing patterns sometimes appear behind the main window, where they aren't useful.
These can be replaced with popovers.

My friend didn't realize there was a context menu on elements. He suggested adding a sidebar with
all the same information, so these options are always visible. Meanwhile, in other applications he
uses a context menu in the Layers box to add new layers, which **Froggum** currently can't do. 

He also didn't notice the button for picking new element types. These can be put directly into a
slightly larger button bar.

Checklist:
 - Reserve more space on the first open/new image screen.
 - Change pattern dialog to popovers
 - Add sidebar with contents of context menu
 - Remove submenu for adding different element types
 - Add context menu to Layers box

## Interaction Improvements

My friend observed that double-clicking and right-clicking are not obvious interactions, and that
nothing indicated where clickable things were. Adding the sidebar as an additional way of accessing
the context menu helps; clicking and updating on hover will make it better.

Checklist:
 - Change double-click interactions to single click
 - Highlight elements, segments, and handles on hover

## Other UI Adjustments

Not all buttons had tooltips. Almost none of them have keyboard shortcuts. Both need to be fixed.

Some parts of the UI refer to all elements as "paths"; these need to be fixed.

There are currently no buttons for zooming in or out. These should be added.

Checklist:
 - Ensure every button has a tooltip
 - Add keyboard shortcuts for permanent buttons
 - Remove/replace references to "paths"
 - Add buttons for zooming in and out.

## Settings

Several things that should be adjustable aren't. These will require actual changes to **Froggum**
internals.

Checklist:
 - Allow setting the tolerance for clicking on handles
 - Allow setting the tolerance for clicking on elements
 - Allow setting the tolerance for snapping to the grid

## Tutorial

My friend mostly ignored the tutorial, which was not useful and did not convey the needed
information.

Items that are important to using **Froggum**, but which are not currently in the tutorial include:
 - Opening the context menu by right-clicking.
 - Adding a new shape to the image
 - Using transforms and gradients
 - Changing the type of a segment or element

Step 5 on the roadmap will also require updating the tutorial, so I might do these together. My
firend also suggested having in-app documentation, so that can be added then as well.

## Things I Might Add Later

My friend is used to having items in menus. We discussed having File, Edit, View, and Help menus,
and what would be in each one. Unfortunately, elementary OS does not use menus this way. I will
probably add these to the native Windows frontend I intend to add in **Froggum 0.4**.

My friend also requested tools for making shapes, like a pen tool in other applications. This would
be useful, but it's not necessary, and **Froggum 0.3** has taken long enough. It will probably be
added eventually.

## Full Checklist
 - Reserve more space on the first open/new image screen.
 - Change pattern dialog to popovers
 - Add sidebar with contents of context menu
 - Remove submenu for adding different element types
 - Add context menu to Layers box
 - Change double-click interactions to single click
 - Highlight elements, segments, and handles on hover
 - Ensure every button has a tooltip
 - Add keyboard shortcuts for permanent buttons
 - Remove/replace references to "paths"
 - Add buttons for zooming in and out.
 - Allow setting the tolerance for clicking on handles
 - Allow setting the tolerance for clicking on elements
 - Allow setting the tolerance for snapping to the grid

---

### A note on timing

As I predicted, doing the work for steps 1 and 2 was very quick. I started on June 13, and both
were fully implemented by June 18. That could have led to being done by the end of June, as
predicted.

Instead, it took three months to create pull requests for them, and another two months to merge
them. Partially, this is due to not having access to a computer with elementary OS on it. I prefer
to test changes on both elementary OS and Windows before merging, and this testing did find a bug
in the scrolling that I was able to fix before I merged them. Now that both systems are available,
testing should not be a bottleneck.

The other issue (that has been an issue since I started **Froggum 0.3**) is convincing myself that
the feature is done and ready to merge. The checklists I add to these posts help, but there could
always be remaining bugs that need to be fixed or features that should be added. To resolve this, I
am setting a time limit. In one week from today (on November 22, 2025), I will either merge these
updates or post an update on what else needs to be done to make this work.


