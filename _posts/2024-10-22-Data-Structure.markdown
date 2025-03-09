---
layout: post
title:  "Data Structure"
date:   2024-10-22 08:55:25
categories: Data Structure
---

# Priority queue
* It is backed by `heap sort`. Queue could be implemented by array or list.
* `heap sort` is implemented by `Complete binary tree`.

# Binary tree
[Video](https://www.bilibili.com/video/BV1Hy4y1t7ij/?vd_source=a7fd092a349f96808b9e90c887c95aad)

* Binary tree can be considered as a List which has `left` and `right` pointing to another List object.
* `Full binary tree`: has 2^depth - 1 leaves.
* `Complete binary tree`: Comparing to `full binary tree`, only the trailing of leaves can be none.
The depth - 1 level are full binary tree.
* Often, we use `LinkedList` to implement binary tree, but we can also use `array`.
* If the storage of `complete binary tree` is by `array`, then the nodes are positioned like this:
The root is the smallest. The left and right child should both be greater than the root, recursively.
So the first element of the array is the smallest one.
the second element of the array is the left node of root, the third element of the array is the right node of the root. 
The fourth element is at the left node of the second element. The fifth element is at the right node of the second element.
If you want to know the left and right children of a node (indexed at i), then just use this formula:
`2 * i + 1 = left index`, `2 * i + 2 = right index`. 
* `binary search tree` means all the left nodes are always smaller than current node, and all the 
right nodes are greater than current node.
* `Balanced` means all the difference of height between left nodes and right nodes is no more than 1.

## Traversing a binary tree
* To traverse a binary tree, there are `Depth-First Traversal` and `Breadth-First Traversal`.
* `Depth-First Traversal`: traverse till a leaf, then back to previous node and continue doing it.
* `Breadth-First Traversal`: traverse from root layers to leaf layers. Root first.

### Three ways of `Depth-First Traversal`
* `(middle node first) Pre-order traversal`: Middle first, left subtree second, right subtree the last.
* `(middle node second) In-order traversal`: Left subtree first, Middle second, right subtree the last.
* `(middle node last) Post-order traversal`: Left subtree first, right subtree second, the middle node the last.

### `Depth-First Traversal`
level order traversal. 