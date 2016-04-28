# Chapter 3: A Multitude of Databases 

Databases, that is, software systems that persist data for future retrieval, 
come in many forms because they face numerous challenges and 
trade-offs.  They abstract away the physical storage and retrieval of data so that 
application developers can work with data models appropriate to their business 
rules and constraints.  The relational data model has been dominant since the 
1970s but now shares the field with a number of other types of data 
models.  Databases introduce unique challenges for operations and maintenance.

## Defining the Database

A database is a system that **persists** data for future retrieval.  Since 
data is such a vital resource, databases are an important part of the 
**infrastructure** of a business.  Other software applications depend on 
databases for access to the data they need.  This means databases must deal 
with two particularly challenging constraints: (1) they cannot lose the data, 
and (2) they cannot be shut down.  Designing and maintaining a database is 
sometimes likened to building and repairing a ship while in the middle of 
the ocean.

Compared to other kinds of programmers, database developers operate under an 
important constraint: the data sticks around.  If you're programming some
other kind of system, such as a shell script, a web page, or a game, you can
discard and re-write your code as many times as you like, iterating on new 
features and bug fixes, re-running and testing your code as many times as
you like.  But data in a database persists from one run to the next.  If you
want to change the structure of the data, you cannot just implement those changes 
for *future* data but must also carefully bring the *existing* data up-to-date
with the new structure.  

Consequently, decisions made in the initial
**data modeling** matter a great deal.

## Data Models

What most real database management systems do is to provide a layer of 
abstraction on top of the physical storage of data.  The abstraction is 
called a **data model** and there are several common ones.  In the **relational 
data model**, data is stored in tables with rows and columns, with a few 
very simple rules:

- No two rows may be identical.
- No two columns may have the same name.
- No data field may have multiple values.
- There is no inherent ordering of rows or columns.

What it means to call this an **abstraction** is that application developers, 
or other users working with the data, will simply interact with tables, rows, 
and columns.  The underlying implementation of data storage on disk will be 
handled by the database management system and will be invisible to users.  A 
data table could be physically implemented on one disk or distributed across 
multiple disks, partitioned to keep rows together or to keep columns together, 
stored in the order data was added or in numerical or alphabetical order---but 
all of these details would be hidden from the user.

Databases are, however, what computer scientists call "leaky 
abstractions".  In actual practice, there are choices you can make in physical 
implementation that will make a difference in terms of performance.  These 
choices are fundamental to understanding the emergence of analytical databases such 
as data warehouses, and Big Data platforms like Hadoop.

To free the developers and users of data-driven applications from working 
directly with physical data storage on disk, databases offer APIs to enable 
database queries.  These APIs can be HTTP web services, accessible over
the Internet, or programming language libraries, but the most well-known API 
for accessing databases is the **SQL** query language used to query the
relational model.  

SQL is a **declarative language** as opposed to an
*imperative* programming language: instead of writing a series of instructions,
you write a description of the result you want, and the database system
itself figures out how to most efficiently deliver on your request.  This has
been so convenient that even the popular new non-relational databases 
(nicknamed "**NoSQL**") have been developing their own declarative query 
languages.

{title="Table 3.1: Relational table of City data"}
| id# | city | state | population |
|---------------------------------|
| 1 | Los Angeles | CA | 3,880,000 |
| 2 | Las Vegas | NV | 603,488 |
| 3 | Phoenix | AZ | 1,510,000 |
| 4 | Tucson | AZ | 526,116 |
| 5 | Santa Fe | NM | 69,976 |
| 6 | Salt Lake City | UT | 191,180 |
| 7 | San Antonio | TX | 1,510,000 |

Consider Table 3.1, a list of cities.  Perhaps they are where our customers 
live.  If we wanted to know the names of the cities in Arizona, in order 
from largest to smallest, we could write a query like:

    SELECT city, population
    FROM cities
    WHERE state='AZ'
    ORDER BY population DESCENDING

