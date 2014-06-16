---
layout: post
title: "BST in Haskell"
date: 2014-06-16 11:57:33 +0530
comments: true
categories: haskell
---

I have just started to learn Haskell from various sources, such as [Lean You Haskell][lyh] and
[Real World Haskell][rwh]. After reading few chapters this is my first attempt to code a
Binary Search Tree in haskell, supporting add, search and delete operation.


{% highlight haskell %}
data Tree a = Node a (Tree a) (Tree a)
      | Nil
      deriving(Show, Eq)
      
{% endhighlight %}


[lyh]: http://learnyouahaskell.com/
[rwh]: http://book.realworldhas

