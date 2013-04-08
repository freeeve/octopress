---
layout: post
title: "Cypher: Finding the sum of properties over a path"
date: 2013-03-16 23:40
comments: true
categories: [cypher, neo4j, programming]
---
Let's say we have properties on our relationships in a path, for example, the distance between two locations. I'll create an example graph. We want to be able to calculate the total of the properties over the graph.

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/jvx343">See it in the console</a>
```
create 
({n:"a"})-[:road {dist:3}]->({n:"b"})-[:road {dist:5}]->({n:"c"})-[:road {dist:12}]->({n:"d"});
```
</div>
<!-- more -->

Here's an initial query, to see our path.

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/i6kbh">See it in the console</a>
```
start a=node:node_auto_index(n="a"), d=node:node_auto_index(n="d") 
match p=a-[r*]-d 
return p;
```
</div>

If your first thought was: "doesn't Cypher have a sum() function?" Indeed it does. However, it's not going to be easy to use it here, treating this as a path. This is one of the ideal use cases for `reduce`. The way `reduce` works, we first set an accumulator variable to a starting value (0 in this case), then, we give it an expression for how the accumulator should be calculated for each item in the collection. When it's done iterating over the collection, it returns the accumulator value. Let's try it out:

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/811v89">See it in the console</a>
```
start a=node:node_auto_index(n="a"), d=node:node_auto_index(n="d") 
match p=a-[r*]-d 
return reduce(acc=0, x in relationships(p): acc + x.dist) as totalDistanceInPath;
```
</div>

There we go, the total distance in a path! `Reduce` can also handle getting averages:

<div>
<a target="_blank" style="position:absolute;right:100px" href="http://console.neo4j.org/r/ol6hq2">See it in the console</a>
```
start a=node:node_auto_index(n="a"), d=node:node_auto_index(n="d") 
match p=a-[r*]-d 
return reduce(acc=0, x in relationships(p): acc + x.dist)/(length(relationships(p))*1.0) as avgDistanceInPath;
```
</div>

You'll note I had to multiply the length by 1.0 in order to get it to do floating point division. I'm hoping one day that won't be necessary, but right now Cypher does integer division between integers.

As a final note: `reduce` is not limited to paths, but it works on any collection type with numerics.

This question has actually been asked in so many forms multiple times, I won't give a source for it!

If you want to see more Cypher stuff, check out my [Cypher page](/cypher).
