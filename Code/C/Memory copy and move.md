
Created <font style="color:tomato; font-family:Consolas;">09-12-2024</font>

Tags: 

Related: NIL

Sources: NIL

****

## `memcpy`

The `memcpy` function in C copies a specific number of bytes of memory from a source address to a destination address.

````c
memcpy(void* dest, void* src, size_t size);
````

This function should not be used to copy items between memory addresses that are known to overlap, for example copying all the elements in an array to another index within the same array.

Copying to overlapping memory locations causes undefined behavior; we do now know how the compiler will optimize/handle the data in the overlapping regions.

## `memmove`

The `memmove` function is similar to `memcpy`, but it first copies the data into a temporary buffer before moving it to the new address.

````c
memmove(void* dest, void* src, size_t size);
````

This function is specifically designed to handle memory overlap issues properly, so it can be used to do things like shifting a bunch of elements around within an array.

`memmove` is slightly slower than `memcpy` but provides much more memory safety as it will not corrupt data overlapping memory locations. Hence, `memcpy` should only be used if we explicitly know that the destination and source addresses do not overlap.