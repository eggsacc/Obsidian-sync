
Created <font style="color:tomato; font-family:Consolas;">28-09-2024</font>

Tags: #arduino #embeded

Related: [[Arduino register manipulation]]

****

# The most optimized blink sketch 

The blink sketch is like the "Hello, world!" for microcontrollers. It is so simple and straight forward that even people with zero coding background knows what it does....right?

Let's try to rewrite the blink sketch without using ANY Arduino library functions, using a "bare-metal" approach to program it instead.

> The final code will ***only work*** for the **ATmega328p chip** (Arduino UNO, Arduino NANO ONLY) !!!!

This is the blink sketch for reference. Note the program size in the comments below the code.

````c++
void setup(){
	// put your code here, to run once:
	pinMode(13, OUTPUT);
}

void loop(){
	// put your code here, to run repeatedly:
	digitalWrite(13, HIGH);
	delay(1000);
	digitalWrite(13, LOW);
	delay(1000);
}

/*
Sketch uses 924 bytes (3%) of program storage space.
Global variables use 9 bytes (0%) of dynamic memory.
*/
````

## 1) Removing `digitalWrite()`&`pinMode()`

`pinMode()` and `digitalWrite()` can be replaced by [[Arduino register manipulation]].

We know from the datasheet that pin D13 on the Arduino UNO corresponds to pin PB5 on the ATmega328p chip. Hence we know that it is in port B and is controlled by bit 5 in the port B registers. 

We can set bit 5 in the `DDRx` (Data Direction Register) register to `1` to configure pin D13 as an output.

Setting bit 5 in the `PORTB` register to `1` pulls pin D13 `HIGH` and `0` pulls it `LOW`.

````c++
void setup(){
	// put your code here, to run once:
	DDRB |= (1<<5);
}

void loop(){
	// put your code here, to run repeatedly:
	PORTB |= (1<<5);
	delay(1000);
	PORTB ^= (1<<5);
	delay(1000);
}

/*
Sketch uses 648 bytes (2%) of program storage space.
Global variables use 9 bytes (0%) of dynamic memory.
*/
````

Upload the code and the on-board LED blinks as expected. We have successfully reduced the program size by $924-648=276$ bytes, and made the code slightly unreadable.

## 2) Removing `delay()`

In the Arduino core library, the `delay()` function basically makes the microcontroller do nothing for a specified amount of time. We can emulate this function by making the Arduino count from 0 to a really large number, and just do nothing while it counts.

We use the loop `for(long i = 0; i < 2000000; i++){ }` to iterate from 0 - 2million without doing anything in between.

````c++
void setup(){
	// put your code here, to run once:
	DDRB |= (1<<5);
}

void loop(){
	// put your code here, to run repeatedly:
	PORTB |= (1<<5);
	for(long i = 0; i < 2000000; i++) {
		// do nothing;
	}
	
	PORTB ^= (1<<5);
	for(long i = 0; i < 2000000; i++) {
		// do nothing;
	}
}

/*
Sketch uses 458 bytes (1%) of program storage space
Global variables use 9 bytes (0%) of dynamic memory.
*/
````

The sketch size is even smaller at only 458 bytes now. However, when we upload the code, the Arduino LED just stays lit the entire time without blinking. Why so?

Turns out, the compiler is actually quite smart; when looking through the code, it notices that nothing happens in the `for()` loop and automatically skips it. So in our code, the LED is being turned on immediately turned off since the `for()` loop is ignored.

To fix this issue, we can put something useless in the loop instead of leaving it empty. In this case, we can just repeatedly set the pin to HIGH by duplicating the `PORTB |= (1<<5);` inside the loop.

````c++
void setup(){
	// put your code here, to run once:
	DDRB |= (1<<5);
}

void loop(){
	// put your code here, to run repeatedly:
	PORTB |= (1<<5);
	for(long i = 0; i < 2000000; i++) {
		PORTB |= (1<<5);
	}
	
	PORTB ^= (1<<5);
	for(long i = 0; i < 2000000; i++) {
		PORTB = 0;
	}
}

/*
Sketch uses 458 bytes (1%) of program storage space
Global variables use 9 bytes (0%) of dynamic memory.
*/
````

Now when we re-upload the code, the LED blinks as expected. However, do note that the delay between blinks is not exactly 1 second now since the 2 million we chose to count up to is just an arbitrarily large number to cause a significant delay. 

## 3) Replacing macros

The DDRB, PORTB etc. used in our code are still not "low level" enough; if we hover our cursor over `DDRB` or `PORTB`, we see that is is actually a macro defined in the Arduino core library:


![[Pasted image 20240928154446.png|center]]


We can avoid using these macros by using pointers instead. Pointers allow us to directly manipulate the bits stored in a memory address.

We know from the ATmega328p datasheet that the memory address of the `DDRB` register is `0x24`, and the `PORTB` register is located at `0x25`.

Hence, we can configure the registers by referencing their memory locations:

