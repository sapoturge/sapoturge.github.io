---
layout: post
title: "Feature Design: Rounded Rectangles"
date: 2023-01-28
---

This was postponed from the [Basic Shapes](/2022/12/13/basic-shapes.html)
feature becuase it needed custom context menu options for toggling
rounding. Hopefully, this will complete all the SVG implementation for
release 0.3, and the remainder will be editor improvements. (I still have
two major features I want to add but haven't started yet.)

## From the [Spec](https://www.w3.org/TR/SVG11/shapes.html):
> ### 9.2 The `rect` element
> 
> The `rect` element defines a rectangle which is axis-aligned with the
> current user coordinate system. Rounded rectangles can be achieved by
> setting appropriate values for attributes `rx` and `ry`.
> 
> Attribute definitions:
> 
>  * `x = "<coordinate>"`\
>     The x-axis coordinate of the side of the rectangle which has the
>     smaller x-axis coordinate value in the current user coordinate
>     system.\
>     If the attribute is not specified, the effect is as if a value of "0"
>     were specified.\
>     Animatable: yes.
>  * `y = "<coordinate>"`\
>     The y-axis coordinate of the side of the rectangle which has the
>     smaller y-axis coordinate value in the current user coordinate
>     system.\
>     If the attribute is not specified, the effect is as if a value of "0"
>     were specified.\
>     Animatable: yes.
>  * `width = "<length>"`\
>     The width of the rectangle.\
>     A negative value is an error (see Error processing). A value of zero
>     disables rendering of the element.\
>     Animatable: yes.
>  * `height = "<length>"`\
>     The height of the rectangle.\
>     A negative value is an error (see Error processing). A value of zero
>     disables rendering of the element.\
>     Animatable: yes.
>  * `rx = "<length>"`\
>     For rounded rectangles, the x-axis radius of the ellipse used to
>     round off the corners of the rectangle.\
>     A negative value is an error (see Error processing).\
>     See the notes below about what happens if the attribute is not
>     specified.\
>     Animatable: yes.
>  * `ry = "<length>"`\
>     For rounded rectangles, the y-axis radius of the ellipse used to
>     round off the corners of the rectangle.\
>     A negative value is an error (see Error processing).\
>     See the notes below about what happens if the attribute is not
>     specified.\
>     Animatable: yes.
> 
> The values used for the x- and y-axis rounded corner radii are determined
> implicitly if the `rx` or `ry` attributes (or both) are not specified, or
> are specified but with invalid values. The values are also subject to
> clamping so that the lengths of the straight segments of the rectangle
> are never negative. The effective values for `rx` and `ry` are
> determined by following these steps in order:
> 
>  * Let rx and ry be length values.
>  * If neither `rx` nor `ry` are properly specified, then set both rx and
>    ry to 0. (This will result in square corners.)
>  * Otherwise, if a properly specified value is provided for `rx`, but not
>    for `ry`, then set both rx and ry to the value of `rx`.
>  * Otherwise, if a properly specified value is provided for `ry`, but not
>    for `rx`, then set both rx and ry to the value of `ry`.
>  * Otherwise, both `rx` and `ry` were specified properly. Set rx to the
>    value of `rx` and ry to the value of `ry`.
>  * If rx is greater than half of `width`, then set rx to half of `width`.
>  * If ry is greater than half of `height`, then set ry to half of
>    `height`.
>  * The effective values of `rx` and `ry` are rx and ry, respectively.
> 
> Mathematically, a `rect` element can be mapped to an equivalent `path`
> element as follows: (Note: all coordinate and length values are first
> converted into user space coordinates according to Units.)
> 
>  * perform an absolute moveto operation to location (x+rx,y), where x is
>    the value of the `rect` element's `x` attribute converted to user
>    space, rx is the effective value of the `rx` attribute converted to
>    user space and y is the value of the `y` attribute converted to user
>    space
>  * perform an absolute horizontal lineto operation to location
>    (x+width-rx,y), where width is the `rect` element's `width` attribute
>    converted to user space
>  * perform an absolute elliptical arc operation to coordinate
>    (x+width,y+ry), where the effective values for the `rx` and `ry`
>    attributes on the `rect` element converted to user space are used as
>    the rx and ry attributes on the elliptical arc command, respectively,
>    the x-axis-rotation is set to zero, the large-arc-flag is set to zero,
>    and the sweep-flag is set to one
>  * perform a absolute vertical lineto to location (x+width,y+height-ry),
>    where height is the `rect` element's `height` attribute converted to
>    user space
>  * perform an absolute elliptical arc operation to coordinate
>    (x+width-rx,y+height)
>  * perform an absolute horizontal lineto to location (x+rx,y+height)
>  * perform an absolute elliptical arc operation to coordinate
>    (x,y+height-ry)
>  * perform an absolute absolute vertical lineto to location (x,y+ry)
>  * perform an absolute elliptical arc operation to coordinate (x+rx,y)

## Design in **Froggum**

### Implementation:
 * Rectangles:
    * New property `bool rounded`

      When this is true, the rounding handles exist, and the element can
      be converted into a Path. When false, rounding handles do not exist,
      and the rectangle can be converted into a Polygon.

      When loading, this is enabled if "rx" or "ry" attributes are on the
      SVG element and they are not both 0. When saving, "rx" and "ry"
      attributes will only be added if this is true and both elements are
      nonzero.

    * New internal properties `double rx, ry`

      These control the positions of the rounding handles when rounding is
      enabled. When rounding is not enabled, these have no effect.

    * New control points 'top_right_round, top_left_round, right_top_round,
      right_bottom_round, bottom_right_round, bottom_left_round,
      left_bottom_round, left_top_round'

      These are calculated from the existing corner locations, rx, and ry.
      They only appear when rounded, and click checking will not check them
      if rounding is disabled.

    * New context menu option 'Rounded'

      This just toggles the `rounded` property. If rounding was not enabled
      and `rx` or `ry` were set to zero (which is set by default), those
      will be set to 1.5 each (or the maximum value of width/2 or height/2,
      if 1.5 is too large).

    * Saving updates:

      If rounding is true, rx != 0, and ry != 0, attributes "rx" and "ry"
      will be added to the SVG element for the rectangle.

    * Loading update:

      If "rx" and/or "ry" attributes are present on the SVG element, they
      will be loaded according to the algorithm in the SVG spec:

        * If neither is specified properly, rounding is disabled.
        * If only one is specified, both are given that value.
        * If the loaded values are greater than the maximum value for that
          dimension (width/2 or height/2), they are set to the maximum
          value instead.

      If values are set, rounded will be set to true. Otherwise, rounded
      will be set to false, and both rx and ry will be set to zero.

    * Drawing updates:

      If rounding is false, drawing works exactly as currently. If true,
      this instead draws four arcs for the corners, with Cairo implicitly
      adding the straight lines.

As always, this is not guaranteed to be how rectangles will actually be
implemented. I may run into issues during implementation that I didn't
think of here, like when I realized I couldn't implement rounded rectangles
earlier without fixing context menus.
