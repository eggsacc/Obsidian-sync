
Created <font style="color:tomato; font-family:Consolas;">14-10-2024</font>

Tags: 

Related: [[Sine lookup table (LUT)]]

****

## Issues

In the previously created sine LUT, there are multiple issues that should be optimized to make it useable.

#### 1) Undesirable data type

The LUT stores an array of floating point values between -1 and 1. Although a `float` type variable occupies the same amount of memory as an `int`, it takes the processor way more time to perform calculations on `float` variables due to it's more complex encoding.

For context, the time taken for an Arduino NANO to sum 64 `float` variables is 550.94 microseconds, while the time taken to sum a similar list of 64 `int` variables is only 60.25 microseconds.

Hence, we want our LUT to contain only integers.

#### 2) Unnecessary repetition

The LUT stores a list of pre-computed values of $\sin x$ from $0-2\pi$ radians. However, we actually only need to compute the values for one quadrant ($0-\frac{\pi}{2}$ ) of $\sin x$ due to the symmetry and periodicity of the sine function.


![[Screenshot 2024-10-15 001038.png]]

#### 3) Low precision

The LUT only computes a value for $\sin x$ every 2 degrees, which leads to a very poor precision and rendering it not suitable for implementation in real life applications. We want a greater subdivision of the angles to create a more accurate LUT.


## Optimization

#### 1) Scaling values to be useable as `int`

The current LUT stores a bunch of `float` values since the range of sine is only from -1 to 1. We can scale the sine function such that it's range covers the entire range of 16-bit signed integers: from -32768 to +32767. We now have a table of 16-bit integer values, which uses half the memory as floating point numbers (2 bytes for 16-bit `int`, 4 bytes for `float`).

![[Screenshot 2024-10-15 115246 1.png]]

To compute the new `int` values, we can use $32767\times \sin x$.

#### 2) Limiting quadrant

By calculating the sine values for just one quadrant ($0-\frac{\pi}{2}$), we can reconstruct the sine values for the entire range of angles by the properties of a sine wave.

- In **Quadrant 2** ($\frac{\pi}{2}-\pi$), $\sin \theta=\sin\left( \frac{\pi}{2}-\theta \right)$ since sine if symmetrical about $\frac{\pi}{2}$.  So, we can reflect the values from the first quadrant.

- In **Quadrant 3** ($\pi-\frac{3\pi}{2}$), sine is negative, and $\sin (\theta)=-\sin \left( \theta-\frac{\pi}{2} \right)$.

- In **Quadrant 4** ($\frac{3\pi}{2}-2\pi$), sine is again negative, and $\sin (\theta)=-\sin (2\pi -\theta)$


![[low-res2.png]]

Hence, we only need to compute the values for the first quadrant, from $0-\frac{\pi}{2}$.

#### 3) Increasing resolution

Since we only need to compute the values for a single quadrant, we can greatly increase the resolution (number of angle subdivisions between $0-\frac{\pi}{2}$) without increasing LUT size.

For most applications involving microcontrollers with limited memory, a subdivision of 64 to 128 steps per quadrant (precision of 0.0123 - 0.0245 rad) is sufficient. We will be using a subdivision of 64 steps.


## Generating new LUT

Below is the code to generate a LUT for angles between $0-\frac{\pi}{2}$, with 64 subdivisions, scaled up to a range of 32767:

````c
#include <stdio.h>
#include <math.h>  

int main()
{
	int resolution = 64;
	double step = M_PI / 128.0;
	int top = 32767;
	
	printf("int sineLUT[] = {");
	
	for(int i = 0; i < resolution; i++)
	{
		int value = (int)(sin(i * step) * top);
		(i == resolution - 1)?(printf("%d", value)):(printf("%d, ", value));
	}
	
	printf("};");
	return 0;
}
````

Output:

````c
int sineLUT[] = {0, 804, 1607, 2410, 3211, 4011, 4807, 5601, 6392, 7179, 7961, 8739, 9511, 10278, 11038, 11792, 12539, 13278, 14009, 14732, 15446, 16150, 16845, 17530, 18204, 18867, 19519, 20159, 20787, 21402, 22004, 22594, 23169, 23731, 24278, 24811, 25329, 25831, 26318, 26789, 27244, 27683, 28105, 28510, 28897, 29268, 29621, 29955, 30272, 30571, 30851, 31113, 31356, 31580, 31785, 31970, 32137, 32284, 32412, 32520, 32609, 32678, 32727, 32757};
````


## Using the LUT

Now that we have the optimized sine lookup table, we need to implement algorithms to extract the relevant value and perform linear interpolation.

We need to write a custom `_sin()` function that take some input angle in radians, find 2 corresponding values from the LUT, and perform linear interpolation to approximate the value.

#### 1) Processing input angle value

To find the index of the element that corresponds to our input angle, we need to map the angle from between $0-2\pi$ to between $0-256$ (64 values per quadrant x 4 quadrants = 256).

$$
index = \frac{angle}{2\pi}\times 256
$$

The index will be some floating point value between 0-256. However, we need to extract the whole number part for array indexing and also extract the decimal part for linear interpolation. Since we are trying to avoid using floating numbers as much as possible, a clever trick can be used here.

