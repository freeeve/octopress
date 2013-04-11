---
layout: post
title: "Cypher: It doesn't all start with the START (in Neo4j 2.0!)"
date: 2013-04-10 21:50
comments: true
categories: [cypher, neo4j, programming]
---
So, apparently, the Neo Technology guys read one of my last blog posts titled "It all starts with the START" and wanted to make a liar out of me. Actually, I'm quite certain it had nothing at all to do with that--they are just wanting to improve Cypher to make it the best graph query language out there. But yes, the `START` clause is now optional. "How do I tell Neo4j where to start my traversals", you might ask. Well, in the long run, you won't need to anymore. Neo4j will keep index and node/rel statistics and know which index to use, and know which start points to use to make the `match` and `where` the most efficient query based on its cost optimization. It's not quite there yet, so for a while we'll probably want to make generous use of "index hints", but I love the direction this is going--feels just like the good old SQL.

I have a feeling this will be a long post, so bear with me. With a fresh unzip of the tar file... let's get dirty. I'll just be running in shell, since some of the index stuff can't be saved in console yet. This is a beautiful thing:

```
neo4j-sh (0)$ match n return n;
+-----------+
| n         |
+-----------+
| Node[0]{} |
+-----------+
1 row
14 ms
```
<!-- more -->

That pesky `start` clause was a lot more typing! Let's try creating a graph I've used before, and see how it works. Here's that pretty graph that builds a tree, for the MCRA query (I'm adding a label to each of the nodes called Node--yes, I know, not a very original name):

```
create (root:Node {n:"root"}), (a:Node {n:"a"}), (b:Node {n:"b"}), (c:Node {n:"c"}), 
(d:Node {n:"d"}), (e:Node {n:"e"}), (f:Node {n:"f"}), (g:Node {n:"g"}), (h:Node {n:"h"}), 
(i:Node {n:"i"}), (j:Node {n:"j"}), (k:Node {n:"k"}), (l:Node {n:"l"}), (m:Node {n:"m"}), 
(n:Node {n:"n"}), (o:Node {n:"o"}), (p:Node {n:"p"}),
root-[:parent_of]->a, root-[:parent_of]->b, a-[:parent_of]->c, 
a-[:parent_of]->d, b-[:parent_of]->e, b-[:parent_of]->f,
c-[:parent_of]->g, c-[:parent_of]->h, d-[:parent_of]->i, 
d-[:parent_of]->j, e-[:parent_of]->k, e-[:parent_of]->l, 
f-[:parent_of]->m, f-[:parent_of]->n, g-[:parent_of]->o, 
g-[:parent_of]->p;
+-------------------+
| No data returned. |
+-------------------+
Nodes created: 17
Relationships created: 16
Properties set: 17
Labels added: 17
```

The old query was like this:
```
start p=node:node_auto_index(n="p"), h=node:node_auto_index(n="h")
match p<-[:parent_of*]-common_ancestor-[:parent_of*]->h
return common_ancestor;
```

Now we can do this:
```
match p:Node<-[:parent_of*]-common_ancestor:Node-[:parent_of*]->h:Node
where p.n='p' and h.n='h'
return common_ancestor;
+--------------------+
| common_ancestor    |
+--------------------+
| Node[18]{n:"root"} |
| Node[19]{n:"a"}    |
| Node[21]{n:"c"}    |
+--------------------+
3 rows
74 ms

Profile data:
ColumnFilter(symKeys=["  UNNAMED12", "  UNNAMED48", "common_ancestor", "p", "h"], returnItemNames=["common_ancestor"], _rows=3, _db_hits=0)
Filter(pred="(((Property == Literal(p) AND Property == Literal(h)) AND hasLabel(p, [Node])) AND hasLabel(h, [Node]))", _rows=3, _db_hits=6)
  PatternMatch(g="(common_ancestor)-['  UNNAMED48']-(h),(common_ancestor)-['  UNNAMED12']-(p)", _rows=3, _db_hits=374)
      Filter(pred="(hasLabel(common_ancestor, [Node]) AND hasLabel(common_ancestor, [Node]))", _rows=17, _db_hits=0)
            NodeByLabel(label="Node", identifier="common_ancestor", _rows=17, _db_hits=0)
```

Looking at the profile data, we can see that it's scanning the full set of matches (PatternMatch ... _db_hits=374) before filtering the `where`. So let's see how this works with an index. We can create indexes in Cypher now!

