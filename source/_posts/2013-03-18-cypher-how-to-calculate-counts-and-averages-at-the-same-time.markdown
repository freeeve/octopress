---
layout: post
title: "Cypher: how to calculate counts and averages at the same time"
date: 2013-03-18 02:11
comments: true
published: false
categories: 
---

http://console.neo4j.org/r/q1mjbv

create (g1 {n:"g1"}),(g2 {n:"g2"}),(g3 {n:"g3"}),(g4 {n:"g4"}),(g5 {n:"g5"})
foreach(x in [1,2,3]: create g1<-[:member_of]-({n:"a"+x}))
foreach(x in [1,2,3,4]: create g2<-[:member_of]-({n:"b"+x}))
foreach(x in [1,2,3,4,5,6]: create g3<-[:member_of]-({n:"c"+x}))
foreach(x in [1,2,3,4,5]: create g4<-[:member_of]-({n:"d"+x}))
foreach(x in [1,2]: create g5<-[:member_of]-({n:"e"+x}))

