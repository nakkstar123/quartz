---
title: Counting hats and Euler's number
---

This is a markdown rendition of my [handout](https://circles.math.ucla.edu/circles/lib/data/Handout-4308-4115.pdf) for [ORMC](https://circles.math.ucla.edu/circles/).
## 1. Motivation
Alex, Bob, Charlie and Dave go to a party wearing identical hats. Upon arriving, they leave their hats in a room and forget which ones are theirs by the end of the party. Since all hats look the same, they decide to randomly pick a hat before going home. 
###### Problem 1.1
What are the odds that all 4 of them (randomly) picked up their own hat?
> [!hint]-
> You can calculate this probability by counting all possible hat-person combinations which equals the number of different orderings or *permutations* of $A,B,C,D$.
> > [!success]- Solution
> > There are $n!$ permutations of $n$ distinct elements so the answer is $4!=24$.
###### Problem 1.2
What are the odds that *none* of them picked up their own hats?
> [!hint]-
> Count the number of permutations of $A,B,C,D$ where none of the letters are in their correct positions. You can solve this by writing out all possible permutations but there is an easier way.

## 2. Permutations
The previous problem can be generalized as follows:

> [!question] Guiding question
> How many permutations on $\{1,2,\ldots,n\}$ are there such that no number returns to its original position?

To answer this question, we set up some definitions and notation.
> [!example] **Permutation**
> A permutation $\pi$ is a one-to-one and onto function from $\{1,2,\ldots, n\}$ to itself. We use $\pi(i)$ to denote the image of $i \in \{1,2,\ldots, n\}$ under the function $\pi$.

For example, the function that maps 
$$
\begin{align*}
    1 \mapsto 2 \\
    2 \mapsto 3 \\
    3 \mapsto 4 \\
    4 \mapsto 1    
\end{align*}
$$
is a permutation on the set $\{1,2,3,4\}$.
###### Problem 2.1
How many permutations are there on $\{1,2,3,4\}$? What about $\{A,B,C,D\}$? 
> [!hint]-
> Check [[#Problem 1.1]].
###### Problem 2.2
How many permutations are there on $\{1,2,\ldots,n\}$? 
> [!hint]-
> How many different positions can 1 be mapped to? Fixing the position of 1, how many positions can 2 be mapped to?
> >[!success]- Solution
> >1 can be mapped to $n$ different positions. Fixing any one such position, 2 can be mapped to $n-1$ remaining positions. Arguing inductively, we see that there are $n\times n-1 \times \cdots \times 2 \times 1 = n!$ permutations. 

Another useful definition is that of a fixed point of a permutation. 
> [!example] **Fixed point**
> We call $i \in \{1,2,\ldots, n\}$ a fixed point of a permutation $\pi$ if $\pi(i) = i$. 

For example, define $\pi_1$ as
$$
\begin{align*}
    1 \mapsto 1 \\
    2 \mapsto 2 \\
    3 \mapsto 4 \\
    4 \mapsto 3    
\end{align*}
$$
then $1$ and $2$ are the fixed points of $\pi_1$. Define $\pi_2$ as
$$
\begin{align*}
    1 \mapsto 3 \\
    2 \mapsto 4 \\
    3 \mapsto 1 \\
    4 \mapsto 2    
\end{align*}
$$
then $\pi_2$ has no fixed points.
###### Problem 2.3
How many permutations are there on $\{1,2,\ldots, n\}$ for which $1$ is a fixed point. Using this result, what is the probability that a (uniformly) random permutation $\pi$ on $\{1,2,\ldots, n\}$ fixes $1$?
> [!hint]-
> Hint
> > [!success]- Solution
> > Solution

Note that this is the same as the probability of a permutation on $\{1,2,\ldots, n\}$ fixing any element $1\leq i \leq n$. 
###### Problem 2.4
What is the probability that a (uniformly) random permutation $\pi$ on $\{1,2,\ldots, n\}$ fixes $1$ **and** $2$? 
> [!hint]-
> Hint
> > [!success]- Solution
> > Solution

> [!example] **Derangement**
> We call a permutation $\pi$ a derangement if it does not fix any element i.e. for all $i$ between 1 and $n$, $\pi(i) \neq i$. 

###### Problem 2.5
How many derangements on $\{1,2,3,4\}$ are there? 
> [!hint]-
> Hint
> > [!success]- Solution
> > Solution

With this new terminology, the question we asked at the start of this section can be
rephrased as follows:
> [!question] Guiding question (rephrased)
> How many derangements on $\{1,2,\ldots,n\}$ are there?

In the next section, we will introduce the final tool required to arrive at the solution.

## 3. Inclusion-Exclusion
### 3.1. Recap
###### Problem 3.1
What is the probability that a (uniformly) random permutation $\pi$ on $\{1,2,\ldots, n\}$ fixes $1$ **or** $2$ (or both)?
> [!hint]-
> Be careful not to double count!
> > [!success]- Solution
> > Solution
> 

The previous question is a bit tricky because we need to count the permutations that fix $1$, ones that fix $2$ as well as ones that fix $1$ and $2$ and subtract them to avoid double counting. This is known as the **Inclusion-Exclusion** principle.
###### Problem 3.2
Use Venn Diagrams to prove that for finite sets $A$ and $B$ we have $$|A \cup B| = |A| + |B| - |A \cap B|.$$
###### Problem 3.3
Use Venn Diagrams to prove that for finite sets $A,B$ and $C$ we have 
$$
    |A \cup B \cup C| =  \left| A \right| + \left| B \right| + \left| C \right| - \left| A \cap B \right| - \left| B \cap C \right| - \left| A \cap C \right| + \left| A \cap B \cap C \right|.
$$
### 3.2. Generalization
It turns out that this formula generalizes for the union of $n$  sets. The general formula is pretty messy to write out and it helps to expand it out for $n=4$ and $n=5$ to get a hang of it. 
$$
\left| \bigcup_{i=1}^{n} A_i \right| = \sum_{k=1}^{n} (-1)^{k+1} \left( \sum_{1 \leq i_1 < i_2 < \cdots < i_k \leq n} \left| A_{i_1} \cap A_{i_2} \cap \cdots \cap A_{i_k} \right| \right)
$$
We can rewrite this as
$$
\left| \bigcup_{i=1}^{n} A_i \right| = \sum_{i} |A_i| - \sum_{i < j}|A_i \cap A_j| + \sum_{i<j<k}|A_i \cap A_j \cap A_k| + \cdots + (-1)^{n+1}|A_1 \cap \dots \cap A_n|.
$$
The intuition here is that you start by adding the "pieces" in the Venn Diagram that correspond to entire sets, then you take away $2$-way intersections to avoid overcounting, then you add back $3$-way intersections to avoid undercounting and so on until you finally add back or take away the intersection of all sets i.e. the center of the Venn Diagram. 
###### Problem 3.4
Prove the above formula using induction.
> [!hint]-
> Hint
> > [!success]- Solution
> > Solution
###### Problem 3.5
Assume that you roll 4 dice independently. What is the probability that you get a six
on at least one of them?
> [!hint]-
> Hint
> > [!success]- Solution
> > Solution

You probably solved the previous question by first counting the number of combinations where none of the faces have a six and then subtracting that from the total number of outcomes. Just for fun, let’s calculate this another way.
###### Problem 3.6
Use the generalized inclusion-exclusion formula for $n=4$ to solve [[#Problem 3.5]]. Use this to verify your previous answer.
> [!hint]-
> Let $X$ denote the set of all $6^4$ outcomes of the experiment described in the previous problem, where each outcome is a list of 4 numbers, indicating the number that appears on the faces of the 4 dice. Let $A$ denote the set of outcomes where the first face shows a six, $B$ the set of outcomes where the second face shows a six, $C$ defined similarly for the third face and $D$ for the fourth face. Then the number of outcomes where at least one of the faces is a six is precisely $|A \cup B \cup C \cup D|$. 

This is a terribly inefficient way to solve the problem. However, it illustrates the method we will use to count derangements. 
### 3.3. Back to derangements
We now return to the question of finding the number of dearrangements on $\{1,2,\ldots, n\}$ which we will henceforth denote $D_n$. 
###### Problem 3.7
How many permutations on $\{1,2,\ldots, n\}$ fix  $1,2,\ldots, k-1$ and $k$  where $k \leq n$?
> [!hint]-
> Hint
> > [!success]- Solution
> > Solution
###### Problem 3.8
Let $i_1, i_2, \ldots, i_k$ be distinct numbers between $1$ and $n$. How many permutations on $\{1,2,\ldots, n\}$ fix  $i_1,i_2,\ldots, i_{k-1}$ and $i_k$?
> [!hint]-
> Hint
> > [!success]- Solution
> > Solution

We will use this result along with the [[#3. Inclusion-Exclusion|Inclusion-Exclusion]] principle, to explicitly find a formula for $D_n$.
###### Problem 3.9
let $S_i$ denote the set of permutations on $\{1,2,\ldots, n\}$ that fix $i$. Explain why it follows that 
$$
D_n = n! - |S_1 \cup \cdots \cup S_n|.
$$
> [!hint]-
> Count the number of derangements as the total number of permutations on $\{1,2,\ldots, n\}$ minus the number of permutations that fix at least one element.
> > [!success]- Solution
> > Solution

All that is left to do now is carefully apply the [[#3. Inclusion-Exclusion|Inclusion-Exclusion]] principle. 
###### Problem 3.10
Calculate $|S_1 \cup \cdots \cup S_n|$ by following the given steps.
1. Note that a straightforward application of the inclusion-exclusion principle gives us that 
$$
\left| \bigcup_{i=1}^{n} S_i \right| = \sum_{i} |S_i| - \sum_{i < j}|S_i \cap S_j| + \sum_{i<j<k}|S_i \cap S_j \cap S_k| + \cdots + (-1)^{n+1}|S_1 \cap \dots \cap S_n|.
$$
2. We want to simplify each of the sums. Using one of the previous problems argue that the size of the intersections does not depend on *which* sets you intersect, just *how many* you intersect. From this deduce that 
$$
\left| \bigcup_{i=1}^{n} S_i \right| = \sum_{i} (n-1)! - \sum_{i < j}(n-2)! + \sum_{i<j<k}(n-3)! + \cdots + (-1)^{n+1}(n-n)!.
$$
3. Observe that the term within each sum is actually independent of the iterator being summed over. Therefore, we just need to count how many different 2-way intersections, 3-way intersections and so on are possible. Using this idea, show that the above can be rewritten as 
$$
\begin{align*}
    \left| \bigcup_{i=1}^{n} S_i \right| &= {n \choose 1} (n-1)! - {n \choose 2}(n-2)! + {n \choose 3}(n-3)! + \cdots + (-1)^{n+1}(n-n)! \\
    &= \frac{n!}{1!} - \frac{n!}{2!} + \frac{n!}{3!} + \cdots + (-1)^{n}\frac{n!}{n!}
\end{align*}
$$
Putting all of this together, we get a formula for $D_n$ 

$$D_n = n! \left(1 - 1 + \frac{1}{2!} - \frac{1}{3!} + \frac{1}{4!} - \cdots + (-1)^n \frac{1}{n!}\right)$$
While this is an explicit formula that can be used to calculate $D_n$ it still looks pretty messy and does not give much intuition about what is going on. For example, what happens to the probability that no one gets their hat back when the number of hats becomes very large? Does it approach $1$? Does it approach $0$? Neither? 
## 4. Limits and $e$
We take a slight detour in this section to explain the idea of a limit and define the number $e$. The focus will be on intuition rather than formalism. 
### 4.1. Lazy tortoise
###### Problem 4.1
A tortoise is competing in a 1 mile marathon. The tortoise gets progressively lazier. Every day (starting from day one) the tortoise walks half of the distance left between his current position and the the finish line. For example, he walks $1/2$ a mile on day one, $1/4$ mile on day two and so on. Find a formula for $d_n$, the total distance travelled by the tortoise upto and including day $n$.
> [!hint]-
> Hint
> > [!success]- Solution
> > Solution
###### Problem 4.2
Prove that the tortoise never crosses the finish line.
> [!hint]-
> Hint
> > [!success]- Solution
> > Solution
###### Problem 4.3
Out of sympathy, the organizers of the marathon decide to make a consolation prize for the tortoise. They draw a second "almost-finish" line, some (positive) distance before the actual finish line. Prove that, no matter where the almost-finish line is drawn, the tortoise always wins the consolation prize.
> [!hint]-
> First convince yourself that if the almost-finish line is drawn $\epsilon = 0.1$ miles, $0.01$ miles or even just $0.001$ miles before the finish line, the tortoise will eventually cross it (i.e. there exists some $N$ such that for all $n \geq N$ we have that $d_n$ is greater than $0.999$ although it is less than $1$.) Writing a formal proof that this works for any $\epsilon > 0$ is tricky but doable!
> > [!success]- Solution
> > Solution

In the previous problems, we saw the notion of a sequence (tortoise) getting \textit{arbitrarily} close (winning the consolation prize) to a certain number even though it never actually reaches that number (finish line). This idea appears again in the next problem.
### 4.2. Millionaire bank
###### Problem 4.4
A bank offers you an interest rate of $100\%$ per year. If you invest $1$ dollar at the start of the year, how much money is there at the end of the year?
###### Problem 4.5
You are determined to exploit the bank to convert the 1 dollar into as much money as possible within the period of one year. The bank offers you a deal where they allow you to invest money for half the time (6 months) for half the interest rate (50%). After 6 months, you may invest the money again (at the same rate) if you'd like. Would you take this deal or not? Why?
###### Problem 4.6
After a lot of begging and pleading, you convince the bank to let you deposit and withdraw the money at any frequency you like, scaling the interest rate accordingly. Specifically, you can put the money in for $1/n$ years at $(100/n)\%$ and compound it $n$ times. Find a formula for the money in your account at the end of one year $m_n$ if you decide to compound it $n$ times.
###### Problem 4.7
Show that $m_n$ is an increasing sequence i.e. the more often you go to the bank, the richer you will become.
###### Problem 4.8
Enthused by your financial success, you conjecture that if you spent the year living at the bank, compounding your money infinitely often, you would become a millionaire. Prove that your conjecture is false. In fact, prove that no matter how many times you compound your money, you will have less than 4 dollars in your bank at the end of the year.
> [!hint]-
> Use the binomial formula to expand your formula for $m_n$ and then use induction. You will have to use some clever inequalities related to geometric sums.
> > [!success]- Solution
> > Solution
### 4.3. Properties of $e$
Intuitively, the sequence is increasing and bounded above so it keeps growing but cannot grow to an arbitrarily large size[^1]. In this way, the sequence $m_n$ is similar to $d_n$ and also "converges" to some number. Using a calculator, for large values of $n$ you can see that $m_n$ is around 2.71828. The exact number that this sequence approaches (called its *limit*) is an irrational number which we call $e$. 

It turns out there is another way to approximate $e$. Let's apply the binomial theorem to the expression $\left(1 + \frac{1}{n}\right)^n$:
$$
\left(1 + \frac{1}{n}\right)^n = \sum_{k=0}^{n} \binom{n}{k} 1^{n-k}\left(\frac{1}{n}\right)^k
$$
This simplifies to:

$$
\sum_{k=0}^{n} \frac{n!}{k!(n-k)!} \frac{1}{n^k} = \sum_{k=0}^{n} \frac{1}{k!} \cdot \frac{n!}{n^k(n-k)!}
$$

Without getting into too many details, as $n$ approaches infinity, the term $\frac{n!}{n^k(n-k)!}$ approaches 1 for every $k$, because the numerator and denominator are roughly of the same order as $n$ grows large. Therefore, the expression becomes:
$$
\lim_{n \to \infty} \left(1 + \frac{1}{n}\right)^n = \sum_{k=0}^{\infty} \frac{1}{k!} = 1 + 1 + \frac{1}{2!} + \frac{1}{3!} + \frac{1}{4!} + \cdots
$$
###### Problem 4.9
We defined $e$ as
$$
\lim_{n \to \infty} \left(1 + \frac{1}{n}\right)^n.
$$
Assuming that limits behave *nicely*[^2] show that 
$$
\lim_{n \to \infty} \left(1 - \frac{1}{n}\right)^n = \frac{1}{e}.
$$
> [!hint]-
> Hint
> > [!success]- Solution
> > Solution
###### Problem 4.10
We used the binomial expansion to rewrite $e$ as a slightly different limit whose *partial sums* can be computed to approximate the limit. Do a similar expansion to show that 
$$
\lim_{n \to \infty} \left(1 - \frac{1}{n}\right)^n = \sum_{k=0}^{\infty} (-1)^k\frac{1}{k!} = 1 - 1 + \frac{1}{2!} - \frac{1}{3!} + \frac{1}{4!} - \cdots
$$
> [!hint]-
> Hint
> > [!success]- Solution
> > Solution
###### Problem 4.11
Use this series expansion for $1/e$ to show that $2 < e <3$.
> [!hint]-
> Show that $1/3 < 1/e < 1/2$.
> > [!success]- Solution
> > Solution
### 4.4. Hats and $e$
###### Problem 4.12
Prove that the probability $D_n/n!$ no one gets their hat back approaches $1/e$ as the number of hats goes to infinity. In fact, this gives a very elegant formula for the number of derangments:
$$
D_n = \left[\frac{n!}{e}\right]
$$
where $[\cdot]$ denotes the nearest integer function.

[^1]: Every monotone bounded sequence of real numbers converges by the [Monotone Convergence Theorem.](https://en.wikipedia.org/wiki/Monotone_convergence_theorem)
[^2]: Limits commute with most algebraic operations like addition, multiplication and exponentiation.