As the user of a relational database, you will have to learn about the 
relational modeling, and the basics of SQL, but you will not need to know 
how the database will retrieve the data from disk, or what algorithms it uses 
to sort and filter the data to produce the result you requested.  In addition 
to `SELECT`, there are SQL queries for creating, updating, and deleting 
data.  Along with "reading", these are called the **CRUD operations** and almost 
any database needs to support them.

## Databases in Applications

Typically we are not writing ad hoc SQL queries directly through the database, 
but rather using software **applications**.  A data-driven application 
(or "app", if you really must use that word), is a piece of software that retrieves or 
processes data from a database for some purpose of the user.  These 
applications serve as "front ends" and depend on the database as a 
"back end" technology.  Therefore, the database must run as a 
**server**---always on and waiting for requests to respond to.

There are a number of different architectures possible for data-driven 
applications.  The simplest is when a database application is implemented 
on a single computer for a single user, a **personal database** such as an 
address book or contacts list.  Although such a database can be useful to 
the individual, it is generally not a good idea that critical business 
data resources be stored in this way.  Personal databases, just like 
personal spreadsheets, face challenges of scale and coordination when
they become bigger or have multiple users who depend on them.

More sophisticated applications are built with **client-server 
architectures**.  A **client** in this context is a piece of software or 
hardware that makes requests to a server over a network.  A **server** is a 
centralized piece of software or hardware that waits for, and responds to, 
these requests.  Client-server architectures can be categorized as "thin 
client" or "fat client", or "two-tier", "three-tier", and 
"*n*-tier".  Enterprises these days are also bringing within their walls 
some of the data architecture concepts that emerged on the Internet, such 
as the service-oriented architecture that will be discussed 
in Chapter 5.

 - TODO: [Fig. 3.1. Diagram of fat-client, 2-tier application]

Figure 3.1 depicts an example of a two-tier, fat client architecture.  In 
the example, the database server supports what is probably a small workgroup 
of knowledgeable users.  The users are running sophisticated software that 
accesses the database through the network.  For example, they might be 
analysts using Excel, Tableau, Python, or data mining software on their 
desktop computers to visualize or mine the data stored in the database.  

The database server is in charge of the **storage logic** of how data is 
persisted to disk, while most of the rest of the processing happens on 
client computers.  An architecture like this allows expert users to select 
or create their own tools, but does not scale up well to thousands or 
millions of users.  Only trusted users, who know enough not to accidentally 
corrupt the data, should be given this kind of direct access.

As a data scientist or data engineer, this might be the sort of **data discovery** environment
in which you munge data and develop new analytical models, but when you want
to make analytics available to a broader user base, you'd want a "production"
environment such as the thin-client, 3-tier architecture diagrammed in Figure
3.2.

 - TODO: [Fig. 3.2. Diagram of thin-client, 3-tier application]

This is a simple architecture that today underlies most websites, mobile 
applications, and other software programs intended for a large user base.  In 
a three-tier architecture, users access the data through an intermediary 
**application server** and it is the software in the middle that communicates 
with the database.  The clients in this context tend to be simpler programs 
that handle only the **presentation logic** of the application: receiving 
input and displaying output.  In what is undoubtedly the most common case, 
the application server is a web server such as Apache, and the thin client 
is a web browser such as Chrome, Internet Explorer, or Safari.

A three-tier architecture allows for a division of labor, as the database 
administrators are in charge of data modeling, maintenance, and integrity, 
and application developers are in charge of the **processing logic** of how 
the application uses or changes the data.  These developers access the data 
by embedding database API calls, such as SQL queries, into their own 
code.  Because the data is independent of the application, other applications 
could be written that access the same database server---for example, a 
website and mobile app may exist side by side, written by different developers 
using different tools.  

In many apps, data processing logic is distributed among the client and 
application server tiers.  The 
"fatness" of the client refers to the amount of processing logic done on the
client side, and mobile applications tend to be "fatter" than websites.  With 
a little imagination, you can further generalize 
these concepts to four-tier or *n*-tier application architectures. 

