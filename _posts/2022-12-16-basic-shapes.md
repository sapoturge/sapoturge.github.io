---
layout: post
title: "Feature Design: Basic Shapes"
date: 2022-12-13
---

Transforms and their controls have been added to **Froggum**. I do plan to
add more to them in Release 0.3, but that will come later. The next
feature to add is the rest of the basic shapes. I added circles along with
groups, but there are several others that should be included as well.

As with the previous post, this is a preliminary design. Changes will
almost certainly be made during actual development.

> Note: **Froggum** currently uses SVG 1.1; SVG 2 exists and **Froggum**
> will be updated to use that at some point. The spec quoted is for SVG
> 1.1.

## From the [Spec](https://www.w3.org/TR/SVG11/shapes.html):

> ### 9.1 Introduction
> 
> SVG contains the following set of basic shape elements:
> 
>  * rectangles (including optional rounded corners), created with the
>    `rect` element,
>  * circles, created with the `circle` element,
>  * ellipses, created with the `ellipse` element,
>  * straight lines, created with the `line` element,
>  * polylines, created with the `polyline` element, and
>  * polygons, created with the `polygon` element.
> 
> Mathematically, these shape elements are equivalent to a `path` element
> that would construct the same shape. The basic shapes may be stroked,
> filled and used as clip paths. All of the properties available for `path`
> elements also apply to the basic shapes.
> 
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
> 
> ### 9.3 The `circle` element
> 
> The `circle` element defines a circle based on a center point and a
> radius.
> 
> Attribute definitions:
> 
>  * `cx = "\<coordinate>"`\
>     The x-axis coordinate of the center of the circle.\
>     If the attribute is not specified, the effect is as if a value of "0"
>     were specified.\
>     Animatable: yes.
>  * `cy = "<coordinate>"`\
>     The y-axis coordinate of the center of the circle.\
>     If the attribute is not specified, the effect is as if a value of "0"
>     were specified.\
>     Animatable: yes.
>  * `r = "<length>"`\
>     The radius of the circle.\
>     A negative value is an error (see Error processing). A value of zero
>     disables rendering of the element.\
>     Animatable: yes.
> 
> The arc of a `circle` element begins at the "3 o'clock" point on the
> radius and progresses towards the "9 o'clock" point. The starting point
> and direction of the arc are affected by the user space transform in the
> same manner as the geometry of the element.
> 
> ### 9.4 The `ellipse` element
> 
> The `ellipse` element defines an ellipse which is axis-aligned with the
> current user coordinate system based on a center point and two radii.
> 
> Attribute definitions:
> 
>  * `cx = "<coordinate>"`\
>     The x-axis coordinate of the center of the ellipse.\
>     If the attribute is not specified, the effect is as if a value of
>     "0" were specified.\
>     Animatable: yes.
>  * `cy = "<coordinate>"`\
>     The y-axis coordinate of the center of the ellipse.\
>     If the attribute is not specified, the effect is as if a value of
>     "0" were specified.\
>     Animatable: yes.
>  * `rx = "<length>"`\
>     The x-axis radius of the ellipse.\
>     A negative value is an error (see Error processing). A value of zero
>     disables rendering of the element.\
>     Animatable: yes.
>  * `ry = "<length>"`\
>     The y-axis radius of the ellipse.\
>     A negative value is an error (see Error processing). A value of zero
>     disables rendering of the element.\
>     Animatable: yes.
> 
> The arc of an `ellipse` element begins at the "3 o'clock" point on the
> radius and progresses towards the "9 o'clock" point. The starting point
> and direction of the arc are affected by the user space transform in the
> same manner as the geometry of the element.
> 
> ### 9.5 The `line` element
> 
> The `line` element defines a line segment that starts at one point and
> ends at another.
> 
> Attribute definitions:
> 
>  * `x1 = "<coordinate>"`\
>     The x-axis coordinate of the start of the line.\
>     If the attribute is not specified, the effect is as if a value of
>     "0" were specified.\
>     Animatable: yes.
>  * `y1 = "<coordinate>"`\
>     The y-axis coordinate of the start of the line.\
>     If the attribute is not specified, the effect is as if a value of
>     "0" were specified.\
>     Animatable: yes.
>  * `x2 = "<coordinate>"`\
>     The x-axis coordinate of the end of the line.\
>     If the attribute is not specified, the effect is as if a value of
>     "0" were specified.\
>     Animatable: yes.
>  * `y2 = "<coordinate>"`\
>     The y-axis coordinate of the end of the line.\
>     If the attribute is not specified, the effect is as if a value of
>     "0" were specified.\
>     Animatable: yes.
> 
> Mathematically, a `line` element can be mapped to an equivalent `path`
> element as follows: (Note: all coordinate and length values are first
> converted into user space coordinates according to Units.)
> 
>  * perform an absolute moveto operation to absolute location (x1,y1),
>    where x1 and y1 are the values of the `line` element's `x1` and `y1`
>    attributes converted to user space, respectively
>  * perform an absolute lineto operation to absolute location (x2,y2),
>    where x2 and y2 are the values of the `line` element's `x2` and `y2`
>    attributes converted to user space, respectively
> 
> Because `line` elements are single lines and thus are geometrically
> one-dimensional, they have no interior; thus, `line` elements are never
> filled (see the `fill` property).
> 
> ### 9.6 The `polyline` element
> 
> The `polyline` element defines a set of connected straight line
> segments. Typically, `polyline` elements define open shapes.
> 
> Attribute definitions:
> 
>  * `points = "<list-of-points>"`\
>     The points that make up the polyline. All coordinate values are in
>     the user coordinate system.\
>     Animatable: yes.
> 
> If an odd number of coordinates is provided, then the element is in
> error, with the same user agent behavior as occurs with an incorrectly
> specified `path` element.
> 
> Mathematically, a `polyline` element can be mapped to an equivalent
> `path` element as follows:
> 
>  * perform an absolute moveto operation to the first coordinate pair in
>    the list of points
>  * for each subsequent coordinate pair, perform an absolute lineto
>    operation to that coordinate pair.
> 
> ### 9.7 The `polygon` element
> 
> The `polygon` element defines a closed shape consisting of a set of
> connected straight line segments.
> 
> Attribute definitions:
> 
>  * `points = "<list-of-points>"`\
>    The points that make up the polygon. All coordinate values are in the
>    user coordinate system.\
>    Animatable: yes.
> 
> If an odd number of coordinates is provided, then the element is in
> error, with the same user agent behavior as occurs with an incorrectly
> specified `path` element.
> 
> Mathematically, a `polygon` element can be mapped to an equivalent
> `path` element as follows:
> 
>  * perform an absolute moveto operation to the first coordinate pair in
>    the list of points
>  * for each subsequent coordinate pair, perform an absolute lineto
>    operation to that coordinate pair
>  * perform a closepath command

