
Created <font style="color:tomato; font-family:Consolas;">12-11-2024</font>

Tags: 

Related: NIL

****

## Issues with simple SoE

While a simple Sieve of Eratosthenes seems to work fine, there are a few potential problems when implementing it in programs:

1) Since we have to create an array equal to the size of $n$, the computer might run out of storage if $n$ was large.
2) The simple SoE is not cache friendly since the algorithm traverses the array without locality of reference.

One way of improving a simple SoE is by using a segmented sieve -- we divide $n$ into smaller, constant-sized segments and find the prime numbers in each segment one by one. This way, we only have to create a temporary array equal to the size of each segment each time, no matter how large $n$ is.


## Code

The `segmentedSieve()` function accept the arguments $n$ and segment size. It first generates a list of primes less than or equal to the segment size using a simple SoE, which is stores in `init_primes[]`. Then, for each "segment", it marks out all the numbers which are multiples of the primes in `init_primes[]`. The remaining unmarked numbers are all primes, and are appended to the list `primes[]`.

Notice that no matter how large $n$ is, only a memory size equal to `segment size + 1` is used to check for primes, which results in an improved space complexity of $O(1)$.

````python
import math

init_primes = []
primes = []

def simpleSieve(limit):
    # no primes below 2
    if(limit < 2):
        return -1
    
    # boolean array to indicate if prime:
    mark = [True] * (limit + 1)
	
    # loop through from 2 -> sqrt(limit)
    # only check if mark == True:
    sqrt_lim = int(math.sqrt(limit))
    for p in range(2, sqrt_lim + 1):
        if(mark[p]):
            # mark all values larger than p^2
            # and is a multiple of p
            for i in range(p*p, limit + 1, p):
                mark[i] = False
    
    # any numbers marked True now are primes
    # append those to primes[]
    for i in range(2, limit + 1):
        if(mark[i]):
            init_primes.append(i)
            primes.append(i)

def segmentedSieve(limit, segment_size):
    # find primes in first segment
    simpleSieve(segment_size)
	
    # update segment index
    low = segment_size
    high = 2 * segment_size
	
    while(low < limit):
        if(high >= limit):
            high = limit
        
        #create boolean array for numbers in segment,
        # initialised to True
        mark = [True]*(segment_size + 1)
        for i in range(len(init_primes)):
            # start from lowest multiple of prime number > low
            low_limit = int((low // init_primes[i]) * init_primes[i]) 
            if(low_limit < low):
                low_limit += init_primes[i]
            # loop through multiples & mark as False (non-prime)
            for j in range(low_limit, high, init_primes[i]):
                mark[j - low] = False
        # add those still marked True to prime list
        for k in range(low, high):
            if(mark[k - low]):
                primes.append(k)
        # update segment index
        low += segment_size
        high += segment_size

########################################################################
segmentedSieve(100, 30)
print(primes)
````
