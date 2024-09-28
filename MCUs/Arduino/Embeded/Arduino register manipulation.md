
Created <font style="color:tomato; font-family:Consolas;">25-09-2024</font>

Tags: #arduino #registers #embeded

Related: NIL

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


## Speed & memory comparisons

Let's compare the speed of execution and memory usage between using registers and built-in Arduino functions.

#### `digitalWrite()` vs `PORTx`

To compare the difference in speed, we want to switch a pin to `HIGH`, then immediately to `LOW` and compare the response time.

Using Arduino functions:
````c++
void setup(){
    // put your code here, to run once:
    pinMode(10, OUTPUT);
	
	// turn pin on then off immediately.
    digitalWrite(10, HIGH);
    digitalWrite(10, LOW);
}
````

Using registers:
````c++
void setup(){
	// put your code here, to run once:
	// configure port B pins to be outputs
	DDRB = 0;
	
	// turn pin 10 on, then off
	PORTB = 0b00000100;
	PORTB = 0b00000000;
}
````

We can then compare the output signal using an oscilloscope:

![[Pasted image 20240926095600.png]]

The time highlighted in red is the delay between the pin switching between `HIGH` and `LOW`. Using the built-in function `digitalWrite()`, there was a delay of 4.32 **micro**seconds compared to the 68 **nano**seconds when using direct register manipulation. Directly writing to the hardware register is 69x faster than using pre-built functions!!!

#### Program size comparison

The code below initializes pins `D8-13` and continuously turns them `HIGH` and `LOW`.

````c++
int pins[] = {8, 9, 10 , 11, 12, 13};

void setup() {
  // put your setup code here, to run once:
  for(int i = 0; i < 6; i++)
  {
    pinMode(pins[i], OUTPUT);
  }
}

void loop() {
  // put your main code here, to run repeatedly:
  for(int i = 0; i < 6; i++)
  {
    digitalWrite(pins[i], HIGH);
  }
  delay(1000);

  for(int i = 0; i < 6; i++)
  {
    digitalWrite(pins[i], LOW);
  }
  delay(1000);
}
````

The same code, but using registers:

````c++
void setup() {
  // put your setup code here, to run once:
  DDRB = 0b00111111;
}

void loop() {
  // put your main code here, to run repeatedly:
  PORTB = 0b00111111;
  delay(1000);

  PORTB = 0b00000000;
  delay(1000);
}
````

Comparing program size:

````c++
************** Standard code **************
Sketch uses 1038 bytes (3%) of program storage space
Global variables use 21 bytes (1%) of dynamic memory

************** Registers **************
Sketch uses 646 bytes (2%) of program storage space
Global variables use 9 bytes (0%) of dynamic memory
````

Using registers decreased the program size by 38%, and also uses no dynamic memory.


## Effectively utilizing registers

Directly controlling hardware using registers is complicated and may sometimes produce unexpected results. Some pins on the microcontroller has dedicated functions, such as serial communication, hardware interrupts, I2C connections etc. If we accidentally configure these pins to do something they're not supposed to do, the entire program will break down.

For example, on the Arduino UNO, pin `D0` is reserved for serial in (Rx) and pin `D1` is reserved for serial out (Tx). If we accidentally reconfigured these pins, the Arduino will no longer establish serial communication with out computer. *Always read the datasheet before using register manipulation!!!*

#### Using macros

Macros could be used to make the code more readable.

````c++
// the ## is a token-pasting operator, which concatenates 'DDR' and the 'port' arguement.

#define SET_ALL_OUTPUTS(port) (DDR ## port = 0xFF)
#define SET_ALL_INPUTS(port) (DDR ## port = 0x00)
#define SET_PORT_MODE(port, ddr_cfg) (DDR ## port = ddr_cfg)
#define PORT_TOGGLE(port) ((PORT ## port) = ~(PORT ## port))
#define PORT_WRITE(port, data) (PORT ## port= data)
#define PORT_READ(port) PIN ## port

void setup() {
  // set all port B to output, and all C to inputs:
  SET_ALL_OUTPUTS(B);
  SET_ALL_INPUTS(C);
  
  // set port B to 0x03, or 00000011 (first 2 pins OUTPUT)
  SET_PORT_MODE(B, 0x03);

  // toggle port B state
  PORT_TOGGLE(B);

  // write port C individual pin states
  PORT_WRITE(C, 0x3e);

  // read port D pins state
  uint8_t state = PORT_READ(D)
}
````

Discrete pin manipulation macros:

````c++
// Addresses pins individually without affecting other irrelevant pins

#define SET_PIN_MODE_OUTPUT(port, pin) (DDR ## port |= (1 << pin))

#define SET_PIN_MODE_INPUT(port, pin) ((DDR ## port) &= ~(1<<pin))

#define TOGGLE_PIN_MODE(port, pin) (PORT ## port ^= (1<<pin))

#define PIN_READ(port, pin) (PIN ## port & (1 << pin))

void setup() {

  // set port B pin2 to output, and port C pin 3 to input:

  SET_PIN_MODE_OUTPUT(B, 2);

  SET_PIN_MODE_OUTPUT(C, 3);

  // set port B to 0x03, or 0b00000011 (first 2 pins OUTPUT)

  SET_PORT_MODE(B, 0x03);

  // reverse (toggle) port B pin 4 state

  TOGGLE_PIN_MODE(B, 4);

  // read portD pin 3 state (returns 0 if state = 0)

  uint8_t state = PIN_READ(D, 3)
}
````


## Summary

This article only covers the very basics of registers. For almost all programming needs, direct register manipulation is NOT required, and it is encourages to use the abstraction functions provided by Arduino to maintain code readability.

To conclude, register manipulation is only required for very specific use cases or for embedded programming. Though it seems to bring some huge benefits in terms of speed and functionality, the issue of it being difficult to read and maintain, together with it's potential of producing more problems, outweighs the pros marginally.



