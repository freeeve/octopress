---
layout: post
title: "The Mythical WITH (Neo4j's Cypher query language)"
date: 2013-03-11 17:15
comments: true
categories: [neo4j, cypher, programming]
---
Coming from SQL, I found Cypher a quick learn. The `match` was new, and patterns were new, but everything else seemed to fit well with SQL concepts. Except `with`, the way to build a sort of sub-query--it seemed hard to wrap my head around. So, what really happens behind the scenes with a `with` clause in your query? How does it work? It turns out, almost any complex query ends up needing a `with` in it, but let's start with a simple example.
<!-- more -->

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/d2dhn1">See it in the console</a>
```
START n=node(*)
WITH n as n_alias
RETURN n_alias
```
</div>

This is pretty self explanatory. We're simply using `with` to alias our `n` node. This actually does come in handy sometimes, when you have functions in your return statement and you'd rather not retype them everytime you want to refer to them. And it can be necessary to alias things, like when trying to aggregate aggregates. Cypher needs to know which aggregate you want to calculate first, so you can alias that one, and then pass it along with a `with`. 

Ok, this is going to sound contrived, but let's say I want to build a collection of 2-tuples (collections of size 2), where the inner collections contain the node, and the median of the id numbers of the nodes they are connected to.

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/os41t4">See it in the console</a>
```
START n=node(*)
MATCH n-->m
RETURN collect([n, percentile_cont(id(m), .5)])
Error: Can't use aggregate functions inside of aggregate functions.
```
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/jk3f2v">See it in the console</a>
```
START n=node(*) 
MATCH n-->m 
WITH n, percentile_cont(id(m), .5) as median_id 
RETURN collect([n, median_id])
```
</div>

What would you use the median id for? No idea.

So, how does it work? Well, `with` is basically just a stream, as lazy as it can be (as lazy as `return` can be), passing results on to the next query. You can also do `skip` and `limit` after `with`, as well as `start` to lookup new values. The values you lookup in the `start` following `with` will be combined as if they were all part of the same `start` (cartesian product unless linked together via `match`). 

What if we have a lot of data to go through in a multi level match, but we only want to see the top N matches for the first level, and don't really care about the following levels for those below the top N. Hmm, recommendation queries come to mind here. You could do something like this:

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/pretty_graph">See it in the console (it's a pretty graph!)</a>
```
START n=node:node_auto_index("name:start")
MATCH n-[r1]->m
WITH n, r1, m
ORDER BY r1.score desc
LIMIT 3
MATCH m-[r2]->o
RETURN n, r1, m, r2, o;
```
</div>

In this case, we use `with` as a filter by sorting the results and taking only the top 3 scores before continuing on to match the next level. This sort of query can cut down on the number of nodes touched, compared to doing it all in one match and scanning them all.

So, what does this one below do? Can you use `n` after the `with` in this case? See the console to find out... (or read on).

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/ydxht6">See it in the console</a>
```
START n=node(*)
WHERE 1=2
WITH n
RETURN n;
```
</div>

`with` doesn't pass anything on, if it doesn't get anything.

How about this one, does it `create` a new node as desired?

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/cf8x40">See it in the console</a>
```
START neo=node(1)
MATCH anyone-[?:LIKES]->neo
WITH neo, count(anyone) as cnt
WHERE cnt = 0
CREATE (anyone {name:"anyone"})-[:LIKES]->neo
RETURN anyone;
```
</div>

Yes! This is because `count` returns a count, even if it's 0. This is a good trick to know. You can often find good ways to pass things via `with` in `count` or `collect`, so that `with` will always pass something on.

Here's a tricky one. What if we want to return our `anyone` node if it exists, but `create` it if not, and `return` it either way. Wow, two `with`s...

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/fnbtn9">See it in the console</a>
```
START neo=node:node_auto_index("name:Neo")
MATCH anyone-[?:LIKES]->neo
WITH neo, count(anyone) as cnt
WHERE cnt = 0
CREATE (anyone {name:"anyone"})-[:LIKES]->neo
WITH 1 as dummy
START anyone=node:node_auto_index("name:anyone")
RETURN anyone;
```
</div>

No, this doesn't actually work. As soon as you create one, the `WHERE cnt = 0` no longer comes back as true. In 2.0 a conditional expression similar to SQL's `case/when` syntax will help us get there. It might even be solved with a better `create unique` syntax with labels. Until then, you might have to make two queries--one to `create` (conditionally), and one to `return`.

Ok, I guess that's enough rambling for now. Maybe I'll have a part 2 for `with` later.

If you want to see more Cypher stuff, check out my [Cypher page](/cypher).
