---
title: Problems with the New Layout
layout: post
date: 19-09-2026
---

This was a busy week for me, and mostly not with **Froggum**. Last Saturday, I managed to do all the
implementation work, then ran out of time to post this. Here it is, a week late.

All of the features I described last time are done. There is one big problem now: I don't like how
it looks.

This is exactly the kind of big, undefinable problem that would stall development for months in the
past. To avoid that this time, I will attempt to define everything I don't like and what could be
better.

## Problems
### Problem 1: The layout changes

Adding a new element causes the sidebar to expand if the name doesn't fit. Deleting the element with
the longest name causes the sidebar to shrink.

The transition between nothing in the sidebar when no element is selected and everything in the
sidebar when an element becomes selected is also jarring, but at least does not affect the size of
the viewport.

#### Problem 1a: Long names

Duplicating an element prepends "Copy of " to the name of the original, resulting in elements named
"Copy of Copy of Copy of Copy of Copy of Copy of Copy of Circle", which takes up a lot of horizontal
space.

### Problem 2: Not all items are visible

Many elements have a lot of options, especially when segment and handle options are added. These do
not all fit in the sidebar, and so some of them are only accessible through scrolling. This will
always be necessary, but there's no obvious indicator that there are more options that could be
accessed if you scroll in the sidebar.

## Solutions
### Solution to Problem 1

The width of the sidebar should not be attached to the length of element names. It should have some
default width, and scroll if necessary within the name box if names don't fit. (Checking the
documentation suggests EditableLabels can't do that by default, so I might need to scroll the whole
box instead.)

*(This fixes the layout issue of Problem 1a, so fixing Problem 1a properly will be moved to step 7
from the roadmap: Fix more bugs)*

### Solution to Problem 2

The side panel will be divided into four sections:
 * A constant-size preview of the element, with fill and stroke color settings
 * A resizable space for element options
 * A resizable space for listing elements
 * The buttons for adding, removing, and otherwise modifying the list of elements

When no element is selected, the preview will show the entire image, and the fill and stroke buttons
will apply to the root SVG element. *(According to the SVG spec, setting fill and stroke colors on
a group or `svg` element sets a default for any child elements without a fill or stroke specified.
I will not be adding that to this version of **Froggum**.)*

The resizable spaces will be set to always show vertical scrollbars when there are more options than
fit.

This won't fix everything, but it should reduce the unnecessary layout updates and make it slightly
more obvious that there are options.

## Checklist

 - [ ] Adding an element with a long name truncates the name for display rather than expand the
   sidebar
 - [ ] Editing long names scrolls the editor so all parts can be edited properly
 - [ ] The preview is always present
 - [ ] Setting fill and stroke patterns on any element (including the root SVG) works saving and
   loading
 - [ ] Scrollbars are visible any time the resizable spaces are too small for their content