## Observations

 * Line and Polyline elements are
   [open paths](https://github.com/sapoturge/froggum/issues/3),
   so adding that to Paths should also be done.

 * Error handling should probably be added at some point.

 * Rectangles and Ellipses are always aligned to the user-space axes,
   so transforms may be used more often with them than with other elements.

 * Rounded rectangles use the same element as non-rounded Rectangles.

 * Polylines and Polygons have a variable number of Segments, similar to
   Paths, but their segments can only be Lines.

## Design in **Froggum**

### Implementation

Each basic shape will have its own class inheriting Element, similar to
the existing Path and Circle classes. 

The classes will have the following control points:

 * Rectangle:
    * Corners
    * Center
    * Radius X (If rounding enabled)
    * Radius Y (If rounding enabled)

 * Ellipse:
    * Corners
    * Center

 * Line:
    * Start
    * End

 * Polyline:
    * One for each point

 * Polygon:
    * One for each point

### Interface

In addition to the control points, the following changes will need to be 
made to context menus:

 * Rectangles need a toggle for rounded corners
 * All basic shapes should have an option to convert to a Path.
 * Segments in Polylines and Polygons should warn that it will
   automatically convert to a Path.

The buttons for adding Paths and Circles will be replaced with a drop-down
menu filed with buttons to add any specific type of path. The default will
still be Path, but choosing a new type of element to add will change the
default. The default can be selected directly from the button rather than
opening the menu.

Groups will still be a separate button and will not be added to the drop
down menu.

