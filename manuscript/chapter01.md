# Chapter 1: Atoms, Bytes, and Databases

This chapter lays the groundwork for learning about data management by
introducing data storage as a physical technology, then discussing how data
is logically organized as files. An example shows some of the practical
problems of a simple file-based approach to data management. Databases are
defined and it is argued that they can provide solutions to these problems,
particularly the problem of program-data dependence. The net effect of this
chapter is to set up the practical challenges that a data engineering discipline
must address, which we will study in the remainder of this course.

## Atoms

Your computer is a physical machine, made of atoms and charged with
electrons. It is easy to lose sight of the artifact and think of the computer only
in symbolic terms: windows, menus, programs, and so on. One component
of your computer is "memory" (or RAM) which holds---in the form of
electrical impulses|the instructions and data that your computer is currently
processing. This memory fades away when the machine is powered off.
Consequently, for you to be able to do any non-ephemeral work, the computer
needs some means of saving its state or its output to **persistent storage**.
Usually this means a device like a hard disk drive (HDD), but there are
other options for persistent storage such as solid-state drives (SSD), optical
disks (CDs/DVDs), thumb drives, and tape drives. Any of these devices can
store files in such a way that they can be "remembered" (loaded into active
memory) the next time the computer is powered up.

In order to really understand "big data", we need to always keep in
mind that these systems have material properties and are affected by laws
of physics; they are more than just software. The physical constraints on
hard drives, processors, and networks have driven the evolution of relational
databases, analytical systems, and cluster-based computing for big data. We
will consider these constraints more in Chapter 8, but take a look at the
videos I've recommended (at the end of this chapter) by Andrew Blum and
Genevieve Bell---both are compelling talks about the unexpected and often
overlooked physical and human aspects of big data and the Internet.

## Bytes

Logically (now we are back in the realm of symbols), any persistent storage
device, such as a hard disk, can be thought of as a long, long list of "ones"
and "zeroes" which encode meaning. Eight of these digits or **bits** taken
together make up a **byte**. We refer to 2^10^ bytes as a kilobyte, 2^20^ bytes as
a megabyte, and so on.[^1] Table 1.1 provides the common terms for various
measures of data.[^2]

[^1]: It's not exactly the metric system. 2^10^ is 1024, close but not quite 
    1000.

[^2]: There's a petition going around to officially name 2^90^---about a 
    nonillion bytes---a "hellabyte".  I'm all for it.  We're going to have to 
    start calling it *something* soon.

![Fig. 1.1: Measures of Data](/images/kilomegagigabytes.png)

The bits and bytes on your disk would simply be electronic gibberish if
your operating system didn't know how to read them. Fortunately, these
bytes are structured according to a **file system** so your operating system
(e.g. Windows, Mac OS, or Linux) can read them and interpret the data
as files. A file is just a chunk of bytes on disk that is given a name and a
place in the structure of folders or directories. In this sense, programs like
Microsoft Excel are files, and so are the spreadsheet documents they allow
you to create. The former are files that contain instructions for the computer,
and the latter are data files that contain program output. More commonly,
we use the word "files" to refer to the latter type only, the data files.

Files (in the second sense) are created by software programs, or by humans
using those programs. You can write a simple program in any programming
language that reads a file or writes to a file. Excel, for example, writes a
.xlsx file and knows how to read a file of that type. You can invent your
own way of encoding data in the files that your program reads and writes.
So who sets the standards about data formats? Anyone can, but Microsoft's
standards are a lot more likely to be taken seriously by the market! If they
change the way they save a spreadsheet file, you can bet that other companies
will quickly update their software to be able to read the new file format.
Files are very versatile. They can big or small, can encode text, images,
sound, video, and other types of data. You can transfer them from one disk
to another, e-mail them to colleagues, and share them on the Internet. But
if this was the end of the story, we'd quickly run into some problems.

## The Trouble With Files

Imagine that you work at a small mail-order business where salesmen take
orders over the phone, write them down on paper, and hand them to a data
entry secretary so that the orders will be fulfilled and sales commissions will
be paid. The secretary enters the handwritten data into a digital file---let's
just say it's a spreadsheet. At the end of every day, she e-mails the file
containing the day's orders to the fulfillment department, which processes
the customers' payments and ships the orders. At the end of every week, she
sends the seven daily files to the accountant, who uses them to compute the
commissions that must be paid to the salesmen.[^3]

