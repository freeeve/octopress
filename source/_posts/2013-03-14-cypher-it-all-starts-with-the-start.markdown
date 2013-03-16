---
layout: post
title: "Cypher basics: it all starts with the START"
date: 2013-03-14 23:39
comments: true
categories: [neo4j, cypher, programming]
---
"It all starts with the START" *-Michael Hunger, Cypher webinar, Sep 2012*

The `start` clause is one of those things that seems quite simple initially. You specify your start point(s) for the rest of the query. Typically, you use an index lookup, or if you're just messing around, a node id (or list of node ids). This sets the stage for you to `match` a traversal pattern, or just filter your nodes with a `where`. Let's start with a simple example--here we're going to find a single node, and return it (later we'll get into why `start` is sort of like a SQL `from`):
<!-- more -->

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/2yznco">See it in the console</a>
```
start n=node(1)
return n;
```
</div>

This syntax specifies a node id. We've defined a *bound identifier* named `n`, and set it to the node with the node id of 1. A *bound identifier* can be used in `match` patterns to find *unbound identifiers*, but I'll leave a full explanation of that for another post.  We could also specify the node_auto_index (which is enabled for all fields by default in console.neo4j.org), like so:

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/v9intl">See it in the console</a>
```
start n=node:node_auto_index('name:Neo')
return n;
```
</div>

This asks the lucene index behind the "auto index" to search for a node with a property `name`, indexed with a value of `Neo`. If you have a space in your query, you'll want to specify it with double quotes, like `'name:"The Architect"'` <a href="http://console.neo4j.org/r/tbs1hj" target="_blank">see in console</a>. Cypher is quote agnostic, but lucene tends to like double-quotes, so I try to remember to use single-quotes around the whole index lookup. You can also use much of the query syntax from lucene 3.5 (at least for Neo4j 1.9). Check out <a href="http://lucene.apache.org/core/old_versioned_docs/versions/3_5_0/queryparsersyntax.html" target="_blank">the 3.5 lucene query syntax</a>. For example, here's a wildcard search:

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/s48x02">See it in the console</a>
```
start n=node:node_auto_index('name:M*')
return n;
```
</div>

You can also do `AND` and `OR` syntax in lucene, like:

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/31ycyp">See it in the console</a>
```
start n=node:node_auto_index('name:M* OR name:N*')
return n;
```
</div>

Or you can use the shortcut syntax--surrounding your match terms with parentheses, it takes it as a list (apparently you don't even need commas!): 

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/ygv3cn">See it in the console</a>
```
start n=node:node_auto_index('name:(M* N*)')
return n;
```
</div>

You can also do range queries, like: 

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/g0ypje">See it in the console</a>
```
start n=node:node_auto_index('name:[Cypher TO Neo]')
return n;
```
</div>

I even had the idea of doing range queries on timestamps stored as strings. If you're storing dates before 2001/9/9, you probably should store them with 0 padding up to 13 digits for milliseconds or 10 digits for seconds. Then you can set up a query like this, say, for example, for the last 24 hours (these are epoch time seconds, as opposed to millis). In lucene 3.5, the support for range queries with numbers isn't there--it looks like that support was added in lucene 4, so maybe someday this idea will be obsolete:

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/kmyfuf">See it in the console</a>
```
create (a {ts:"1363323368"}), 
       (b {ts:"1363323534"}), 
       (c {ts:"1363373534"}), 
       (d {ts:"0363323368"});
start n=node:node_auto_index('ts:[1363237233 TO 1363323633]') 
return n;
```
</div>

Ok, that's all very exciting. But what might you not know about `start`? Well, for one thing, each *bound identifier* that you specify in the `start` clause sort of becomes a table in a SQL `from`. A *TABLE??* some might say--this is a graph database! Yes, well, Cypher is sort of a bridge between the graph and tabular results, so it makes sense that even early on, it starts to feel a lot like SQL. What do you think might happen with this query? We've defined two *bound identifiers*:

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/qg4ud7">See it in the console</a>
```
start a=node(1,2), b=node(3,4)
return *;
```
</div>

Wait, why are the results all repeated? This is because each identifier in the start clause is sort of its own table. When there are multiple nodes/rels bound to a single identifier, and you have more than one identifier in `start`, you end up with the cartesian product--just like you would if you forgot to specify a foreign key between two tables in the `from` in SQL (I'm sure anyone who's spent a fair amount of time writing SQL by hand has done this more than they can count). In any case, what we need to do, in Cypher, rather than specify foreign keys (assuming we didn't really want the cartesian product), is specify some connection between the nodes--even just an optional connection. I've added a match below--now it uses the relationship `r` (an *unbound identifier*) to link together the nodes in `a` and `b`.

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/w7n2gs">See it in the console</a>
```
start a=node(1,2), b=node(3,4)
match a-[r]->b 
return *;
```
</div>

That's probably enough on `start` for now. I left out non-auto indexes, but really they work basically the same as auto indexes while querying in `start`. And, I totally left out the fact that you can also specify `relationships` in the `start` clause, which is sometimes a handy feature, although I've found limited use for it in my queries. Oh, one tidbit I learned about lucene is that it can only handle 512 terms in a single `OR` list. Kind of lame if you're trying to a big list of lookups--you end up needing to do multiple queries in batches of 512. I think this limit was actually increased in the latest versions of lucene, so maybe when Neo4j upgrades its lucene backend, we'll be pleasantly surprised with a higher limit.

**Don't forget to use parameters in your Cypher queries to boost your perfomance!**

If you want to see more Cypher stuff, check out my [Cypher page](/cypher).
