	
Created <font style="color:tomato; font-family:Consolas;">01-12-2024</font>

Tags: 

Related: NIL

Sources: [Learn OpenGL - Shaders](https://learnopengl.com/Getting-started/Shaders) | [OpenGL Course - YouTube](https://www.youtube.com/watch?v=45MIykWJ-C4&t=1966s)

****

## Function

Shaders are little programs living on the GPU that are executed for very specific tasks in the graphics pipeline. They could be thought of as a function which takes an input and transforms it into some output that is fed into another shader down the line.

## GLSL

GLSL is the language used to write shader-related code. The language is tailored for use with graphics and has features targeted at vector and matrix multiplications.

Shaders always begin with a version declaration followed by a list of inputs and output variables, uniforms, and finally the `main()` function.

````cpp
#version version
in type in_variable_name;
out type out_variable_name;
uniform type uniform_name;

void main()
{
	// ;;
	out_variable_name = processed_stuff;
}
````

In the context of a vertex shader, each variable is also known as a vertex attribute.

### Vectors

A vector in GLSL is a container for a specific type of element, storing between 2 to 4 values. The vector types include:

- `vecx`: default vector of x floats
- `bvecx`: vector of x booleans
- `ivecx`: vector of x integers
- `uvenx`: vector of x unsigned integers
- `dvecx`: vector of x doubles

The values could be accessed using `vecx.x/y/z/w`. The vector datatype allows for a interesting method of component selection called **swizzling**.

````cpp
vec4 some_vec = (0.1, 0.2, 0.3, 0.4);
vec4 another_vec = (some_vec.xywx); 
// another_vec = (0.1, 0.2, 0.4, 0.1)
````

### Inputs / outputs

Vertex shaders slightly differ from other shaders in that it receives its input straight from the vertex data. This is why we have to specify some extra parameters which defines the location and how the data is stored. The location is the one defined by `layout (location=0)` in the shader function.

Another exception is that the fragment shader must output a `vec4` color output variable since its purpose is to generate the final color output.

#### Example: custom vertex color

We can add the RGB values of each vertex in the vertex array.

````cpp
/* 3D vertices coordinates & RGB color */
GLfloat vertices[] =
{
	/*      Coords    ||      Color    */
	-0.5f,  0.5f, 0.0f, 0.8f, 0.3f, 0.02f,
	 0.5f,  0.5f, 0.0f, 0.7f, 0.4f, 0.02f,
	-0.5f, -0.5f, 0.0f, 0.6f, 0.5f, 0.02f,
	 0.5f, -0.5f, 0.0f, 0.5f, 0.6f, 0.02f
};
````

Update the vertex shader to have another output called `color` at location `1`, and add an input to the fragment shader with the same name `color`.

>Vertex shader
````cpp
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aColor;
out vec4 color;

void main()
{
    gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
    color = vec4(aColor, 1.0f);
}
````

>Fragment shader
````cpp
#version 330 core 
out vec4 FragColor;
in vec4 color;

void main()
{
    FragColor = color;
}
````

Lastly, we have to tell OpenGL how to interpret the `vertices[]` array.

![[Pasted image 20241203084922.png#invert]]

````cpp
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);

glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)(3 * sizeof(float)));
````


## Uniforms

Another way to pass data to shaders is through the use of `uniform` variables. These variables are globally declared within the scope of each shader program, meaning they can be accessed by any shader at any point in the shader program and will remain unchanged unless modified or deleted.

For example, in the vertex shader, we can add a `scale` variable to be adjusted in the main code:

````cpp
#version 330 core
layout (location = 0) in vec3 aPos;
uniform float scale;

void main()
{
    gl_Position = vec4(aPos.x * scale, aPos.y * scale, aPos.z * scale, 1.0);
    color = vec4(aColor, 1.0f);
}
````

The `scale` variable could be accessed using it's ID in the main code:

````cpp
GLuint scaleID = glGetUniformLocation(shaderProgram_ID, "scale");
glUseProgram(shaderProgram_ID);
glUniform1f(scaleID, 0.5f);
````

Note that it is mandatory to activate the shader program prior to modifying any uniform variables.

Depending on what type of data the uniform variable is, there are different functions used to access it:

- `f`: float
- `i`: int
- `uint`: unsigned int
- `1f/2f/3f/4f`: 1/2/3/4 floats
- `fv`: float vector / array