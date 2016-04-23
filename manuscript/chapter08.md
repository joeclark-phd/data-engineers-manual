# Chapter 8: Analytical Systems at Scale

Takes concepts from the previous chapter and adds in consideration of extreme
scale, the CAP theorem.  Shows how some of the new architectures (e.g. NoSQL)
are inspired by scale problems.  Introduces the "analytics platform" concept
that shows how a data lake, relational database, and "discovery" tool may be
used together.

## The Cloud

Explain why web giants have excess capacity and how they started
to sell it "by the drink", and why that makes things less costly
for everyone.  Use the Grace Hopper oxen quote.

Define IaaS, PaaS, SaaS, DBaaS.

## Distributed Databases

Talk about replication and partitioning.

Explain the CAP theorem with examples, and compare the reasons for ACID
to the reasons why a web app might be better served by BASE.  Clarify
that the C/A tradeoff is a business decision.

## Hadoop and Shared-Nothing Architecture

Explain HDFS and (briefly) MapReduce, and how they are different from
supercomputing or HPC because of shared-nothing architecture, and the
locality of data means they can scale linearly.  Hadoop is for batch
processing and really for giving you layers of abstraction so you don't
have to program your own frameworks.

## Columnar and in-Memory Analytical Databases

Other approaches to analytics at enormous scale.  NewSQL.  Hive, etc.

## The Era of Trade-offs

How might we combine systems.  Hadoop for the data lake.  Columnar/analytic 
database to serve analytical queries (e.g. data marts).  Relational databases
for web sites and operational systems.  Data "discovery" tool, maybe
Spark, for doing ultra-fast exploration.
