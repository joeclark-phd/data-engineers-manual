# Chapter 7: A Workflow for Analytics Development 

This chapter tries to map out the artifacts and transformations found in an 
analytics pipeline or "data refinery", to look inside the black box at the
skills and toolkits that will be employed in analytics work.  A traditional
data warehousing model is compared to an emerging "data lake" model based on
Hadoop.  I introduce the idea of Agile analytics
and discuss technological affordances that are needed to make it possible.

## The "Black Box" View

There's a great deal of hype about analytics.  Much of what we read treats
it as a "black box", though.  What that means is that we have read a lot
about the benefits organizations can get from analytics, and a lot about
the ingredients that seem necessary for success, but much less is said about
the process by which those inputs are turned into those outputs.

![Fig. 7.1: A proverbial black box](/images/blackbox.png)

What goes in:

- Data scientists, often PhDs from diverse fields like biology and physics
- Databases, SQL, NoSQL
- Programming languages like Java, R, and Python
- Hadoop (whatever the heck that is)

What comes out:

- Better targeting of customers
- Managers making better decisions
- Competitive advantage (whatever the heck that is)

But in this chapter, I'd like to explore the *inside* of the black box as 
much as I can.  In my interview research with  data scientists and
analytics managers I have sought to understand more about the activities and
workflows within data teams.  I find that the metaphor of a data "pipeline" 
or "refinery" is a useful way to discuss and compare the alternatives.

## A Pipeline to Analytics

We may think of a refinery or assembly line that defines a number of 
**transformations** of raw material into finished products.  At each step,
some kind of intermediate artifact or stockpile is observed.  Let us first 
consider the traditional data warehousing and business intelligence (DW/BI)
model, and then I'll share my thought about an emerging "data lake" model
based on cheap bulk storage options like Hadoop and Amazon S3.

### Data warehousing with ETL

In the two main **data warehousing** architectures--the Corporate Information
Factory (CIF) or "Inmon" model and the Dimensional Data Warehouse (DDW) or 
"Kimball" model--the data pipeline starts with a process called ETL, for
**extract, transform, load**.  Data wranglers and engineers write
complicated programs to pull data out of the various places it lives, such
as the operational systems that run your business, reshape it, reconcile
it with other sources, normalize it, keep track of its history of changes,
and load it into a centralized, structured, professinally managed database 
(or collection of databases) called the data warehouse.  This is tricky
work that may use a combination of commercial ETL software, database
queries, and handwritten code.

![Fig. 7.3: Traditional DW/BI with ETL](/images/ch07_dwbi.png)

Once ETL is complete, the data warehouse is ready for business. It is
a (comparatively) quick task to publish selections of the data,
called "data marts" or "cubes" that analysts and managers can query 
directly.  For more on dimensional modeling, I recommend *Star Schema* by
Adamson, and *The Data Warehouse Toolkit* by Kimball. Here's the big 
takeaway: ETL means a *lot* of up-front work to get data into shape *before* 
you can start gaining value from it.

### A data pipeline built on "bulk storage"

Lately, we are seeing the emergence of a somewhat modified data pipeline based
on cheap bulk storage, sometimes called the "data lake".  It's no secret
what had made this possible: **Hadoop**[^s3] is the *sine qua non* of the
modern big data pipeline.

![Fig. 7.2: A data "refinery"](/images/ch07_refinery.png)

The big difference we're seeing is that nowadays data engineers are loading
the data first into the data lake, then processing it into structured,
usable data later.  Instead of ETL, it's "ELT": load first, transform 
later.  The complex writing of code, SQL, and MapReduce jobs that used to
come before the data was stored, now comes after it.

[^s3]: Amazon's **S3** service also deserves a mention here.  It, too, is
    a platform for massive, cheap storage on a distributed system in the 
    cloud.

For any kind of a **data application** like a web page or a business 
intelligence dashboard you still need structured databases.  Usually
you would use relational databases for critical operational systems, 
dimensional data marts for business intelligence, and increasingly, perhaps, 
NoSQL databases for web and mobile apps.  Although still important,
in the modern analytics pipeline, these are no longer the "systems
of record".  

Instead, the raw data in Hadoop is the centralized
system of record, maintained indefinitely. Since the structured databases 
are created by code (e.g. Python, SQL, or 
MapReduce), they can be re-created from the data lake if needed.
    
![Fig. 7.4: Data pipeline built on bulk storage](/images/ch07_hadoop.png)

A major benefit of this is that you can run some quick analyses right away,
delivering value to the business, and then go back to the same raw data
to analyze it a new way later.  For example, if you acquire a social media
dataset, you could quickly write some code to tally up "likes" and "shares",
preparing a report for the business.  Then you could take time to analyze
sentiment in the text comments, or build up a social graph of your
potential customers.  

The distinction that makes a difference is this: You 
aren't required to do anything up front that locks you in to one and only 
one way of analyzing the data later.  The Hadoop-based workflow allows you
to get up and running quickly: just stash some new data into bulk storage and 
start analyzing it.

### Jobs in big data

It's helpful to map the data refinery in terms of artifacts and "transformations"
because each of these highlights a role for human contribution.  There are
numerous jobs to be done in the data refinery, as this sample reveals:

- There's a role for **data engineers** to set up the infrastructure and teach
  others how to use it (e.g., helping data scientists to run their Python
  code on the Hadoop cluster in the cloud).
