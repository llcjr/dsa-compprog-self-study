## Lectures 5 and 8: Queues

Queues are abstract data types with the following property: First-in-first-out (FIFO). Specifically, it has the following operations:
- enqueue: insert from rear
- dequeue: delete from front

### Implementations
#### Straight Queue (Array)
- array $A[n]$
- $rear \leftarrow 0$
- $front \leftarrow 0$
- def dequeue():
	- if $front = rear$:
		- throw $UNDEFLOW$
	- $val \leftarrow A[front]$
	- $front \leftarrow front + 1$
	- if $front = rear$:
		- $front \leftarrow 0$
		- $rear \leftarrow 0$
	- $return $val$
- def moveQueue():
	- if $front = 0$:
		- throw $OVERFLOW$
	- for all i in $(front + 1, front + 2, ..., n)$:
		- $A[i - front] \leftarrow A[i]$
	- $rear \leftarrow rear - front$
	- $front \leftarrow 0$
- def enqueue($e$):
	- if $rear = n$:
		- $moveQueue()$ 
	- $rear \leftarrow rear + 1$
	- $A[rear] \leftarrow e$

The implementation works by incrementing `front` during a `dequeue` operation and incrementing `rear` during a `enqueue` operation. The running time of the `enqueue` and `dequeue` operations are $O(n)$ (due to the `moveQueue` function that needs to be called when `rear` points to the end of the array) and $O(1)$ respectively. However, the implementation below optimizes the `enqueue` operation down to $O(1)$.

#### Circular Queue (Array)
- array $A[n+1]$
- $front \leftarrow 0$
- $rear \leftarrow 0$
- def enqueue($node$):
	- if $front = (rear + 1) \mod (n+1)$:
		- throw $OVERFLOW$
	- $rear \leftarrow (rear + 1) \mod (n+1)$
	- $A[rear] \leftarrow e$
- def dequeue():
	- if $front = rear$:
		- throw $UNDERFLOW$
	- $val \leftarrow A[front]$
	- $front \leftarrow (front + 1)\mod (n+1)$
	- return $val$

This implementation eliminates the potential need to re-offset the queue to the start of the array during an `enqueue` operation since the modular arithmetic handles all of that. An array with length $n+1$ is needed to be able to disambiguate the cases where the queue is empty and where the queue is full. Using an array of length $n$ leads to `front` and `rear` having the same value for both cases.

#### Linked List Implementation
- typedef $NODE$:
        - value $data$
        - pointer(value) $next$
- pointer(NODE) $front = NULL$
- pointer(NODE) $rear = NULL$
- $length \leftarrow 0$
- def enqueue($node$):
	- pointer(NODE) $p \leftarrow address(node)$
	- if $front = NULL$:
		- $front \leftarrow p$
	- $length \leftarrow length + 1$
	- if $length > 1$:
		- $deref(rear).next \leftarrow p$
	- $rear \leftarrow p$
- def dequeue($node$):
	- $val \leftarrow deref(front).data$
	- if $length = 0$:
		- throw $UNDERFLOW$
	- $length \leftarrow length - 1$
	- $front \leftarrow deref(front).next$
	- if $length = 0$:
		- $rear \leftarrow NULL$
	- return $val$

### Working Example: Topological Sorting
SPS: You are given a partial order relation $\prec$ and a directed acyclic graph of nodes where each edge from node $v_1$ to $v_2$ represent $v_1 \prec v_2$ (or any DAG actually). Find a list representation of the nodes $A$ in the DAG such that $a_i < a_j$ if $a_i \prec a_j$ 

#### Implementation 1
- stack[array] $S$
- input graph $G$
- input $n \leftarrow |G.v|$
- array $E \leftarrow |G.e|$
- input array(array) $adj$, form $(v \in (1, 2, ..., n)| \ u \in (1, 2, ..., n) \setminus (v,) | (v, u) \in E)$
- input array $count$, form $(v \in (1, 2, ..., n)| \ |u \in (1, 2, ..., n) \setminus (v,) | (u, v) \in E|)$
- while $¬S.isFull()$:
	- for all $i$ in (1, 2, ..., n):
		- if $count[i] = 0$:
			- $S.push(e)$
			- for all $child$ in $adj[i]$:
				- $count[child] \leftarrow count[child] - 1$
			- $count[i] \leftarrow -1$

This implementation scans whether the stack is full. Otherwise, it looks for nodes with a zero predecessor count, then decrements the predecessor count of their successors and appends the nodes themselves to the stack.
#### Analysis of Implementation 1 (my take)
We first check if this algorithm does indeed return a topologcally ordered list of nodes.
Claim: at any point within the algorithm's execution, the stack contains a correct topological ordering of the nodes.
We proce this by induction. 

Suppose $S$ has two elements. There are only two possible cases. The first one is that $S[1]$ and $S[2]$ don't have predecessors in the first place, and the invariant of the stack holds vacuously. The second is that $S[2]$ is a direct successor of $S[1]$, and that the vertex of $S[1]$ came first in the node ordering, and the invariant holds trivially. 

Suppose this claim holds for some point in time where $S$ has $1 \leq j \leq n-1$ elements. The $(j+1)-th$ element falls under one of two cases:
- Its predecessor count is zero to begin with: The claim holds vacuously.
- Otherwise: This implies that all its direct predecessors have been iterated through, decremented the node's count, and pushed onto the stack. Thus the claim holds as well.

Consider the case where $v_1 \succ v_2 \succ ... \succ v_n$, and no other partial orders are defined. Then this represents a path DAG. For each time the algorithm iterates through the vertex list, there is only one vertex that's pushed onto the stack and only one vertex whose predecessor count is zeroed out. Each edge, however, is traversed exactly once when the origin vertex has zero predecessor count. Therefore, the algorithm's running time is $O(|V|^2 + |E|)$.

#### Implementation 2
Consider the infinite array
$$S := \sum_{i=1}^\infty (1,2,...,n)$$

The indices in this array corresponds to a point in time where the algorithm in implementation 1 is processing the node whose id is $S[k]$. Nodes with zero predecessor count are marked. As such, when $count[S[k]]=0$ and one of its children also has zero predecssor count, the nearest element in $S$ that corresponds to that node and whose index is greater than $k$ is marked. Elements that are marked first are also the ones first added to a stack; a FIFO property (well sort of since there are cases where the adjacency list of a node isn't sorted). With that in mind, this is an optimized flow of the toposort algorithm:
- Iterate through all nodes and find those that have zero element count. Add them to the queue
- For each node in the queue, dequeue and do the same thing but for child nodes that have zero predcessor count, enqueue them as well.
- Repeat step 2 until the queue is empty.
Provinng the correctness of this algorithm is nearly identical to how the correctness of implementation 1 is proven.
