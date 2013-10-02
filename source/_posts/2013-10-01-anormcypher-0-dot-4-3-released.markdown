---
layout: post
title: "AnormCypher 0.4.3 released!"
date: 2013-10-01 21:01
comments: true
categories: [anormcypher, cypher, neo4j, programming]
---
Thanks to [mvallerie](https://github.com/mvallerie), AnormCypher 0.4.3 now uses the official typesafe play-json repository, to go with the release of play 2.2.

[AnormCypher](http://anormcypher.org/) is a Cypher-oriented Scala library for Neo4j Server (REST). The goal is to provide a great API for calling arbitrary Cypher and parsing out results, with an API inspired by Anorm from the Play! Framework.

### 0.4.3 changes:

* Switch to typesafe package for play-json 2.2
* Switch to typesafe repository instead of mandubian (play-json)
* Update unit tests to handle new M05 syntax for Cypher
* Bump integration tests to Neo4j 2.0.0-M05

### Roadmap:

- 0.5.0: Scala 2.10 Future/Stream non-blocking support
- 0.6.0: Cake pattern with pluggable interfaces for different protocols, REST batch/transactional API