```
neo4j-sh (0)$ create index on :Node(n);
+--------------------------------------------+
| No data returned, and nothing was changed. |
+--------------------------------------------+
10 ms

profile match p:Node<-[:parent_of*]-common_ancestor:Node-[:parent_of*]->h:Node
where p.n='p' and h.n='h'
return common_ancestor;
+--------------------+
| common_ancestor    |
+--------------------+
| Node[21]{n:"c"}    |
| Node[19]{n:"a"}    |
| Node[18]{n:"root"} |
+--------------------+
3 rows
20 ms

ColumnFilter(symKeys=["  UNNAMED12", "  UNNAMED48", "common_ancestor", "p", "h"], returnItemNames=["common_ancestor"], _rows=3, _db_hits=0)
Filter(pred="(((Property == Literal(h) AND hasLabel(common_ancestor, [Node])) AND hasLabel(common_ancestor, [Node])) AND hasLabel(h, [Node]))", _rows=3, _db_hits=3)
  PatternMatch(g="(common_ancestor)-['  UNNAMED48']-(h),(common_ancestor)-['  UNNAMED12']-(p)", _rows=3, _db_hits=29)
      SchemaIndex(identifier="p", _db_hits=0, _rows=1, label="Node", query="Literal(p)", property="n")
```

So, Cypher is smart enough to use an index here without needing to be told. Awesome! I'm also not going to let the fact that it seemed to automatically add the current nodes to that index automatically (which is also great). Let's ask it a harder question with a different example graph.

```
neo4j-sh (0)$ match n:Node-[r?]-() delete n,r;
PatternException: Can't use optional patterns without explicit START clause. Optional relationships: `r`
neo4j-sh (0)$ start n=node(*) match n-[r?]-() delete n,r;
+-------------------+
| No data returned. |
+-------------------+
Nodes deleted: 35
Relationships deleted: 32
16 ms
```

I guess it was wishful thinking to not have to type START for optional relationships. I'm sure that requirement will go away in time. I suppose it's somewhat dangerous to performance in case you don't have a small enough list of nodes in the label.

Here I've run <a href="https://gist.github.com/wfreeman/5303592" target="_blank">this script</a> to create the Westeros graph from the Game of Thrones example. 

```
neo4j-sh (0)$ profile 
match westeros:Continent<-[:HOUSE]-house:House<-[:OF_HOUSE]-ancestor:Person, family=ancestor<-[:CHILD_OF*0..]-last:Person
where house.house='Tully' and last.name='Catelyn'
return house.house, collect(distinct last.name);
+-------------------------------------------+
| house.house | collect(distinct last.name) |
+-------------------------------------------+
| "Tully"     | ["Catelyn"]                 |
+-------------------------------------------+
1 row
0 ms

ColumnFilter(symKeys=["house.house", "  INTERNAL_AGGREGATE357570430"], returnItemNames=["house.house", "collect(distinct last.name)"], _rows=1, _db_hits=0)
EagerAggregation(keys=["Cached(house.house of type Any)"], aggregates=["(  INTERNAL_AGGREGATE357570430,Distinct)"], _rows=1, _db_hits=2)
  Extract(symKeys=["  UNNAMED27", "ancestor", "  UNNAMED49", "last", "  UNNAMED92", "house", "family", "westeros"], exprKeys=["house.house"], _rows=1, _db_hits=1)
    ExtractPath(name="family", patterns=["  UNNAMED92=ancestor<-[:CHILD_OF*0..]-last"], _rows=1, _db_hits=0)
      Filter(pred="(((Property == Literal(Catelyn) AND hasLabel(westeros, [Continent])) AND hasLabel(ancestor, [Person])) AND hasLabel(last, [Person]))", _rows=1, _db_hits=1)
        PatternMatch(g="(ancestor)-['  UNNAMED49']-(house),(house)-['  UNNAMED27']-(westeros),(ancestor)-['  UNNAMED92']-(last)", _rows=1, _db_hits=8)
          SchemaIndex(identifier="house", _db_hits=0, _rows=1, label="House", query="Literal(Tully)", property="house")
```

So, the cool part about this, is that even though there are two indexes Cypher could have picked to use here, it still used one, even though it might not have been the best one. What we can do in this case is use the `using index` clause.

