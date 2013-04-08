---
layout: post
title: "Cypher basics: filtering with WHERE"
date: 2013-03-16 16:54
comments: true
published: false
categories: [cypher, neo4j, programming]
---
"If [Neo4j] is not the answer, it's the wrong question, my friend... :)" *-Andrés Taylor, Cypherologist*

Of all the things I've covered so far, `where` is certainly the least foreign for people remotely familiar with SQL. However, there are some really neat things you can do in `where` in Cypher--after all, Cypher supports collection data types and `patterns-as-predicates` that SQL only dreams about supporting natively. I'll start with a quick introduction for non-SQL practitioners, first.

The `where` clause is used to filter results--it doesn't get more simple than that. You might have 100 results, but as soon as you add `where 1=2`, your result count will go to 0, guaranteed.

The interesting parts...