[^3]: This is a real-life example, more or less. At my first real IT job, we 
    had an order entry process like this, except instead of e-mail, the data was 
    transferred from one person to another by floppy disk!
    
There are a number of problems that the business is going to experience
in this scenario:

1. The secretary is quickly going to find that she has a lot of files to keep
  track of, if she creates a new one for each day. If she keeps it all in one
  file, it's going to get *big* very quickly, and the two departments who
  use the data are going to find it hard to navigate.

2. The file contains all the information from the paper order form. This
  is a huge security risk, even without the obvious threats of hacking
  or stolen laptops---the secretary is *routinely* sharing customer credit
  card numbers with the accountant, who has no business reason to see
  them. Although a file may be encrypted with a password, there is no
  finer-grained means of control that would enable the secretary to grant
  one user access to part of the data, another user access to the other
  part. It's all or nothing.
  
3. An alternative is to have the secretary enter the information twice: one
  file for the accountant, and one file for fulfillment, each file containing
  just what its intended users are allowed to see. This is a lot of extra
  work, and doubles the number of typos and other errors that will creep
  into the files.

4. If the business grows, and a second data entry secretary is hired, the
  two must coordinate their work somehow. If they both open the data
  file at the beginning of the new day, and add new rows for new orders,
  and then save their work to the same place (i.e. a network drive or
  Dropbox folder that they share), there's a real risk that the second
  one who hits "save" will overwrite and delete all the work of the first.
  Some strategy needs to be devised so that they work independently,
  then consolidate their work.

5. What if the fulfillment department needs to add their own annotations
  to the file, for example, noting when a payment was declined or an
  order was returned by the customer? We quickly run into a **versioning**
  problem, where the fulfillment department is making changes to the
  file they received yesterday, then sending them to the secretary who
  has already added new orders for today. She has to find the changes
  and integrate them into her new file, which may have already had
  other modifications made. This gets even more complicated when the
  accountant starts requesting updates on the old orders, so commissions
  may be adjusted if the customer returns an order three weeks after it
  was taken. In addition to providing the updated file, the secretary also
  needs to highlight *how* it was changed.

6. If the content or structure of the information captured on the paper
  order form changes, it's going to create real headaches for data entry
  and processing. If, for example, salesmen are assigned to different
  regional territories, and need to record "region" on the order form,
  the secretary must add a new column to the spreadsheet. But this
  information is not present in all the historical records, which affects the
  accountant---her process for calculating sales and commission totals
  must change. Over time, the spreadsheet's structure may change several
  times, forcing everyone to keep learning new processes.
  
7. If, in time, the business decides to re-use the order data for some new
  purpose, an analytical purpose, such as customer relationship management
  or marketing research, the analysts are going to find themselves
  dealing with monstrous spreadsheets, probably multiple versions of
  some of the data, and old versions having different structure and definitions 
  than newer versions. The files may be all but indecipherable to outsiders.  

In the early days of business computing, problems like these showed
the limits of using files alone for persisting data to disk. In review, these
problems are:

1. Challenges of scale
2. Security and privacy issues
3. Redundancy and inconsistency
4. Challenges of coordination
5. Version control problems
6. Program-data dependence
7. Files don't describe themselves well

And so, the people of the information age embarked upon a quest for a
better way to manage data. The solutions they developed were **databases**.

## Databases

A database is defined by Oxford as "a structured set of data held in a
computer, especially one that is accessible in various ways".[^4] This is a 
good enough definition. A database management system (DBMS) is the software
that both structures the data, and makes it accessible. From here on out,
when I use the word "database", I am usually referring to the whole package
(database and DBMS), as this is the common usage.

[^4]: http://www.oxforddictionaries.com/us/definition/american_english/database
  
What you get with any kind of database is a software system specifically
designed to keep track of data *and its meaning and structure* somewhat
independently of other programs, and prepared to receive new data from,
or provide access to stored data to, multiple users at the same time with
different needs. At a minimum, a database is a software system that stores
data along with metadata and has some kind of API[^5] by which users can
create, read, update, and delete[^6] data.

[^5]: application programming interface

[^6]: the so-called "CRUD" operations

The term **metadata** is often defined as "data about data" although I
think "information about data" is more accurate. What it means is that,
instead of just storing the data, the database can also inform its users
about what the data is. If the data is `4809650024`, the metadata may be
`Professor Clark's phone number`. There are many types of databases
and therefore many types of metadata---we will explore these more beginning
in chapter 3---but the point is that users of a database can make some sense
of the data without knowing the process or program that created it. Consider
the analyst in my example, who wants to use the historical order data but
was not familiar with the order entry process. If the data were stored in
some kind of database, he could use the metadata as a guide to know which
number was sales, which was returns, and so on.

