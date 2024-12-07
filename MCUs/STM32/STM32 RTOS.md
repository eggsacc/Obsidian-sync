
Created <font style="color:tomato; font-family:Consolas;">30-11-2024</font>

Tags: #stm32

Related: NIL

****

## RTOS

A Real Timer Operating System (RTOS) is used to simulate software architecture for applications that require multitasking, priority, or real-time tasks. It is essentially an automatic event scheduler for the STM32 to perform different tasks with pseudo parallelism. 


## Setup

A default RTOS library is provided in STM32CubeIDE. `V1` of RTOS is compatible with most microcontrollers, so we will stick to that. Also ensure that the `USE_PREEMPTION` feature is enabled in the configuration.


![[Pasted image 20241130181942.png#center]]


We can then pre-define the number of tasks under the section `Tasks and Queues`. To add a new task, click `Add` and choose a name for the task.


![[Pasted image 20241130182301.png#center]]


Save the project to generate the relevant code. If the project raises an error regarding `systick should not be enabled`, change the `Timebase Source` under `SYS` to `Timer1`. 


## Code

>[!tip] Threads
>A thread in RTOS is an execution unit in the process that has its own program counter, stack, as well as a set of registers. Or simply put, a thread is a unit process with its own context. Each unique task in RTOS is also called a thread.

### 1) Thread ID
RTOS first defines an unique thread ID for each thread / task we created earlier on. Any operations we perform relating to these threads will require the thread ID.

````c
osThreadId defaultTaskHandle;
osThreadId Task2Handle;
````

### 2) Entry function declaration
Next, the entry points of each thread is defined. These are the functions we will be writing our task-related code in.

````c
void StartDefaultTask(void const * argument);
void Task2_init(void const * argument);
````

### 3) Create threads
Next, in the `main` function, each thread is initialized in the following format:

````c
osThreadDef(task_name, entry_point, priority_level, instance, stack_size);
````

The `osThreadCreate` function then creates the threads.

````c
/* definition and creation of default task */
osThreadDef(defaultTask, StartDefaultTask, osPriorityNormal, 0, 128);
defaultTaskHandle = osThreadCreate(osThread(defaultTask), NULL);
/* definition and creation of Task2 */
osThreadDef(Task2, Task2_init, osPriorityNormal, 0, 128);
Task2Handle = osThreadCreate(osThread(Task2), NULL);
````

### 4) Start OS
Lastly, `osKernelStart();` will be called and the scheduler takes over the execution of the program. The main code will cease execution after this line since everything is taken over by the RTOS scheduler.

````c
/* Start scheduler */
osKernelStart();

/* We should never get here as control is now taken by the scheduler */
````


## Writing the tasks

If we scroll down, we will find the entry point functions for each task.

![[Pasted image 20241130185502.png]]

This is where we insert the code for each task. In the code above, a simple function to toggle the on-board LED on and off every 200 milliseconds is written to test the RTOS. Note that `osDelay()` is used instead of the conventional `HAL_Delay()` since RTOS is handling all the timing features.

### Test example

Let's try to multi-task using RTOS. For a simple example, we will blink an LED every 200 milliseconds while reading an encoder output via I2C and streaming the data over USB to a serial COM port.

````c
/* USER CODE END Header_StartDefaultTask */
void StartDefaultTask(void const * argument)
{
	/* init code for USB_DEVICE */
	MX_USB_DEVICE_Init();
	/* USER CODE BEGIN 5 */
	/* Infinite loop */
	for(;;)
	{
		HAL_GPIO_TogglePin(GPIOC, GPIO_PIN_13);
		osDelay(200);
	}
	/* USER CODE END 5 */
}

/* USER CODE BEGIN Header_Task2_init */
/**
* @brief Function implementing the Task2 thread.
* @param argument: Not used
* @retval None
*/
/* USER CODE END Header_Task2_init */
void Task2_init(void const * argument)
{
	/* USER CODE BEGIN Task2_init */
	/* Infinite loop */
	for(;;)
	{
		char buff[50];
		AS5600_ReadAngle(&sensor);
		int angle = 1000 * sensor.total_angle_rad;
		sprintf(buff, "angle: %d\n", angle);
		CDC_Transmit_FS((uint8_t *)buff, strlen(buff));
		osDelay(10);
	}
	/* USER CODE END Task2_init */
}
````

