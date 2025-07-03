## Lecture 2: Algorithm Analysis
Runtime of an algorithm is denoted as $T(n)$
Best case analysis: Fastest running time possible. The test cases involved allow for skipping as much parts of the algorithm as possible.
Worst case analysis: Slowest running time possible. The test cases allow for minimal to no skipping of parts of the algorithm
Average case analysis: Running time on average
### Notations
Algorithm analyses typically focus on growth rate than on exact running time functions. Asymptotic notations are used to describe these growth rates
#### $O$-notation
$g(n)=O(f(n))$ if there exists a positive constant $C$ such that $0\leq g(n) \leq Cf(n)$ for all possible input sizes $n$.
#### $\Omega$-notation
$g(n)=\Omega(f(n))$ if there exists a positive constant $C$ such that $0\leq Cf(n) \leq g(n)$ for all possible input sizes $n$.
#### $\Theta$-notation
$g(n)=\Theta(f(n))$ if there exists two positive constants $C_1$ and $C_2$ such that $0\leq C_1 f(n)\leq f(n) \leq C_2 g(n)$ for all possible input sizes $n$.
### Working Example: Insertion Sort
A pseudocode for this algorithm for an array $A$ is as follows (assume 1-indexing):
- for all $i$ in $(2, 3, ..., n)$:
	- $key\leftarrow A[i]$
	- $j \leftarrow i-1$
	- while $j \ge 0, A[j]\ge key$:
		- $A[j+1]\leftarrow A[j]$
		- $j \leftarrow i-1$
	- $A[j+1] \leftarrow key$

The algorithm works by iterating through every index and moving the corresponding element to the right side of the largest element smaller or equal to it on its left side. In other words, $A[:i-1]$ is sorted. Now consider cases where the array is reversed and there are no duplicate elements. For any $i$, all elements in $A[:i-1]$ are larger than $A[i]$. This means that $A[i]$ has to move to the very first index. This is an $O(i)$ operation, and when done for every element, the entire algorithm is $\sum_{i=2}^n i=\frac{n(n+1) }{2}-1=O(n^2)$.
### Remarks
- Any positive exponential function grows faster than any polynomial function
- Any positive polynomial function grows faster than any polylogarithmic function

## Exercises
1. Prove that the remarks hold.
2.  It is known that $n!\approx \sqrt{2\pi n}(\frac {n}{e})^n$. What is $n!$ in $O$-notation?
3. 
## My Answers for the Exercises
