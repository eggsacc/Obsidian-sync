
Created <font style="color:tomato; font-family:Consolas;">17-02-2025</font>

Tags: 

Related: NIL

Sources: NIL

****

## IEEE 754

The IEEE 754 convention is implemented to provide a universal representation for floating point numbers in computers.

## Representation

Floating point numbers are stored as binary in the following format:

![[Pasted image 20250217110157.png#invert]]

Converting a floating point number to it's binary representation consists of the below steps. We will be using the number `13.625` as example.
#### 1) Integer portion binary

Determine the binary representation of the integer portion of the number. For `13.625`, the integer part is 13, or `1101` in binary.

#### 2) Decimal portion binary

Next, we need to find the binary representation of the decimal part. For `13.625`, the decimal portion `0.625` could be represented exactly in binary, `.101`. To find the binary after the decimal point, the $n^{th}$ binary digit represents $\frac{1}{2^n}$. Hence, `0.625` is `.101` since $0.625=\frac{1}{2}+\frac{1}{2^3}$.

>[!tip] Lucky guess!
>Do note that the above case is a actually a rather rare and convenient event where the decimal part could be represented **exactly** within the finite precision of a 32-bit floating point number.
>
>For example, the decimal `0.1` is not fully representable in binary since it is a infinitely recurring binary sequence `0.0001100110011001100110011......`
>
>Hence, most decimal numbers are actually rounded to the nearest representable binary within the precision of a 23-bit mantissa. Taking `0.1` as example again, it is actually stored as `0.100000001490116119384765625` in computers due to the limited precision. 

#### 3) Normalize binary (mantissa)

Right now, the binary representation of `13.625` is `1101.101`. By the IEEE 754 standards, we have to normalize this binary into the format $1.xxxxxx \times 2^n$ . We also have to add `0s` behind the binary to fill up the entire 23-bit sequence. Hence, the normalized binary of `1101.101` will be:

$$
1.10110100000000000000000\times 2^3
$$

#### 4) Bias exponent

From the earlier step, we see that after normalizing the binary, the exponent (offset) is $2^3$. Instead of just converting 3 to binary an appending it to the exponent section of the final binary, we have to bias it by a fixed amount. 

For 32-bit floating point numbers, the bias amount if 127. For 64-bit double-precision floating point numbers, the bias is 1023.

Hence, the final exponent is $3+127=130$, which is `10000010` in binary.

>[!tip] Why bias?
>The exponent is biased since the sign is already represented in the MSB. Hence, the exponent is biased to effectively convert it from a `signed int8_t` into an `unsigned int8_t`. This is also why the bias amount is `127`: The range of a 8-bit signed integer is [-126, 127],  while the range of the unsigned counterpart is [1, 254], hence adding a bias of 127 shifts a signed int into the range of an unsigned int.
>
>Another important thing to note is that the range of a 8-bit integer is usually [0, 255], so why is it only [1, 254] here? This is due to the extreme values of `0` and `255` being reserved for special cases, explained in the last section.

#### 5) Sign

The sign bit is set to `1` if the number is negative, and `0` otherwise.

#### 6) Putting it together

The final IEEE-754 representation of the number `13.625` is hence:

$$
\text{sign + biased exponent + mantissa bits leading 1}


$$
$$
=0\ 10000010\ 10110100000000000000000
$$


## Special cases

The IEEE-754 convention identifies specific binaries as special cases.

| Type               | Sign bit (S) | Exponent (E) | Mantissa (M) | Significance      |
| ------------------ | ------------ | ------------ | ------------ | ----------------- |
| Zero               | `0` or `1`   | `00000000`   | All `0`      | `+0` or `-0`      |
| Normalized         | `0` or `1`   | `1` to `254` | Any non-zero | Regular numbers   |
| Denormalized       | `0` or `1`   | `00000000`   | Non-zero     | Very small number |
| Infinity           | `0` or `1`   | `11111111`   | All `0`      | `+∞` or `-∞`      |
| NaN (Not a Number) | `0` or `1`   | `11111111`   | Non-zero     | Undefined number  |

## Precision implications

AS explained earlier, some, if not most, decimal numbers could not be represented exactly due to the fixed precision of floating point numbers. This causes floating point operations to be somewhat imprecise to some extent in nature, and this is especially evident when dealing with decimals in code.

When we are trying to compare the result of some floating point operation to a pre-defined constant decimal, for example, we often obtain unexpected results:

````c
int main() {
    // Write C code here
    float a = 5;
    float b = 2.1;
    
    if(a - b == 2.9)
    {
        printf("True\n");
    }
    else
    {
        printf("False\n");
    }
    printf("%.10f", a - b);
}
````

Output:

````
False
2.9000000954
````

In this case, `2.1` could not be be represented exactly and has to be rounded, which causes the imprecision in the output result, and causing the comparison operation to fail.

However, if we change the numbers to one where the decimal portion could be represented exactly in binary, we get the expected output:

````c
int main() {
    // Write C code here
    float a = 5.625;
    float b = 2.5;
    
	if(a - b == 3.125)
    {
        printf("True\n");
    }
    else
    {
        printf("False\n");
    }
    printf("%.10f", a - b);
}
````

Output:

````
True
3.1250000000
````

