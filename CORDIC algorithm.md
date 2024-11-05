
Created <font style="color:tomato; font-family:Consolas;">01-11-2024</font>

Tags: 

Related: NIL

****

## Heading 

The CORDIC (Coordinate Rotation Digital Computer) algorithm is the most widely used method to estimate the values of $\sin(x)$ and $\cos(x)$ in digital devices, like calculators, microcontrollers etc. 

## Principle: convergent sequences

Imagine a person standing at at point `A` trying to get to point `B` which is only 1 meter away. However, each step he take can only cover **half of the remaining distance** between him and point `B`. Will he ever reach point `B`?

![[Series-ezgif.com-video-to-gif-converter.gif|700]]

Our intuition tells us that he will probably reach point `B` eventually. However, the unfortunate reality is that no matter how many steps he took, he will only get closer and closer to point `B` but never reach the point itself. The only way he could theoretically reach point `B` was if he walked an infinite number of steps, where he will be so close to the point that he is basically standing on the point itself.

The total distance travelled by the man could be represented as the sum of a series:

$$
\sum_{n=0}^{\infty}\left( \frac{1}{2} \right)^{n}=\frac{1}{2}+\frac{1}{4}+\frac{1}{8}+\frac{1}{16}+\dots+\frac{1}{2^n} \approx 1 
$$