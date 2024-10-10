---
layout: post
title: "Feature Design: Applied Transforms"
date: 2024-10-07
---

Error reporting is not done. It is turning out to be more complicated than I expected, and needs
further design before I finish it. In the meantime, I will be moving on to the other, last major
feature of **Frogum 0.3**: applied transforms.

## The Problem

One of the major selling points of **Froggum** is the ability to anchor items to the pixel grid.
This works perfectly well when no transforms are used. However, when a transform is used, it
creates a new frame of reference, which currently is inaccessible. This means that edits in the
global reference will often cause items to become misaligned in the local frame, reducing the
benefit of using transforms at all.

## The Solution

Elements will get a new context menu button named "Apply Transform". When activated, this button
will shift the global perspective, effectively undoing all parent transforms, so that the selected
element will appear and can be edited in its local coordinate space. While a transform is applied,
there will be an info bar at the top of the screen reminding the user that this is not how the
image actually looks, and with a button to reset the view.

## Checklist

* Add "Apply Transform" button to context menus
* Create inverted transform from all parent transforms
* Apply this inverted transform before drawing elements
* Do not apply the inverted transform when drawing pixel grid
* Add an info bar stating "Viewing image from the perspective of 'element name'"
* Add a button to the info bar saying "Reset view"
* Remove the inverted transform when "Reset view" button is activated
* Hide the info bar when no inverted transform is applied
* Make edits align to the pixel grid of the inverted transform rather than the global view
* Prevent edits to the transform while it is applied
* Add "Reset view" button to context menus while a transform is applied

I've been planning this feature for almost two years now. This is a relatively niche feature, but
I do expect it to be useful for people who use transforms. This is the last feature that will be
added in **Froggum 0.3**. Everything else will be testing, bugfixes, and documentation, and then
(finally) the release.
