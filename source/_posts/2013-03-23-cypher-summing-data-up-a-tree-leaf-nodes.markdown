---
layout: post
title: "Cypher: Summing data up a tree (leaf nodes)"
date: 2013-03-23 16:53
comments: true
categories: [cypher, neo4j, programming]
---
Say I have a tree where values are stored in the leaf nodes. I want to find the sum of values under a particular node (and, if that node itself is a leaf node, its value). As usual, let's start by building an example graph.

<!-- more -->
<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/qci94h">See it in the console</a>
```
create (root {name:"root", val:0}), (a {id:"a", val:1}), 
(b {id:"b", val:2}), (c {id:"c", val:3}), (d {id:"d", val:4}), 
(e {id:"d", val:5}), (f {id:"f", val:6}), (g {id:"g", val:7}), 
(h {id:"h", val:8}), (i {id:"i", val:9}), (j {id:"j", val:10}), (k {id:"k", val:11}),
root-[:parent_of]->a, root-[:parent_of]->b, a-[:parent_of]->c, a-[:parent_of]->d,
b-[:parent_of]->e, b-[:parent_of]->f, c-[:parent_of]->g, d-[:parent_of]->h,
e-[:parent_of]->i, e-[:parent_of]->j, f-[:parent_of]->k;
```
</div>

Ok, so let's start with an easy query. Here, we'll find all of the leaf nodes and `sum` them. This works for most cases, but doesn't cover the case where the node queried is a leaf node itself. I'm going to use `node(*)` here, to check the values of all of the subtree sums in one query. In a real environment, you would want to query for specific start points instead of the whole graph. The `has()` is just to exclude the reference node since we're using `node(*)`.

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/9ugpig">See it in the console</a>
```
start n=node(*) 
match n-[?:parent_of*]->child 
where has(n.val) and not(child-[:parent_of]->()) 
return n, sum(child.val) as leaf_sum;
```
</div>

We've got to find some way to handle the condition where we want to return only the leaf node value (if we're searching on the leaf node itself). This seems like a good use of `coalesce`--but in order for `coalesce` to work, we need something that will return null if the collection is empty.

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/srxxs6">See it in the console</a>
```
start n=node(*) 
match n-[?:parent_of*]->child 
where has(n.val) and not(child-[:parent_of]->()) 
with n, n.val as n_val, collect(child.val) as leaf_vals 
return n, reduce(acc=coalesce(head(leaf_vals), n_val), x in tail(leaf_vals): acc + x), leaf_vals, n_val;
```
</div>

Here, we've exploited the fact that `head()` will return null if the collection is empty, and used that as the start value in a simple `reduce` sum over the collection. Note that we had to use `tail()` in order to not include the head element during the rest of the calculation. We've left the `leaf_vals` and `n_val` in the `return` to make sure it's working right.

[Original question here](http://stackoverflow.com/questions/15590800/summing-data-up-a-tree-data-structure-in-a-graph-database) It doesn't quite match this, but I tried to make it a bit harder to show off some other cypher functions.

Update: After I posted this, Michael Hunger mentioned that I could improve this by using the `0..` path length specifier (in fact, it solves the exact problem of including the leaf node values when we start the query at a leaf node). It avoids using `reduce` and `coalesce`, but I'll leave my previous example up--if not just as an example of how those functions can be used.

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/5fd0z">See it in the console</a>
```
start n=node(*) 
match n-[:parent_of*0..]->child 
where has(n.val) and not(child-[:parent_of]->()) 
return n, sum(child.val) as leaf_sum;
```
</div>

If you want to see more Cypher stuff, check out my [Cypher page](/cypher).
