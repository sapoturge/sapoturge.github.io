---
layout: post
title: "Error Processing"
date: 2024-12-4
---

Since the original post, I've worked on making **Froggum** handle errors. In the process, I've made
a few realizations:

* There are a lot of errors that could happen when attempting to load an image.
* Any non-trivial handling of these errors is going to be really complicated.
* The strategy I proposed for reporting errors in the previous error reporting posts might not be
  ideal, and is definitely incomplete.
* Almost all errors happen while loading.

## Considerations For What Makes Error Handling "Worth It"

In order of importance, the error handling method I end up with should:

1. Not result in unintentional data loss

    This does not mean **Froggum** always has to preserve all information from the file (although
    that would be ideal). It means the user should always be warned and explicitly consent before
    **Froggum** removes or replaces any data.

2. Allow reading as many files as possible

    If an otherwise loadable file has one element or attribute that **Froggum** doesn't understand,
    whenever possible **Froggum** should load the rest of the file, subject to the above restriction
    of getting user approval before removing or replacing the unsupported element or attribute.

3. Be reasonably simple to implement

    I want to get **Froggum 0.3** published, which requires me to actually finish it. This serves
    to counteract the "whenever possible" of the previous requirement to get a balance that works.

## Solutions

I've come up with several possible methods for resolving errors. Since different methods apply to
different errors, **Froggum** will end up with some combination of these methods.

### Give Up

The simplest way to deal with encountering an error is to give up trying to load that file. This can
be done for any error.

The error should be shown to the user, and then the tab should be closed or brought back to the "New
Image" page.

In response to not being able to open the file or major structural errors that prevent loading
anything (e.g. the file is not an SVG file), there isn't an image to load, so nothing more needs to
or can be done. All that needs to be implemented is going back to the new tab page after the user
acknowledges the error.

- [X] No unintentional data loss
- [ ] Allow reading many files
- [X] Reasonably simple to implement

#### Variant: Overwrite It

**Froggum** currently, in cases where it can't load the file at all, replaces the file with an
empty 16x16 image. This is not ideal, and is what inspired this feature to begin with.

- [ ] No unintentional data loss
- [X] Allow reading many files
- [X] Reasonably simple to implement

### Ignore It

When encountering unexpected elements or attributes, **Froggum** could remove that element or
attribute from the image and just process what it can.

While this does result in data loss, it can be made conditional on user input, and when **Froggum**
isn't able to load it, the only other option is to [give up](#give-up).

**Froggum** currently does not check for any attributes or elements that are not supported,
resulting in anything unsupported being discarded from the file when it is saved. Implementing this
requires checking for unsupported elements and attributes and informing the user of them.

- [X] No unintentional data loss
- [X] Allow reading many files
- [X] Reasonably simple to implement

### Default Value

When encountering an missing or malformed attribute, **Froggum** could apply a default value. The
SVG standard defines default values for many attributes, which would be used automatically for
missing attributes when the standard default value is supported. Unfortunately, several default
values are percents, which **Froggum** cannot handle currently, so these defaults cannot be used.

In the case of malformed attributes, replacing them with a default value would necessarily cause
data loss, and so must have user input to approve it.

The replacement can be done at loading time, so long as the original file is not overwritten before
the user approves the replacement.

- [X] No unintentional data loss
- [X] Allow reading many files
- [X] Reasonably simple to implement

### User-specified Value

For attribtes that are missing or malformed, **Froggum** could let the user input a value to use
instead. This would allow loading any valid SVG that uses unsupported attribute values but no
unsupported elements. Applying a user-specified value is considered an intentional edit, and so is
not considered data loss. 

The cost of this is that the image has to start in a partially loaded state and allow the missing
value to be entered later.

- [X] No unintentional data loss
- [X] Allow reading many files
- [ ] Reasonably simple to implement

### Save a Backup

This doesn't handle the error, but when using any method that allows data loss, it would be good to
give the user an option to save a backup of the original file and/or save the new file to a
different name.

- [X] No unintentional data loss
- [X] Allow reading many files
- [X] Reasonably simple to implement

### Another consideration: Presentation

The previous post proposed to present the errors in an error bar at the top of the screen. This
allows the user to deal with errors any time after they come up, and never interrupts them. However,
since almost all errors happen while loading, most errors should be resolved before editing, and
the only "interruption" I can think of is if **Froggum** was closed and one of the multiple previous
images has an error.

An alternative would be to have errors open a dialog or similar interface that prevents interacting
with the image until the errors are resolved. This would require a third state of each tab ("new",
"edit", and "error"), but would also help prevent abnormal edits to erratic images. It also gives
more space for displaying the error, which could be useful. While it does prevent editing a
malformed image, it also does not show any information that was able to be loaded, which information
could be helpful in determining what to do with the errors.

Compromise: Make the error bar larger and disable editing until the image is fully loaded. This
shows the error and all loaded information, without interrupting the user.

## The Final Solution

Errors will be displayed using the error bar at the top of the standard editing tab layout. While
loading errors are being processed, the error bar will be present across about a third of the
screen, and the viewport and sidebar will prevent edits (other interactions, like scrolling the
viewport, can be done normally). Within the error bar, there will be a large title describing the
kind of error (e.g. "Unknown Attribute"), then a short bit of text giving details (e.g. "Attribute
'X' of element 'rect' has value '5%', which **Froggum** is not able to understand), then along the
bottom a series of buttons for various options of what to do with the error.

All errors will have the option to stop loading the file and go back to the "New Tab" state. This
button is the only non-destructive option, so it will be the suggested action. All other options
will be marked as destructive.

Errors from unknown elements or attributes will have the option to delete the element or attribute.

Errors from invalid values of attributes will be automatically filled with some default value, with
the error message showing the default value and giving the option to accept it.

If I determine it is reasonable, I will add an option to give a custom value to replace invalid
values. This might be pushed to a second feature branch.

When clicking the button for the first destructive error-correcting action, a dialog will appear
asking the user if they want to save a backup or save to a new file. There will also be the option
to overwrite the existing file, marked as destructive.

### Checklist

 - Detect unknown elements (that are children of known elements; this doesn't need to be
   recursive)
 - Detect unknown attributes on all known elements
 - Report all unknown elements and attributes when loading
 - Apply some default value to all missing and malformed attributes
 - Report all missing and malformed attributes
 - In the error bar, show a title, description, and resolution buttons
 - Disable editing in the viewport and sidebar while errors are present
 - For all errors, have a suggested action button named "Create new file" or similar
 - When the "Create new file" button is activated, reset the tab to the new image state
 - Where applicable, have a "Delete element" button
 - Where applicable, have a "Delete attribute" button
 - Where applicable, have an "Accept default" button
 - When a default value was applied, show the original and default values in the error details
 - When any destructive action button is activated for the first time on an image, show a dialog
   asking the user to save a backup, save to a new file, or overwrite the original file.
 - In the backup file dialog, specify that changes will only occur after all errors are
   resolved.
 - When all errors are resolved, save a backup of the original file if necessary, then save the
   changed image to the updated filename.

Optional extension (to be dropped if infeasible):
 - Where applicable, have a "Use custom value" button
 - Show the backup dialog when activating the custom value button
 - Provide a place for the user to enter their new value, while showing the original value
 - Validate the entered value before allowing the user to submit it
 - Set the attribute to the given value

