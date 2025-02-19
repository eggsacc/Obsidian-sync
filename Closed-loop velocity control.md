
Created <font style="color:tomato; font-family:Consolas;">19-02-2025</font>

Tags: #foc

Related: NIL

Sources: NIL

****

## Principle

The principle behind closed-loop velocity control is relatively straight forward. Similar to closed-loop position control, the torque output (`Uq`) of the motor is adjusted to be proportional to it's real-time angular velocity, which is obtained through the encoder.

$$
\text{Uq}=\text{P}\times \omega 
$$

### Obtaining velocity from encoder

To read the motor velocity using an encoder, we can divide the angle difference by the time elapsed between each function call to estimate the velocity.

````c
float AS5600_GetVelocity(AS5600* dev)
{
	uint32_t now_us = micros();
	
	/* Save previous angle */
	float prev_angle = dev->total_angle_rad;
	
	/* Calculate time delta */
	float time_delta_s = (now_us - dev->prev_time_us) * 0.000001f;
	time_delta_s = (time_delta_s > 0.1) ? 0.0001f : time_delta_s;
	
	/* Calculate angle delta */
	float angle_delta = AS5600_ReadAngle(dev) - prev_angle;
	
	/* Update sensor timestamp */
	dev->prev_time_us = now_us;
	
	return angle_delta / time_delta_s;
}
````


However, if we tried to just use this velocity reading to control the motor, we realize that while the motor does move at the requested velocity, it also produces significant vibrations and electrical noise. This is due to noise in the velocity readings being amplified by the proportional constant in the equation, causing the motor to constantly correct for minute changes in the velocity reading.


## Low-pass filtering (LPF)

Low-pass filters are useful in removing and suppressing noise in electrical signals. 

![[Pasted image 20250219172357.png#invert]]

The top half of the image illustrates what the raw angle readings may look like, and the bottom half represents the same signal after filtering.

In audio signal processing, the LPF removes and attenuates frequencies above a certain threshold. In our case, the LPF will remove and filter out the high-frequency noise in the velocity feedback signal.

### How LPF works

We will be using a model of the LPF called a exponential moving average (EMA) filter:

$$
V_{filtered}(t)=\alpha \cdot V_{raw}(t) \ +(1-\alpha) \cdot V_{filtered}(t-1)
$$

Where $\alpha$ is in the range `[0, 1]` and controls how much filtering is being applied.

$V_{raw}(t)$ could be seen as the current un-filtered velocity value, while $V_{filtered}(t-1)$ represents the previous velocity value. The filter basically adds a **portion** $(\alpha)$ of the current actual velocity together with a portion $(1-\alpha)$ of the previously filtered velocity value to produce the filtered value. 

Hence, there is a trade-off between **signal smoothness and response time** as we adjust the value of $\alpha$: a larger $\alpha$ value will produce a smoother signal, while a smaller $\alpha$ will produce a rougher signal which is more responsive to real-time changes.

### Implementation in code

````c

````
