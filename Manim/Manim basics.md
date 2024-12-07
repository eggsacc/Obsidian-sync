
Created <font style="color:tomato; font-family:Consolas;">31-10-2024</font>

Tags: 

Related: NIL

****

## Canvas

The default Manim canvas spans from -7.1 to 7.1 along the x-axis, and -4 to 4 in the y-axis. The camera frame itself is actually also a `Mobject`, and benefits from object methods like `.scale`, `.move_to` etc.

## `Mobjects`

`Mobject` is the base class for all Manim objects. However, for rendering efficiency, Manim also introduced another class called `VMobject`, or vectorized M-objects. These `VMobjects` are rendered using vector graphics

All objects in Manim are defined by their `x, y, z` coordinates.

## `VGroups`

`VGroup` is a group of vectorized M-objects. It could be used to combine multiple objects together to be transformed/scaled.

### Example: creating a square object

````python
from manim import *

class Series(Scene):
    def construct(self):
        sq = VMobject()
        points = [
            [1, 1, 0],
            [1, -1, 0],
            [-1, -1, 0],
            [-1, 1, 0],
            [1, 1, 0] # back to first point
        ]
        # combine all the points using VGroup
        dots = VGroup(*[Dot(p) for p in points] )
        self.add(dots)
        sq.set_points_as_corners(points)
        self.play(Create(sq), run_time= 2)


````

![[Series-ezgif.com-video-to-gif-converter (1) 1.gif|700]]


## Bezier curves

Every length in Manim has 2 anchor points (start and end), as well as a handle point in the middle. When 2 points are set as corners like those in the square example above, Manim implicitly adds the handle points to make the lines a Bezier curve. As such, we can use methods like `make_smooth()`  to "smoothen" the square out, or just use the method `set_points_smoothly()` while initializing the square corner coordinates.

````python
from manim import *

class Series(Scene):
    def construct(self):
        sq = VMobject()
        points = [
            [1, 1, 0],
            [1, -1, 0],
            [-1, -1, 0],
            [-1, 1, 0],
            [1, 1, 0] # back to first point
        ]
        dots = VGroup(*[Dot(p) for p in points] )
        self.add(dots)
        sq.set_points_as_corners(points)
        sq.make_smooth()
        # same as:
        # sq.set_points_smoothly(points)
        self.play(Create(sq), run_time= 2)
````

![[Series-ezgif.com-optimize 1.gif|700]]


## Placing objects in scene

After adding an object to the scene using `self.add(object)`, the objects could be repositioned and using the following methods:

- `object.move_to(coord)`
- `object.shift(relative coord)`
- `object.to_edge(dir, buff=1)`
- `object.to_corner(corner, buff=1)`

Directions in Manim include `UP, DOWN, LEFT, RIGHT`, and corners are defined by the macros `UL, UR, DL, DR`.  There is also a `ORIGIN` macro which represents the coordinates `(0,0,0)`.

Other transformation methods:

- `object.rotate(angle * DEGREES)`
- `object.scale(factor)`

![[Series-ezgif.com-video-to-gif-converter (2).gif|700]]

## Get coordinates on object

There are methods available which allows us to retrieve the coordinates of specific points on an `MObject`. These points are dictated by the anchor & handle points added automatically by Manim to create Bezier curves, as explained earlier.

![[Pasted image 20241107115245.png]]