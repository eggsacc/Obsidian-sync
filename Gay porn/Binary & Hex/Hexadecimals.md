
Created <font style="color:tomato; font-family:Consolas;">08-10-2024</font>

Tags: #hexadecimal

Related: [[1) The binary system]]

****

## Base 16 system

Instead of just using the numbers 0 through 9 to represent a digit in the decimal system, or 1s and 0s like the binary system, hexadecimals use both numbers and alphabets to produce a base-16 system. Hexadecimals use the alphabets A-F as an extension to the numbers 0-9, forming the 16 digit sequence:

$$
0\, 1\, 2\, 3\, 4\, 5\, 6\, 7\, 8\, 9\, A\, B\, C\, D\, E\, F
$$

For example, the hexadecimal `0xC2F` will be $12\times 16^2+2\times 16^1+15\times 16^0 = 3119$.

Hexadecimals are more commonly referred to as just hex. In code, we tell the computer a value we entered is in hexadecimal using the prefix `0x`. E.g. `a = 0x3C`.


## Decimal to hex

To convert a decimal number to hex, we use the same division method as binary but instead of dividing by 2, we divide by 16.

Assuming the number 753,

| Number | Quotient | Remainder |
| ------ | -------- | --------- |
| 753    | 47       | 1         |
| 47     | 2        | 15        |
| 2      | 0        | 2         |

Now we map the remainder to the 16 hexadecimal digits and arrange them from bottom to top. Hence, 753 is `0x2F1` in hex.


## Binary to hex

Since hexadecimals are base 16, a single digit of hexadecimal is equivalent to 4 bits. We can split a binary number into groups of 4 bits and find the hexadecimal equivalent of each group before piecing them together.

Let's say we have an 8-bit number `0b10011111`. First, we can split it into `0b1001` and `0b1111`. Then, we find the hexadecimal equivalent for each group: `0b1001` is 9 in decimal, which is also 9 in hex; `0b1111` is 15 in decimal, which corresponds to `F` in hex. Hence, `0b10011111` is `0x9F` in hex.




