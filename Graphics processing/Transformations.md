
Created <font style="color:tomato; font-family:Consolas;">04-12-2024</font>

Tags: 

Related: NIL

Sources: [Learn OpenGL - Transformations](https://learnopengl.com/Getting-started/Transformations)

****

## Library

Linear algebra libraries like [GLM](https://github.com/g-truc/glm) could be used to work with matrices efficiently.


## Translation matrix

To translate a vertex is to shift it to another coordinate. Given a 4x1 matrix representing the x, y, z, and w values of a point, the translation matrix will be of the size 4x4 and given as:

$$
\begin{bmatrix}
1&0&0&T_{x}\\ 
0&1&0&T_{y}\\  
0&0&1&T_{z}\\
0&0&0&T_{w}\\ 
\end{bmatrix} \cdot
\begin{pmatrix} 
x \\
y \\
z \\
w\\
\end{pmatrix} = \begin{pmatrix}
x+T_{x} \\
y+T_{y} \\
z+T_{z} \\
w+T_{w}
\end{pmatrix}
$$

>[!info] `w` component
>The `w` component of the vector is called a homogeneous coordinate. A 3D vector could be obtained from a homogeneous vector by dividing the x, y and z coordinates by the homogeneous coordinate `w`. 

## Scaling matrix

Scaling a shape involves multiplying the coordinates of all its vertices by the scaling factor. The matrix could be represented as:

$$
\begin{bmatrix}
S_{x}&0&0&0 \\
0&S_{y}&0&0 \\
0&0&S_{z}&0 \\
0&0&0&S_{w} \\
\end{bmatrix} \cdot
\begin{pmatrix}
x \\
y \\
z \\
w
\end{pmatrix}=
\begin{pmatrix}
S_{x} \cdot x \\
S_{y} \cdot y \\
S_{z} \cdot z \\
S_{w} \cdot w
\end{pmatrix}
$$

## Rotation matrix

To rotate a point around the x, y and z axis, the relevant matrices are:

> Around x-axis
$$
\begin{bmatrix}
1 & 0 & 0 & 0 \\
0 & \cos\theta & -\sin\theta & 0 \\
0 & \sin\theta & \cos\theta & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}\cdot
\begin{pmatrix}
x \\
y \\
z \\
1
\end{pmatrix}=
\begin{pmatrix}
x \\
\cos\theta\cdot y-\sin\theta\cdot z \\
\sin\theta\cdot y+\cos\theta\cdot z \\
1
\end{pmatrix}
$$
 > Around y-axis
$$
\begin{bmatrix}
\cos\theta & 0 & \sin\theta & 0 \\
0 & 1 & 0 & 0 \\
-\sin\theta & 0 & \cos\theta & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}\cdot
\begin{pmatrix}
x \\
y \\
z \\
1
\end{pmatrix}=
\begin{pmatrix}
\cos\theta\cdot x+\sin\theta\cdot z \\
y \\
-\sin\theta\cdot x+\cos\theta\cdot z \\
1
\end{pmatrix}
$$
 > Around z-axis
$$
\begin{bmatrix}
\cos\theta & -\sin\theta &0 &  0 \\
sin\theta & \cos\theta & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}\cdot
\begin{pmatrix}
x \\
y \\
z \\
1
\end{pmatrix}=
\begin{pmatrix}
\cos\theta\cdot x-\sin\theta\cdot y \\
\sin\theta\cdot x+\cos\theta\cdot y \\
 z\\
1
\end{pmatrix}
$$
 
 If we want to rotate a point around all 3 axis simultaneously, a simple approach would be to multiply the point by each x, y and z rotation matrices. However, this introduces the issue of **gimbal lock**: a phenomenon where the alignment of 2 rotational axis causes the loss of 1 degree of freedom.

There exists a matrix representing the rotation around an arbitrary normalized vector $(R_{x}, R_{y}, R_{z})$:

