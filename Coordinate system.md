
Created <font style="color:tomato; font-family:Consolas;">06-12-2024</font>

Tags: 

Related: NIL

Sources: [Learn OpenGL - Coordinate Systems](https://learnopengl.com/Getting-started/Coordinate-Systems)

****

## Coordinate systems

There are 5 coordinate system important to us:

- Local space (object space)
- World space
- View space (eye space)
- Clip space
- Screen space

The local coordinates attached to an object is usually transformed in the following sequence: Local coordinates -> World coordinates -> View coordinates -> Clip coordinates -> Screen coordinates.

#### Local coordinates
Coordinates relative to the coordinates of the object's local origin (center). For example, a triangle can have the local coordinates `[0, 1, 0], [1, -1, 0], [-1, -1, 0]` which will remain relative to the center of the triangle. Also, since objects are usually created at the world center, they are often also the initial positions of the object.

#### World coordinates
Position with respect to the world origin, or global origin point. All objects are positioned relative to the world origin. If we imported all the different objects into a world without defining their world coordinates, they will just cramp together at the origin. A `model` matrix handles the translation, scaling and rotation of objects in world space.

#### View space
The coordinates of objects are transformed based on how they will be perceived by a camera or eye, which is affected by depth of field, distortions etc. This is basically the camera in OpenGL. A `view matrix` is used to store all the transformations required.

#### Clip space
Coordinate of objects are transformed to fit within the normalized screen coordinates of $[-1, 1]$ , and any coordinates not visible within this range is clipped off. A `projection matrix` maps a specific portion of the world to the normalized range. Once the vertices are transformed to clip space an operation called perspective division is performed. This step is automatically performed at the end of every vertex shader.

#### Screen space
In a process called viewport transform, the normalized clip-space coordinates are transformed to the coordinate range defined by the size of our window.


## Orthographic projection

An orthographic projection matrix defines a cube-like frustum box that corresponds to the clipping space. The box is defined by its height, weight and length. The height and width defines the area that will be directly mapped to the normalized clip space, while the length defines a near and far plane; Objects nearer than the near plane and further than the far plane are discarded. 

![[Pasted image 20241206151645.png#invert|center]]

Orthogonal projections do not touch on the `w` vector, and thus does not apply any perspective division-related effects.

### Orthographic matrix

GLM provides us with a function to create orthographic matrices.

````cpp
glm::mat4 ortho = glm::ortho(left, right, bottom, top, near_offset, far_offset);
````

Orthographic projections eliminate any perspective, so objects appear weird and unnatural since there is no depth of field.


## Perspective projection

With perspective projection, objects will be transformed relative to its distance from the observer.

![[Pasted image 20241206153615.png]]

In perspective projection, the projection matrix maps a given frustum to the clip space, but also manipulates the `w` value of each vertex. The further away a point is from the observer, the larger the `w` value. After transformation to clip space, the `w` values lie in the range $[-1, 1]$. 

Perspective division is then performed in clip space:

$$
out = \begin{bmatrix}
x \div w \\
y \div w \\
z \div w
\end{bmatrix}
$$
### Perspective projection matrix

Again, we are using GLM functions to create the matrix for us.

````cpp
glm::mat4 proj = glm::perspective(fov, aspect_ratio, near_offset, far_offset);
````

`FOV` is the field of view in radians. Normally the `FOV` is set to 45 degrees. Aspect ratio is simply the ratio of `width / height`, and the `near, far_offset` are the distance between the near and far planes. The near and far plane offsets are usually set to `0.1` and `100` respectively. 


![[Pasted image 20241206154522.png#invert|center]]


## Combining matrices

Now that we have the mode, view and projection matrices, a vertex coordinate could be transformed to clip space coordinates by the following steps:

$$
V_{clip} = M_{proj.} \cdot M_{view} \cdot M_{model} \cdot V_{local}
$$

Remember that the order of matrix multiplication is the reverse of the sequence of transformations. The resulting vertex coordinates will be assigned to `gl_Position` in the vertex shader and OpenGL will automatically perform perspective division and clipping.

````cpp
GLfloat cube_vertices[] =
{
	0.5f, 0.5f, 0.5f,
	0.5f, 0.5f, -0.5f,
	-0.5f, 0.5f, -0.5f,
	-0.5f, 0.5f, 0.5f,
	0.5f, -0.5f, 0.5f,
	0.5f, -0.5f, -0.5f,
	-0.5f, -0.5f, -0.5f,
	-0.5f, -0.5f, 0.5f,
};

GLuint cube_indices[] =
{
	0, 4, 7, 7, 3, 0,
	0, 4, 5, 5, 1, 0,
	0, 3, 2, 2, 0, 1,
	1, 5, 6, 6, 1, 2,
	2, 3, 6, 6, 3, 7,
	7, 6, 4, 4, 5, 6
};

glm::mat4 model = glm::mat4(1.0f);
model = glm::rotate(model, glm::radians(-55.0f), glm::vec3(1.0f, 0.0f, 0.0f));

glm::mat4 view = glm::mat4(1.0f);
view = glm::translate(view, glm::vec3(0.0f, 0.0f, -3.0f));

glm::mat4 proj = glm::perspective(glm::radians(25.0f), 1.0f, 0.1f, 100.0f);
````

> Vertex shader
````cpp
#version 330 core
layout (location = 0) in vec3 aPos;

uniform mat4 model;
uniform mat4 view;
uniform mat4 proj;

void main()
{
    gl_Position = proj * view * model * vec4(aPos, 1.0);
}
````

![[Pasted image 20241206175409.png]]

The cube looks fine, but we can see that all the hidden faces that are out of our view are also being rendered. To solve this issue, OpenGL stores the depth information of each vertex in something known as the `z-buffer`. We can use this information to decide if a vertex should be drawn or not.

Continued on the next chapter.