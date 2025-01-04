
Created <font style="color:tomato; font-family:Consolas;">06-12-2024</font>

Tags: 

Related: NIL

Sources: [Explaining Homogeneous Coordinates & Projective Geometry — Tom Dalling](https://www.tomdalling.com/blog/modern-opengl/explaining-homogenous-coordinates-and-projective-geometry/)

****

## Intersecting parallel lines

*Can 2 parallel lines intersect?*

The answer it no, they can't. It simply does not make sense for 2 parallel (i.e. constant distance apart) lines to ever meet. Yet, if we stood at one end of 2 perfectly parallel train tracks, they seem to converge at the distant horizon. 

The reason why the 2 parallel train tracks seem to converge is due to our perspective: all things far enough away will converge into a single point, and this "far enough" distance is actually infinity. Euclidean mathematics perfectly defines two and three dimensional geometry in the cartesian space, but they fall short when it comes to the projective space. The term "infinity" is meaningless in the cartesian space since any point at $(\infty, \infty)$ simply could not be represented.


## Homogeneous coordinate system

August Ferdinand Möbius introduced the homogeneous coordinate system to make calculations for geometry and graphics possible in projective space. Homogeneous coordinates represent N-dimensional coordinates with $N+1$ coordinates.

For instance, a point $(X, Y)$ in cartesian space will be have the homogeneous coordinates $(x, y, w)$. The homogeneous and cartesian coordinates are related by:

$$
X=\frac{x}{w},\ \ Y=\frac{y}{w}
$$

For instance, the cartesian point $(2, 5)$ will be $(2, 5, 1)$ in homogeneous coordinates. Now, if this point moved to infinity, i.e. $(\infty, \infty)$, the homogeneous coordinates would be $(2, 5, 0)$ since $\frac{2}{0}\approx \infty$.

Homogeneous coordinates hence provides us with a way to represent points at infinity, without using infinity.

Another property of homogeneous coordinates is that it is scale invariant. The point $(1, 3, 2)$, $(2, 6, 4)$, and $(3, 9, 6)$ all translates to the same cartesian coordinate $(1,3)$ despite being multiplied by a different factor.



