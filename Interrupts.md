
Created <font style="color:tomato; font-family:Consolas;">17-02-2025</font>

Tags: 

Related: NIL

Sources: NIL

****

## What are interrupts?

Interrupts are sub-routines in a computer program that is ran only when very specific events occurs. The processor pauses the execution of the main code to handle these interrupts.

### Priority level

Interrupts/exceptions in a system can be assigned a "priority level" to signify it's relative importance so the processor can decide which interrupts to handle first if multiple instances are triggered simultaneously.

A priority level is usually 8-bit in size, and the **lower the number, the higher the priority.** For example, the `reset` interrupt in an STM32 has the smallest priority number of -3, and hence has the highest priority.

### Non-maskable interrupts (NMI)

Non-maskable interrupts are interrupts that are so crucial that the processor have no option of ignoring them. These interrupts also has the highest priority out of any interrupt.

An example of a NMI is the watchdog timer present on all processors. These timers generate a reset signal when it detects a system malfunction. This interrupt signal is critical for system performance and cannot be ignored by the processor.

### Interrupt vector table

An interrupt vector table (IVT) is a table stored in the processor memory that maps a list of enabled interrupt requests to the memory address of the interrupt handler function. This allows the processor to quickly check and pull up the function assigned to handle a interrupt.

## What happens during a interrupt event?

When an interrupt is asserted, the processor pushes (saves) the current process and context on the stack and switches to running the exception handler at the execution priority of the interrupt.

If the asserted interrupt is of a lower or equal priority than the one currently being executed, it is pended to run.

Otherwise, the vector address of the interrupt is loaded from the interrupt vector table, and the event handler function starts to execute in handler mode.

After all pending interrupts are being handled, the main program context is popped from the stack to resume execution.

### Nesting and pre-emption

What happens if multiple interrupts are asserted before the previous even finishes executing? In such a case, processors that support **nested interrupts** will pause the currently active interrupt, push the context onto the stack, and start handling the exception with a higher priority. The higher-priority interrupts are said to **preempt** the lower ones.

### Tail chaining

Normally when the processor wants to pause the current program to execute a interrupt handler of a higher priority, it pushes the current (main) code context onto the stack, and pops it after the exception handler completes to resume execution from where it left off. This is also true for events where multiple interrupts are pended to be executed.

However, with tail-chaining, the processor needs not fully save the context of the current program on the stack before proceeding with a higher priority task. This skips the push & pop procedures, saving time and improving efficiency.

### Exception states

- **Inactive:** Not pending nor active.
- **Pending:** Interrupt has been fired but not yet handled.
- **Active:** Exception handler has begun execution.
- **Active and pending:** The exception is currently being serviced, but there is another exception from the same source.


## Interrupt service routine (ISR)

The interrupt service routine is the function that is executed when an interrupt is asserted. 

Since the execution of an ISR pauses the main program, there should be minimal code and **strictly no delays/pauses** within an ISR. A poorly implemented ISR will cause timing issues in a program and even cause other functions to miss critical timings. An ISR should also clear the interrupt flag once it's done executing.

## External interrupts (EXTI)

An external interrupt consists of an edge detector that could be set to trigger the interrupt at a rising and/or falling edge (configurable interrupt event). These interrupts are also independently maskable, meaning the processor can choose to enable or disable them.




