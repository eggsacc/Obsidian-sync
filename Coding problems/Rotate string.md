
Created <font style="color:tomato; font-family:Consolas;">03-11-2024</font>

Tags: 

Related: NIL

****

## Description

![[Pasted image 20241103200942.png]]


## Approach

### Auxiliary array

The string `s` of size `n` can be duplicated to create an auxiliary array of size `2n`. For example, if the string `s` is `"abcde"`, duplicating it will produce `"abcdeabcde"`. Now for any index `i`, we can just take the next `n` elements after it.

This solution is simple, but costs extra space to store the larger array.

### Circular array

Another approach is to treat the character array `s` as a circular array, meaning `s[i] = s[i % length]`. This approach does not cost any extra memory.

Another optimization we can add to the program is to only compare the elements in the `goal` array if the first character matches between `goal` and `s`.

````c
bool rotateString(char* s, char* goal) {

    int len_s = strlen(s);
    int len_goal = strlen(goal);

    // If the lengths are different, s can't be rotated to match goal
    if (len_s != len_goal) {
        return false;
    }

    // Try each position in s where s[i] matches goal[0]
    for (int i = 0; i < len_s; i++) {
        if (s[i] == goal[0]) {
            bool match = true;
            
            // Check if rotating s starting from i gives goal
            for (int j = 0; j < len_goal; j++) {
            
                // Use modulo to wrap around the end of s
                if (s[(i + j) % len_s] != goal[j]) {
                    match = false;
                    break;
                }
            }

            // If all characters matched, return true
            if (match) {
                return true;
            }
        }
    }

    // If no rotation matched goal, return false
    return false;
}
````

### Using built-in functions

The code could be written in 3 lines if we use the built-in `in` function in python which checks if a substring exists.

````python
def rotateString(s, goal) ->bool:
	aux_string = s + s
	return goal in aux_String
````

C also has a function to check for the presence of substrings, called `strstr()`. The `strstr()` in C and `in` method in Python are based on the Knuth–Morris–Pratt and Boyer-Moore Bad suffix algorithms respectively, which are highly optimized for searching substrings.