```
neo4j-sh (0)$ profile 
match westeros:Continent<-[:HOUSE]-house:House<-[:OF_HOUSE]-ancestor:Person, family=ancestor<-[:CHILD_OF*0..]-last:Person
using index last:Person(name)
where house.house='Tully' and last.name='Catelyn'
return house.house, collect(distinct last.name);
+-------------------------------------------+
| house.house | collect(distinct last.name) |
+-------------------------------------------+
| "Tully"     | ["Catelyn"]                 |
+-------------------------------------------+
1 row
0 ms

ColumnFilter(symKeys=["house.house", "  INTERNAL_AGGREGATE357570430"], returnItemNames=["house.house", "collect(distinct last.name)"], _rows=1, _db_hits=0)
EagerAggregation(keys=["Cached(house.house of type Any)"], aggregates=["(  INTERNAL_AGGREGATE357570430,Distinct)"], _rows=1, _db_hits=2)
  Extract(symKeys=["  UNNAMED27", "ancestor", "  UNNAMED49", "last", "  UNNAMED92", "house", "family", "westeros"], exprKeys=["house.house"], _rows=1, _db_hits=1)
    ExtractPath(name="family", patterns=["  UNNAMED92=ancestor<-[:CHILD_OF*0..]-last"], _rows=1, _db_hits=0)
      Filter(pred="((((Property == Literal(Tully) AND hasLabel(house, [House])) AND hasLabel(westeros, [Continent])) AND hasLabel(ancestor, [Person])) AND hasLabel(house, [House]))", _rows=1, _db_hits=1)
        PatternMatch(g="(ancestor)-['  UNNAMED49']-(house),(house)-['  UNNAMED27']-(westeros),(ancestor)-['  UNNAMED92']-(last)", _rows=1, _db_hits=5)
          SchemaIndex(identifier="last", _db_hits=0, _rows=1, label="Person", query="Literal(Catelyn)", property="name")
```

As you can see, the profile wasn't much better in this case, but we got to try the query with a different index by hinting it. What if it might be able to use multiple indexes? For now, you need to hint on both of them explicitly.

```
neo4j-sh (0)$ profile
match westeros:Continent<-[:HOUSE]-house:House<-[:OF_HOUSE]-ancestor:Person, family=ancestor<-[:CHILD_OF*0..]-last:Person
using index last:Person(name) 
using index house:House(house)
where house.house='Tully' and last.name='Catelyn'
return house.house, collect(distinct last.name);
+-------------------------------------------+
| house.house | collect(distinct last.name) |
+-------------------------------------------+
| "Tully"     | ["Catelyn"]                 |
+-------------------------------------------+
1 row
0 ms

ColumnFilter(symKeys=["house.house", "  INTERNAL_AGGREGATE357570430"], returnItemNames=["house.house", "collect(distinct last.name)"], _rows=1, _db_hits=0)
EagerAggregation(keys=["Cached(house.house of type Any)"], aggregates=["(  INTERNAL_AGGREGATE357570430,Distinct)"], _rows=1, _db_hits=2)
  Extract(symKeys=["  UNNAMED27", "ancestor", "  UNNAMED49", "last", "  UNNAMED92", "house", "family", "westeros"], exprKeys=["house.house"], _rows=1, _db_hits=1)
    ExtractPath(name="family", patterns=["  UNNAMED92=ancestor<-[:CHILD_OF*0..]-last"], _rows=1, _db_hits=0)
      Filter(pred="(hasLabel(westeros, [Continent]) AND hasLabel(ancestor, [Person]))", _rows=1, _db_hits=0)
        PatternMatch(g="(ancestor)-['  UNNAMED49']-(house),(house)-['  UNNAMED27']-(westeros),(ancestor)-['  UNNAMED92']-(last)", _rows=1, _db_hits=2)
          SchemaIndex(identifier="house", _db_hits=0, _rows=1, label="House", query="Literal(Tully)", property="house")
            SchemaIndex(identifier="last", _db_hits=0, _rows=1, label="Person", query="Literal(Catelyn)", property="name")
```

Ok, I guess that's enough hashing out of the new label style indexes. Some other cool things released in 2.0 are the `case ... when ... else` syntax in the SQL style. It's great for grouping results into buckets, etc., just like it is in SQL. There's also `union`, and `timestamp()` will be merged in for M02. I'm very excited about these changes. :)

Michael Hunger gave some examples for `union` and `case` in his <a href="http://jexp.de/blog/2013/04/cool-first-neo4j-2-0-milestone-now-with-labels-and-real-indexes/" target="_blank">post</a>. So I won't be repetitive here!

If you want to see more Cypher stuff, check out my [Cypher page](/cypher).
