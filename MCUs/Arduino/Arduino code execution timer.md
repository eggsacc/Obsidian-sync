
Created <font style="color:tomato; font-family:Consolas;">18-10-2024</font>

Tags: #arduino #registers 

Related: NIL

****

## Timer functions

> This will only work for Arduinos using the ATmega328p chip, and only for very fast processes taking <4ms.

````c
void initTimer()
{
	TCCR1A = 0;
	TCCR1B = bit(CS10);
	TCNT1 = 0;
}

void resetTimer()
{
	TCNT1 = 0;
}

float getTime()
{
	unsigned int cycles = TCNT1;
	float _ms = (float)(cycles - 1) / 16;
	return _ms;
}
````


#### Sample usage:

````c
void setup()
{
	Serial.begin(115200);
	initTimer();
}

void loop()
{
	resetTimer();
	// perform some task
	Serial.println(getTime());
}
````