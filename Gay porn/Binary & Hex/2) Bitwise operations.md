
Created <font style="color:tomato; font-family:Consolas;">08-10-2024</font>

Tags: #binary 

Related: [[1) The binary system]]

****

# Working with binary

Instead of just performing basic arithmetic operations on binary numbers, we can manipulate binary numbers using a special set of bitwise operators.

#### 1) NOT ( ~ )

The NOT operator inverts all the bits, i.e. 0's become 1's, and vice versa.

**Example:** `~ 0b00010110 = 0b11101001`

**Use case:** Often used to convert and store signed numbers. Explained in [[3) Signed bits]].

#### 2) AND ( & )

The AND operator compares each bit between 2 binary numbers. If the bits are the same, the output bit follows both bits. If the bits are different, output bit is 0.

**Example:** `0b001011 & 0b111001 = 0b001001`

**Use case:** Used for bit-masking, which is to select a specific set of bits. For example, if we only want the last 4 bits in `0b10110011`, we can bit-mask it using `0b10110011 & 0b1111 = 0b0011`.

#### 3) OR ( | )

The OR operator compares each bit between 2 binary numbers, and outputs `1` if at least one of the bits are 1, else 0.

**Example:** `0b10010001 | 0b10000110 = 0b10010111`

**Use case:** Set specific bits to `1` without changing others. For example, if we want to set bit 3 in `0b11010000` to `1`, we can use `0b11010000 | 0b1000 = 0b11011000`.

#### 4) XOR ( ^ )

The XOR operator compares each bit between 2 binary numbers. It outputs `1` if the bits are different, and `0` if the bits are the same.

**Example:** `0b11001100 ^ 0b10101010 = 0b01100110`

**Use case:** Toggles a specific set of bits. For example, to toggle the last 3 bits in `0b11000011`, we can use `0b11000011 ^ 0b111 = 0b11000100`.

#### 5) Left / right shift operator ( << or >> )

The left/right shift operator shifts a set of bits by a specific number of positions. Right-shifting bits will cause overflowing bits to be deleted, while left shifting bits adds `0s` to the empty positions.

**Example:** 
1) Left-shifting `0b110` by `3`: `0b110 << 3 = 0b110000`
2) Right-shifting `0b1011101` by `2`: `0b1011101 >> 2 = 0b10111`.

**Use case:** Right-shifting bits by `n` positions is essentially dividing it by $2^n$, and left-shifting by `n` positions is multiplying it by $2^n$.

