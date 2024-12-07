
Created <font style="color:tomato; font-family:Consolas;">27-09-2024</font>

Tags: #stm32 #pwm

****

# IOC config

Select the external crystal resonator as system clock.

![[Pasted image 20241008220222.png | center ]]

Under `CLOCK CONFIGURATION`, set the oscillator frequency to 8MHz, PLLMul to 9x for 72MHz, and check frequency of the timer clocks.

![[Pasted image 20241008220706.png | center]]

Choose timer and set clock source to internal clock. Set the appropriate channels to PWM generation.

![[Pasted image 20241008221150.png | center]]

Set the pre-scalar, counter period and enable auto-reload preload so the timer resets on overflow.

![[Pasted image 20241009092615.png | center]]

Set the PWM mode to mode 1, and optionally set the default duty cycle (usually not required).

![[Pasted image 20241009093255.png | center]]


## In code

We first have to start the PWM timer, then set the `CCRx` register in the `timx` struct to control the duty cycle.

````c
/* Assumming the use of Timer1 and pin attached to chanel 1*/
TIM1->CCR1 = 0;

HAL_TIM_PWM_Start(&htim1, TIM_CHANEL_1);

for(int i = 0; i < 255; i++)
{
	TIM1->CCR1 = i;
	HAL_Delay(50);
}
````

