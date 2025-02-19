
Created <font style="color:tomato; font-family:Consolas;">19-02-2025</font>

Tags: #foc

Related: NIL

Sources: NIL

****

## Principle

To make the motor always return to a set angle, we just have to apply a torque in the opposite direction of the angular offset, with the magnitude of the torque being proportional to the offset amount.

$$
Uq=P \times (\theta-\theta_{s})
$$

The value of the proportional constant $P$ will determine how reactive the motor is to disturbances: a larger $P$ value will produce a higher restoring torque for the same shaft angle offset.

The value of $P$ could be determined based on **when** we want the maximum restoring torque to be applied. For example, if we want the maximum restoring torque when the shaft is offset by 45° ($\frac{\pi}{4}$ rads):

$$
P= \text{max Uq}\ \div 45°   
$$

Where the `max Uq` value is half of the motor voltage limit.

### In code

````c
void CLPositionControl(BLDCMotor* motor, float target_pos)
{
	/* Check if sensor is attached to motor */
	if(motor->sensor == NULL)
	{
		return;
	}
	
	/* Electrical angle calculated based on sensor angle * pole pairs */
	motor->vars->shaft_angle = (float)(motor->sensor_dir * AS5600_ReadAngle(motor->sensor));
	/* KP sets max torque when shaft is 45deg (0.7854 rad) off from target pos */
	motor->dqVals->Uq = KP * (target_pos - (motor->sensor_dir * motor->vars->shaft_angle));
	
	SetTorque(motor);
}
````
