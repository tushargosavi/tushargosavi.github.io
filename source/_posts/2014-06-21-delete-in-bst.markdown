---
published: false
layout: post
title: "Delete in BST"
date: 2014-06-21 12:44:45 +0530
comments: true
categories: 
---

In the [previous post][post1], we implemented a simple Binary Search Tree(BST) and added functionality to
add/search and traverse binary tree. In this post we will add functionality to remove
keys from BST. Deleting a key in binary tree is little difficult, to delete a node
from tree we need to consider following cases.

1. The Tree is Nil, Then after deleting resulting tree is Nil.
2. If the Node has matching key and it's left and right children are Nil, then resulting tree is Nil tree.
3. If nodes right children in Nil
   - If key matches the resulting tree is left tree.
   - else if node's key is bigger then try deleting search key from left tree
   - otherwise search key do not exists in tree, tree remains as it is.
4. Symmetric to case 3, for left children.
5. If node does not have matching key, and node's key is bigger than search key, then try deleting from left subtree, else try deleting from right subtree.
6. The complex case is when, Node key is matching and it's right and left subtrees are not Nil, In this case we will pick up maximum key from left subtree and replace it with current node, and delete maximum key from the left tree.

The resulting Haskell implementation is below.

```haskell
delete :: (Ord a) => Tree a -> a -> Tree a
-- case 1
delete Nil _ = Nil
-- case 2
delete (Node a Nil Nil) key
  | a == key = Nil
  | otherwise = Node a Nil Nil
-- case 3
delete (Node a Nil right) key
  | a == key  = right
  | a < key = Node a Nil (delete right key)
  | otherwise = Node a Nil right
-- case 4
delete (Node a left Nil) key
  | a == key = left
  | a > key = Node a (delete left key) Nil
  | otherwise = Node a left Nil
-- case 5 and 6  
delete (Node a left right) key
  | a == key = Node maxLeft (delete left maxLeft) right
  | a > key  = Node a (delete left key) right
  | otherwise = Node a left (delete right key)
  where
    maxLeft = maxElemNonNil left
    maxElemNonNil (Node a _ Nil) = a
    maxElemNonNil (Node a _ left) = maxElemNonNil left
```

A ghci session to test delete function.
```
*Main> let a = [10, 2, 13, 5, 18, 28, 27, 95, 73, 75, 49, 93]
*Main> let tree = list2Tree a
*Main> tree
Node 10 (Node 2 Nil (Node 5 Nil Nil)) (Node 13 Nil (Node 18 Nil (Node 28 (Node 27 Nil Nil) (Node 95 (Node 73 (Node 49 Nil Nil) (Node 75 Nil (Node 93 Nil Nil))) Nil))))
*Main> delete tree 73
Node 10 (Node 2 Nil (Node 5 Nil Nil)) (Node 13 Nil (Node 18 Nil (Node 28 (Node 27 Nil Nil) (Node 95 (Node 49 Nil (Node 75 Nil (Node 93 Nil Nil))) Nil))))
```

[post1]: /blog/2014/06/16/bst-in-haskell/

