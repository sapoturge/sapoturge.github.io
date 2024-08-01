---
layout: post
title: "New Feature: Error Reporting"
date: 2024-07-31
---

I'm finally reasonably satisfied with the status bar. It might change again before the release, and
there are probably bugs left, but it's merged now and I need to move on.

This is not the "final major feature" I mentioned in the original status bar post. That will come
right after this. (Of the two features, this one is probably more important, but I still want both
before releasing 0.3.) After these two features, there should only be bugfixes before the actual
release. (And fixing the documentation. Everything has changed since **Froggum 0.2** was almost
published four years ago, and all of the documentation is out of date.)

## The Problem

Many things could go wrong when loading an image. For example, you could try opening something that
isn't an SVG file. Right now, **Froggum** doesn't notice, and will happily autosave a default 16x16
icon over the incorrect file, without informing the user that anything is wrong.

## The Solution

This requires an integrated system to properly handle errors.

When an error is detected, an info bar will display a message at the top of the edit window. It will
also disable autosave while the error message is shown. Error messages will include buttons with
suggested actions to take, such as "Overwrite" (marked as destructive) and "Create new". This error
bar will be red or otherwise clearly shown to be an error.

For issues that might result in problems, but still allow loading, such as "This file uses one of
the [many unsupported SVG elements](https://github.com/sapoturge/froggum/issues/19)", autosave will
still be disabled while the error is present, but the error bar will be yellow or otherwise show
a "warning" status.

## Checklist

 * Detect errors when loading icons.

   Errors detected include:
   * File is not an SVG file.
   * File uses unsupported elements.
   * File uses unsupported attributes.
   * Other issues I think of during development.
 * Detect errors when saving.
   * This should only be "Couldn't open file for saving", but I might find other error cases.
 * Errors shouldn't happen when editing files, but if I come up with any, report those too.
 * Disable autosave when errors are reported.
 * Show a box at the top of the screen informing the user of the error and suggesting actions
 * If multiple errors happen at once, only the first should be shown, but all should be resolved
   before autosave is reenabled. (Multiple errors should never happen, and if I can prove they
   won't, I will reduce this requirement.)
 * Have severity and suggested actions depend on the kind of error.
 * Reactivate autosave after the error is resolved (using a suggested action).
 * Fix bugs.

If this goes quickly, **Froggum 0.3** could be out in a month or two. 
