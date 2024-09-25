
Created <font style="color:tomato; font-family:Consolas;">25-09-2024</font>

Tags: #arduino #registers #embeded

****

## Arduino registers

Registers are memory locations built into the the processor itself, making it highly accessible but also limited in memory size. Hardware registers are special registers that control certain hardware features using it's memory space.

For example, storing the bits `00010011` in the `PORTB` hardware register will make pins D8, 9, 12 output 5V.

## Why use registers?

Arduino functions like `digitalWrite(), analogWrite()` etc. are all pre-built abstraction functions in the Arduino core library which make the code easier to read and maintain. Whenever these functions are called, many more lines of code are executed in the background, which costs extra memory and time. 

Low-level manipulation gives us full control and direct access to the hardware registers, which is much faster and is very useful in some scenarios, such as switching a pin between `HIGH` and `LOW` at a very high frequency for ultrasound generation.

## Arduino ports & mapping

>*Register locations are hardware specific; the following is only for the ATmega328p processor found on Arduino UNO & Arduino Nano.*

The ATmega328p processor has 3 digital IO ports `PORTB, PORTC, PORTD` which maps to it's physical pins. A port is simply a group of pins, and each port has it's corresponding port registers which we could use to manipulate the associate hardware pins. Below is the mapping between the port and the actual pin.


![[Pasted image 20240925174137.png|center]]


## Port registers

The pins associated with each port is controlled by 3 registers:

*Where x = B, C or D;* 
1) `DDRx` : set pin mode, or the data direction. `1` for input, `0` for output.
2) `PORTx` : set the state of the pin. `1` for `HIGH`, `0` for `LOW`.
3) `PINx` : input buffer register to read pin state (read only).

For example: 
````c++
DDRB = 00001101; // in port B, set pins D8, 10, 11 to OUTPUT.
PORTD = 10010110; // in port D, set pins D1, 2, 4, 7 to HIGH.
byte pin_states = PINC; // reads the state of all pins in port C.
````





