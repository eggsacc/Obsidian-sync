
Created <font style="color:tomato; font-family:Consolas;">08-10-2024</font>

Tags: #binary #1complement #2complement

Related: NIL

****

## Reserving MSB

*How do we represent negative numbers in binary?*

The simplest and oldest way of doing so is to reserve the most significant bit (MSB) as a sign indicator. The number was negative if the left-most bit was a `1`, and positive if it was a `0`.

For example, `00000101` is 5 and `10000101` will be -5.

Reserving one bit as a sign indicator changes the range of values the binary sequence could represent. For example, an unsigned 8-bit number can have values from 0-255. However, if we reserved the MSB as a signed bit, the signed 8-bit number now has a range of $\pm 127$ instead.

While this method of representing signed numbers seems straight forward, it causes binary addition and subtraction to become incredibly cumbersome since it does not follow basic arithmetic properties.

Also, the number `0` can be ambiguously represented as either `0000` or `1000`.


## One's complement

A better method of representing negative numbers is using the One's complement, where the negative number is simply an inverse of their positive counterparts.

For example, 107 is `01101011` while -107 is `10010100` (all bits inversed).

One of the major advantages of using one's complement is the ease of subtraction.

$a-b$ is the same as $a+(-b)$ . Using One's complement, we can simply invert all the bits in `b` and add it `a`, following the usual rules of binary addition. However, we need to add `1` to the result to convert it into before interpreting it as a decimal number.

One issue with the One's complement is that the value `0` still has an ambiguous representation of either `0000 or `1111`.


## Two's complement

The Two's complement is the most popular way of representing an storing negative number today. Almost all code compilers, computers and microprocessors use Two's complement.

Two's complement also inverts all the bits, but there's an extra step of adding a `1` to the result. 

Consider the number 97 `(01100001)`. To find the Two's complement of 97, we have to 

1) Invert all the bits: `01100001` becomes `10011110`.
2) Add `1`: `1011110 + 1 = 1011111`

Hence, -97 is `1011111` in Two's complement.

The main advantage of Two's complement is that it eliminates the ambiguous representation of 0: +0 is `0000`, while -0  is also `1111 + 1 = 0000` now.

The basic rules of binary addition still applies, and the result could be translated to decimal without any extra steps.