{title="Table 3.2: Types of program logic in data-driven applications"}
| Logic | Features | state | Location |
|-------------------------------------|
| Presentation logic | Input and output; visual presentation of data | Client-side software |
| Processing logic | Business rules, data processing and management | "Fat client" or application server such as a web server |
| Storage logic | Data persistence, optimization, and retrieval | Database server |

## Choices in Logical Database Design

If this book were written anywhere between 1985 and 2005, give or take, there 
would really only be one starting point in learning data modeling: the 
relational model mentioned previously.  Specialized analytical databases 
called data warehouses use **dimensional models** which are a variation on 
relational models and are generally implemented with the same 
technology---relational databases.  Today, however, we are living in the 
midst of a "Cambrian explosion" of new types of databases and new types of 
data models.  This necessitates some consideration of the benefits each type 
of data modeling offers.

Relational data modeling begins by identifying the **entity types** in the 
domain of the data.  These are the nouns---people, organizations, things, 
events---that the data must describe or refer to.  Employees and departments, 
products and purchases, are some examples.  For each entity type, a table is 
created, with one row for every **instance** of the entity type.  Instances 
of data in different tables are related by the use of unique identifiers 
known as **keys**.  For example, a row in the Orders table might indicate 
that the order was for product #42 (a reference to a row in the Products 
table) by customer #54321 (a reference to a row in the Customers table).  In 
this way, details of the product and customer are stored only once in the 
database, despite participating in numerous orders.

At the time relational databases were introduced in the late 1970s, disk 
space was extremely expensive.[^disk] By eliminating unnecessary redundancy, 
the relational data model was an elegant solution that kept databases as 
small as possible.  Also, by splitting the data up into its distinct 
entities and making explicit the relationships between them, relational 
databases enable us to make complex, ad hoc queries to retrieve data in 
interesting combinations.  

[^disk]: According to Matt Komorowski, a gigabyte of disk space cost 
    over \$100,000 in 1980 and was less than 10 cents in 
    2010. http://www.mkomo.com/cost-per-gigabyte

Furthermore, relational data modeling helps businesses protect the integrity 
of their data by preventing **anomalies** or inconsistencies that can crop 
up as a database is updated over time.  Because each piece of data is stored 
in only one place, for example, there is only one "source of truth".  For 
example, a customer's name and address are stored in only one place, so if he 
changes it, the updated information will be the only information kept in 
the database.  Contrast this to something like a spreadsheet of orders, 
in which the same customer may appear with multiple entries of old and 
new information.

All in all, the relational model is a powerful and versatile way to model 
data.  So why do we need any others?  

Two broad categories of reasons are performance at scale, and 
ease of use.  Analytics applications such as business 
intelligence systems have unique needs: they are intended to allow 
people who are not experts (in database technology) to summarize, slice, 
and dice large amounts of data, and must support queries that aggregate 
many rows at once without bogging down.  Relational databases at the enterprise 
level can have dozens or even hundreds of tables; therefore, writing ad 
hoc SQL to analyze the data can be extremely complex, beyond the capabilities 
of a casual user.  Also, the joining of numerous tables into a query can slow 
down the database's performance.  In analytics applications, moreover, the 
data is typically accessed in a read-only manner: analysts are not making 
changes to the data, so there's little risk of the kind of anomalies that 
the relational model serves to prevent.

For analytical data warehouses, therefore, the **dimensional modeling** 
popularized by Ralph Kimball is recommended.  In this way of structuring 
data, each model features one large "fact" table and a number of smaller 
"dimension" tables.  This makes it simple to create queries of the form 
"*FACT by DIMENSION*": sales by quarter, shipments by zip code, returns 
by product type, etc.  The goals of dimensional modeling are fast query 
performance, and simple query structure for casual database users.

