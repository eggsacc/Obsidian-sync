
Created <font style="color:tomato; font-family:Consolas;">04-11-2024</font>

Tags: #algo

Related: NIL

****

## Finding substrings

The Boyer-Moore Bad & Good suffix algorithms are used to locate substrings in a string. It is able to comb through large amounts of text and locate specific strings of letters with high efficiency.

The difference between the Bad and Good Suffix algorithms is the way they deal with mismatches in the string sequence. In this article, we will explore only the Bad suffix rule.


## Bad character heuristic

The goal of the algorithm is to locate a specific pattern of text, `P`, in a much larger set of characters `S`. The algorithm starts by aligning the `P` to the start of `S` and comparing each character in reverse order, until a mismatch is found.

In the example below, `S = "ABECFDEAABACC"` and `P = "ABAC"`. We maintain a `S` and `P` pointer pointing to the respective indexes being compared.


![[Pasted image 20241105113443.png#invert]]


Starting from the last character in `P` (index 3), we can see that the first mismatch occurs at index 2. `E` is identified as a **bad character** since it does not coincide with the pattern.

Each time we come across a bad character, we attempt to locate it within the pattern `P`. In the case above, `E` does not exist in the pattern `P`, so we shift the pattern beyond the bad character and begin matching in reverse order again.


![[Pasted image 20241105113824.png#invert]]


Notice that the `S pointer` shifted from 4 positions from index 2 to index 6, which is equivalent to the length of the pattern `P`, and the `P pointer` is reset to the last index (index 4) of `P`. Hence, we can say that:

>[!tip] Observation 1
>When a mismatch occurs and the bad character **does not exist** in the `P`, shift `S pointer` by a distance equivalent to the **length of the pattern**, and reset `P pointer` to the pattern endpoint.

Now, the bad character is `B`. However, `B` is present in the pattern `P` this time, so we shift `P` such that the `B` in the both `P & S` coincide.


![[Pasted image 20241105122656.png#invert]]


`S pointer` is shifted by 2 positions from index 6 to index 8, which corresponds to the distance between `B` and the last index in `P`  $(3-1=2)$. Hence, our second conclusion is that:

>[!tip] Observation 2
>When a mismatch occurs and the bad character is present in `P`, shift `S pointer` **by the distance between the character and endpoint**, before resetting `P pointer` to the pattern endpoint.

Note that if there are multiple characters corresponding to the bad character in `P`, we pick the one closest to the endpoint.

The process is repeated until all the characters in `P & S` match.


![[Pasted image 20241105123032.png#invert]]

The algorithm can simply return `S pointer`, which points to the position in `S` where the pattern is found.


## Bad characters table

The Boyer Moore implements some sort of lookup table that precomputes the positions of all unique characters in the pattern `P` to improve performance.

The table stores the number of positions to shift the `S pointer` for each bad character present in the pattern. For the above example where `P = "ABAC"`, the bad characters table would be something like:

| Bad character | Shift size |
| ------------- | ---------- |
| A             | 1          |
| B             | 2          |
| C             | 0          |
| *             | 4          |

The `*` represents all other characters that are not present in the pattern, where we will shift the pointer by the length of the pattern as explained in observation 1.

Also, as mentioned earlier,  we will only take the closest index to the endpoint for characters that exist multiple times, like `A` in the above example which appears twice in the pattern.

To compute a bad characters table in C, we create an `int` array with 256 elements, which represents the 8-bit encoding of ASCII characters. We can typecast any character to an integer to extract it's encoding value, which we will use to index the table.

The entire table is initialized by filling it with the length of pattern.

````c
int* badCharactersTable(char* pattern)
{
	int* temp = (int*)malloc(256 * sizeof(int));
	
	int length = 0;
	while(pattern[length] != '\0')
	{
		length++;
	}
	
	for(int i = 0; i < 256; i++)
	{
		temp[i] = length;
	}
	
	for(int i = 0; i < length; i++)
	{
		temp[(int)pattern[i]] = length - i - 1;
	}
	
	return temp;
}
````


## Search algorithm in code

````c
int BMsearch(char* s, char* p)
{
    int* bad_chars = badCharactersTable(p);

    int len_s = 0;
    int len_p = 0;

    // find str lengths
    while(s[len_s] != '\0')
    {
        len_s++;
    }

    while(p[len_p] != '\0')
    {
        len_p++;
    }

    int s_ptr = len_p - 1;

    while(s_ptr <= len_s)
    {
        int p_ptr = len_p - 1;

        while(p_ptr > 0 && s[s_ptr] == p[p_ptr])
        {
            s_ptr--;
            p_ptr--;
        }

        if(p_ptr == 0)
        {
            //s_ptr += len_p;
            printf("Found at index[%d]\n", s_ptr);
            s_ptr += len_p;
            continue;
        }

        int distance = bad_chars[(int)s[s_ptr]];
        s_ptr += distance;
    }
    
    free(bad_chars);
    return -1;
}
````