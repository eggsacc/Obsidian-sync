
Created <font style="color:tomato; font-family:Consolas;">01-12-2024</font>

Tags: 

Related: NIL

****

## OpenGL

GLFW is a lightweight library that allows developers to create, render and manage windows in the OpenGL context. Below are the steps to setup a simple window using the relevant functions.

### 1) Init, window hint

Initializes GLFW and provides GLFW with information such as the version of OpenGL we are working on, the type of functions to include etc. The `CORE` profile holds all the modern OpenGL functions, whereas the `COMPAT` profile includes legacy functions **and** modern ones.

````cpp
/* Init glfw */
glfwInit();

/* 
 * glfw hints:
 * major version, minor version, core profile (modern functions)
 */
glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
````

### 2) Create window

All windows are of the type `GLFWwindow*`. It is good practice to also insert a simple check to terminate the program if window creation was somehow unsuccessful.

````cpp
/*
 * Create window. Windows are of type GLFWwindow*
 * glfwCreateWindow(width, height, name, fullscreen?, idk);
 */
GLFWwindow* window = glfwCreateWindow(800, 800, "test", NULL, NULL);

/* Window initialization error handling */
if (window == NULL)
{
	std::cout << "Window creation failed.\n" << std::endl;
	glfwTerminate();
	return -1;
}
````

### 3) Create window context

Each window has an associated OpenGL context.

````cpp
/* Create window context */
glfwMakeContextCurrent(window);
````

### 4)  Task while window is open

This is where we write the code telling GLFW what to do while the window is not closed / exited. If we do not add this loop, the window will be created and destroyed immediately. `glfwPollEvents()` tells GLFW to react to / process any inputs and changes to the window. If this function was not present, the window will remain static.

````cpp
/* Stuff to do while window is open */
while (!glfwWindowShouldClose(window))
{
	/* Process polled events */
	glfwPollEvents();
}
````

### 5) Termination

At the end of the code, remember to destroy all the windows and terminate GLFW.

````cpp
/* Destroy window before termination */
glfwDestroyWindow(window);

glfwTerminate();
````