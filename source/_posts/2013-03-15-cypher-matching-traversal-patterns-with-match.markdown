---
layout: post
title: "Cypher basics: Matching Traversal Patterns with MATCH"
date: 2013-03-15 08:35
comments: true
categories: [cypher, neo4j, programming]
---
"Because friends don’t let friends write atrocious recursive joins in SQL." *-Max De Marzi*

The `match` clause is one of the first things you learn with Cypher. Once you've figured out how to look up your starting *bound identifiers* with `start`, you usually (but not always) want to `match` a traversal pattern, which is one of Cypher's most compelling features.

The goal of this post is not to go over the syntax for all of the different cases in `match`--for that the docs do a good job: <a target="_blank" href="http://docs.neo4j.org/chunked/snapshot/query-match.html">Cypher MATCH docs</a>. Rather, I hoped to explain more the *how* of how `match` works.
<!-- more -->

First, you need to understand the difference between *bound* and *unbound* identifiers (sometimes we call them *variables*, too, in case I slip up and forget to be consistent). *Bound identifiers* are the ones that you know the value(s) of--usually you set these in the `start` clause, but sometimes they're passed through with `with`. *Unbound identifiers* are the ones you don't know the values of: the part of the pattern you're matching. If you don't specify an identifier, and instead just do `a-->()`, or something of that sort, an implicit *unbound identifier* is created for you behind the scenes, so Cypher can keep track of the values it's found. The goal of the `match` clause is to find real nodes and relationships that match the pattern specified (find the unbound identifiers), based on the *bound identifiers* you have from the `start`.

Although it's not the primary use case, the `match` clause can be used to narrow your results (this will only return something if `a` and `b` match the pattern). (note: this `match` clause would work the same if we used `where` instead of `match`)

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/9pckms">See it in the console</a>
```
start a=node(1), b=node(2)
match a-->b
return a, b;
```
</div>

Typically, though, the `match` is used to find patterns that match. You can specify *unbound identifiers* that you want to be able to refer to after the pattern is matched, or not. Here's an example without any named *unbound identifiers*.

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/qrd7sd">See it in the console</a>
```
start a=node(1)
match a-->()-->()
return a;
```
</div>

In this case, it actually returns two `a` nodes, because that pattern exists for more than one path going out from `a`.

Here's an example of a `match` pattern with unbound identifier names that we can refer to:

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/qai89i">See it in the console</a>
```
start a=node(1)
match p=a-[r]->b-[r2]->c
return *;
```
</div>

Wow, we got a lot of results out of that query. We got `p`, a path (paths are really just collections of nodes and relationships, in the order that they are linked together); `a`, the *bound identifier*; `r`, the relationship between `a` and `b`; `b`, the node connected to `a` via `r`; `r2`, the relationship connecting `b` to `c`, and finally, `c` itself, the node connected to `b` via `r2`. The only *bound identifier* we had was `a`, the rest were *unbound identifiers*. I'll leave it up to your imagination what you could do with all of these nice values we've named in our `match` (and I'll post about it later).

You can also specify multiple patterns in the `match` clause. For example, you can have a pattern like `a<--b-->c, c-->a`. Only one node needs to be bound, but it doesn't matter which one--both of these `match` patterns need to match in order for the values to pass to the next part of the query in Cypher, because they're connected by `c` and `a`.

As powerful as Neo4j is, if you have a very interconnected graph, and you send it a query with an unbounded variable path pattern `a-[*]-b` without a `limit` clause, you're going to bring your server to its knees while it literally scans every single path in your database (each node multiple times). So, a good practice is to *always* set a reasonable bound for your upper limit on a path match (if possible), like `a-[*1..5]-b`.

One other thing to remember while using `match` is that once an *unbound identifier* has claimed a node or rel, that node or rel won't be claimed by another *unbound identifier* for that record. So if you have `a<--b-->c, b-->d`, and your *bound* identifier is `b`, `a`, `c`, and `d` will all be unique nodes for each record passed on from `match`.

### A bit about Cypher's query optimization as it pertains to `match` and `where`
Cypher is getting smarter with each milestone release--now it tries to calculate predicates from the `where` while it's matching patterns in `match` (thus, not traversing paths that aren't going to match the `where` conditions), if it can. There are still some cases that it can't, in particular, if you're passing values through a `with`, or if you're using a path identifier in your `where`. Hopefully, these cases will be handled soon, so we can write queries without worrying about optimizing around some of these cases. 

Cypher in 1.9 also has a powerful bidirectional matcher for patterns that have bound identifiers on the ends (which is a pretty common use case). It tries to find a path between the nodes from both sides simultaneously, thus lessening the chance that a supernode (a node with a lot of relationships) will slow down the query.

The `match` and `where` clauses currently don't use the index features of Neo4j. For most use cases, this is fine--you use `start` to look up values via the index, and do traversals based on the `match` and `where`. This confuses some people, because they expect indexes to work as they do in a relational database (where everything is filtered in the `where` clause, so it must use the index to be performant). I'll post later about the current state of indexes in Neo, but just remember that in 1.9 or less, your indexes will only be used when you explicitly state that you want to use them (in the `start` clause). 

**For the best Cypher experience, use the milestone versions if possible! Cypher is improving by leaps and bounds for every milestone release.**

If you want to see more Cypher stuff, check out my [Cypher page](/cypher).
