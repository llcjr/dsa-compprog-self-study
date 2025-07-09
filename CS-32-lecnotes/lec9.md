## Lecture 9: Linear Lists
- An ordered set $(a_1, a_2, ..., a_n)$ of non-list elements (atoms).
- *Generalized lists are those that may contain a list as an element.*
- Linear lists allow for insertion and deletion anywhere on top of deque functionality.

### Implementations
#### Fixed Array Implementation
- Useful for scenarios where random access is the use case.
- Insertion and deletion takes $O(n)$ time.
- Only allows at most $n$ elements without reallocation, which is also an $O(n)$ operation.
#### Singly Linked List Implementation
- typedef node:
	- number $data$
	- pointer(node) $next$
- $head \leftarrow node(NULL, NULL)$
- pointer(node) $l \leftarrow address(head)$
- pointer(node) $r \leftarrow NULL$
- $n \leftarrow 0$
- def $insert(node, nodeBefore)$:
	- $p \leftarrow address(node)$
	- $q \leftarrow address(nodeBefore)$
	- $curr \leftarrow l$
	- while $curr.next \ne NULL \vee curr.next \ne q$:
		- $curr \leftarrow deref(curr).next$
	- $node.next \leftarrow curr.next$
	- $r \leftarrow (p, r)[curr.next = NULL]$
	- $deref(curr).next \leftarrow p$
	- $n \leftarrow n + 1$
- def $delete(node)$:
	- $p \leftarrow address(node)$
	- $curr \leftarrow l$
	- while $curr.next \ne NULL \vee curr.next \ne p$:
		- $curr \leftarrow deref(curr).next$
	- if $deref(curr.next) = NULL$:
		- throw $NOT\ IN\ LIST$
	- else:
		- $deref(curr).next \leftarrow node.next$
		- $node.next \leftarrow NULL$
		- $r \leftarrow$
	- $n \leftarrow n-1$
- def $length()$: return $n$
- def $left()$: return $head.next$
- def $right()$: return $r$

This implementation utilizes a list head that serves as the starting point of the list. the `insert` function inserts $node$ on the left of $nodeBefore$. If $nodeBefore$ is not found, then $node$ gets inserted on the right side of the list. Both `insert` and `delete`  operations have $O(n)$ runtime.
#### Doubly Linked List Implementation (Insert and Delete)
- typedef node:
	- number $data$
	- pointer(node) $next$
	- pointer(node) $prev$
- def $insert(node, nodeBefore)$:
	- $left \leftarrow nodeBefore.prev$
	- $p \leftarrow address(node)$
	- $node.prev \leftarrow left$
	- $deref(left).next \leftarrow p$
	- $node.right \leftarrow address(nodeBefore)$
	- $nodeBefore.prev \leftarrow p$
- def $delete(node)$:
	- $deref(node.prev).next \leftarrow node.next$
	- $deref(node.next).prev \leftarrow node.prev$
	- $node.prev \leftarrow NULL$
	- $node.next \leftarrow NULL$

The `insert` and `delete` operations work more as static methods, not needed to be called under an instance of a list. Said operations both take $O(1)$ runtime for any node.
