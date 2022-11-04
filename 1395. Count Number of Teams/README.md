# 1395. Count Number of Teams

## Link:
https://leetcode.com/problems/count-number-of-teams/

## Naive Solution (Brute force):
Our intuition leads us to devise a naive (brute force) solution, i.e., using nested loops to find all possible increasing or decreasing subsequences with length 3. Our goal is straightforward - checking all possible combinations and counting all satisfying combinations.

---

<p align="center"> PSEUDOCODE </p>

$\mathrm{rating[1…n]\ :=\ ratings\ of\ all\ the\ soldiers}$<br>
$\mathrm{ct\ :=\ counter,\ denotes\ number\ of\ satisfying\ combinations}$

$$
\begin{align}
&\mathrm{ct = 0}\\
&\mathrm{for\ i = 1\ to\ n:}\\
&\hspace{1.5em}\mathrm{for\ j = i + 1\ to\ n:}\\
&\hspace{1.5em}\hspace{1.5em}\mathrm{for\ k = j + 1\ to\ n:}\\
&\hspace{1.5em}\hspace{1.5em}\hspace{1.5em}\mathrm{if\ ith,\ jth,\ kth\ elements\ form\ a\ [in/de]creasing\ subseq,\ then:}\\
&\hspace{1.5em}\hspace{1.5em}\hspace{1.5em}\hspace{1.5em}\mathrm{ct\ increment\ 1}\\
&\mathrm{return\ ct}
\end{align}
$$

---

<p align="center"> IMPLEMENTATION </p>

In my brute force solution, every part is the same as my given pseudocode, except I use variable `llen` to denote the length of list `rating`.

```python
class Solution(object):
    def numTeams(self, rating):
    
        llen = len(rating)
        ct = 0
        
        for i in range(1, llen+1):
            for j in range(i+1, llen+1):
                for k in range(j+1, llen+1):
                    if rating[i-1] < rating[j-1] < rating[k-1] or rating[i-1] > rating[j-1] > rating[k-1]:
                        ct += 1
        return ct
```

## Naive Runtime Analysis:
It is trivial to see that the runtime of the naive solution is $O(n^3)$. Here is the computation:

$$
\begin{align}
\sum_{i=1}^{n}\sum_{j=i+1}^{n}\sum_{k=j+1}^{n}1&=\sum_{i=1}^{n}\sum_{j=i+1}^{n}n-j\\
&=\sum_{i=1}^{n}(n\sum_{j=i+1}^{n}1-\sum_{j=i+1}^{n}j)\\
&=\sum_{i=1}^{n}(n(n-i)-(\sum_{j=1}^{n}j-\sum_{j=1}^{i}j))\\
&=\sum_{i=1}^{n}(n^2-ni-\frac{n(n+1)}{2}+\frac{i(i+1)}{2})\\
&=\frac{n^2-n}{2}\sum_{i=1}^{n}1-n\sum_{i=1}^{n}i+\frac{1}{2}\sum_{i=1}^{n}i^2+i\\
&=\frac{n^3-n^2}{2}-\frac{n^3+n^2}{2}+\frac{n^3+3n^2+2n}{6}\\
&=\frac{n^3-3n^2+2n}{6}
\end{align}
$$

Thus, the computation shows that the runtime of the naive solution is $O(\frac{n^3-3n^2+2n}{6})=O(n^3)$, which is not good.

## Dynamic Programming Solution:
Before starting this problem, three things I want to mention first:
1. Even though each combination consists of three variables: $i$, $j$, and $k$, a 2D table is enough to solve the problem because tracking the first two variables $i$ and $j$ can determine the range of the last variable $k$.
2. The table I will use is an $n\times n$ matrix, and I call it `dp`. `dp[i][j]` denotes # of all qualified subsequences after choosing $i$ and $j$.
3. Variable $k$'s range controls the result of `dp[i][j]`.

---

