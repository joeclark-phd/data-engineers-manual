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
**hash function**[^hash] which determines, in one step, where that particular
piece of data is stored on disk.  This is about the fastest possible way
data can be located and retrieved, so that's the main value proposition
of the key-value data model.

[^hash]: A hash function, in computer science, is any function that can
    "crunch" an arbitrary key and produce a number within a desired range.  For
    example, if there are 1000 blocks where data may be stored on a particular
    disk, the database may hash a key by interpreting it as a number, dividing
    by 1000, and taking the remainder as the hash.  This is guaranteed
    to be a number between 0 and 999 and could be used as the physical "address"
    for a piece of data.  See 
    [Wikipedia](https://en.wikipedia.org/wiki/Hash_function) for more on 
    hash functions.

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
require a full scan of every record in the entire database. [^indexes]

[^indexes]: At least in theory.  In practice, the leading document store 
    databases, such as MongoDB,
    offer the ability to define **indexes** and perform **joins**, features
    borrowed from the relational model.  Most commercial databases are 
    hybrids of these categories in some ways.

<fig 4.2, two queries against a document store>

The relational model was proposed by E. F. Codd with this problem in mind.
Its solution is to provision one data table for every "entity type" in the 
business domain, with a unique row for each instance of those entity 
types. Instances would be related to each other by **foreign keys**.  Users 
can **join** relational tables at query time to reconstruct the natural
aggregates, like blog posts, that they represent.  Because users can specify
which tables to join, and which criteria to select data by, they can take any
entity they like as the "starting point" without sacrificing efficiency. The
trade-off is that designing the data model becomes much more complicated.

<fig 4.3,same two queries across a relational database> 

In figure 4.3, the same two queries related to photo blogs are depicted but
the data model is relational.  To find all the tags associated with a blog
post, the database firsts finds the relevant row of the POSTS table, filters
the associative table to just the rows that match the post's `id` number,
then joins it with the TAGS table to get the text of each tag.  To find all
of the blog posts associated with a tag, it's the same procedure: first find
the appropriate row of the TAGS table, filter the associative table just to 
the rows that have the tag's `id` number, and then join it with the POSTS 
table to get the titles.  This is somewhat slower than a key-value lookup,
but much faster than a full scan of the database.

## The Query Optimizer

I will argue in this chapter that, although we have many excellent analytics
tools to choose from---programming languages such as R and Python, statistical
software such as SAS and SPSS, visualization tools, data mining tools and
more---it is often a good choice to use the database itself for many kinds
of data analysis rather than pulling data out and processing it in one of
these fine tools.  As a matter of fact, databases are some of the most
frequently overlooked analytical engines in a data science toolkit.  Not only
does in-database processing reduce the amount of memory and bandwidth we'd
otherwise use moving and processing the data, it also allows us to take
advantage of a database's **query optimizer**.

The query optimizer is a built-in function of a database system that,
given a query, decides on the best plan to retrieve the data.  You could, of
course, come up with your own **query plan**.  If you have taken a computer
science degree, you know that there are many known algorithms for
sorting and searching through lists of, speeding up retrieval with
hash functions and indexes, and employing parallel processing to accelerate
an analysis.  But writing code to do those things is a major effort, and 
it has already been done for you... by database developers at IBM, Oracle,
Microsoft, Teradata, and elsewhere.  Even non-programmers can take advantage
of their expertise by designing their analyses as database queries.

In order to leverage a query optimizer, queries are written in **declarative
languages**, the best known such language being SQL.  In a declarative 
language, you don't write instructions for the computer to follow, but
instead describe the output you want.  A query optimizer can interpret
this description and then decide for itself how to process the query.

### Example using relational algebra

To illustrate the role of a query optimizer in a relational database,
consider a database of student enrollment in university courses, 
and the query 

    SELECT students.id, fname, lname
    FROM students 
    JOIN enrollment ON students.id=enrollment.student
    JOIN classes ON enrollment.class=classes.id
    WHERE classes.code="CIS355"

which should produce a roster of all students enrolled in CIS 355.  (In this 
simple example we're ignoring the issues of semester and multiple sections 
if the class.)  In relational algebra, the result of this query can be 
expressed as:

{$$}
\pi_{id,fname,lname}(students \times enrollment \times \sigma_{code=CIS355}(classes))
{/$$}

sigma<classes.code=CIS355>(pi<id,firstname,lastname>(students*enrollment*classes))

I'll say more about relational algebra in chapter 9, but suffice it to say
that just like in your high school algebra, there are many different paths
by which we might "simplify the expression".  We could start by applying
the two *join* operations to unite the three tables, then *project* just the 
three columns we want, and then *select* just the rows pertaining to CIS 355.
Or we could apply the *select* first on just one table (classes), before 
the first *join*, and finish in a different way.  But which way is most
efficient?  When dealing with data tables that may have thousands, millions,
or billions of rows, efficiency counts, and the best databases have query
optimizers smart enough to analyze its options in advance and decide on
the likely best plan.



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
