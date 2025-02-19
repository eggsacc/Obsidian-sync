
Created <font style="color:tomato; font-family:Consolas;">01-12-2024</font>

Tags: 

Related: NIL

****

## Raster vs Vector graphics

Raster and vector graphics are the 2 fundamental implementations of computer graphics. These 2 types of computer graphics represent images using different techniques, with the former using pixels and the latter using vector equations.

### Raster graphics

In raster graphics, each image is represented by a collection of square pixels arranged in a 2D matrix. Each square in the image corresponds to a pixel on our screen.

![[Pasted image 20241201105132.png]]
Each pixel is simply a number that represents its color. For a **true color** photo, each pixel stores the 8-bit color value of each red, green and blue channel in a 24-bit value. This allows for over 16 million colors per pixel, resulting in very color accurate images.

For **black and white** images, each pixel is only a 1-bit number indicating if it is black or white. For **greyscale images**, each pixel is extended to an 8-bit number that represents its level of brightness between black and white.

### Vector graphics

In vector graphics, a set of mathematical statements govern the position and behavior of a bunch of mathematical curves that forms the image. Since each vector graphic is saved as a sequence of vector statements, these images are resolution-independent, meaning that they could be enlarged indefinitely without looking jagged and blurry like rasterized images.


![[Pasted image 20241201110419.png#invert]]