Let's first ignore the base case, i.e., $i=1$, and assume I already knew all the results for `dp[1][j]`, where $j\in(i, n]$. Without loss of generality, assume we are at the $i$<sup>th</sup> row. When I move to the $(i+1)$<sup>th</sup> row, I can use constant time to find a relationship between `rating[i]`, `rating[i+1]`, and `rating[j]`, and their relationships fit one of the following inequalities:
1. `rating[i] < rating[j]` and `rating[i+1] < rating[j]`, where $i+1 < j$
    - $\mathrm{dp[i][j]} = \mathbf{card}(\\{m, m\in\mathrm{rating(j,n]}\land \mathrm{rating[j]}< m\\})$, where $\mathbf{card}$ denotes the cardinality of a set.
    - $\mathrm{dp[i+1][j]} = \mathbf{card}(\\{m, m\in\mathrm{rating(j,n]}\land \mathrm{rating[j]}< m\\})$ 
    - $\mathrm{dp[i+1][j] = dp[i][j]}$ because their $k$'s ranges are the same.
2. `rating[i] < rating[j]` and `rating[i+1] > rating[j]`, where $i+1 < j$
    - The interpretation for $\mathrm{dp[i][j]}$ is the same as 1.
    - $\mathrm{dp[i+1][j]} = \mathbf{card}(\\{m, m\in\mathrm{rating(j,n]}\land \mathrm{rating[j]}>m\\})$
    - $\mathrm{dp[i+1][j] = \mathbf{card}(k) - dp[i][j]}$, where $\mathrm{k=rating(j,n]}$. Using the above two conclusions, it's obvious to see $\mathrm{dp[i+1][j]}$ and $\mathrm{dp[i][j]}$ are mutually complementary.
3. `rating[i] > rating[j]` and `rating[i+1] > rating[j]`, where $i+1 < j$
    - The ideas behind this case are very similar to 1.
    - $\mathrm{dp[i+1][j] = dp[i][j]}$
5. `rating[i] > rating[j]` and `rating[i+1] < rating[j]`, where $i+1 < j$
    - The ideas behind this case are very similar to 2.
    - $\mathrm{dp[i+1][j] = \mathbf{card}(k) - dp[i][j]}$, where $\mathrm{k=rating(j,n]}$

(skip this if you know the answer)<br>
You may need clarification why I don't consider the relationship between `rating[i]` and `rating[i+1]`. The reason is it won't affect $k$'s range since they have the same $j$ value. ( $\mathrm{k's\ range=rating(j, n]}$ )

However, I have to use brute force or other fancy ways to calculate the base case since I can't make any conclusions from the $(j−1)$<sup>th</sup> entry. The reason is the calculation for $(j−1)$<sup>th</sup> entry only builds up a relationship between `rating[j-1]` and `rating[j,n]`. Therefore, as $\mathrm{rating[j−1]\ne rating[j]}$, the result for `rating[j-1]` won't relate to `rating[j]` at all.

---

<p align="center"> IMPLEMENTATION </p>

```python
class Solution(object):
    def numTeams(self, rating):
        
        def initializer(elem, flag, var):
            ct = 0
            for k in range(var+1, llen+1):
                ct += elem < rating[k-1] if flag else elem > rating[k-1]
            return ct
                
        llen = len(rating)
        dp = [[0] * (llen+1) for i in range(llen+1)]
        ct = 0
        
        for i in range(1, llen+1):
            for j in range(i+1, llen+1):
                if i == 1:
                    dp[i][j] = initializer(rating[j-1], rating[j-1] > rating[i-1], j)
                    
                elif (rating[i-1] > rating[j-1] and rating[i-2] > rating[j-1]) or \
                    (rating[i-1] < rating[j-1] and rating[i-2] < rating[j-1]):
                    dp[i][j] = dp[i-1][j] 
                    
                elif (rating[i-1] > rating[j-1] and rating[i-2] < rating[j-1]) or \
                    (rating[i-1] < rating[j-1] and rating[i-2] > rating[j-1]):
                    dp[i][j] = llen - j - dp[i-1][j]
                    
                ct += dp[i][j]
        return ct
```
## Dynamic Programming Runtime Analysis

$$O(n^2)+O(\sum_{i=1}^{1}\sum_{j=i+1}^{n}\sum_{k=j+1}^{n}1)=O(n^2)+O(\frac{n^2-3n+2}{2})=O(n^2)$$

Thus, the runtime of my dp solution is $O(n^2)$.




