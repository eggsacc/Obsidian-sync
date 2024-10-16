
Created <font style="color:tomato; font-family:Consolas;">28-09-2024</font>

Tags: #binary

Related: NIL

****

## The decimal system

The number system all too familiar to us humans is known as the decimal, or base-10 system. It is a base-10 system since there are 10 distinct numbers (0-9) that could be used to represent a digit. 

In a number with multiple digits, the rightmost (last) digit represents $10^0$, and each digit towards the left represented a higher power of 10, e.g. 2nd digit from right represents $10^2$ , 3rd digit represents $10^3$ etc.

| Digits     |       3       |       2       |       8       |       7       |
| ---------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Represents | $\times 10^3$ | $\times 10^2$ | $\times 10^1$ | $\times 10^0$ |

So for the number 3287, for example, it could be thought of as 

$3\times 10^3+2\times 10^2+8\times 10^1+7\times 10^0 = 3287$ .  


## The binary system

Instead of having 10 numbers that can represent each digit, we now only have 2; either a 0 or 1. This makes binary numbers a base-2 numeral system, where each digit represents an increasing power of 2 instead.

To interpret a binary number as decimal, we can just sum up each digit multiplied by it's corresponding power of 2:

| Digits     | 1            | 0            | 0            | 1            | 1            | 1            | 0            | 1            |
| ---------- | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ |
| Represents | $\times 2^7$ | $\times 2^6$ | $\times 2^5$ | $\times 2^4$ | $\times 2^3$ | $\times 2^2$ | $\times 2^1$ | $\times 2^0$ |

The binary number `10011101` is hence 

$1\times 2^7+0\times 2^6+0\times 2^5+1\times 2^4+1\times 2^3+1\times 2^2+0\times 2^1+1\times 2^0 = 157$  

in decimal.


## Decimal to binary

How do we convert a decimal number to it's binary form?

First, we keep dividing the number by 2 and recording the remainder until it reaches 0:

| Number      | Quotient | Remainder |
| ----------- | -------- | --------- |
| $137\div 2$ | 68       | 1         |
| $68\div 2$  | 34       | 0         |
| $34\div 2$  | 17       | 0         |
| $17\div 2$  | 8        | 1         |
| $8\div 2$   | 4        | 0         |
| $4\div 2$   | 2        | 0         |
| $2\div 2$   | 1        | 0         |
| $1\div 2$   | 0        | 1         |

Now we arrange the remainders from bottom to top to obtain the binary representation. Hence, the number 137 is `10010001`.

For binary numbers, bit 0 (right-most bit) is called the Least-Significant Bit (LSB) and the left-most bit is called the Most-Significant Bit (MSB).


## Binary in code

We cannot just write `a = 10100011` in code as it will be interpreted as the integer `10100011` instead. We can tell the compiler that it is a binary number by adding the prefix `0b` or `B`, so that `a = 0b10100011` is compiled properly as a binary number.

