---
layout: post
title: "Feature Design: Transforms"
date: 2022-12-13
---

Now that groups have been added, the next major feature I'll be adding to
**Froggum** is transforms. This is a brief description of what I plan to
add for this; they will not necessarily be added in the same way as
presented here. 

> Note: **Froggum** currently uses SVG 1.1; SVG 2 exists and **Froggum**
> will be updated to use that at some point. The spec quoted is for SVG
> 1.1.

## From the [Spec](https://www.w3.org/TR/SVG11/coords.html#TransformAttribute):

### 7.6 The `transform` attribute

The value of the `transform` attribute is a `<transform-list>`, which is
defined as a list of transform definitions, which are applied in the order
provided. The individual transform definitions are separated by whitespace
and/or a comma. The available types of transform definitions include:

 * `matrix(<a> <b> <c> <d> <e> <f>)`, which specifies a transformation in
   the form of a transformation matrix of six values.
   `matrix(a,b,c,d,e,f)` is equivalent to applying the transformation
   matrix `[a b c d e f]`.
    

 * `translate(<tx> [<ty>])`, which specifies a translation by `tx` and
   `ty`. If `ty` is not provided, it is assumed to be zero.
    

 * `scale(<sx> [<sy>])`, which specifies a scale operation by `sx` and
   `sy`. If `sy` is not provided, it is assumed to be equal to `sx`.
    

 * `rotate(<rotate-angle> [<cx> <cy>])`, which specifies a rotation by
   `rotate-angle` degrees about a given point.

   If optional parameters `<cx>` and `<cy>` are not supplied, the rotate
   is about the origin of the current user coordinate system. The
   operation corresponds to the matrix `[cos(a) sin(a) -sin(a) cos(a) 0 0]`.

   If optional parameters `<cx>` and `<cy>` are supplied, the rotate is
   about the point (cx, cy). The operation represents the equivalent of
   the following specification:
   `translate(<cx>, <cy>) rotate(<rotate-angle>) translate(-<cx>, -<cy>)`.
    
 * `skewX(<skew-angle>)`, which specifies a skew transformation along the x-axis.
    

 * `skewY(<skew-angle>)`, which specifies a skew transformation along the y-axis.
     

All numeric values are <number>s.

## Observations

 * All transforms, no matter how complex, can be reduced to a single
   matrix by multiplying together all sub-transforms.

 * A rectangle in the new coordinate system will always map to a
   parallelogram in the base coordinate system.

 * Translations are easy to extract from a compiled transformation matrix. 

 * If no skews are present, the rotation and scaling sub-matrices can also
   be extracted without too much trouble. (Square the first two terms, then
   divide all four by the sum.)

 * No skews are present when the cross product terms are negatives of each
   other.

 * A skew can be calculated from the difference between the cross product
   terms.

 * All transformation matrices can be reduced to a translation, rotation,
   scale, and skew.

## Design in **Froggum**

### Implementation

`Transform` will be a new data class, and every Element will have a
`Transform` associated with it through a property, similar to the `fill`
and `stroke` properties.

Since any transform can be represented by a translation, rotation, scale,
and skew, they will be stored this way internally. They will be set to this
form when loading from files, and stored in this order when saving. Any 
components that would have no effect (a scaling of 1, translation of 0, 
rotation of 0, or skew of 0) are not written out when saving. If all
components have no effect, no transformation is output at all.

Only skews along the X axis will be used by **Froggum**.

When drawing elements, the element's transformation will be applied before
drawing, then restored afterward so it does not interfere with other
elements. Coordinates will be transformed by the elements when checking for
clicks.

### Interface

By default, elements (other than groups) will not show handles for their
transforms unless the transform is not the identity when loaded or it is
explicitly enabled. It can be enabled on any element by clicking the
hide/show transform button in the context menu.

Transforms have seven handles: one in each corner, one in the center, one
outside to control rotation, and one to control skew. Moving the center
handle adjusts only the translation component of the transformation. The
outside handle only adjusts the rotation. The points on the outside of the
rectangle can change both the translation and the scale of the transform.
These points all act similarly to the corresponding handles on `Arc`
segments. The skew handle starts aligned with the rotation handle, but can 
be moved perpendicularly to that axis to produce the intended skew.

Transform handles are blue, to differentiate them from the red path handles
and green gradient handles.

For now, handles will only snap to the base grid, not to a transformed
grid. Adding that ability is left for a future feature (which will likely
be in this update, 0.3).
