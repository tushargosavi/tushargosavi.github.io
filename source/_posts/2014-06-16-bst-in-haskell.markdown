---
layout: post
title: "BST in Haskell"
date: 2014-06-16 11:57:33 +0530
comments: true
categories: haskell
---

I have just started to learn Haskell from various sources, such as
[Lean You Haskell][lyh] and [Real World Haskell][rwh]. After reading
few chapters, this is my first attempt to code a [Binary Search Tree][bst] in
Haskell, supporting add, search and traverse operation.

A node is Binary Search Tree contains a comparable key and left and
right subtrees, which can be empty. The left tree contains
smaller keys and right tree contains bigger keys that the node's key.

The Tree is recursively defined as a Nil tree i.e a empty tree, or a Node with key, left and right subtrees. This recursive
data definition is specified in Haskell easily using abstract data type as follows.

```haskell
data Tree a = Node a (Tree a) (Tree a)
      | Nil
      deriving(Show, Eq)
```

## Insert

To add a key in a BST is easy, if tree is empty, then after adding
one key is equivalent of creating a new Node with left and right tree
set to Nil, and data set to the key.

If new key is smaller than current node's key then add a key to the left
tree, else add the key to right tree.

The following function implements the insert method which takes a Tree
and key to be added to the tree. If key is already present in the Tree
then this function returns the same Tree, else returns a new Tree with
key added. As Haskell is purely functional language, we can not modify
original tree, instead it creates a new tree every time a key is added
to the tree.

```haskell
{-
Add a node to Tree, If element already present in the Tree,
then it is ignored.
-}
insert :: (Ord a) => Tree a -> a -> Tree a
insert Nil elem = Node elem Nil Nil
insert (Node val left right) elem
  | elem < val = Node val (add left elem) right
  | elem > val = Node val left (add right elem)
  | otherwise = Node val left right
```

If we want to add bunch of keys to the Tree, then built in fold
function can be used, A one line function to add list of keys to
the Tree is

```haskell
list2Tree = foldl add Nil
```

Let's try these functions in ghci session.

```	
*Main> let a = [30, 10, 45, 4, 8, 69, 85] 
*Main> let tree = list2Tree a
*Main> tree
Node 30 (Node 10 (Node 4 Nil (Node 8 Nil Nil)) Nil) (Node 45 Nil (Node 69 Nil (Node 85 Nil Nil)))
```
  
Inserting a new node
```
*Main> tree
Node 30 (Node 10 (Node 4 Nil (Node 8 Nil Nil)) Nil) (Node 45 Nil (Node 69 Nil (Node 85 Nil Nil)))
*Main> let newtree =  insert tree 35
*Main> newtree
Node 30 (Node 10 (Node 4 Nil (Node 8 Nil Nil)) Nil) (Node 45 (Node 35 Nil Nil) (Node 69 Nil (Node 85 Nil Nil)))
```

## Search

Searching in binary tree starts at the root node and then recursively
follow left or right branch until we reach at empty tree (Nil) or
search key is found. The function is split into two cases.

+ If current root is Nil then we have reached at the leaf node, and element
  does not exists in the tree, so we return false. (line 3)
+ If current root is not Nil and key matches then element present in the tree,
  else we recursively call search with left or right node depending on value of 
  key. (line 5,6,7)


```haskell
{- Search a tree -}
search :: (Ord a) => Tree a -> a -> Bool
search Nil _ = False
search (Node elem left right) a
  | elem == a = True
  | elem > a  = search left a
  | otherwise = search right a
```

Lets try out search on some of the keys.
```
*Main> search newtree 85
True
*Main> search newtree 80
False
```

## Traversing elements in the tree.

Apart from Breath first and depth first traversal, binary tree can be
traverse in pre-order, post-order and in-order traversal. In order
traversal visit elements in the sorted order. Following is the
implementation in Haskell which returns a list of element in order
they are visited in the traversal.

* Pre-order 
  - Visit the root.
  - Traverse the left subtree.
  - Traverse the right subtree.

```haskell  
{- Pr-eorder tree traversal -}
preOrder :: Tree a -> [a]
preOrder Nil = []
preOrder (Node a left right) =
  a : preOrder left ++ preOrder right
```


* In-order (symmetric)
  - Traverse the left subtree.
  - Visit the root.
  - Traverse the right subtree.

```haskell
{- In-order tree traversal -}
inOrder :: Tree a -> [a]
inOrder Nil = []
inOrder (Node a left right) =
  inOrder left ++ [a] ++ inOrder right
```

* Post-order
  - Traverse the left subtree.
  - Traverse the right subtree.
  - Visit the root.

```haskell
{- Post-order tree traversal -}
postOrder :: Tree a -> [a]
postOrder Nil = []
postOrder (Node a left right) =
  postOrder left ++ postOrder right ++ [a]
```

Lets try these functions, see that in-order traversal of BST gives
element in sorted order.
```
*Main> preOrder newtree
[30,10,4,8,45,35,69,85]

*Main> postOrder newtree
[8,4,10,35,85,69,45,30]

*Main> inOrder newtree
[4,8,10,30,35,45,69,85]
```

## Find Max and Min

The maximum element is the right most element in the tree, i.e keep following
right branch until node's right branch is Nil. That node has the maximum value
of the key.


```haskell
maxElem :: Tree a -> Maybe a
maxElem Nil = Nothing
maxElem (Node elm _ Nil) = Just elm
maxElem (Node a _ right) = maxElem left
```

Similarly the minimum element is found by following left branch.

```haskell
minElem :: Tree a -> Maybe a
minElem Nil = Nothing
minElem (Node a Nil _) = Just a
minElem (Node _ right _) = minElem right
```

Let's try this out in ghci.
```
*Main> minElem newtree
Just 4

*Main> maxElem newtree
Just 85
```

That's it for now, In this post we have implemented a simple binary tree supporting basic operations except delete for which I am planing
to write a separate post. Haskell language is very concise, and because of pattern matching and good support for writing recursive functions
, most of the recursive algorithms on binary tree are translated directly into Haskell without much effort.

The code for binary search tree is available on [Github][code]



[lyh]: http://learnyouahaskell.com/
[rwh]: http://book.realworldhas
[bst]: http://en.wikipedia.org/wiki/Binary_search_tree
[code]: https://github.com/tushargosavi/haskell-learn/blob/master/btree.hs