---
layout: post
title: "Cypher: longestPath?"
date: 2013-03-16 01:01
comments: true
categories: [cypher, neo4j, programming]
---
Most people want to find the shortest path between two nodes, which is why there's a `shortestPath` function in Cypher. However, there are some use cases where you want to find the longest path while doing a variable length path pattern match. This is why: say I want to see the latest updates in my status list (organized in a linked list of nodes). A first attempt at this query might look like:

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/hxo3rl">See it in the console</a>
```
start people=node:node_auto_index('name:(me you)') 
match people-[:status]->status, p=status-[:next*]->() 
return nodes(p) as statuses;
```
</div>
<!-- more -->

The problem here is that you get more results than you want. It looks like the status lists you want are there, but you only want one per person, and you want them to be 0-3 statuses each (for example). It's currently matching all of the possible paths of various lengths, so a-->b, a-->b-->c, and so forth. Let's try again, this time we'll check to make sure that our last node in the path is really a "last" node, by which I mean, it has no outbound relationships:

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/lqir32">See it in the console</a>
```
start people=node:node_auto_index('name:(me you)') 
match people-[:status]->status, p=status-[:next*]->last
where not(last-->())
return nodes(p) as statuses;
```
</div>

This is close, we got it down to one record per person, but it's not quite to the specification because we're going over our desired length. Let's try again using a length range on the path pattern.

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/d9d4pw">See it in the console</a>
```
start people=node:node_auto_index('name:(me you)') 
match people-[:status]->status, p=status-[:next*0..2]->last
where not(last-->()) or length(p) = 2
return nodes(p) as statuses;
```
</div>

The length of a path is actually the number of relationships in it, as opposed to the number of nodes, so with a limit of 2 for the length, and a limit of 2 for the path pattern, we get our three statuses, instead of the full five.

[Original question here](https://groups.google.com/forum/?fromgroups=#!topic/neo4j/BHaSNfU1q0o) (question and some of the answer credit goes to [@aseemk](https://twitter.com/aseemk)).

If you want to see more Cypher stuff, check out my [Cypher page](/cypher).