1) Setup a pointer to point at the memory address
2) De-reference the address to manipulate the bits stored there

````c++
void setup(){
	// put your code here, to run once:
	byte* pGay = 0x24;
	*pGay = (1<<5);
}

void loop(){
	// put your code here, to run repeatedly:
	byte* pLesbian = 0x25;
	*pLesbian |= (1<<5);
	for(long i = 0; i < 2000000; i++) {
		*pLesbian = (1<<5);
	}
	
	*pLesbian ^= (1<<5);
	for(long i = 0; i < 2000000; i++) {
		*pLesbian = 0;
	}
}
````

I named the pointers something gay to prove that it is not used or pre-defined in any libraries. However, when we upload this code, the LED **does not blink**.

This is again due to the compiler optimizing part of the code out for us. In this case, the compiler sees that the pointer variable `*pGay` is not used anywhere in the code after it's been assigned a value in `*pGay = (1<<5);` and just automatically deletes it to save memory.

We can fix this issue by making the pointer variable `volatile`: this is a keyword that tells the compiler "just leave it here, my doings are beyond your understanding."

````c++
void setup(){
	// put your code here, to run once:
	volatile byte* pGay = 0x24;
	*pGay |= (1<<5);
}

void loop(){
	// put your code here, to run repeatedly:
	volatile byte* pLesbian = 0x25;
	*pLesbian |= (1<<5);
	for(long i = 0; i < 2000000; i++) {
		*pLesbian = (1<<5);
	}
	
	*pLesbian ^= (1<<5);
	for(long i = 0; i < 2000000; i++) {
		*pLesbian = 0;
	}
}
````

#### 3.1) Common practice in formatting code

Most embedded programmers have a specific way of writing code so other developers can have a easier time interpreting it. Instead of setting up a pointer variable before de-referencing it, we can write it in a single line of code:

````c++
void setup(){
	// put your code here, to run once:
	*((volatile byte*) 0x24) |= (1<<5);
}

void loop(){
	// put your code here, to run repeatedly:
	*((volatile byte*) 0x25) |= (1<<5);
	for(long i = 0; i < 2000000; i++) {
		*((volatile byte*) 0x25) |= (1<<5);
	}
	
	*((volatile byte*) 0x25) ^= (1<<5);
	for(long i = 0; i < 2000000; i++) {
		*((volatile byte*) 0x25) = 0;
	}
}
````

What we are doing here is typecasting `0x25` as a `volatile byte` pointer, and immediately de-referencing it with the `*` operator. 

While this may look even more confusing for us, other developers look at it and be like "oh so we're setting bit 5 in 0x24 to 1".

## 4) Removing `setup()`, `loop()`

Finally, the only 2 pre-defined Arduino functions left are the `void setup()` and `void loop()`. Arduino code is just highly abstracted C++, so everything still runs under the `int main()` function. 

The `void setup()` function abstracts a bunch of initialization and configuration stuff for the timers, interrupts etc. that we can totally just ignore if we are not using timer-related Arduino functions like `delay()`, `millis()` etc. 

`void loop()` is even simpler; it is just an infinite `while(true)` loop within the `int main()` function.

The code will look something like this:

````c++
int main() {
	// runs once
	*((volatile byte*) 0x24) |= (1<<5);
	
	// infinite loop
	while(1) {
		*((volatile byte*) 0x25) |= (1<<5);
		for(long i = 0; i < 2000000; i++) {
			*((volatile byte*) 0x25) |= (1<<5);
		}
		
		*((volatile byte*) 0x25) ^= (1<<5);
		for(long i = 0; i < 2000000; i++) {
			*((volatile byte*) 0x25) = 0;
		}
	}
	return 0;
}
````

Maybe it doesn't bother you as much, but the repeated use of `*((volatile byte*) 0x25) |= (1<<5)` to fill in the `for` loops looks rather messy and unprofessional. Instead, we can make the processor do something equally useless that could be written in a much shorter form:  enabling or disabling the global interrupt flag, using the functions `cli()` or `sei()`.

>If you know what `cli()` or `sei()` is, your life is probably not that interesting

The final code looks like this:

````c++
int main() {
	// runs once
	*((volatile byte*) 0x24) |= (1<<5);
	
	// infinite loop
	while(1) {
		*((volatile byte*) 0x25) |= (1<<5);
		
		for(long i = 0; i < 2000000; i++) {
			cli();
			
		}
		
		*((volatile byte*) 0x25) ^= (1<<5);
		
		for(long i = 0; i < 2000000; i++) {
			cli();
			
		}
	}
	
	return 0;
}

/*
Sketch uses 182 bytes (0%) of program storage space.
Global variables use 0 bytes (0%) of dynamic memory.
*/
````

The original sketch used 924 bytes of storage, while our "bare-metal" program only takes up 182 bytes of memory. This is a **80% reduction in program size** while achieving the same outcome!! However, we have also inadvertently made our code about **8000% harder to understand**.

## Summary 

Just write the code like a normal person.