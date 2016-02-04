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

<fig 4.4 sample database>

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
\Pi_{id,fname,lname}(students \Join enrollment \Join 
\sigma_{code=CIS355}(classes))
{/$$}

I'll say more about relational algebra in chapter 9, but suffice it to say
that just like in your high school algebra, there are many different paths
by which we might "simplify the expression".  We could start by applying
the two *join* operations to unite the three tables, then *project* just the 
three columns we want, and then *select* just the rows pertaining to CIS 355.
Or we could apply the *select* operation first on just one table (classes), 
then do
the *joins*, and finish with the *project* operation which reduces the
number of columns in the result.  Or we may do these operations
in another sequence.  But which way is most
efficient?  

When dealing with data tables that may have thousands, millions,
or billions of rows, efficiency counts, and the best databases have query
optimizers smart enough to analyze its options in advance and decide on
the likely best plan. If we tried to write our own program in Python, 
Java, R, or another
*imperative* programming language, we'd have to make a decision ourselves
about which way to process the query, which is a lot of work, and we might
get it wrong.  Major database vendors and open source projects are competing
with each other to develop the most efficient and effective query optimizers,
so I advise you to take advantage of their efforts.

### Other optimizations

In addition to the use of relational algebra, major databases offer several
other features to speed up your analytical queries that cost you little to
no effort.  Among these are caching, indexing, and parallelization.

A good database engine will notice when queries are being repeated and 
**cache** the results (either in active memory or on disk) for quick 
retrieval.  This can be a startlingly impactful optimization.  Although
you may not think that you'll run the same query many times, it may be the
case that many of your queries join together the same two tables, and a good
database should be smart enough to cache this intermediate step so that it
doesn't need to be processed repeatedly.

A database **index** is similar to the index at the back of a book: it is an
added-on table, sorted for easy lookup, which tells the database where to find
certain values in another table.  There are different ways to implement an
index internally, and different solutions are best for indexing different 
tables.  Although you can specify indexes manually, a clever database will
also automate some of the decisions related to indexing, not only when to
create them but also when to use them.

In the era of big data, databases are often partitioned over large clusters 
of computers or at least multiple hard drives, and even personal computers
these days often have multiple processor cores, so we can gain a lot of
efficiency by doing data processing operations in parallel.  Determining
how, and when, to parallelize query processing can be a real brain-teaser
even for a computer scientist, and is beyond what the casual database user
can figure out easily.  The best databases know how to take advantage of
parallelization, and some (such as Teradata) are famous for it.

## Complex Analyses Made Simple

Overall, my claim is that databases allow you to leverage the hard work
of decades-long research by developers to execute complicated analyses
without having to write complex queries.  This fact is rarely even hinted
at in introductory database textbooks, because so much attention is given
to databases as simple engines for storing and retrieving unprocessed 
data.  A few examples of analyses that can be done very simply within
databases are:

* "pivoting" a data table
* time series analysis
* matrix multiplication to create a search engine index

### Pivoting a data table

A pivot table is the *sine qua non* of data analysis.  It is a summarization
and cross-tabulation of data that allows the user to arbitrarily select
column headings, row headings, and the aggregation function (i.e. sum,
average, or count), and automate the calculations.  For example, given
a large data table of sales transactions, such as

    Date      Customer    Product   UnitPrice Quantity  ...
    03-Jan    Acme Corp   Widget    $75.00    3         ...
    03-Jan    Acme Corp   Gizmo     $199.00   1         ...
    05-Jan    Peabody Inc Gizmo     $179.10   2         ...
    10-Jan    Acme Corp   Widget    $75.00    3         ...
    ...       ...         ...       ...       ...       ...

You might be interested in sales by product by month, hence "pivot" the
data to a table like this:

                  Product:
    Month:          Gizmo     Widget  Thingamajig     TOTAL
    January       3794.10    5625.00      1008.47  10427.57
    February      3221.00    6150.00      1231.88  10602.88
    TOTAL         7015.10   11775.00      2240.35  21030.45

Pivot tables are a powerful feature of spreadsheet software, but data in a
spreadsheet cannot realistically span very many columns or rows before it
becomes unwieldy.  Relational databases are excellent for applying these
kinds of aggregations to
arbitrary groupings of the data, and they can keep track of many more rows 
and columns than you could display on a screen.  In SQL, pivots 
are function of the
GROUP BY clause.  A query to produce the subtotals above might
look something like this[^totals], depending on the database vendor:

    SELECT MONTH(Date) AS Month, Product, SUM(UnitPrice*Quantity)
    FROM Transactions
    GROUP BY Month, Product

