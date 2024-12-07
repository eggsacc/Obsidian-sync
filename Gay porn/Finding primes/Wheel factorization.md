
Created <font style="color:tomato; font-family:Consolas;">14-11-2024</font>

Tags: 

Related: NIL

****

## Principle

Wheel factorization is used to filter out a set of candidate numbers to be checked for primality, reducing the total checks required when finding primes up to a limit $n$.

>[!info] Wikipedia
>**Wheel factorization** is a method for generating a sequence of natural numbers by repeated additions, as determined by a number of the first few primes, so that the generated numbers are coprime with these primes, by construction.

The **basis** of a factorization wheel is a small set of prime numbers, such as $\{ 2, 3, 5\}$. The **wheel size** is the lowest common multiple (LCM) of the basis; in this case, the LCM is $2\times 3\times 5=30$.

First, we find all the co-prime numbers less than the wheel size. To do so, we simply check is each number is divisible by any of the primes in basis; if it is indivisible by any, the number is co-prime. We get:

$$
co-prime=\{1, 7, 11, 13, 17, 19, 23, 29\}
$$

If we arrange these numbers in a wheel where each round is equal to the wheel size, we get something like:

![[Pasted image 20241115203634.png#invert]]


Notice that in every cycle of the wheel, where each cycle is equal to the wheel size (30), the prime candidates are of the form:

$$
30k + co-prime[i]
$$

I.e. if we want all the prime candidates from 30-60, the only numbers we have to check is:

$$
\{31,37, 41, 43, 47, 49, 53, 59\}
$$

This improves efficiency since we only have to check 8 out of the 30 numbers for primality. In the above case, we can check the primality by dividing the candidate numbers by the set of co-prime numbers found earlier. Since 49 is a multiple of 7, it is eliminated from the candidates list, leaving us with 

$$
\{31, 37, 41, 43, 47, 53, 59\}
$$

as all the primes between 30-60.


## Code implementation

````c
void simpleWheelSieve(int limit)
{
    int candidates[200];
    int index = 0;
	
    // find all the co-prime numbers to 2, 3, 5
    for(int i = 2; i <= limit; i++)
    {
        if(i % 2 != 0 && i % 3 != 0 && i % 5 != 0)
        {
            candidates[index] = i;
            index++;
        }
    }
    
    // for each candidate, check if it is a
    // multiple of previous primes.
    // mark as 0 if found to be a multiple (non prime)
    for(int k = 0; k < index; k++)
    {
        if(candidates[k] != 0)
        {
            int prime = candidates[k];
            for(int j = k + 1; j < index; j++)
            {
                if(candidates[j] % prime == 0)
                {
                    candidates[j] = 0;
                }
            }
        }
    }
	
    // print all the primes (those not marked 0)
    for(int p = 0; p < index; p++)
    {
        if(candidates[p] != 0){
            printf("%d\n", candidates[p]);
        }  
    }
}
````