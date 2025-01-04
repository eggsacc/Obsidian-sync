
Created <font style="color:tomato; font-family:Consolas;">07-12-2024</font>

Tags: 

Related: NIL

Sources: NIL

****

## Object depth

The z buffer, or depth buffer, is automatically created by OpenGL to store the depth information of objects. The depth of any vertex is stored as the `z` component, and OpenGL can automatically compare this `z` value to determine if a vertex is covering another, so it can decide to discard the vertex or overwrite it.

By default, depth testing is disabled in OpenGL. To enable it, use:

````cpp
glEnable(GL_DEPTH_TEST);
````

A single buffer is used for all the frames, so we have to clear it each frame to avoid preserving false data.

````cpp
glClear(GL_DEPTH_BUFFER_BIT);
````
