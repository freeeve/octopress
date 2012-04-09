---
layout: post
title: "Testing with MongoDB: Part 1"
date: 2012-04-08 11:03
comments: true
categories: ['programming', 'mongodb']
---
Testing with MongoDB is made easy with the JavaScript-based shell. You can write scripts to generate lots of data quickly, and use that data to test indexes and queries.

For example, say you want to test some compound-index range queries to see how they perform. One of the convenient things about Mongo is you don't need to do any setup (like create a table) before you start inserting data. So we can start with a simple for loop:

``` javascript
for(var i=0; i < 1000000; i++) {
  db.test.insert(
    {x:Math.floor(Math.random()*1000000), 
     y:Math.floor(Math.random()*1000000)});
}
```

I usually type this into an editor and paste it into the mongo shell, but in 2.2 they'll have a way you can edit shell commands in an editor, which will be convenient.
<!--more-->
In any case, once this command finishes (it will probably take a few minutes), you can start testing queries like the one below. What we really care about here are the nscanned statistics, and the millis.

``` javascript
> db.test.find({x:{$gt:1000}, y:{$gt:10000}}).explain();
{
	"cursor" : "BasicCursor",
	"nscanned" : 1000000,
	"nscannedObjects" : 1000000,
	"n" : 988980,
	"millis" : 928,
	"nYields" : 0,
	"nChunkSkips" : 0,
	"isMultiKey" : false,
	"indexOnly" : false,
	"indexBounds" : {
		
	}
}
```

And compare the difference to what it looks like with an index on x and y:

``` javascript
> db.test.ensureIndex({x:1,y:1});
> db.test.find({x:{$gt:1000}, y:{$gt:10000}}).explain();
{
	"cursor" : "BtreeCursor x_1_y_1",
	"nscanned" : 998959,
	"nscannedObjects" : 988980,
	"n" : 988980,
	"millis" : 4036,
	"nYields" : 0,
	"nChunkSkips" : 0,
	"isMultiKey" : false,
	"indexOnly" : false,
	"indexBounds" : {
		"x" : [
			[
				1000,
				1.7976931348623157e+308
			]
		],
		"y" : [
			[
				10000,
				1.7976931348623157e+308
			]
		]
	}
}
```

This is one of those counterintuitive situations where an index is actually slower than no index.
The problem is we have two very wide ranges, and we're having the query scan both indexes with not much benefit.

However, if you were to use a smaller range on x, the query would actually be quicker than having no index.

``` javascript
> db.test.find({x:{$gt:900000,$lt:910000}, y:{$gt:10000}}).explain();
{
	"cursor" : "BtreeCursor x_1_y_1",
	"nscanned" : 9993,
	"nscannedObjects" : 9911,
	"n" : 9911,
	"millis" : 36,
	"nYields" : 0,
	"nChunkSkips" : 0,
	"isMultiKey" : false,
	"indexOnly" : false,
	"indexBounds" : {
		"x" : [
			[
				900000,
				910000
			]
		],
		"y" : [
			[
				10000,
				1.7976931348623157e+308
			]
		]
	}
}
```

More on indexing in MongoDB later. MongoDB is a great tool, but optimizing indexes for a lot of data takes a bit of practice. Having a range query on two fields is something to be avoided, but if you must have one, try to limit the first column of the index as much as possible. It's usually worth redoing your index to have the most selective field in the first column.
