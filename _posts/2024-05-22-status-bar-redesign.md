---
layout: post
title: "Redesigning the Status Bar - Again"
date: 2024-05-22
---

I recently had the opportunity to use **Froggum** to design an icon, as it was designed to do.
While doing so, I made some observations.

 1. I don't like the way the status bar works.

    This is an extension of observations made months ago while it was being implemented originally,
    and contributed to the most recent pause in development. (The other major cause was school.)
    There are too many numbers, editing them does not work, and it looks weird, especially on
    Windows, which is where I do most of my development currently.

 2. Gtk.EditableLabels don't do what I need them to do.

    For this to do what I need, I need to detect when any coordinate entry is activated, when focus
    is changed from one coordinate entry to another, when the editing is finished, and when editing
    is cancelled. EditableLabels let me detect when it's activated and when it's finished, but not
    when it's cancelled.

 3. Bugs are annoying.

    This testing led to two new bugfix branches, which have now been merged into the release
    branch and the status bar branch.

 4. I really like knowing where the handles are.

    While working on the selection fixes, I started making a vector tileset as a proof of concept
    for a future project. Since the bugfix branch was split off the release branch, I did not have
    the status bar, and I missed it.

## Proposed Changes

 * By default, only show the top-level coordinates of the point (the location in the final image).
   Expanders can be added to show the other levels when needed.

 * Next to the coordinates of the handle, show the coordinates of the cursor. This lets me see
   where things should be before putting them there.

 * Fix the separator between coordinate levels. The current version has an arrow icon, which looks
   terrible on Windows. This will be changed as part of adding the expander.

 * Make a custom text widget to manage editing the coordinates.

These changes will be made to the existing status bar branch.

As always, and as made very evident by this post, these are subject to change again as I realize
things do or don't work as I expected.

## Updated checklist

(Items I've already implemented are struck out.)

 * ~~Add a StatusBar widget~~
 * ~~Add the StatusBar to the bottom of the EditorView~~
 * ~~Detect when a handle is selected~~
 * ~~Find the path to the selected handle~~
 * ~~Put the path to the selected handle in the StatusBar~~
 * New: Make the full path hidden by default
 * New: Add an expander to show or hide the full path
 * ~~Put the handle's coordinates in the StatusBar~~
 * Add editable coordinate controls to the StatusBar
 * New: Restrict the entries to only contain numbers
 * New: Maintain focus when switching between coordinate entries
 * Update the handle's coordinates in the Statusbar when dragging in the
   Viewport
 * Update the handle's coordinates in the Viewport when editing them in
   the StatusBar
 * Make the StatusBar add an undoable command after all entries have been
   defocused
 * New: Reset all positions if the editing is cancelled
 * ~~Find the transformed position of the handle for each transformation~~
 * Transform the edited coordinates to set them on the Handle
 * Add an undo command after finishing editing any coordinate, unless
   another coordinate is focused immediately instead
 * New: Show the coordinates of the cursor in the StatusBar
 * Fix bugs
 * Anything else I think of during development