{title="Table 3.3: Types of data models"}
| Modeling Approach | Brief Definition |
|--------------------------------------|
| Relational model | Numerous tables corresponding to atomic, non-redundant entity types, with relationships strictly defined by keys. Complex queries may join many tables. |
| Dimensional modeling | Each model (schema) has one large "fact" and several small "dimension" tables. Queries allow filtering and summarizing the fact *by* the dimensions. |
| Aggregate-oriented | Arbitrary data structures are stored without breaking them up, addressed with unique keys. Data models may be document-oriented, understanding the data as JSON-like structures, or simple key-value stores. |
| Graph-oriented | Data modeled as a network with nodes and edges. No tables. |

Another driver behind the move away from relational databases is to 
simplify application development for the Internet.  One of the complexities 
of using a relational model is that intuitive "objects" or aggregates of 
data that we may work with in a software program, when we want to persist 
them to disk, must be split up into a number of rows in different 
tables.  An *order*, for example, probably refers to rows of a customers 
table, products table, order lines table, and others.  When we want to 
enable complex queries on the data, this may be worth it, but in many cases 
developers would find it simpler to just store the entire aggregate in one 
place.  This is known as the **impedance mismatch** problem: the way data 
is modeled in computer memory when our software is running does not match 
the way it must be modeled when we store it to disk.

As a result, in recent years we have seen a rapid growth 
of **aggregate-oriented databases** such as MongoDB.  In aggregate-oriented 
databases, an entire complex data structure is stored with a unique 
identifier (a key) and can be retrieved all at once with a call to that 
key.  Some are **document stores** which understand the data as JSON-like 
or XML-like documents and can process complex queries on them; others are 
**key-value stores** that are indifferent to the data format and simply 
store and retrieve the data in any form the user wants.[^aggor] In either 
case, the data is modeled in whatever way the user wants, without the 
formal restrictions of the relational model.  A further advantage for 
application developers is that the model can change or expand at any 
time, so less up-front planning is needed.

[^aggor]: Key-value stores and document stores have often been treated 
    as distinct types, but Martin Fowler (see Recommended Reading) argues 
    that they are two extremes of one continuum, and the term 
    "aggregate-oriented" is a good label for this category.

It turns out that aggregate-oriented data models are also very helpful when 
we're building distributed systems.  With a relational database, you run 
the risk that the rows of data needed for a particular query may be stored 
in separate disks all over your computer cluster.  With a document store 
or key-value store, each aggregate is self-contained---the data that will 
be retrieved together is all stored in the same place.

Of course, this does not apply when one wants to conduct a query that cuts 
across aggregates.  To give an example: if you create a document store for 
a job search website containing numerous user profiles, it would be easy 
to query a user and retrieve all of the jobs listed on his CV.  If you 
wanted to query a particular company and find all of the people who had 
worked there, however, such a query would have to scan all of the profiles 
(on all of the disks in the cluster!) costing a great deal of time.  One 
of the strengths of the relational model is its ability to make these kinds 
of ad hoc queries.

In addition to relational, dimensional, and aggregate-oriented data models, 
a few others have gained traction recently.  **Graph databases**, which model 
data as networks of nodes and edges (mathematical graphs), have the potential 
to simplify queries on social network type data.  Message queues, columnar 
databases, and other models have found applications.  In general, the rapid 
rise of new types of data models is referred to as the **NoSQL** 
movement.  NoSQL itself stands for "not only SQL" and it is an umbrella 
term that doesn't have a strict definition. Martin Fowler's video on NoSQL, 
and the book *Seven Databases in Seven Weeks*, are both excellent 
references to learn more. See Recommended Reading, below.

![Table 3.4: Strengths of different data modeling approaches](/images/datamodelprocon.png)

## References & Recommended Reading

- "Introduction to NoSQL" by Martin Fowler.  [[Link]](https://youtu.be/qI_g07C_Q5I)
  
- Redmond, E., & Wilson, J. (2012). Seven Databases in Seven Weeks. The
  Pragmatic Bookshelf.

- Hoffer, J., Topi, H., & Ramesh, V. (2014). Essentials of Database 
  Management.  Pearson.
  
- Kim, J. (2014). "Why I Love Databases." 
  [[Link]](https://medium.com/@jeeyoungk/why-i-love-databases-1d4cc433685f)
