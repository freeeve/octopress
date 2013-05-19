---
layout: post
title: "AnormCypher 0.4.1 released!"
date: 2013-05-19 11:34
comments: true
categories: [anormcypher, cypher, neo4j, programming]
---
Thanks to [Pieter](https://github.com/PieterJanVanAeken), AnormCypher 0.4.1 supports versions earlier than Neo4j 1.9 (I didn't realize this was an issue). 

[AnormCypher](http://anormcypher.org/) is a Cypher-oriented Scala library for Neo4j Server (REST). The goal is to provide a great API for calling arbitrary Cypher and parsing out results, with an API inspired by Anorm from the Play! Framework.

### 0.4.1 changes:

* Allow configurable Cypher endpoint.

### Roadmap:

- 0.5.0: Scala 2.10 Future/Stream non-blocking support
- 0.6.0: Cake pattern with pluggable interfaces for different protocols, REST batch/transactional API

