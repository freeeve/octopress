---
layout: post
title: "Cypher: Most Recent Common Ancestor"
date: 2013-03-16 14:24
comments: true
categories: [cypher, neo4j, programming]
---
We've got a tree structure with parents and children, and we want to figure out, starting from two children, which ancestor is the most recent common ancestor (MRCA). Let's start by constructing an example graph (I usually just type this out into the console!). The root will be the common ancestor of all the others, so we'll always get an answer in this case.
<!-- more -->

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/jp1rlt">See it in the console</a>
```
create (root {n:"root"}), (a {n:"a"}), (b {n:"b"}), (c {n:"c"}), 
(d {n:"d"}), (e {n:"e"}), (f {n:"f"}), (g {n:"g"}), (h {n:"h"}), 
(i {n:"i"}), (j {n:"j"}), (k {n:"k"}), (l {n:"l"}), (m {n:"m"}), 
(n {n:"n"}), (o {n:"o"}), (p {n:"p"}),
root-[:parent_of]->a, root-[:parent_of]->b, a-[:parent_of]->c, 
a-[:parent_of]->d, b-[:parent_of]->e, b-[:parent_of]->f,
c-[:parent_of]->g, c-[:parent_of]->h, d-[:parent_of]->i, 
d-[:parent_of]->j, e-[:parent_of]->k, e-[:parent_of]->l, 
f-[:parent_of]->m, f-[:parent_of]->n, g-[:parent_of]->o, 
g-[:parent_of]->p;
```
</div>

So, an initial attempt might be something like this, where we find common ancestors via variable length paths--let's try it with nodes `p` and `h` first:

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/tsc6u5">See it in the console</a>
```
start p=node:node_auto_index(n="p"), h=node:node_auto_index(n="h")
match p<-[:parent_of*]-common_ancestor-[:parent_of*]->h
return common_ancestor
```
</div>

Cool, well, we got our three ancestors. How can we figure out which one is the most recent? In reality, this is as easy as sorting by the length of the returned path! So we define a `path` variable so we can check the length of the whole path pattern we're matching easily. Then we just `limit` by one, and we're done.

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/6c3jer">See it in the console</a>
```
start p=node:node_auto_index(n="p"), h=node:node_auto_index(n="h")
match path=p<-[:parent_of*]-common_ancestor-[:parent_of*]->h
return common_ancestor as most_recent_common_ancestor
order by length(path)
limit 1
```
</div>

Try it for other children!

The original question was posted on <a target="_blank" href="http://stackoverflow.com/questions/15370547/can-i-use-cypher-to-guarantee-mrca-of-a-tree-structure/15378442#15378442">Stack Overflow</a>.

If you want to see more Cypher stuff, check out my [Cypher page](/cypher).
