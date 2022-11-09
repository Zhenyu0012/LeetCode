# 518. Coin Change II

## Link:
https://leetcode.com/problems/coin-change-ii/

## Recursive Solution:
Our intuition leads us to devise a simple solution, i.e., tracing every permutation and determining whether or not a permutation forms a unique combination. Count the number of unique combinations, and we're done.

---

<p align="center"> RECURSIVE DEFINITION </p>

$\mathrm{coins[1,n]\ :=\ coins\ of\ different\ denominations}$<br>
$\mathrm{\ \ \ amount\ :=\ a\ total\ amount\ of\ money}$

$$
\mathrm{change(amount, coins)}=
\begin{cases}
0&\mathrm{(amount<0)\lor(\mathrm{a\ combination\ is\ not\ unique})}\\
1&\mathrm{a\ combination\ is\ unique}\\
\Sigma_{i=1}^n\mathrm{change(amount-coins[i], coins)}&\mathrm{otherwise}
\end{cases}
$$

---

<p align="center"> IMPLEMENTATION </p>

To avoid future confusion, I add two variables, `choices` and `uchoices`, in my implementation. `choices` is a list that aims to trace chosen denominations, and `uchoices` is a set that helps to check the uniquenesses of combinations. I will add `choices` to `uchoices` whenever selected denominations form a distinctive combination. Unfortunately, since set objects only accept immutable objects, I have to convert `choices` to a string object, which causes a check for a unique combination to be an $O(n)$ operation.

```python
class Solution(object):    
    def change(self, amount, coins):
        
        uchoices = set()
        size = len(coins)
        choices = [0] * size
        
        def change_helper(amount, coins):            
            if amount < 0:
                return 0
            if amount == 0:
                cur = str(choices)
                if cur not in uchoices:
                    uchoices.add(cur)
                    return 1
                return 0

            ct = 0
            for i in range(size):
                choices[i] += 1
                ct += change_helper(amount-coins[i], coins)
                choices[i] -= 1
            return ct
        
        return change_helper(amount, coins)
```

## Recursive Runtime Analysis:
Since it is a recursive solution, we can use the following generic formulas to calculate the runtime:

$$T(n) = m(n-1)+O(1)$$

$$T(0) = O(n)$$

Thereinto, $m$ denotes the size of `coins`. Here is the computation:
```math
\begin{aligned}
T(n) &= m(n-1)+O(1)\\
&= m(m(n-2)+O(1))+O(1)\\
&=...\\
&=m^nT(0)+O(1)\cdot(m^0+m^1+...+m^{n-1})\\
&=nm^n+\frac{m^n-1}{m-1}\\
&=O(nm^n)
\end{aligned}
```
Thus, the computation shows the runtime of my recursive solution is $O(nm^n)$, which is really bad.

## Dynamic Programming Solution
