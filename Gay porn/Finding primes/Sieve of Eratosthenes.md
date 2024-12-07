
Created <font style="color:tomato; font-family:Consolas;">11-11-2024</font>

Tags: 

Related: NIL

****

## Finding prime numbers

The Sieve of Eratosthenes (SoE) is an ancient method used to find prime numbers less than or equal to $n$. It goes through each number starting from 2 and "marks" any number that is divisible by it. In doing so, all the numbers that are "unmarked" at the end will be primes.

For example, to find all the primes below $n=50$, we can construct a table of all the numbers:

![[Pasted image 20241112190914.png#invert]]
Now, starting from the number 2, we "mark" all the numbers that is divisible by it and also greater than it's square.

![[Pasted image 20241112191144.png#invert]]

Moving on to the next "unmarked" number, which is 3, we repeat the same process:

![[Pasted image 20241112191423.png#invert]]

The next "unmarked" number is 5:

![[Pasted image 20241112191558.png#invert]]

Repeat the process until the square of the next "unmarked" number exceeds our range. In this case, we stop at 11 since $11^2>50$.

At this point, the table looks like this:

![[Pasted image 20241112192121.png#invert]]
All of the "unmarked" numbers left are primes.


## Implementation in code

Below is a simple implementation of the Sieve of Eratosthenes in C.

````c
int* SoE(int n, int* returnSize)
{
	// no primes under 2
    if(n < 2)
    {
        *returnSize = 0;
        return NULL;
    }
    // initialise array of size n+1 since array starts from 0
    bool nums_arr[n + 1];
    // 0 and 1 are not included in primes
    nums_arr[0] = false;
    nums_arr[1] = false;
    // initialise all values to true
    for(int i = 2; i <= n; i++)
    {
        nums_arr[i] = true;
    }
	
    int marked = 0;
	
    for(int p = 2; p * p <= n; p++)
    {
	    // if number is unchanged (true), it is prime
        if(nums_arr[p])
        {
	        // update all numbers >= square of current number
	        // and are multiples of the number
            for(int i = p * p; i <= n; i += p)
            {
                if(nums_arr[i])
                {
                    nums_arr[i] = false;
                    // track how many marked (non-prime)
                    marked++;
                }
                
            }
        }
    }
    
    *returnSize = n - marked - 1;
    
    int* primes_arr = (int*)malloc((n-marked-1) * sizeof(int));
    int index = 0;
    for(int j = 2; j <= n; j++)
    {
        if(nums_arr[j])
        {
            primes_arr[index] = j;
            index++;
        }
    }
    
    return primes_arr;
}

int main()
{
	int num_primes = 0;
	// get primes under 1000
	int* primes = SoE(1000, &num_primes);
}
````


## Time / space complexity

The SoE has a time complexity of $O(N*\log(\log(N)))$ and a space complexity of $O(N)$.