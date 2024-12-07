
Created <font style="color:tomato; font-family:Consolas;">01-12-2024</font>

Tags: 

Related: NIL

Sources: [Learn OpenGL - Hello Triangle](https://learnopengl.com/Getting-started/Hello-Triangle)

****

## What is a graphics pipeline?

A graphics pipeline is the process of transforming a 3D scene into 2D pixels to be displayed on a screen. There is no standardized pipeline procedure, which is why many frameworks such as OpenGL, DirectX and Vulcan are developed to standardize the process.

### Pipeline in OpenGL

In OpenGL, the graphics pipeline consists of 2 major parts: 

1) Transforming 3D coordinates to 2D coordinates,
2) Transforming 2D coordinates to actual colored pixels.

Each step in the pipeline is highly specialized and has only 1 specific function, allowing these processes to be executed in parallel on the GPU. A small program called a **shader** is ran on each core of the GPU for each step in the pipeline. 

The image below is an abstract representation of the graphics pipeline in OpenGL:

![[Pasted image 20241201132841.png]]

The tiles tinted blue are the steps in the process where the user can inject their own custom shaders.

### Vertex

We pass a list of three 3D coordinates that should form a triangle into the processing pipeline. This list of coordinates, called `vertex data`, defines a collection of vertices.

````cpp
/* sample 3D vertices coordinates */
GLfloat vertices[] =
{
	-0.5f, -0.5f * SQRT_3_3, 0.0f,
	0.5f, -0.5f * SQRT_3_3, 0.0f,
	0.0f, 0.5f * SQRT_3_3 * 2, 0.0f
};
````

However, OpenGL has no idea on what we want to do with these vertices. The vertices can be rendered as a collection of points, triangles or simply a line; we need to provide OpenGL with some primitives, or hints, so that it knows what to make of the coordinates. Some of these hints include `GL_TRIANGLES`, `GL_POINTS`, and `GL_LINE_STRIP`.

````cpp
glDrawArrays(GL_TRIANGLES, 0, 3);
````

### Pipeline steps

#### Vertex shader
The vertex shader takes a single vertex as the input and does some basic processing on the vertex attributes.

#### Geometry shader
The output of the vertex shader is optionally passed on to the geometry shader, which takes as input a collection of vertices that form a primitive and generates other shapes by emitting new vertices to form new (or other) primitives.

#### Primitive assembly
The primitive assembly stage combines the collection of vertices produced by the vertex or geometry shader and assembles all the vertices.

#### Rasterization
The assembled primitives from the previous stage is mapped to the corresponding pixels on the screen, producing **fragments** for the fragment shader to use. Before the fragment shader is ran, **clipping** is performed where all fragments outside our view are discarded to increase performance.

>[!tip] Fragments
>A fragment in OpenGL is all the data required for a single pixel to be rendered.
#### Fragment shader
The fragment shader calculates the color of the final pixel. This is the stage where more advanced OpenGL effects occur. The fragment shader calculates how other elements in the 3D scene affects the final pixel color, like lights, shadows, light source color etc.

#### Alpha test / blending
This is the stage where the depth (and stencil) value of each fragment is checked to see if a fragment is placed in front of another. Fragments covered by other opaque fragments are discarded. The alpha values of the fragments are also checked, and overlapping fragments with non-opaque alpha values may be blended together.

In OpenGL, we are required to **at least** define a **vertex** and **fragment** shader on our own since there are no default implementations available.
