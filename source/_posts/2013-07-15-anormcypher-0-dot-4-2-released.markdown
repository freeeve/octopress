---
layout: post
title: "AnormCypher 0.4.2 released!"
date: 2013-07-15 11:34
comments: true
categories: [anormcypher, cypher, neo4j, programming]
---
Thanks to [okumin](https://github.com/okumin), AnormCypher 0.4.2 now supports non-ascii characters with the proper UTF-8 settings.

[AnormCypher](http://anormcypher.org/) is a Cypher-oriented Scala library for Neo4j Server (REST). The goal is to provide a great API for calling arbitrary Cypher and parsing out results, with an API inspired by Anorm from the Play! Framework.

### 0.4.2 changes:

* Allow non-ascii characters by default.
* Bump to scala 2.10.2.
* Bump integration tests to Neo4j 2.0.0-M03

### Roadmap:

- 0.5.0: Scala 2.10 Future/Stream non-blocking support
- 0.6.0: Cake pattern with pluggable interfaces for different protocols, REST batch/transactional API