In addition to metadata, databases also need to present an interface to
humans (and generally also to other software programs) by which they can
access the data. For the past few decades, the best-known such interface
is the structured query language SQL.[^7]. SQL has four main commands:
`INSERT`, `UPDATE`, and `DELETE`, which are used to manipulate data in the
database, and `SELECT`, which is used to **query** the database, in other words,
to request data for some purpose. Dialects of SQL with minor differences are
used by most of the long-established database brands on the market---Oracle,
IBM DB2, Microsoft SQL Server, PostgreSQL, MySQL, etc---so it has the
benefit of being an industry standard.

[^7]: The name of SQL may be pronounced "sequel" or "ess, queue, ell"

Because SQL is a public interface, it means that multiple users and
multiple programs can access the data in the same database. Salespeople
may enter their orders in a mobile app, customers may place orders online
through a website, secretaries may enter the data using a Microsoft Access
form, the shipping department may receive the data via e-mail reports, and
the accountant may view it in a spreadsheet, but, since all of these programs
are speaking to the database via SQL, it doesn't matter that they were all
purchased at different times, programmed in different languages, and used
by different users. Databases therefore offer the benefit of **program-data
independence**: since the data has metadata and a stable interface, we can
change a program or process that interacts with it (such as the structure of
a data entry form) without worrying about disrupting every *other* program
or process that uses it.

Databases can be designed to overcome each the other problems with a
file-based approach that I identified earlier. Performance can be tuned as
databases scale, controlling the levels of redundancy and structure depending
on practical needs. Databases can apply fine-grained security policies to
manage what each user can view or alter, as well as ensuring reliability with
replication and backups. Databases can coordinate the actions of multiple
concurrent users and enforce atomicity and integrity of transactions so that
versions of the data do not get mixed up. New types of databases have
emerged in the past few years to deal with the demands of "big data" and we
will discuss them in this course. But the two universals are that databases
can describe themselves via metadata, and grant program-data independence
via stable APIs such as---but not limited to---SQL.
  
## Data Engineering

The primary limitation on the database approach is that it requires discipline
and expertise. Someone must create and maintain the database---and the
server upon which it resides---so that other users can access it. Since all of
the data is kept "in one basket", so to speak, backups must be made, and
measures must be taken to protect the database servers from hackers as well
as natural disasters. Performance optimizations must be made based on
the scale of the database, the types of queries it receives, and other context.
Moreover, someone has to make decisions about the structure and definitions
of the data---for example, when is revenue counted: when the order is placed,
when payment is received, or later when it is known that the customer
will not return the merchandise? These are some of the responsibilities of
database administrators (DBAs).

In the age of big data, a new role is emerging which takes a larger view
of the flow of business data, a role which I call Data Engineering. For
many years (from perhaps 1985 to 2005), data management primarily meant
choosing a relational database brand and hiring DBAs who knew that brand
(e.g., Oracle). But now, primarily due to explosions in volume, velocity, and
variety of data, a single centralized database can no longer be the entire
picture of how data is organized and accessed in a business. Data engineers
must consider the *flow* of data at Internet speed and at scales beyond what
can be stored on a single server. Cluster-based computing presents new
trade-offs that must be made between data consistency and response time. And 
the different uses of data have such radically different operational needs
(transaction processing vs. big data analytics, for example), that different
data models need to be employed at the same time for different purposes.

A data engineer therefore needs to know about how data is accessed and
shared between users and applications over networks and in computer clusters.
He needs to know about the different types of data models and which tasks
they are best suited for. He needs to know about the new challenges of "big
data" and the tools and techniques that are being developed to work with
it. And fnally he needs to integrate a variety of data management solutions
into a data "pipeline", automate it, and maintain it. The remainder of this
book surveys each of these knowledge areas.

## References & Recommended Reading

- "What is the Internet, really?", TED talk by Andrew Blum, author of "Tubes".
  https://youtu.be/XE_FPEFpHt4. (Or see this longer version of the talk: 
  https://youtu.be/28hzKbcLlWg.)
- "The Secret Life of Big Data", keynote talk at Supercomputing 2013 by 
  Genevieve Bell of Intel.  https://youtu.be/CNoi-XqwJnA
