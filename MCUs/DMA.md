
Created <font style="color:tomato; font-family:Consolas;">30-11-2024</font>

Tags: #stm32 

Related: NIL

****

## Direct Memory Access (DMA)

Direct Memory Access, or DMA, is performed by a dedicated DMA controller on computer architectures to offload the task of managing memory transfers from the CPU.

On a typical system without a DMA controller, the CPU has to fetch instructions from the flash memory as well as move data to and from peripherals to memory.


![[Pasted image 20241130153051.png#invert]]

When the peripherals all try to constantly stream data to the processor, the CPU receives an insane amount of interrupts for data transfers and has to juggle everything by itself. This significantly bottlenecks the number of other events the CPU can handle per unit time since the majority of it's cycles are wasted on managing data transfer.


![[Pasted image 20241130154809.png#invert]]


With the addition of a DMA controller, the task of moving data to and from the peripherals into memory is offloaded from the CPU. Whenever the peripherals want to stream data to the processor, it creates a request to the DMA controller which then puts the data received directly into the memory without CPU intervention. This way, peripherals can exchange data with the processor while the CPU performs other calculations and tasks. The DMA controller only tells the CPU if each data transaction is successful or not.

DMA controllers manage 3 types of data exchange:

- Peripheral to memory
- Memory to peripheral
- Peripheral to peripheral
- Memory to memory (copying blocks of data in RAM)
## In context of STM32F103


![[Pasted image 20241130155738.png#invert]]

The STm32F103C8T6 microcontroller has 2 DMA controllers which offers a total of 12 channels, 7 for `DMA1` and 5 for `DMA2`. Each channel is dedicated to managing memory access requests from one or more peripherals.

A DMA request may pause the CPU access to the system bus for some bus cycles if both the CPU and DMA are targeting the same destination. 

Each channel also has a programmable priority level. It could be set in software to *very high, high, medium or low*, or set by hardware (peripheral 1 always has priority over peripheral 2 etc.).

### DMA transfer operations

Each DMA transfer consists of 3 operations:

- Loading of data from peripheral / memory location addressed by an internal memory register. The start address is programmed in the `DMA_CPARx` or `DMA_CMARx` register.

- Storage of data to peripheral / memory location addressed by an internal memory register. The start address is in the same registers as above.

- Post-decrementing of the `DMA_CNDTRx` register, which contains the number of remaining transactions to perform.

