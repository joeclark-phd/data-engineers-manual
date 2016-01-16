# Preface: Productizing Data

## Analytics and Business Value

Businesses can get value from data in more than one way.  A paper by Tamm and
colleagues analyzed a number of business analytics success stories and drew a
vital distinction between two roles played by analytics professionals:
"advisory services" to produce one-off analyses and "tool building" to enable
end users to run their own analyses in a self-service manner.  In 2014, Anqi
Xu and I interviewed eleven data scientists and managers and broke down the 
concept of tool building into analytical systems and operational analytics.
Our framework, which follows, depicts a hypothesized "pipeline" from data
science to business value.  The three pathways to value then, are:

1. Custom analytical reports resulting in better **one-off decision outcomes**.
2. Development of analytical systems (such as business intelligence 
  dashboards) resulting in **systematically better decision outcomes**.
3. Embedding analytical capabilities into systems used by front-line employees
  and customers, or operational analytics, resulting in **operational 
  efficiencies**.
  
![Fig. 0.1: Pipelines from Data to Business Value](/images/both_pipelines_2.png)

Most of the attention in the industry has been on the data scientists who use
powerful algorithms and visualization tools to develop new ways of analyzing
business data and find new insights.  This is challenging, creative work, but
by itself it only produces one-time benefits.  There is, I believe, an
equally important new role that has received much less attention than it 
deserves: that of the data engineer (or data wrangler) who can take a new
model or algorithm and automate it, make it repeatable and accessible to
non-expert users such as managers and customers.  These unsung heroes create
*analytical systems*, also called "data products", and are critical to making
the benefits of data analysis accessible throughout the organization.

As a colleague of mine is fond of pointing out, the reason *Star Trek*'s 
science officer Spock can be so effective is that he works on the bridge and
not in a laboratory below decks.  Some data engineer, probably an unnamed 
red shirt, developed the systems that gave him instant access to sensor data
and analytics.

In this course, we will survey the skills and knowledge areas used by data
engineers to build analytical systems.  After completing it, you should be
prepared to work with data scientists and analysts, to take their innovative
new algorithms and models and turn them into automated systems that provide
the information when and where and by whom it is most needed.

## A Hierarchy of Data Engineering Knowledge

I propose that there is a sort of Maslow's hierarchy of data wrangling skills
and knowledge, in which we need to understand the basics of obtaining,
storing, and transforming data, before we can engineer automated analytical
systems built on databases.  I propose that the education of a data engineer
or data wrangler should proceed in stages:

1. First, students need to get a sense of what data "looks like"; they should
  learn about bits and bytes, the physical constraints of disks and memory,
  and see how data is structured in text and binary files, in formats like
  CSV, JSON, and XML.  They should learn to manipulate and transform this data
  with a scripting language (i.e. Python, Perl, or R) and with Excel.
2. They should learn about how data is accessed and communicated over the
  Internet, including an understanding of HTTP, web servers, and RESTful web
  service APIs.  They should first learn how to acquire data via web APIs, and
  then learn how to present data on rudimentary web apps of their own.
3. Next, students should be taught how to model and structure data, and the 
  various advantages of using databases.  I would argue that exposure to a 
  variety of database paradigms -- document-oriented, relational, and 
  graph -- will increase their understanding and appreciation of the benefits
  of data modeling.  They should also learn SQL and the query languages
  for a selection of NoSQL databases.
4. Once they're familiar with database basics, students should be shown how
  databases are affected by scale.  A discussion of the need for indexes and
  other physical optimizations should lead directly to a discussion of how
  analytics use cases are different from transaction processing, and the
  professor can then introduce dimensional modeling and Map/Reduce.
5. At this point, students know enough to build complete analytical 
  applications with database back-ends and web or JSON presentation layers.
  Now the training can turn to the "polish": stylish BI and visualization
  tools, advanced automation of data ingest and preparation, and real-time
  data stream management.

This conceptual sequence is a work in progress, and it will shape the 
organization of my course, CIS 355.

## References  & Recommended Reading

- Clark, J., and Xu, A. (2014). Plumbing for Philosophers: The Operations of a 
  Data Science Team. Paper presented at pre-ICIS SIGDSA Workshop, Auckland, NZ.
- Davenport, T. H., & Patil, D. J. (2012). Data Scientist: The Sexiest Job Of 
  the 21st Century. Harvard Business Review, 90(10), 70-76.
- Driscoll, M. E. (2013?). let us now praise data engineers. Retrieved from
  [http://medriscoll.com](
  http://medriscoll.com/post/49783223337/let-us-now-praise-data-engineers).
- Jurney, R. (2014). Agile Data Science. Sebastopol, CA: O'Reilly.
- Tamm, T., Seddon, P., & Shanks, G. (2013). Pathways to Value From Business 
  Analytics. Paper presented at ICIS 2013, Milan.