- **Data modelers** and **database administrators (DBAs)** are needed now
  more than ever to design and manage the structured databases that underpin
  data-based applications.
- **Application developers** (web and mobile) and **business intelligence
  developers** are needed to create the front-end tools that allow people
  to see, interact with, and analyze data in those databases.
- **Data scientists** need to know how they want to analyze data, and they 
  must be supported by **data wranglers** and **ETL developers** to engineer
  the processes for transforming raw data into structured datasets.
- **Analytics managers** and **project managers** must learn how to support
  all of these professionals in their evolving work.

## Agility in Analytics

One of the reasons the "data lake" model has ascended has been its
compatibility with **Agile** approaches to analytics.  Agile is a term from 
the software development community
for a new movement (the term was coined in 2003) away from traditional
**waterfall** development.  In the waterfall approach, software is
created through a sequence of activities, each one finishing before the
next one begins.  In practice, it means that the project has only
one schedule, only one chance to get it right, and typically the 
customer doesn't get a chance to see the product until almost all of
the time and money is spent.

![Fig. 7.5: W. Royce (1970)'s famous "waterfall" diagram, and the crucial sentence that followed it(!)](/images/ch07_waterfall.png)

In Agile software development, the work is done in "slices" rather than
in "stages".  In a series of short cycles or iterations, developers build one
module or feature at a time.  This means working in a cross-functional team
with designers, coders, testers, and every other skill needed to deploy
working software.  At the end of every cycle, which may be as short as one
or two weeks long, the customer should receive a working product that delivers
at least a small amount of value--and importantly, the customer can give
feedback on it and change the prioritization of what will be built next.

Agile may be *especially* well suited for analytics work, for this reason: in
analytics, we're not building "features" so much as we're answering 
questions.  As a corollary, the customer often can't tell us what to work on 
*next* until he sees the results of the current analysis.  If you're analyzing
the effectiveness of your marketing, for example, and discover a new customer
segment, the next question might be to analyze the buying habits of those
kinds of customers, and so on.  Therefore, getting quick and clear feedback 
from the business is absolutely vital.  The analytics team that is better
at getting analysis *to* the business and getting feedback *from* the business
will have the greatest impact.

### Tools that enable Agile analytics

That said, how do we do it?  What tools and practices will enable us to be
that winning analytics team?

Let's first consider the **unit of work** that we're going to deliver to 
the business.  According to Lawrence Corr and Jim Stagnitto, authors of 
*Agile Data Warehouse Design*, the minimum piece of value we deliver is
one star schema with the ETL process that populates it and the BI tool
or application to access it.  We may generalize to say that the unit of 
work is one analysis or transformation of the data, with a front-end
or report.  So that's what we'd need to produce in one cycle or iteration, 
typically just 2 weeks.

Here's where Hadoop and the "data lake" model really pays off.  Since we can
get data into storage and ready to analyze *without* doing a lot of 
painstaking data modeling and preparation up front, we can get to our first
results much faster.  Those first results might be from a very quick and
crude analysis, but often that's good enough to show the business that 
you can deliver something valuable--and to justify your budget for the next
few cycles.  If we tried to use the traditional DW/BI architecture, we'd
get bogged down designing database schemas and developing ETL processes 
perhaps for months before we could make a first release.

A few other tools and techniques are also enabling analytics teams to
get from idea to results faster:

- Use the cloud for infrastructure, so you don't have to do a lot of
  installation and configuration yourself.

- Use simple frameworks (like MapReduce) that do the behind-the-scenes
  work for you (like coordinating a computer cluster) so you can just write 
  the small amount of code you
  really care about, and don't re-invent the wheel.

- Use tools that allow you to use the same code on your personal computer
  that you'll use in the "production" environment.  If you do your analysis
  with Python or R, it's a lot easier to scale up than if you did it in a
  point-and-click tool like Excel or Tableau.
  
- Set up your workspace like a "design studio" so the whole team can 
  better understand what their teammates are working on and how their
  techniques work.  Don't hide your work in your office until demo day!
  
Agile approaches to software development have many names: Scrum, XP, Crystal,
Kanban, DevOps, and now "Agile Analytics".  All of them have a few things in
common: they're iterative, employ cross-functional teams, and leverage
feedback from an actively engaged customer.  The idea is to combine
flexibility and creativity with discipline.

Instead of being told the requirements, Agile teams "discover" requirements
as it often requires lots of trial and error, and as the customer learns more
about the possibilities of their data.  Agile approaches are designed to
adapt to those changing requirements.

Agile was always a good idea, but wasn't always possible.  It requires new 
technologies and techniques, especially those techniques that allow us to 
avoid lots of up-front preparation and infrastructure work before we can 
start delivering value.  Tools are evolving rapidly in the field, but keep
this in mind: it's not just about what the tool itself can do, it's about 
*what kind of workflow* it supports.

## References and Recommended Reading

- Adamson, C. (2010). Star Schema: The Complete Reference. McGraw-Hill.
- Collier, K. (2012). Agile Analytics. Upper Saddle River, NJ: Addison-Wesley.
- Corr, L., & Stagnitto, J. (2011). Agile Data Warehouse Design. Leeds, 
  UK: DecisionOne Press.
- Jurney, R. (2014). Agile Data Science. Sebastopol, CA: O'Reilly.
- Kimball, R. & Ross, M. (2013). The Data Warehouse Toolkit.  Indianapolis: Wiley.