Implied in that little GROUP BY clause is potentially a huge amount of work
sorting the data and computing subtotals that the query optimizer will plan
and execute.  In a massive data warehouse, it will likely make use of 
parallelization to balance the workload over a computer cluster and 
must carefully organize the work to minimize data transfer over 
the network.  But for you, it's just two words at the end of the query.

[^totals]: Computing the row, column, and grand totals would be done 
    separately.
    
### Time series analysis

Typically we think about SQL as a tool to retrieve stored data. Even
if we're accustomed to calculating sums, averages, and other aggregations,
the assumption is that we're limited to what's already there.  This overlooks
the fact that some very powerful analyses can be accomplished by using SQL's
JOIN clause and some external or arbitrary data.

One good case for this is generating time series.  The problem with just using
GROUP BY as in the example above is that, if there is no data for a particular
time period, it won't occur in the results.  We cannot easily make a line graph
of a time series, though, if it is missing months and days.  The solution
can often be to generate a **temporary table** containing the series of
months, weeks, or days that we want to analyze, and then JOIN it to a
pivot table of the data as before.  This is an incredibly simple operation
in most databases but one I've never seen in an introductory database
textbook.[^except]

[^except]: I *did* read about it in Eric Redmond and Jim R. Wilson's book
    *Seven Databases in Seven Weeks*, an excellent field guide to wild
    databases.

This concept of joining temporary or external tables to your existing data
offers numerous other possibilities.  For example, given a dictionary of
words with sentiment scores, it should be relatively easy to join it with
natural language data (for example, a database of product reviews) and 
sum up the customers' average feelings about products, brands, and
companies.  People get paid quite a lot to write custom sentiment analysis
software, but you might be able to do it with a few SQL tricks.
    
### Matrix multiplication to enable keyword search

Matrix multiplication is very important to many applications of business
and scientific data analysis.  Sparse matrices (that is, matrices with 
zeroes in most positions) can be efficiently represented as data tables
with three columns---row number, column number, value---and multiplied
with a succinct SQL query like this one:

    SELECT A.row_num, B.col_num, sum(A.value * B.value)
    FROM A, B
    WHERE B.row_num = A.col_num
    GROUP BY A.row_num, B.col_num

Here's an example of how that could be useful in building a search 
engine.[^howe] Imagine you have a database of word counts from various
web pages, structured like so:

    Document         Word  Frequency
    doc1.htm      traffic          3
    doc1.htm      weather          1
    doc2.htm   politician          2
    doc2.htm          tax          2
         ...          ...        ...
    
In fact, this can be seen as part of a sparse matrix with one row for each
document and one column for each word---sparse because most documents only
contain a small fraction of possible words in the language.  If we multiply
this matrix by its own transpose[^transpose], we produce a matrix of
document similarity.  Basically, the more words two documents have in common,
and the more frequent those words in those documents, the higher the
similarity score in this new matrix, which could look like:

              doc1.htm  doc2.htm  doc3.htm  doc4.htm  ...
    doc1.htm         -         3        15         0  ...
    doc2.htm         3         -         8        55  ...
    doc3.htm        15         8         -        21  ...
    doc4.htm         0        55        21         -  ...
         ...       ...       ...       ...       ...  
     
