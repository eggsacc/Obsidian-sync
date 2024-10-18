
Created <font style="color:tomato; font-family:Consolas;">14-10-2024</font>

Tags: 

Related: [[Optimizing sine LUT]] 

****

## What is a LUT?

A lookup table (LUT) is a pre-computed array of values that model some function. Instead of computing the values during runtime, the program can just retrieve the values from the LUT. This can lead to significant improvements in performance in programs where computationally expensive calculations are performed repeatedly, such as computing trigonometric functions, logarithms, inverse square roots etc.

However, a LUT of acceptable precision will also take up significantly more memory; hence, it is a trade-off between speed and memory usage.


## Sine LUT

To compute a sine lookup table, we can increment the value of $x$ in uniform discrete steps and store the corresponding values of $\sin(x)$ at each step.

For example, we can create a C program which calculates and stores a list of values of $\sin(x)$ every 2 degrees:

````c
#include <stdio.h>
#include <math.h>

int main()
{
    for(int i = 0; i < 360; i+=2)
    {
        float angle_radians = i / 360.0 * 2 * M_PI;
        float value = sin(angle_radians);
        printf("%f", value);
        printf(", ");
    }
    return 0;
}
````

The output is a list of 180 elements:

````c
int sineLUT[180] = {0.000000, 0.034899, 0.069756, 0.104528, 0.139173, 0.173648, 0.207912, 0.241922, 0.275637, 0.309017, 0.342020, 0.374607, 0.406737, 0.438371, 0.469472, 0.500000, 0.529919, 0.559193, 0.587785, 0.615662, 0.642788, 0.669131, 0.694658, 0.719340, 0.743145, 0.766044, 0.788011, 0.809017, 0.829038, 0.848048, 0.866025, 0.882948, 0.898794, 0.913545, 0.927184, 0.939693, 0.951057, 0.961262, 0.970296, 0.978148, 0.984808, 0.990268, 0.994522, 0.997564, 0.999391, 1.000000, 0.999391, 0.997564, 0.994522, 0.990268, 0.984808, 0.978148, 0.970296, 0.961262, 0.951056, 0.939693, 0.927184, 0.913545, 0.898794, 0.882948, 0.866025, 0.848048, 0.829038, 0.809017, 0.788011, 0.766044, 0.743145, 0.719340, 0.694658, 0.669131, 0.642788, 0.615661, 0.587785, 0.559193, 0.529919, 0.500000, 0.469472, 0.438371, 0.406737, 0.374606, 0.342020, 0.309017, 0.275637, 0.241922, 0.207912, 0.173648, 0.139173, 0.104528, 0.069756, 0.034899, -0.000000, -0.034899, -0.069756, -0.104528, -0.139173, -0.173648, -0.207912, -0.241922, -0.275637, -0.309017, -0.342020, -0.374607, -0.406737, -0.438371, -0.469472, -0.500000, -0.529919, -0.559193, -0.587785, -0.615661, -0.642788, -0.669131, -0.694658, -0.719340, -0.743145, -0.766045, -0.788011, -0.809017, -0.829038, -0.848048, -0.866025, -0.882948, -0.898794, -0.913545, -0.927184, -0.939693, -0.951056, -0.961262, -0.970296, -0.978148, -0.984808, -0.990268, -0.994522, -0.997564, -0.999391, -1.000000, -0.999391, -0.997564, -0.994522, -0.990268, -0.984808, -0.978148, -0.970296, -0.961262, -0.951056, -0.939693, -0.927184, -0.913545, -0.898794, -0.882948, -0.866025, -0.848048, -0.829038, -0.809017, -0.788011, -0.766044, -0.743145, -0.719340, -0.694658, -0.669131, -0.642788, -0.615661, -0.587785, -0.559193, -0.529919, -0.500000, -0.469471, -0.438371, -0.406736, -0.374607, -0.342020, -0.309017, -0.275638, -0.241922, -0.207912, -0.173648, -0.139173, -0.104529, -0.069756, -0.034900}
````

Now if we want to find the value of $\sin(60)$, for example, we can simply just look at the value stored at position $60\div{2}=30$ since the angle increment between each value is 2 degrees. 

````c
float sin60 = sineLUT[30];
````

However, since the values are only stored in increments of 2 degrees, what happens if we want to look at some value between 2 steps? For example, how do we retrieve the value of $\sin(63.5)$ from the table?


## Interpolation

#### Linear interpolation

Linear interpolation is basically drawing a straight line between adjacent points and finding an intermediate value on the line.

Using the LUT above, let's say we want to find the value of $\sin(75.5)$. Since the values in the LUT are in increments of 2 degrees, the closest 2 value from 75.5 degrees are 74 and 76 degrees respectively. 

The values corresponding to 74 and 76 degrees stored in the LUT are `sineLUT[37] = 0.961262` and `sineLUT[38] = 0.970296`.

Hence, we can interpolate a value for 75.5 degrees based on the gradient of the line between the 2 closest points.


![[Screenshot 2024-10-14 154403.png]]

Since it is a straight line, $\Delta y$ (change in value of $\sin x$) is proportional to $\Delta x$ (change in degrees). Hence, the interpolated value can be calculated by:

$$
y_{1}+\frac{x-x_{1}}{x_{2}-x_{1}}\times (y_{2}-y_{1})
$$

For the example above, the interpolated value will be 

$$
0.961262+\frac{75.5-74}{76-74}\times (0.970296-0.961262)=0.9680375
$$

The actual value of $\sin(75.5)$ is `0.9681476`, which differs by `0.00011` from value calculated using the lookup table.

The lookup table we produced earlier is just an example and has very poor precision. In the next article, we will discuss some methods to optimize a sine lookup table for precision, speed and also memory efficiency using the properties of sine and some clever mathematical tricks.