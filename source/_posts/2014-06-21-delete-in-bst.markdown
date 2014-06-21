---
published: false
layout: post
title: "Delete in BST"
date: 2014-06-21 12:44:45 +0530
comments: true
categories: 
---

delete

```haskell
delete :: (Ord a) => Tree a -> a -> Tree a
delete (Node a Nil Nil) key
  | a == key = Nil
  | otherwise = Node a Nil Nil
delete (Node a Nil right) key
  | a == key  = right
  | otherwise = Node a Nil (delete right key)
delete (Node a left Nil) key
  | a == key = left
  | otherwise = Node a (delete left key) Nil
delete (Node a left right) key
  | a == key = Node maxLeft (delete left maxLeft) right
  | a > key  = Node a (delete left key) right
  | otherwise = Node a left (delete right key)
  where
    maxLeft = maxElemNonNil left
    maxElemNonNil (Node a _ Nil) = a
    maxElemNonNil (Node a _ left) = maxElemNonNil left
```