This result, obtainable with a simple SQL query, could be used to power
a recommendation engine (e.g. "if you liked this article, you might like
these other articles") or to support a keyword search engine.  The trick to
the latter is to treat the search phrase as a "document" in its own right, 
and compute the similarity score between the search phrase and other 
documents.
     
[^howe]: I first learned about this by taking Bill Howe's MOOC on "Data
    Manipulation at Scale" via Coursera.
    
[^transpose]: The **transpose** is the matrix with rows flipped to columns
    and vice versa.

For the record, I can't say that the tricks I'm describing here are
guaranteed to be faster or more efficient in a database than in a specialized
tool; however, I'd like to emphasize the point that these methods leverage
the work that others have done for you, and often utilize programming 
techniques like parallelization that may be beyond your abilities.  The
magic ingredient is that in each case you are using a *declarative* query
language, telling the database not what *to do* but what you *want*,
allowing that query optimizer to find the most efficient method
to do the job.
    
## Crunching Big Data with MapReduce in the Database

Database queries are often about "finding" data, that is, retrieving a small
piece of data from within a large database.  SQL and other query languages 
do this very well, and databases use tricks like indexing to speed up these 
kinds of search-and-retrieve operations.  However, for some Big Data analysis, 
what you want is to crunch the entire database at once.  When Google updates
its search engine index, for example, it may analyze literally the entire
contents of the World Wide Web.  When Amazon or Netflix update their
recommendation engines, they may analyze the entire history of customer
transactions in one big process.  And so on.

Out of the problems faced by Google, Amazon, Facebook, Yahoo and other web
giants emerged new technologies for massive data processing jobs on whole
databases.  Here the challenge is not search-and-retrieval, but figuring out
how to parallelize the job so that the database can be split up into chunks
over a few dozen (or hundred, or thousand) servers that can all work
independently---the only way to do this kind of data processing in an
acceptable time frame.

One such contribution is the MapReduce algorithm invented by Google for its
own use and popularized by the Hadoop project initiated by Yahoo.  In a
nutshell, a developer writes two programs: a `map()` program that reads
one row or record of data and generates ("emits") an intermediate result,
and a `reduce()` program that takes all those intermediate results and
combines or sums them up into a final answer.  This can be massively
parallelized if the `map()` program is written in such a way that it can
run independently on each piece of data, no matter in what order they're
processed.  A typical example would be a word counting program like
this pseudo-code:

    def map(document):
      for word in document:
        emit({word:1})

This would produce a number of key-value pairs as the intermediate 
data.  Those would be shuffled around and delivered to a `reduce()` program
that runs once for each key.  (In this case, once for each word.)
    
    def reduce(word,emits):
      count=0
      for e in emits:
        count = count + 1
      return({key:count})

For each word counted, this reducer would count the output emitted by the
`map()` function and return a grand total.  The final output of all the 
reducers might begin like so:

    { {"a": 12345},
      {"an": 4567},
      {"and": 8765},
      ...
      }

Although in this example you have to write the "map()" and "reduce()" 
programs in an imperative programming language such as Python or
Java, those two functions are by design quite simple.  The really hard 
work is organizing the parallel processing and the "shuffling" of data 
from the independent mappers and reducers to deliver the intermediate 
and final results.  Consistent with the theme of this chapter, I want
to encourage you to get a database engine to do that work for you.

You have probably heard the name Hadoop.  (If not, I just used it a few
paragraphs back.  Now may be the time to get some caffeine before finishing
the chapter.)  Hadoop is the most famous platform for running MapReduce jobs,
but it's not exactly a database.[^hadoopis]  There *are* databases that 
support MapReduce jobs, however, especially some of the non-relational or 
NoSQL databases that have emerged in the era of cluster computing.  MongoDB,
the leading document store, and Riak, an open-sourced key-value store based
on Amazon's proprietary Dynamo database, are two of the leading ones.  In
MongoDB, for example, you define `map()` and `reduce()` functions in 
JavaScript and the database takes care of everything else.

[^hadoopis]:  Hadoop consists of a file system, HDFS, that allows you to 
    store data on a cluster of hundreds or thousands of commodity computers
    but deal with them as if they were just one big hard disk, *and* a 
    framework for defining and running MapReduce jobs in Java.  There is also
    an "ecosystem" of related tools, such as Hive, which generally go along
    with any Hadoop installation.
    
## Logic in the Database

As mentioned in Chapter 3, database-driven applications are often architected
in three tiers, with the presentation logic on the client side, data storage
logic in the database, and data *processing* logic in an application server
in between.  This separation of responsibilities allows developers to work
together without ruffling each other's feathers too much.  I believe, however,
in the somewhat heretical view that more and more program logic should be moved
into the database.  Databases should not be treated as "dumb" systems merely
for storing and fetching data.

Moving more logic into the database has the advantage of leveraging the query
optimizer, for sure, but that's not all.  An additional advantage is that more
and more often, you may have multiple application servers (say, a web server
and some kind of mobile application) that need to do the same kinds of
processing (such as generating a product recommendation for the user), built
on the same database.  Does it make sense for the developers of both apps
to replicate each other's work?  Knowing what you now know about the 
possibilities of doing analytics right in the database, you may see the value
in moving the logic there where it can be consumed by both apps.

Databases offer a number of ways we can define program logic:

Stored procedures or user-defined functions... Triggers...

Views...

## Summary

Querying a database *is* analyzing data.

Use a declarative language so you can let the query optimizer apply the
accumulated wisdom of other developers and make your work easier.

SQL and MapReduce, among other tools, can make some forms of complex data 
analysis very simple.

MapReduce is for whole-database processing.

There are a number of ways to move data processing logic into the database.

## References & Recommended Reading

- "Data Manipulation at Scale".  MOOC by Bill Howe of U. Washington.  Via
  [Coursera](https://www.coursera.org/learn/data-manipulation).
  
- Redmond, E., & Wilson, J. (2012). Seven Databases in Seven Weeks. The
  Pragmatic Bookshelf.

- "MapReduce".  Wikipedia.  https://en.wikipedia.org/wiki/MapReduce