#### 2) Fixed point arithmetic

We can use fixed point arithmetic to store the fractional part of the index as an integer by scaling the number up by a certain amount.

For example, we can scale the decimal `0.918342` by a factor of `10000` which gives us `9183.42` and discard the remaining decimal places, resulting in an integer `9183`. This integer is effectively storing the original number to a "fixed precision" of only 4 decimal places.

This integer is then used in calculations as per normal, and all we have to do is divide the final result by the scaling factor `(10000)` to obtain the accurate output.

However, since we are dealing with microcontroller which works based on the binary system, the scaling factor is preferably chosen to be a power of 2. Multiplication by $2^n$ is extremely fast and efficient since all the processor has to do is just left-shift the bits `n` times.

In our case, we will multiply the floating point index by `256` to preserve 8-bits of decimal part. We can then use the bitwise operator `&` to extract the top 8 bits which represents the whole number, and the bottom 8 bits which represents the decimal part.

````c
float _sin(float angle)
{
		unsigned int index = (unsigned int)(angle / _2_PI * 65536.0f);
		int frac = index & 0xFF;
		index = (index >> 8) & 0xFF;
}
````


#### 3) Array indexing

We need to find the 2 values closest to the requested angle for linear interpolation. The 2 values are `t1` and `t2`. We check the quadrant the requested angle lies in to determine which values to take based on the properties of the sine function explained earlier.

````c
float _sin(float angle)
{
		unsigned int index = (unsigned int)(angle / _2_PI * 65536.0f);
		int frac = index & 0xFF;
		index = (index >> 8) & 0xFF;
		
		int32_t t1, t2;
		
		if(index < 64){
			t1 = (int32_t)sineLUT[i];
			t2 = (int32_t)sineLUT[i+1];
		}
		else if(index < 128){
			t1 = (int32_t)sineLUT[128 - i];
			t2 = (int32_t)sineLUT[127 - i];
		}
		else if(index < 192){
			t1 = -(int32_t)sineLUT[i - 128];
			t2 = -(int32_t)sineLUT[i - 127];
		}
		else{
			t1 = -(int32_t)sineLUT[256 - i];
			t2 = -(int32_t)sineLUT[255 - i];
		}
}
````

#### 4) Linear interpolation

After we have `t1` and `t2`, we can perform linear interpolation, divide the fractional part by 256 (previous scaling factor for fixed point arithmetic) and the final value by 32768 (scaling factor of sine).

````c
float _sin(float angle)
{
		unsigned int index = (unsigned int)(angle / _2_PI * 65536.0f);
		int frac = index & 0xFF;
		index = (index >> 8) & 0xFF;
		
		int32_t t1, t2;
		
		if(index < 64){
			t1 = (int32_t)sineLUT[i];
			t2 = (int32_t)sineLUT[i+1];
		}
		else if(index < 128){
			t1 = (int32_t)sineLUT[128 - i];
			t2 = (int32_t)sineLUT[127 - i];
		}
		else if(index < 192){
			t1 = -(int32_t)sineLUT[i - 128];
			t2 = -(int32_t)sineLUT[i - 127];
		}
		else{
			t1 = -(int32_t)sineLUT[256 - i];
			t2 = -(int32_t)sineLUT[255 - i];
		}
		
	return (1.0f / 32768.0f) * (t1 + (((t2 - t1) * frac) >> 8));
}
````

>We bit-shift the value to the right by 8 bits to effectively divide it by 256. While doing it this way is much faster, there is a small loss in precision from doing so since some bits are truncated from the shifting operation

## Full code

````c
// pre-computed values to speed up code
double _2_PI = 6.28318530718;
double DOWNSCALER = 0.00003051757;

int sineLUT[] = {0, 804, 1607, 2410, 3211, 4011, 4807, 5601, 6392, 7179, 7961, 8739, 9511, 10278, 11038, 11792, 12539, 13278, 14009, 14732, 15446, 16150, 16845, 17530, 18204, 18867, 19519, 20159, 20787, 21402, 22004, 22594, 23169, 23731, 24278, 24811, 25329, 25831, 26318, 26789, 27244, 27683, 28105, 28510, 28897, 29268, 29621, 29955, 30272, 30571, 30851, 31113, 31356, 31580, 31785, 31970, 32137, 32284, 32412, 32520, 32609, 32678, 32727, 32757};

float _sin(float angle)
{
		unsigned int index = (unsigned int)(angle / _2_PI * 65536.0f);
		int frac = index & 0xFF;
		index = (index >> 8) & 0xFF;
		
		int32_t t1, t2;
		
		if(index < 64){
			t1 = (int32_t)sineLUT[i];
			t2 = (int32_t)sineLUT[i+1];
		}
		else if(index < 128){
			t1 = (int32_t)sineLUT[128 - i];
			t2 = (int32_t)sineLUT[127 - i];
		}
		else if(index < 192){
			t1 = -(int32_t)sineLUT[i - 128];
			t2 = -(int32_t)sineLUT[i - 127];
		}
		else{
			t1 = -(int32_t)sineLUT[256 - i];
			t2 = -(int32_t)sineLUT[255 - i];
		}
		
	return DOWNSCALER * (t1 + (((t2 - t1) * frac) >> 8));
}
````
