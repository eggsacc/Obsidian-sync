
Created <font style="color:tomato; font-family:Consolas;">06-03-2025</font>

Tags: 

Related: NIL

Sources: NIL

****

## Problem

Given a set of available coins, `coins = [1, 5, 10, 20, 50, 100]`, where each number represents the value of coins in cents, write a function `count_change(amount, coin_types)` that returns the total possible combination of `coin_types` that can add up to the amount exactly. For example, `count_change(20, 3)` returns the number of ways of finding change for 20 cents, using only the first 3 types of coins, which are 1, 5 and 10 cent coins.

## Recursive solution

To solve this using recursion, we can break it down into the following steps:

- In each iteration, we can either choose to use an available coin type or skip it.
- If we use the coin, deduct the coin's amount from the total amount
- If we skip the coin, move on to the next available coin
- If the total amount is 0, it means that the change is exact, and the combination is accepted
- Otherwise, if the total amount becomes negative or we run out of coin types to test, the combination is invalid.

````python
def count_change(amount, coin_available)
	coin_types = [1, 5, 10, 20, 50, 100]
	# Amount = 0: exact change found
	if(amount == 0):
		return 1
	
	# Amount < 0 or no more available coins: no exact change
	if(amount < 0 or coin_available == 0):
		return 0
	
	# Get last available coin type
	coin = coin_types[coin_available - 1]
	
	# 2 Options: Use coin, or skip this type
	return count_change(amount - coin, coin_available) + 
		   count_change(amount, coin_available - 1)
````
