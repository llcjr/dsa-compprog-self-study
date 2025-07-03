## Lectures 3 and 4: Stacks

### Some Terms
Abstract Data Type (ADT): set of elements and operationsdefined on them
Data Structure (DS): implementation of an ADT.
- has two variants: contiguous and linked design.

### Definition of a Stack
ADT with the following operations:
- `push` - place an element on top of the stack
- `pop` - remove an element from the top of the stack and return it
i.e., Last-in-first-out (LIFO): at any point in time, only the element on top of the stack is accessible

### Implementations
#### Array Implementation
Involves an actual array implemented by the programming language to be used. 
- $array\ S[n]$
- $top \leftarrow 0$
- def push($e$):
	-	if $top = n$:
		-	throw $OVERFLOW$
	-	$top \leftarrow top + 1$
	-	$S[top] \leftarrow e$
-	def pop($e$):
	-	if $top = 0$:
		-	throw $UNDERFLOW$
	-	$val \leftarrow S[top]$
	-	$top \leftarrow top - 1$
	-	return $val$
-	def isEmpty(): return $top = 0$
-	def isFull(): return $top = n$
#### Linked List Implementation
Involves a `Node` type, usually implemented by the user. The top of the stack is the head of the linked list
- typedef $NODE$:
	- value $data$
	- pointer(value) $next$
- $top \leftarrow NULL$
- NODE $head$
- def push($node$):
	- $head \leftarrow node$
	- $head.next \leftarrow top$
	- $top \leftarrow address(node)$
- def pop():
	- if $top = NULL$:
		- throw $UNDERFLOW$
	- $val \leftarrow head.data$
	- $top \leftarrow head.next$
	- $head \leftarrow deref(head.next)$
	- return $val$

Note that dereferencing $NULL$ will most likely lead to an error/segfault. One can instead use a "null node" or have the stack define placeholder values for `head` when it's empty.

### Pros and Cons
Capacity: Linked list allows for allocating of endless nodes, while array maintains a limit for the number of elements
- In the context of C/C++, one may use a combination of `malloc` and `realloc` to get around the array memory limit.

Access: Array technically allows for random access, i.e. access to any element, as opposed to using linked lists which requires traversal (an $O(n)$ operation).

Overhead: Using nodes has more overhead as you're defining two values for one element (value and next-node), as opposed to using arrays where you can just append the element value itself.

Do note that a pure stack has no random access, and searching or accessing a specific element is an $O(n)$ operation.

### Working Examples
Only solutions using pure stacks will be discussed.
#### Pattern Recognition
SPS: given a string containing only the letters a, b, and c,  determine whether it's in the following form: $S := T + "c" + T[::-1]$, where T is contains only the letters a and b. 
Solution: Let $S$ be a stack containing the string. Then. initialize a stack $H$. Pop elements from $S$ and push them to $H$. If $pop(S) = "c"$, stop and do the next step below.
Pop an element from stacks $S$ and $H$. If the values are not equal, return False. Otherwise, keep going. If the stacks are now empty, return True.
This works because when you pop a set of elements from a stack and push them to another, the ordering is reversed. Applying that logic to strings in the form $S := T + "c" + T[::-1]$, pushing elements of $T$ to another stack yields $T[::-1]$, and that serves as a checking criteria for the solution.

#### Balanced Brackets
SPS: Given a string containing characters in this string: "()[]{}" determine if the brackets in the former are all balanced. This means no unmatched brackets (e.g. "[)", "(}"), and every substring completely enclosed with a bracket is also balanced.
Solution: Let $S$ be a stack and iterate through every character in the string, starting from the leftmost character. If the character is an opening one, push the respective bracket onto the stack. Otherwise, pop from the string and check if the character is equal to the closing bracket's opening variant. If it's not equal or the stack is empty, return False. Do this until you've iterated through the string completely. Return True if the loop finishes normally and the stack is empty.

#### Subarray Min
SPS: Given an array of integers, compute the sum of the minimum element of every subarray. Provide an $O(n)$ solution.
Solution: For some $1 \le i \le n$, consider all subarrays in array $A$ ending at the the $i$-th element. 
Note the following special test case:
- $A$ is non-increasing; consider the same set of subarrays given some $i$. Then, the sum is $\sum_{i=1}^n n \cdot A[i]$

Another trivial property we can use in deriving the solution is the following: $min(A + (a,)) = min(A)$ if $A[-1] \le a$.

This leads us to our 2 major findings: 
1. For some array A with length $n$, $min(A[1:n]) \le min(A[2:n]) \le ... \le min(A[n:n])$
We prove this by induction, with a 2-element array as our base case. If $A[1] \le A[2]$, then $min(A[1:2]) = A[1]$ and $min(A[2:2]) = A[2]$, thus the inequality holds. Otherwise, $min(A[1:2]) = min(A[2:2]) = A[2]$ in which the inequality also holds. Now assume this holds for some $n$. Let $A' = A + (a,)$. Unless $a > min(A)$ There exists an interval $[j,n]$ such that $min(A'[j:n]) = a$, and the inequality holds.
2. The inductive step in (1) reveals a LIFO property where appending an element is preceded by repeated pop operations until the stack is empty or the top element is less than said element.
Corollary : For duplicate $min(A[j:n])$ values, there's no need to list them down multiple times. We call such values

Implementation:
- input array $A[n]$
- stack $S$
- $ans \leftarrow 0$
- def peek():
	- if $S.isEmpty()$:
		- return $0$
	- $val \leftarrow S.pop()$
	- $S.push(val)$
	- return $val$
- for all $i$ in $(1, 2, ..., n)$:
	- while $~S.isEmpty() \vee A[peek()] > A[i]$:
		- $curr \leftarrow S.pop()$
		- $ans \leftarrow ans + A[curr](i-curr)(curr-peek())$
	- $S.push(A[i])$
- while $~S.isEmpty()$:
	- $curr \leftarrow S.pop()$
	- $ans \leftarrow ans + A[curr](i-curr)(curr-peek())$

Here, the stack uses indices for storing subarray mins. the $(i-curr)$ factor represents the popped element's "lifetime" and $(curr-peek())$ represents how many duplicate values it has when the stack is mapped out into the inequality at (1)

	


