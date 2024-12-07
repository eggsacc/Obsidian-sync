
Created <font style="color:tomato; font-family:Consolas;">06-11-2024</font>

Tags: 

Related: NIL

****

## Creating objects in scene

The core function of Manim is `self.play(animation, run_time=x)`, which renders an animation.

The `self.wait(seconds)` is another commonly used function which adds a delay. The default waiting time is 1 second.

[These animations](https://azarzadavila-manim.readthedocs.io/en/latest/animation.html) are used to introduce objects to a scene. Some common ones are below:

- `Create(MObject)`
- `FadeIn(MObject)`
- `FadeOut(MObject)`
- `DrawBorderThenFill(MObject)`
- `Uncreate(MObject)
- `ShowIncreasingSubsets()`
- `ShowSubmobjectsOneByOne()`
- `Transform()`
- `TransformFromCopy()`

### Animations specific to strings

- `Write()`
- `AddTextLetterByLetter()`
- `AddTextWordByWord()`


## Animating transformations

The `.animate` method is used to animate the transformation of `MObjects`. This `.animate` is followed by the type of transformation to be applied.

- `.animate.scale()`
- `.animate.rotate()`
- `.animate.move_to()`
- `.animate.to_corner()`
- `.animate.to_edge()`
- `.animate.become()`


## Updaters

Updaters are used to dynamically update `MObjects` based on a function that is called every frame. 

````python
def updateMObject(mob, dt):
	# function 
```` 

The `dt` parameter is the time delta, or the time elapsed between each frame. 

$$
dt = \frac{1}{framerate}
$$

For example, we can define an updater function `update_square()` which shifts a square `dt` units to the right every time it's called. The function is called repeatedly during the `self.wait()` period.

````python
from manim import *

class MoveSquare(Scene):
    def construct(self):
        sq = Square(side_length=0.5).set_fill(TEAL, 0.5)
        sq.to_edge(LEFT, buff=1)
		
        def update_square(mob, dt):
            mob.shift(RIGHT * dt)
		
        self.play(Create(sq), run_time=2)
		
        sq.add_updater(update_square)
        
        self.wait(5)
````

![[MoveSquare-ezgif.com-video-to-gif-converter.gif|700]]

### Using lambda functions

For updaters that perform simple, 1-line tasks, like tracking the position of another object to follow, we can use the Python `lambda` function method to define it in an updater.

````python
MObject.add_updater(lambda c: #function)
````

For example, a `lambda` function can be used to always position a circle below a square.

````python
circ = Circle(radius=1).set_fill(BLUE, 0.5)
circ.add_updater(lambda c: c.next_to(sq, DOWN, buff=1))
````

Now when the square `sq` moves around, `circ` will also update to remain below `sq`.

````python
class MoveSquare(Scene):
    def construct(self):
		
        sq = Square(side_length=2).set_fill(TEAL, 0.5)
        circ = Circle(radius=1).set_fill(BLUE, 0.5)
        sq.to_corner(UL, buff=1)
        circ.next_to(sq, DOWN, buff=1)
		
        def update_square(mob, dt):
            mob.shift(RIGHT * dt * 2)
            mob.rotate(2 * DEGREES * dt * 15)
		
        self.play(Create(sq), Create(circ), run_time=2)
		
        sq.add_updater(update_square)
        circ.add_updater(lambda c: c.next_to(sq, DOWN, buff=1))
        
        self.wait(5)
````

![[MoveSquare-ezgif.com-video-to-gif-converter (1).gif | 700]]


### Alternatives

There is another functions `always_redraw()` that could be used to make an `MObject` dynamic.

````python
always_redraw(lambda: MObject)
````

````python
always_rotate(_mobject_, rate=)
````

````python
always_shift(_mobject_, _direction=array([1., 0., 0.])_, _rate=)
````