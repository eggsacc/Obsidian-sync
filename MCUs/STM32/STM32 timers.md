
Created <font style="color:tomato; font-family:Consolas;">08-10-2024</font>

Tags: #stm32 #timer #embeded 

Related: NIL

****

# Types of timers

The STM32F103C8T6 has 7 hardware timers: 

- 1x 16-bit Advanced-control timer, TIM1
- 3x 16-bit General purpose timers, TIM2-4
- 2x Watchdog timers
- 1x SysTick timer


## Advanced-control timer

TIM1 on the STM32F103C8T6 is classified as an advanced control timer since it has extra functionalities that the general purpose timers TIM2-TIM4 does not.

The table below summarizes all the differences between TIM1 and TIM2-4, courtesy of ChatGPT. 

![[Pasted image 20241008205611.png]]


## PWM modes

There are 2 PWM modes available in the STM32cubeIDE: 

1) Mode 1: Active signal when timer is counting up, inactive after exceeding `CCRx`.
2) Mode 2: Inactive signal when counting up, active after exceeding `CCRx`.


## Registers

All the configurable timer registers are stored in a timer structure.

![[Pasted image 20241009151331.png]]

The highlighted registers are the ones we are interested in.




