

## Review of CS 31 Concepts
### Definition of a Tree
- A connected graph that has no cycles
- Has exactly $n-1$ edges
### Binary Trees
- Each non-leaf node has at most two vertices
- Full binary trees with $n$ internal vertices have $2n+1$ total vertices and $k+1$ leaves
- The number of leaves in a binary tree with height $h$ is $t\le 2^h$, or is $O(2^h)$.

## Lecture 1: Trees
### Ordered Tree
Finite set of nodes such that
- there's one assigned as a root
- the rest are partitioned into subsets which are subtrees
### Some terms
Degree of a node: Number of subtrees
Degree of a tree: Degree of the node with the highest degree
Height of a tree: Level of its bottommost node
### Free tree
Tree with unspecified root
### Forest
Set of zero or more disjoint trees
### Natural Correspondence of Forest Nodes
A binary tree can be obtained a forest of ordered trees by doing the following
- Link all the nodes that are in the same level and same tree
- Remove the original edges between each node and their parent (except if they go first in the ordering)
### Tree Traversal
#### Preorder Traversal
```python
def preorder(root: Node):
	print(root)
	for child in children(rootNode):
		preorder(root)
```
#### Postorder Traversal
```python
def postorder(root: Node):
	for child in children(rootNode):
		preorder(root)
	print(root)
```
Implementations of preorder and postorder traversals of forests are more or less the same. It's equivalent to traversing a tree whose root's children are the roots of the trees in the forest.

### Linked List Representation
#### Preorder Sequential
```cpp
typedef struct node {
	Node* next_sibling;		// NULL if no sibling or is the last sibling
	int value;				
	bool is_leaf;		
} Node;
```
#### Family-order Sequential
```cpp
typedef struct node {
	Node* first_child;		// NULL if no sibling or is the last sibling
	int value;				
	bool is_last_child;		
} Node;
```

### Sample Problem: Equivalence Relations
SPS: Given a list of equivalence relations of the form $i\equiv j$, determine whether $k$ is equivalent to $l$.

Note that an equivalence relation over a set partitions it into disjoint equivalence classes. Such structures can be represented by a forest of oriented trees.

Assembling the forest is as follows. Let $parent(i)$ be the parent of $i$. Given an equivalence relation $i\equiv j$ 
- set $i$ and $j$ to $parent(i)$ and $parent(j)$, respectively
- set $parent(i)$ to $j$

For determining whether $k \equiv l$, we just check whether they have the same root of the tree they belong to.

However, consider the scenario where the set has only one equivalence class. An example test case would be $\{1\equiv2,1\equiv3,...,1\equiv n\}$. We end up with a path tree where $n$ is the "root" and $1$ would be the "leaf". This tree has height $O(k)$ for some $1\leq k \leq n$. And doing this for all $n$ equivalence relations, we end up with a time complexity of $O(n^2)$. 
*Note: The algorithm is $\Omega(n)$, where one such best-case would be $\{1\equiv2,3\equiv4, 2n-1\equiv 2n\}$*

#### Optimization (based from the lecture)
Let $len(i)$ be the number of nodes in tree/subtree $i$
We tweak the following operations involved:
- Union operation: for some roots $i$ and $j$, if $len(i) > len(j)$, then $parent(j) \leftarrow i$, and $parent(i)\leftarrow j$ otherwise
- Find operation: let $(n_1, n_2, ..., n_m)$ be the nodes in the path from $n_1$ to $root(n_1)$. We do $parent(n_p) \leftarrow root(n_1)$ for all $1\leq p \leq m$

The union operation is used for merging two equivalence class fragments, and the find operation is used for finding the roots of the equivalence trees.
#### Optimization (my take, doesn't involve trees)
- Given that the subject revolves around C, this implementation requires having to implement lists or "dynamic arrays" manually.
- Define the following: 
	- $adj(i)$ for the adjacency list of node $i$. 
	- $G=(V,E)$ for the graph formed by representing the equivalence relations as edges.
	- $class(i)$ for the equivalence class $i$ resides in. $NULL$ if it's not in any
- First, initialize $adj(n)$ for all $n\in V$, which is an $O(n)$ operation.
- Initialize an integer value $C\leftarrow 0$ representing the number of equivalence classes identified so far
- For each node $n_k$ do the following:
	- If $class(i)\neq NULL$, then continue.
	- Otherwise, do the following:
		- For each node in $adj(n_k)$, set $class(n_k)$ to $C+1$. Do the same for the nodes in their respective adjacency lists, and again recursively until all the nodes in the equivalence classes are tagged 
		- Increment $C$ by 1
- The check query is similar to the previous implementations discussed but you're instead comparing via $class$ instead of $root$.
- The number of nodes is $O(n)$, and the inner recursive loop is $\Theta(n)$ (goes through all edges twice). Therefore, our total runtime is $\Theta(n)$.


# Exercises
1. Prove that by linking trees in a forest as specified in the "Natural Correspondence of Forest Nodes" (NCFN) does indeed form a binary tree.
2. Provide an implementation of the NCFN algorithm in a.) Python b.) C
3. Prove the correctness of your NCFN implementation
4. Determine, with proof, the time complexity of your NCFN implementation
5. Determine, with proof, the time complexity of the optimized (the one in the lecture) equivalence check implementation

# My Answers for the Exercises
1. In a binary tree, all leaves have degree 1. The root has degree 1 or 2 (unless it's the only node in the tree, i.e. trivial). The internal nodes of a binary tree have degree 2 or 3. 
	a. A connected graph is formed: The first layer of the forest are all connected. By induction, assume that the first to the $k$-th layer of the forest are all connected. In the $(k+1)$-th layer, the first node of each horizontally connected group of nodes (including the "groups" with only one node) is connected to their parent in the $k$-th layer. 
	b. There are no cycles: When first linking the nodes at the same layer and at the same tree, a cycle is formed between the following triples of nodes: the parent node and two consecutive sibling nodes. In the removing edges part, it removes 1 or 2 edges in all cycles, satisfying the required property.
	c. Root node: The root of the first tree will serve as the root node of the binary tree since it's connected to its first child and the root of the 2nd tree, if any.
	d: Leaves: The last leaves of every tree will serve as the leaves because they're only connected to their previous sibling node, which would be their parent in the binary tree.
	e: Internal vertices: This applies for the rest of the nodes in the forest that don't fall in either of the two categories. Their previous sibling node would serve as the parent, and their next sibling node and first child node would be their children in the binary tree.
2. To follow
3. To follow
4. To follow
5. $\Theta(n)$
a. Union operation: This is $\Theta(1)$ and is done for every equivalence.
b. Find operation: We claim that the equivalence class fragments/final trees have height $\Theta(1)$, and therefore the operation has $\Theta(1)$ runtime. Consider the equivalence $i \equiv j$ and the following cases:
	- $i$ and $j$ are isolated nodes: the find operation doesn't take long to finish since they're already roots. The union operation makes a tree with height 1.
	- $i$ and $j$ belong to equivalence class fragments with height 1, Again, the find operation doesn't take long to finish since again it's not a deep tree. The union operation makes a tree with height 2.
	 - Inductive case (assume all classes/fragments have height 1). We consider the worst case where $i$ and $j$ already belong to distinct fragments. The find operation makes a tree with height 3. Such class or fragment tree cannot have a higher height since an equivalence or check query could compress the paths involved. 
