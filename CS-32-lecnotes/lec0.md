
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
- Link all the nodes that are in the same level
- Remove the edges between each node and their parent (except if they go first in the ordering)
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


# Exercises
1. Prove that by linking trees in a forest as specified in the "Natural Correspondence of Forest Nodes" (NCFN) does indeed form a binary tree.
2. Provide an implementation of the NCFN algorithm in a.) Python b.) C

2a. Prove the correctness of your implementation.

2b. What is the time complexity of your implementation?
