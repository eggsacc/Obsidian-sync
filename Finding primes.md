
Created <font style="color:tomato; font-family:Consolas;">04-03-2025</font>

Tags: 

Related: NIL

Sources: NIL

****

## Some facts

- Instead of testing up to the number `n`, we can just test to the square root of `n`.
- Instead of testing every number up to the square root of `n`, we can jump in steps of 2 since all the even numbers are non-prime.
- We can also skip all multiples of 3 by first testing if the number is divisible by 3. I.e. `n % 3 == 0`
- Since all multiples of 2 and 3 are skipped, we can just check all multiples of 6 +- 1. I.e. `n % 6k±1 == 0`

## Optimized code

In code:

````python
def is_prime(n):
	# Base cases: 2, 3 = prime, 0, 1 = non-prime
    if(n <= 1):
        return False
    if(n == 2 or n == 3):
        return True
    
	# Check divisible by 2 or 3
    if(n % 2 == 0 or n % 3 == 0):
        return False
    
	# Start number at 6 - 1 = 5
    i = 5
    
	# Repeat until square root n 
    while i*i < n:
	    
	    # Increment i by 6 every iteration and check i & i + 2
        if(n % i == 0 or n % (i + 2) == 0):
            return False
        i += 6
    
    return True
````


## Generating prime numbers

To generate a list of primes under a specific number, we can just go through each odd number and check if it is divisible by any previous primes. If this number is in-divisible by any of the previously found primes, it is also a prime.

````python
def generate_prime(n):
    primes = [2, 3]
	
    if(n <= 1):
        return []
    if(n == 2):
        reutn [2]
    if(n == 3):
        return [2, 3]
	
    for i in range(5, n + 1, 2):
        divisible = False
        for p in primes:
            if(i % p == 0):
                divisible = True
        if(not divisible):
            primes.append(i)
    return primes
````