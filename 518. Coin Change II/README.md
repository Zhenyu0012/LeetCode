# 518. Coin Change II

## Link:
https://leetcode.com/problems/coin-change-ii/

## Recursive Solution:
Our intuition leads us to devise a simple solution, i.e., tracing every permutation and determining whether or not a permutation forms a unique combination. Lastly, count the number of unique combinations, and we're done.

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

In my solution, I use list `choices` to trace denominations, and I will add this list to set `uchoices` whenever it forms a unique combination. Unfortunately, since a set object only accepts immutable objects, I have to convert `choices` to a string object, which causes checking the uniqueness of a combination to be an $O(n)$ operation.

```python
class Solution(object):    
    def change_helper(self, amount, coins, uchoices, choices):            
        if amount < 0:
            return 0
        if amount == 0:
            cur = str(choices)
            if cur not in uchoices:
                uchoices.add(cur)
                return 1
            return 0
        
        ct = 0
        for i in range(len(coins)):
            choices[i] += 1
            ct += self.change_helper(amount-coins[i], coins, uchoices, choices)
            choices[i] -= 1
        return ct
            
    def change(self, amount, coins):
        uchoices = set()
        choices = [0] * len(coins)
        return self.change_helper(amount, coins, uchoices, choices)
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
Thus, the computation shows that the runtime of my recursive solution is $O(nm^n)$, which is really bad.

## Dynamic Programming Solution
