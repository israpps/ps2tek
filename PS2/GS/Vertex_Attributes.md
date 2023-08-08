---
sort: 3
---

# GS Vertex Attributes

## `01h` RGBAQ
- 0-7    Red
- 8-15   Green
- 16-23  Blue
- 24-31  Alpha
- 32-63  Q

These attributes are applied to newly created vertices.

## `04h/0Ch` XYZF2/XYZF3
- 0-15   X
- 16-31  Y
- 32-55  Z
- 56-63  Fog coefficient (placed in FOG register)

This defines a new vertex to be placed in the vertex queue, a process known as the vertex kick. X and Y are 12-bit fixed-point integers with a 4-bit fractional component, ranging from 0 to 4095.9375.

When 1, 2, or 3 vertices have been defined as per the primitive type, a "drawing kick" occurs, where the GS begins drawing the primitive.

```note
Drawing kicks can only occur on XYZF2! XYZF3 will clear the queue as needed but will not draw any primitives. XYZF3 exists to allow the programmer to easily cull polygons, as the GS has no native support for culling.
```


See [GS Fog](./Fog.md) for details on fog.

## `05h/0Dh` XYZ2/XYZ3
- 0-15   X
- 16-31  Y
- 32-63  Z

Same as XYZF2/XYZF3, except Z is a 32-bit unsigned integer rather than a 24-bit unsigned integer.

XYZ2 can also result in a drawing kick, whereas XYZ3 cannot.

## `18h/19h` XYOFFSET_1/2
- 0-15   X
- 32-47  Y

When a vertex kick occurs, XYOFFSET is subtracted from the vertex's X and Y coordinates.
