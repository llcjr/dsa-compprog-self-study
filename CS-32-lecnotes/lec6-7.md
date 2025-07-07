## Lectures 6 and 7: Deques (Double-ended queue)

Dequeues are a generaliation of linear data structures whose main operations are in $O(1)$ time at best. 
Primary operations:
- push_left
- push_right
- pop_left
- pop_right

### Implementations
#### Linear Implementation (Array)
- array $D[n]$
- $r \leftarrow \lfloor \frac {n}{2} \rfloor$
- $l \leftarrow r+1$
- def $insertDeque(left, e)$:
	- if $(left \wedge l=1) \vee (\neg left \wedge r=n)$:
		- $moveDeque(left)$
	- if $left$:
		- $l \leftarrow l-1$
		- $D[l] \leftarrow e$ 
	- else:	
		- $r \leftarrow r+1$
		- $D[r] \leftarrow e$
- def $moveDeque(left)$:
	- if $l=1 \wedge r=n$:
	- call $OVERFLOW$
	- if $left$:
		- $s \leftarrow \lceil \frac{n-r}{2}\rceil$
		- for $i$ in $(r, r-1, ..., l)$:
			- $D[i+s] \leftarrow D[i]$
		- $r \leftarrow r-s$
		- $l \leftarrow l-s$
	- else:
		- $s \leftarrow \lceil \frac{l-1}{2}\rceil$
		- for $i$ in $(l, l+1, ..., r)$:
			- $D[i-s] \leftarrow D[i]$
		- $r \leftarrow r-s$
		- $l \leftarrow l-s$
- def $deleteDeque(left)$:
	- if $l >  r$:
		- throw $UNDERFLOW$
	- number $val$
	- elif $left$:
		- $val \leftarrow D[l]$
		- $l \leftarrow l+1$
	- else:
		- $val \leftarrow D[r]$
		- $r \leftarrow r-1$
	- if $l > r$:
		- $r \leftarrow \lfloor \frac {n}{2} \rfloor$
		- $l \leftarrow r+1$
	- return $val$
- def $length()$: return $r - l + 1$
- def $isEmpty()$: return $l > r$
- def $isFull()$: return $l = 1 \wedge r =  n$

The implementation is similar to that of its queue counterpart, but for moving the deque, it moves the elements to the middle of the array. Indication for an empty deque is different however, as it uses a $l > r$ case as a flag for it. Insertion takes $O(n)$ time and deletion takes $O(1)$ time.

#### Linked Implementation 
- typedef node:
	- number $data$
	- pointer(node) $next$
	- pointer(node) $prev$
- pointer(node) $left \leftarrow NULL$
- pointer(node) $right \leftarrow NULL$
- $n \leftarrow 0$
- def $insertDeque(left, node)$:
	- pointer(node) $p \leftarrow address(node)$
	- if $left$:
		- $node.next \leftarrow left$
		- if $left \ne NULL$:
			- $deref(left).prev \leftarrow p$
		- $left\leftarrow p$
		- $right \leftarrow (right, p)[right = NULL]$
	- else:
		- $node.prev \leftarrow right$
		- if $right \ne NULL$:
			- $deref(right).next \leftarrow p$
		- $right \leftarrow p$
		- $left \leftarrow (left, p)[left = NULL]$
	- $n \leftarrow n + 1$
	- def $deleteDeque(left)$:
		- if $l = NULL \wedge r= NULL$:
			- throw $OVERFLOW$
		- $node \leftarrow (deref(right), deref(left))[left]$
		- $val \leftarrow node.data$
		- if $left$
			- if $node.next \ne NULL$:
				- $deref(node.next).prev \leftarrow NULL$
			- $left \leftarrow node.next$
			- $right \leftarrow (right, NULL)[left = NULL]$
		- else:
			- if $node.prev \ne NULL$:
				- $deref(node.next).next \leftarrow NULL$
			- $right \leftarrow node.prev$
			- $left \leftarrow (left, NULL)[right = NULL]$
		- $n \leftarrow n-1$
		- return $val$
- def $length()$: return $n$
- def $isEmpty()$: return $left = NULL \wedge right = NULL$

### Working Example: Sliding Window
SPS: determine the minimum value of all possible subarrays of length $k$ of a given array, In other words, evaluate
$$S(A) := \sum_{i=1}^n (\min(A[i:i+k]),)$$
#### Analysis of a Brute-force Implementation
Getting the minimum value of an array is an $O(n)$ operation. For any array, there are $\max(0,n-k+1)$ possible subarrays of length $k>0$. Therefore, the running time of a brute-force implementation is $k(n-k+1) = nk-k^2+k = \frac{n^2}{4}+k-(\frac{n}{2} - k)^2$ provided that $n \ge k$. This means that the running time of the implementation is $O(n^2)$, with $k = \frac{n}{2}$ cases being the slowest to process.
#### Observations
 1. $S(A)$ is non-decreasing if we take the indices instead of the values themselves as elements: 
 - when processing the next subarray, either the previous minimum is within the subarray and will only change if the rightmost element is lower. If the previous minimum is not within the subarray, then any element within the next subarray is a possible choice. In both cases where the minimum changes it has a demonstrably higher index than the previous one.
 - this implies a FIFO property within the problem.
2. The elements in the first subarray that are candidate minimums for the next subarrays form a subsequence that (a) starts after the minimum element of the first array, (b) is strictly increasing
- (a): Direct implication of observation 1.
- (b): If the subsequence $B$ wasn't strictly increasing, then it would contain elements of to the left of a relative loose minimum (elements that satisfy $B[\max(1, i+1)] \ge B[i] \le B[\min(|B|,i+1)]$) that are greater than it. Such elements are unecesary because the relative loose minimum would be a better candidate minimum and the elements would be unncessary via observation 1.
- (b) among relative loose minimums, it's preferrable to break ties with the larger index so that the value maximizes the number of subarrays that it is a candidate minimum of.
- Corollary: Either the first element in the subsequence or the last element in the next subarray are the candidate minimums of that subarray.
3. $S(A) = \min(A[:k]) + S(A[2:])$
- When constructing the subsequence $B$ as described in 2b, one can achieve this by taking the $B$-subsequence of $A$ (implemented as a deque), removing the first element, and popping from the right until all elements to the left of the new element are less than it, then inserting that to the right of the deque.
#### Implementation
- input array(number) $A[n]$
- deque $D[n]$
- def $SW(k)$:
	- if $n < k$:
		- return $()$
	- for $i$ in $(1, 2, ..., k)$:
		- while $A[i] \le D[D.r]$:
			- $D.removeDeque(TRUE)$
		- $D.insertDeque(FALSE)$
	- for $i$ in $k, k+1, .., n$:
		- output $D.deleteDeque(TRUE)$
		- while $A[i] \le D[D.r]$:
			- $D.removeDeque(TRUE)$
		- $D.insertDeque(FALSE)$

Since all deque operations are $O(1)$ and each element is only used twice for the deque, this implementation has $O(n)$ runtime.
