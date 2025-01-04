
Created <font style="color:tomato; font-family:Consolas;">12-12-2024</font>

Tags: 

Related: NIL

Sources: [Learn OpenGL - Camera](https://learnopengl.com/Getting-started/Camera)

****

## Camera view

The camera view transforms all the vertex coordinates in a scene to how it would be perceived by a camera. A camera is defined by its position in world space, the direction it is looking at, a vector pointing to its right and a vector pointing to its upwards position. Basically, we are attaching a coordinate frame to the camera.



![[Pasted image 20241212203400.png#invert|center]]


### Camera position

The camera position is simply the x, y, and z coordinates of the camera in world space.

````cpp
glm::vec3 cameraPos = glm::vec3(x, y, z);
````

### Camera direction

For simplicity sake, the camera will be set to point towards the world origin, or $(0,0,0)$. Recall that  $\vec{AB}=\vec{OB}-\vec{OA}$, and we want to find the vector pointing from the camera to the origin, hence:

````cpp
glm::vec3 target = glm::vec3(0.0f, 0.0f, 0.0f);
glm::vec3 cameraDirection = glm::normalize(cameraPos - target);
````

### Camera right axis

To find the right axis of the camera, we can use a cross product between the camera view direction and the world `z-axis`.

````cpp
glm::vec3 Up = glm::vec3(0.0f, 1.0f, 0.0f);
glm::vec3 cameraRight = glm::normalize(glm::cross(Up, cameraDirection));
````

### Camera up axis

Now that we have established both the camera direction axis and camera right axis, the camera up axis can be found by taking the cross product of these 2 axes.

````cpp
glm::vec3 cameraUp = glm::normalize(glm::cross(cameraRight, cameraDirection));
````



