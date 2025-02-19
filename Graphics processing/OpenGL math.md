
Created <font style="color:tomato; font-family:Consolas;">06-12-2024</font>

Tags: 

Related: NIL

Sources: [OpenGL Projection Matrix](https://www.songho.ca/opengl/gl_projectionmatrix.html#google_vignette) | [The Math behind (most) 3D games - Perspective Projection](https://www.youtube.com/watch?v=U0_ONQQ5ZNM&list=WL&index=3)

****

## A deep dive

This article explores the fundamental mathematical concepts used by OpenGL to manage all the different perspectives and coordinate systems.


## Canonical view volume

The canonical view volume is a standardized cube-shaped 3D space with coordinates ranging from -1 to 1 on each axis. 


![[Pasted image 20241206213922.png#invert|center]]


## Orthogonal view volume

The orthogonal view volume is a rectangular frustum that defines the volume of the world scene that will be rendered. It consists of a near and far plane of equal height and width, and could be placed anywhere in the world. Any vertices outside the orthogonal view volume will be clipped and discarded.


![[Pasted image 20241206151645.png#invert|center]]


Since each point within the volume is directly projected onto the near plane, there is no perspective involved. All objects will remain their original size no matter how far they are from the camera.

### Aligning orthogonal projection to canonical view

We need to shift the orthogonal view volume to the canonical view volume and scale it down to fit all the points in the canonical view to be rendered. Assume the orthogonal view volume is as follows:

![[Pasted image 20241206220704.png#invert]]

To translate its center to the center of the canonical view volume, we use the matrix:

$$
\begin{bmatrix}  
1&0&0&-\frac{r+l}{2} \\
0&1&0&-\frac{t+b}{2} \\
0&0&1&\frac{f+n}{2} \\
0&0&0&1
\end{bmatrix}
$$

Now we need to scale it down to the normalized range of $[-1,1]$ for all axis.

$$
\begin{bmatrix}
\frac{2}{r-l} &0&0&0 \\
0&\frac{2}{t-b} &0&0 \\
0&0&-\frac{2}{f-n}&0 \\
0&0&0&1
\end{bmatrix}
$$

If we combine the matrices, we get the orthogonal transformation matrix:

$$
\begin{bmatrix}
\frac{2}{r-l} &0&0&0 \\
0&\frac{2}{t-b} &0&0 \\
0&0&\frac{-2}{f-n}&0 \\
0&0&0&1
\end{bmatrix} \cdot
\begin{bmatrix}  
1&0&0&-\frac{r+l}{2} \\
0&1&0&-\frac{t+b}{2} \\
0&0&1&\frac{f-n}{2} \\
0&0&0&1
\end{bmatrix}=
\begin{bmatrix}
\frac{2}{r-l}&0&0&-\frac{r+l}{r-l} \\
0&\frac{2}{t-b}&0&-\frac{t+b}{t-b} \\
0&0&-\frac{2}{f-n}&-\frac{f+n}{f-n} \\
0&0&0&1
\end{bmatrix}
$$


## Perspective matrix

An observer's perspective can be represented as a square frustum.


![[Pasted image 20241206230919.png#invert]]

We need to project each point between the 2 planes onto the near plane.


![[Pasted image 20241206234307.png#invert]]


We can use the property of similar triangles to find a relationship between the coordinates of the projected point and the actual coordinates. The above figure only illustrates the y-z coordinate frame, but the same concept applies to the x-z frame.

Hence, we get:

$$
\frac{y_{p}}{y}=\frac{n}{-z},\ \ \frac{x_{p}}{x}=\frac{n}{-z}
$$
$$
y_{p}=\frac{n\cdot y}{-z}, \ \ x_{p}=\frac{n\cdot x}{-z}
$$


Now, we can try to construct the 4x4 matrix that transforms each coordinate to their projected coordinates:

$$
\begin{bmatrix}  
?&?&?&? \\
?&?&?&? \\
?&?&?&? \\
?&?&?&?
\end{bmatrix} \cdot
\begin{pmatrix}
x \\
y \\
z \\
1
\end{pmatrix}=
\begin{pmatrix}
\frac{n\cdot x}{-z} \\
\frac{n\cdot y}{-z} \\
z \\
1
\end{pmatrix}
$$

...Except we can't. Notice how the `x` and `y` values needs to be divided by `z`. There is no way for us to move the `z` value out to be used for division. 

This is where homogeneous coordinates are used. We are probably all wondering why the vector representing each 3D point has 4 components -- the last `w` component is known as the **homogeneous value**, and OpenGL automatically divides all 3 `x, y, z` values by `w` internally. 

We can use it to "store" the value of `z` by multiplying it by `-z`.

$$
\begin{bmatrix}  
n&0&0&0 \\
0&n&0&0 \\
0&0&u_{1}&u_{2} \\
0&0&-1&0
\end{bmatrix} \cdot
\begin{pmatrix}
x \\
y \\
z \\
1
\end{pmatrix}=
\begin{pmatrix}
n\cdot x \\
n\cdot y \\
z^2 \\
-z
\end{pmatrix}
$$

Notice that the `z` component must have the value $z^2$ since it will also be divided by `w` which is set to `z`. This leaves us with 2 unsolved variables, $u_{1}$ and $u_{2}$ .

If we expand the 3rd row of the matrix multiplication, we get:

$$
u_{1}\cdot z+u_{2}=z^2
$$

This is a quadratic equation, so solving it will yield at most 2 distinct values. The only 2 known z-values we can use for substitution is the near-plane and far-plane offsets, `n` and `f`.

Substituting  $z=n$ and $z=f$:

$$
\begin{array} \\
n \cdot u_{1}+u_{2}=n^2\\
f\cdot u_{1}+u_{2}=f^2 
\end{array}
$$

Re-arranging and substituting to find $u_{1}$:

$$
\begin{array}\\
u_{2}=n^2-n\cdot u_{1} \\ \\

f\cdot u_{1} + (n^2-n\cdot u_{1})=f^2 \\ \\

u_{1}(f-n)=f^2-n^2 \\ \\

u_{1}=\frac{f^2-n^2}{f-n} \\ \\

u_{1}=f+n
\end{array}
$$
Substituting $u_{1}$ to find $u_{2}$:

$$
\begin{array}\\
u_{2}=n^2-n\cdot (f+n) \\ \\

u_{2}=-n\cdot f
\end{array}
$$


$n$ and $f$ are known constants. Hence, we finally have the perspective transformation matrix:

$$
\begin{bmatrix}
n&0&0&0 \\
0&n&0&0 \\
0&0&f+n&-n\cdot f \\
0&0&1&0
\end{bmatrix}
$$

Ideally, we would want to map all the points between the planes linearly onto the near-plane. However, the best we could do is to make this true for only the points lying **on** the near-plane and far-plane. 

Any point in between will be transformed non-linearly as illustrated below. The green line represents the ideal linear transformation, while the blue line represents the actual transformation of z-coordinates by the perspective matrix.


![[Pasted image 20241207114102.png#invert]]


However, this is not really an issue since the relative ordering of z-depth values are preserved between the planes. This means that we can still use the output $z_{p}$ to determine the ordering of objects in the scene.

In fact, this non-linear relationship is beneficial to some extent since it aids in mitigating the issue of z-fighting, where vertices very close together on the same z-depth can be rendered together.


## Final perspective transformation matrix

The final perspective transformation matrix is a combination of the perspective matrix and the orthogonal matrix.

$$
\begin{bmatrix}
\frac{2}{r-l}&0&0&-\frac{r+l}{r-l} \\
0&\frac{2}{t-b}&0&-\frac{t+b}{t-b} \\
0&0&-\frac{2}{f-n}&-\frac{f+n}{f-n} \\
0&0&0&1
\end{bmatrix} \cdot \begin{bmatrix}
n&0&0&0 \\
0&n&0&0 \\
0&0&f+n&-n\cdot f \\
0&0&-1&0
\end{bmatrix}=
\begin{bmatrix}
\frac{2n}{r-l}&0&\frac{r+l}{r-l}&0 \\
0&\frac{2n}{t-b}&\frac{t+b}{t-b}&0 \\
0&0&-\frac{f+n}{f-n}&\frac{2n\cdot f}{f-n} \\
0&0&-1&0
\end{bmatrix}

$$

The transformation matrix could be simplified by setting the far-plane offset `f` to infinity in the third row.

$$
\lim_{ f \to \infty } -\frac{f+n}{f-n}=-1
$$
$$
\lim_{ f \to \infty } -\frac{2n\cdot f}{f-n}=2n
$$

Hence, 

$$
P=\begin{bmatrix}
\frac{2n}{r-l}&0&\frac{r+l}{r-l}&0 \\
0&\frac{2n}{t-b}&\frac{t+b}{t-b}&0 \\
0&0&-1&2n \\
0&0&-1&0
\end{bmatrix}
$$
### Field of view

It is difficult to derive the left, right, top and bottom `(l, r, t, b)` values from only the plan offsets and dimensions. We can instead use the field of view (FOV) angle and the screen aspect ratio to easily determine these values using basic trigonometry.

Consider the vertical field of view of $\theta$ degrees:

![[Pasted image 20241207170220.png#invert]]

![[Pasted image 20241207170252.png#invert]]


To calculate the top value `t` :

$$
t=n \cdot \tan\left( \frac{\theta}{2} \right)
$$


The right value `r` is found by substituting $h=2t$ and $w=2r$

$$
r=\frac{w}{h} \times t 
$$

Since the near and far planes are centered and hence symmetrical around the origin, we deduce that:

$$
\begin{array} \\
r=-l \\
t=-b \\
l+r=0 \\
t+b=0
\end{array}
$$

This could be used to simplify the transformation matrix even further:

$$
P=\begin{bmatrix}
\frac{n}{w/h \times n \times \tan\left( \frac{\theta}{2} \right)}&0&0&0 \\
0&\frac{n}{n \times \tan\left( \frac{\theta}{2} \right)}&0&0 \\
0&0&-1&2n \\
0&0&-1&0
\end{bmatrix}
$$





