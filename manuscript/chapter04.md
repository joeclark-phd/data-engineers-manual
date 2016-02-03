# Chapter 4: Analytics in the Database 

In the previous chapter I talked about how databases offer us ways to store
structured data and retrieve it, and how databases fit into typical 
architectures for information systems such as web sites and mobile apps.  In
this chapter I want to discuss some of the ways databases allow us to *process*
and to *query* data more effectively.  Query optimizers, relational algebra,
views, user-defined procedures, and the MapReduce algorithm will be introduced.

## Querying a Database

An important implication of the logical data model you choose for a database,
be it relational, dimensional, aggregate-oriented, or some other, is that
the type of model determines how you may retrieve a piece of data that you
want.  In the simplest model, the key-value store, each piece of data has a
unique identifier called a **key**.  To retrieve a piece of data, the user
queries the database with the desired key.  The key is passed through a 
hash function[^hash] which determines, in one step, where that particular
piece of data is stored on disk.  This is about the fastest possible way
data can be located and retrieved, so that's the main value proposition
of the key-value data model.

[^hash]: A hash function, in computer science, is any function that can
"crunch" an arbitrary key and produce a number within a desired range.  For
example, if there are 1000 blocks where data may be stored on a particular
disk, the database may hash a key by interpreting it as a number, dividing
by 1000, and taking the remainder as the hash.  This would be guaranteed
to be a number between 0 and 999 and could be used as the physical "address"
for a piece of data.  See [Wikipedia](https://en.wikipedia.org/wiki/Hash_function)
for more on hash functions.

<fig 4.1. a key-value query: ex. from Riak>

A document store is a kind of tree structure, not unlike the **hierarchical
databases** that were dominant before the invention of relational databases
in the 1970s.  Like key-value stores, individual documents can be retrieved
extremely quickly by their unique keys, and like hierarchical databases, one
can very quickly traverse the tree structure to retrieve contents and 
sub-structures of documents.  If a database designer knows in advance what
kinds of queries his system must process, he can organize the hierarchy
to respond to those queries very efficiently.  The trade-off, though, is
that a document store has very poor performance with queries it was not
designed for.  In Figure 4.2, for example, a photo blog's database can 
retrieve a complete blog post and its topical tags with a simple 
key-value lookup, but to query a tag and find all related blog posts would
require a scan of every record in the entire database. [^indexes]

[^indexes]: In theory.  In practice, the leading document store databases
    offer the ability to define **indexes** and perform **joins**, features
    borrowed from the relational model.  Most databases are in some way
    hybrids of these categories.

<fig 4.2, two queries against a document store>

The relational model was proposed by E. F. Codd with this problem in mind.
Its solution is to provision one data table for every "entity type" in the 
business domain, with a unique row for each instance of those entity 
types. Instances would be related to each other by **foreign keys**.  Users can
**join** relational tables at query time to reconstruct the natural
aggregates, like blog posts, that they represent.  Because users can specify
which tables to join, and which criteria to select data by, they can take any
entity they like as the "starting point" without sacrificing efficiency. The
trade-off is that designing the data model becomes much more complicated.

<fig 4.3,same two queries across a relational database> 

## The Query Optimizer




A discussion of some of the things databases do to support efficient 
crunching of data (e.g. relational algebra, MapReduce, stored procedures, etc)
and some examples of analytics within the database.

"old style" Scalability = database allows you to work with data too big to fit in main memory
"new style" scalability = works on clusters of 100s or 1000s of computers

logical data independence = you work with the data model, not with the physical implementation

Databases are great at "needle in a haystack" problems; they use indexes to find stuff right away.
Hadoop/MR is great at problems where you need to analyze the entire database.  MongoDB allows both.

Indexes, sorting and searching algorithms all built in.  Parallelization of some processing
is all built in.  Take advantage of this engineering!

Declarative languages & relational algebra: you could write your own program to sort, search, and join tables, but it would probably be less efficient.  The query optimizer does this better; therefore, queries are written in declarative form.

Some complex things that become simple when you move them into the database?
- JOIN of two tables
- pivot table statistics?
- sentiment analysis of a bunch of tweets
- matrix multiplication?

Use views and user-defined functions in the database instead of the application server.
Why? Diagram how multiple 3-tier apps can be built on one database if you do.
