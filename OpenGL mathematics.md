
Created <font style="color:tomato; font-family:Consolas;">06-12-2024</font>

Tags: 

Related: NIL

Sources: NIL

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
\frac{y_{p}}{y}=\frac{n}{z},\ \ \frac{x_{p}}{x}=\frac{n}{z}
$$
$$
y_{p}=\frac{n\cdot y}{z}, \ \ x_{p}=\frac{n\cdot x}{z}
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
\frac{n\cdot x}{z} \\
\frac{n\cdot y}{z} \\
z \\
1
\end{pmatrix}
$$

...Except we can't. Notice how the `x` and `y` values needs to be divided by `z`. There is no way for us to move the `z` value out to be used for division. 

This is where homogeneous coordinates are used. We are probably all wondering why the vector representing each 3D point has 4 components -- the last `w` component is known as the **homogeneous value**, and OpenGL automatically divides all 3 `x, y, z` values by `w` internally. 

We can use it to "store" the value of `z` by multiplying it by `z`.

$$
\begin{bmatrix}  
n&0&0&0 \\
0&n&0&0 \\
0&0&u_{1}&u_{2} \\
0&0&1&0
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
z
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