$$
\begin{gathered}
\begin{bmatrix}
\cos\theta+R_x^2(1-\cos\theta) & R_xR_y(1-\cos\theta)-R_z\sin\theta & R_xR_z(1-\cos\theta)+R_y\sin\theta & 0 \\
R_yR_x(1-\cos\theta)+R_z\sin\theta & \cos\theta+R_y^2(1-\cos\theta) & R_yR_z(1-\cos\theta)-R_x\sin\theta & 0 \\
R_zR_x(1-\cos\theta)-R_y\sin\theta & R_zR_y(1-\cos\theta)+R_x\sin\theta & \cos\theta+R_z^2(1-\cos\theta) & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
\end{gathered}
$$

## Combining matrices

One benefit of using matrices is that we can combine multiple operations into a single matrix representation. For example, if we want to scale a vertex by 2 and translate it by $(1, 2, 0)$, we can combine both the operations into a single matrix:

$$
\begin{bmatrix}
1&0&0&1 \\
0&1&0&2 \\
0&0&1&0 \\
0&0&0&1
\end{bmatrix} \cdot
\begin{bmatrix}
2&0&0&0 \\
0&2&0&0 \\
0&0&2&0 \\
0&0&0&1
\end{bmatrix}=
\begin{bmatrix}
2&0&0&1 \\
0&2&0&2 \\
0&0&2&0 \\
0&0&0&1
\end{bmatrix}
$$

Note that the order of multiplication is important. If we reversed the above order, the scaling matrix would also affect the distance translated in each axis.

One rule about matrix multiplications is that the sequence of transformations is the reverse of their multiplication sequence. Suppose that I want to **scale** an object, **translate** it then **rotate** it, we will have to multiply the relevant matrices in the **reverse order**: **rotate**, **translate** then **scale**.
## GLM library

The GLM library simplifies a lot of the matrix operations for us and provides a variety of methods to manipulate them. It is optimized to leverage the power of SIMD to crunch those matrix multiplications really fast.

Import the following libraries:

````
#include<glm/glm.hpp>
#include<glm/gtc/type_ptr.hpp>
#include<glm/gtc/matrix_transform.hpp>
````

GLM uses the same naming convention for variables as OpenGL. 

### Empty matrix

To initialize an empty identity matrix, we need to call the relevant type and initialize all the diagonal member value to 1. The below code creates a 4x4 identity matrix.

````cpp
glm::mat4 trans = glm::mat4(1.0f);
````

### Transformations

>[!info] Unit vectors only
>Any directional vectors (axis etc.) needs to be **normalized** before being fed into GLM.

Let's try to perform some transformation operations on a cube. We will try to shift it right by 0.5 units, scale it to 0.3x the original size, and rotate it 45 degrees about the z-axis.

````cpp
trans = glm::translate(trans, glm::vec3(0.5f, 0.0f, 0.0f));
trans = glm::scale(trans, glm::vec3(0.3f, 0.3f, 0.3f));
trans = glm::rotate(trans, 45 * RADIANS, glm::vec3(0.0f, 0.0f, 1.0f));
````

Add a uniform variable `transform` to the vertex shader to apply the transformations:

````
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aColor;
uniform mat4 transform;
out vec4 color;

void main()
{
    gl_Position = transform * vec4(aPos, 1.0);
    color = vec4(aColor, 1.0f);
}
````

Query the ID of the uniform variable and set it to the transformation matrix:

````cpp
GLuint uniformID = glGetUniformLocation(shaderProgram.ID, "transform");
shaderProgram.Activate();
glUniformMatrix4fv(uniformID, 1, GL_FALSE, glm::value_ptr(trans));
````

We use `glUniformMatrix4fv` to tell OpenGL that the input data type will be a square matrix vector of size 4x4. It accepts the following arguments:

````cpp
glUniformMatrix4fv(matrix, num_matrices, transpose?, type);
````

Since GLM stores matrices as a different type from OpenGL, we have to use the `glm::value_ptr` function to first convert the matrix into a compatible format.