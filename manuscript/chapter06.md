# Chapter 6: Data for Humans

The products of data analysis must ultimately serve human interests and
lead to some kind of value.  In order to become better "publishers" of
data we survey the ways that information and analytics can augment
the intellect, and identify some guidelines for how analytics 
developers should take human beings into account.

## Value from data

At the end of the day, we analyze data because we think it will lead to
some kind of **value**.  In a for-profit business, that generally means 
greater revenue, lower costs, or some other improvement in the economics
of the business.  In a non-profit, government, or scientific organization, 
value could mean any of those things, too, or it could be realized as
growth in services performed, health outcomes, environmental outcomes,
electoral success, or any of a number of other signifiers of success.

Data and analysis can bring value to individuals and organizations by
three general pathways: ad-hoc analysis, analytical systems, and operational
analytics.

####Ad-hoc analysis

**Ad-hoc analysis** refers to the case where a lone data scientist or 
an analytical team works on a unique data problem to find an answer or 
provide guidance to a decision maker.  This is closely related to **data 
journalism**, the use of analysis and visualization to tell stories
with data.  What the two approaches have in common are that the data
analysis is done just once, and then the team moves on to another 
problem for another decision maker.  Acting as advisors, they enable
decision makers to realize better decision outcomes in one-off scenarios.

####Analytical systems  

**Analytical systems** represent the automation of data analysis.  For 
the kinds of decisions that are made many, many times---such as which 
loan applications to approve, which offers a salesperson should make to
a customer, or which aircraft need to be maintained---managers and
decision makers benefit from having systems like business intelligence
dashboards, data-driven mobile apps, and other systems that deliver
the data in raw or analyzed format that they need.  These data products
or services are built by teams that combine analytical skills with
"data engineering" skills with databases and business intelligence
tools like Tableau.  Analytical systems create value in the form of 
systematically better decision making over time.

####Operational analytics  
**Operational analytics** is a term that refers to the embedding of
data analysis capabilities into the front-line systems used by employees
and customers on the front lines of a business.  Think of the 
recommendation engines on e-commerce websites that tell you about
products you might like to buy, or the machine at the grocery store
that prints out coupons for just the things you might like to buy. The
people who use these systems may not know about the sophisticated
data analysis that makes them work, but the developers of such systems
need excellent skills in data analysis and data engineering as well as 
traditional software development and project management skills.  Because
these applications impact the everyday workings of the business, they
may potentially create the most value of all by directly increasing
revenue and cutting costs.
  
![Fig. 6.1: Pathways to value from data](/images/3pathways.png)

Figure 6.1 summarizes this framework.  As these three pathways to value
differ, you might wonder whether the sort of data that's needed differs
as well.  In fact, one of the biggest determinants of how you should
analyze and deliver data is whether it will be used by a human decision
maker or by a machine.
  
## Analytics for humans or for machines?

Some of the more glamorous and headline-getting applications of big data 
are those in which we see computers making automated decisions on a scale
and with an accuracy impossible for humans.  Algorithmic traders on Wall
Street are machines that find opportunities in the market and make trades
in milliseconds.  Self-driving cars in Silicon Valley analyze rich data
streams coming in from cameras, GPS, and radar to drive, steer, and brake 
in real time.  Bank systems flag suspicious transactions and put them on
hold to prevent fraud, all based on the slightest irregularities.  How
do they make the right decisions so often?

The answer is: we can't really know.  Systems like these commonly us use 
sophisticated data mining or **machine learning** algorithms and complicated
statistical methods to build models, but these models may be expressed as a
set of coefficients or something even more bizarre.  They may draw on dozens
or hundreds of variables to squeeze every drop of value out of the 
available data.  Although machine learning and artificial intelligence 
systems can make reliable and 
accurate predictions, some of their models communicate next to nothing 
about the cause-and-effect mechanisms at work.

Analytics for humans must be different.

If there is only one sweeping generalization that we can make about a human 
decision maker, it's that he has to make a decision.  The nature of reality
is that only certain things are within his control.  Therefore, he needs
information about cause-and-effect relationships that pertain to what he 
has the power to affect.  

The classic case study of analytic findings that could *not*
be put to use is the "diapers and beer story": around 1992, Osco Drug
conducted a large data mining project to analyze customer purchases
and found that between 5:00pm and 7:00pm customers were often buying
diapers and beer in the same shopping carts.  The story has become
something of a legend, but what came of it?  In fact, Osco Drug didn't
do anything with the finding, because it doesn't tell them anything
about what caused it or how it might be leveraged.[^osco]

[^osco]: The true story behind this legend was tracked down by Daniel
    J. Power and documented at http://www.dssresources.com/newsletters/66.php.

On the contrary if they had found out that changes in the prices, or 
in where the items were located in the store, were correlated with 
sales of diapers or beer or both together, *then* the retailer would
have learned something they could use to improve the bottom line.  we
can say that analytics for humans must say something about the "how"
or the "why", and often use simple techniques and fewer variables
that focus on the decision maker's **levers of control**.  The goal
of such analytics is to augment, rather than to challenge or replace,
the human brain.

## Augmenting the brain

The quest to develop information systems that could augment the human mind
is a fascinating story that includes some of the most famous names in the
history of 20th-century computing.  It began, most would agree, with a 1945
article in *The Atlantic* by Dr. Vannevar Bush entitled "As We May 
Think". Bush had been the Director of the U.S. Office of Scientific 
Research and Development, overseeing military R&D work throughout 
World War II.  Reflecting on the vast body of scientific work and the
increasing specialization of its fields, he saw that the accumulated 
knowledge of the world was becoming too large to easily navigate.  Mendel's
discovery of genetics, for example, was lost for decades because the few
biologists who could have understood and built on it weren't the ones who
happened to come across it at the time it was written.

The problem, as he saw it, was not due to the limitations of printing,
photography, or other media technologies, but due to the way we organize
information:

> "Our ineptitude at getting at the record is largely caused by the 
> artificiality of systems of indexing.  When data of any sort are placed
> in storage, they are filed alphabetically or numerically, and information
> is found (when it is) by tracing it down from subclass to subclass. It can
> be in only one place, unless duplicates are used; one has to have rules as
> to which path will locate it, and the rules are cumbersome. Having found
> one item, moreover, one has to emerge from the system and re-enter on a
> new path.
>
> The human mind does not work that way. It operates by association.  With
> one item in its grasp, it snaps instantly to the next that is suggested
> by the association of thoughts, in accordance with some intricate web of
> trails carried by the cells of the brain.... the speed of action, the 
> intricacy of trails, the detail of mental pictures, is awe-inspiring
> beyond all else in nature."

As a potential solution, Bush imagined a future system called the "**memex**"
which would store a library of books and articles as well as the owner's own
notes and correspondence on futuristic microfilm, allowing him to call up
any file at will.  The most fascinating feature he envisioned was that the
memex would allow users would be able to define "associative trails" between 
documents, linking this book to that article to another conversation.  These 
"trails" could then be shared with other memex users and linked to other 
trails.  These associations might not be as complex or as immediate as
human pattern recognition, but they would have one advantage over the brain:
their permanence.  Over the years, he envisioned, the body of associative
knowledge would result in new kinds of encyclopedias, histories, and other
documents organizing information by associative links instead of 
by alphabet or Dewey decimal number.

### Hypertext

The idea of organizing data by association was something of a crackpot idea
in the computer world at that time, because computers were seen as tools
for automating computation and not for supporting "knowledge work".  Bush's 
paper was discovered, however, by just the right crackpot: Douglas 
Englebart.  Over the span of two decades, Englebart developed technologies
with the single goal in mind of augmenting human intelligence and human
ability to collaborate on complex problems.  

In a 90-minute presentation in 1968 now known as "The Mother of All Demos", 
Engelbart and his team at Stanford
unveiled the first computer mouse, graphical user interface, word processor,
and other technologies that became the foundation of modern computer 
interfaces, among them the idea of clicking on "links" to jump to different
documents in the database.  The demo inspired the innovators of the
Xerox PARC, whose prototypes subsequently inspired Apple, IBM, and Microsoft 
as they brought the first commercial personal computers to the world.

The term **hypertext** was first coined by author Ted Nelson who worked
on a similar idea also inspired by Vannevar Bush's memex, and refers to
a document or documents linked by association.  Several projects of varying
success tried to make hypertext a reality during the 1970s and 1980s, such
as Apple's HyperCard software, but it became a major part of our lives in
the 1990s when a CERN researcher named Tim Berners-Lee designed the HTTP
protocol and a hypertext technology for the open Internet that he called the 
WorldWideWeb.  

Originally intended to host scientific papers and allow them
to link to other papers in their bibliographies, this became the Web we
know today with the invention of graphical web browsers around 1995. Also 
in 1995, Ward Cunningham invented the first hypertext system that could
be directly edited by its users: the wiki.  By now, a generation has
grown up familiar with, and having the expectation of, being able to
navigate from one piece of information to the next by associative links.

What potential does this idea have for the delivery of data analysis?  Very
possibly it has enormous value to help managers and analysts put information 
in the necessary context for making decisions.  When looking at a
total of, say, monthly sales, one of the first things a manager may want to 
do is "**drill down**" to the details, such as subtotals of sales by region 
or by product line to better understand the source of his good or bad 
fortune.  Or the reverse: he may want to "**roll up**" tabular data into
a time series or set of subtotals.  Sometimes he might even 
"**drill across**" two data models, analyzing sales side-by-side with
marketing expenses or other data.  As data engineers, we need to select
tools that allow the possibility of **hyperlinking** from one view of
the data to others.

### Visualization

At the same time Bush, Engelbart, Berners-Lee, Cunningham and others were
working on computer systems to organize human knowledge by associative 
links, another series of innovators were researching ways to augment our
cognitive limits with charts, graphs, and other data visualizations.  The
common line graphs, bar charts, and pie charts we are all familiar with
date back to William Playfair, an 18th-century Englishman, and it has
long been recognized that these graphics help us to intuitively compare and
"tell a story" about the data.  In the 1960s and 1970s, new types of 
visualizations were developed to adapt to the use of computers for 
statistical analysis, including some created by mathematician John Tukey.

Tukey introduced the idea of "exploratory data analysis" as distinguished
from "confirmatory data analysis".  In addition to using statistics and
visualization to show our findings, he also wanted statisticians to use
these tools to summarize the data, visualize how it was distributed, and
perhaps form new hypotheses about it before rushing to confirm their
prior theories.  He invented tools like the box-and-whisker plot to allow
researchers to see the distribution of their data and identify outliers.

![Fig. 6.2: Example box-and-whisker plot (from Wikipedia)](/images/boxplot.png)

As computer technology advanced, scientists and businesspeople found
themselves working with bigger and bigger sets of data, and new inventions
were needed to make it possible to understand it all.  Edward Tufte is one
of the men best known for innovation in data graphics over the past few 
decades, and his most famous innovations (including sparklines and
small multiples) tend to share this property: they are clever ways of 
compressing lots of data into graphics that can fit on a single page.

Visualization works because it helps us with one of the most important
limitations of how our brains work.  The human mind seems to have unlimited
long-term memory but very limited short-term "working memory".  Like computers
with too little RAM, we can only hold a few ideas in our heads at once, maybe
three or four. The funny thing is, though, that these ideas can be of very 
different kinds. We can hold three or four *letters* in working memory,
or three or four *words*, or three or four *concepts*.  This idea is
called "chunking": by combining pieces of information into sensible 
groupings, we can then work on the groupings instead of individual 
parts.  Similarly, with quantitative data, a visualization like a line
graph allows us to "chunk" a whole series of numbers into one line, and
compare it to a few other lines at the same time.  In effect, we are
lightening the load on our working memory by organizing data into
patterns and pictures.

Good data visualizations take advantage of what we know about the intuitive
or instinctive capabilities of human vision.  Certain aspects of graphics
are interpreted in different ways.  For example, when we see one line longer
than another, or a symbol bigger than another, we intuitively associate that
with quantitative difference: the first represents a larger number than
the second.  On the other hand, differences in shape or color naturally
seem to signify qualitative differences: if we see blue dots and green 
triangles on a chart, we assume they represent two different kinds of
cases.  Jacques Bertin, a French cartographer, mapped the features of 
graphics to the way a human eye interprets them in a monumental work
called *Semiologie Graphique* in 1967, and his work has been the guide
for almost all researchers in information visualization since.

## Iterating toward enlightenment

When humans use analytics, we are trying to solve complex problems.  For
a complex problem, there is no instruction manual---that's the 
definition.  Analyzing data as a human, with humans, and for humans,
therefore requires creativity and experimentation.  In Silicon Valley,
the term they'd use is **Agile**.  Agile approaches to problem solving
all have a few things in common: they involve iteration, they depend on
frequent, empirical feedback, and they try to stimulate the creativity
of a cross-functional team.  

An Agile analytics team must employ "enlightened trial and error"; it cannot
simply assume that its first analysis will be right.  In fact, one of the
big differences between analytics development and traditional software
development is that in analytics, you often don't know what's second on
the list of priorities.  You have to ask the first question of the data,
and find out what the data tells you, in order to decide what's the next
question to ask.  In order to implement an Agile approach, therefore, you
need two capabilities: first, you have to be able to rapidly try new
analyses, and second, you have to be able to get feedback and share it
with the team quickly.  That's not possible if you only have access to
run your MapReduce jobs on Sunday nights when the business is closed, and
it's not possible if the guy who does the data visualizations is on
five other teams and can only give you 20% of his time.

Data scientist Russell Jurney in his book *Agile Data Science* describes the
environment that an Agile analytics team needs as part design studio, part
library, because team members need collaboration spaces as well as private
spaces for intense contemplation.  He advises that such a workspace include
large-format printers so that preliminary data visualizations can be 
printed out and taped on the wall so that everyone can look at them in 
search of patterns.  Most of all, he describes a style of working in 
which a working data product, whether it be a chart, a report, or a
hyperlinked web app, should be produced and shared at every iteration
of an analytical project.  In other words, don't wait to "publish" at 
the very end of the project.  Early "drafts" are just as useful to the
analytics team itself (which is cross-functional and may include the
eventual "customer") as they will be to the end user.

Ralph Kimball, one of the best-known champions of dimensional modeling
for business intelligence, uses the metaphor of a publishing house to 
situate the role of an analytics team.  Just as a magazine publisher needs
to understand its customers, select high quality content that appeals to
their interests, and continually learn and adapt to the changing readership,
an analytics team needs to understand the business's problem, "publish" 
analytics reports and systems that they can use, and continue to improve
and develop the content as it learns from experience.  The key insight here
is that analytics is never "done", and you cannot wait until an analysis
is perfect before you deliver it in a useful way to the decision
maker.  Trial and error, with discipline, is the only way to get from
data to business value.


## References and Recommended Reading

- Bush, V. (1945). As We May Think. *The Atlantic* July 1945 issue.
- Clark, J., and Xu, A. (2014). Plumbing for Philosophers: The Operations of a 
  Data Science Team. Paper presented at pre-ICIS SIGDSA Workshop, Auckland, NZ.
- Jurney, R. (2014). Agile Data Science. Sebastopol, CA: O'Reilly.
- Li, M. (2014). The Question to Ask Before Hiring a Data Scientist. 
  [Link](http://blogs.hbr.org/2014/08/the-question-to-ask-before-hiring-a-data-scientist/)
- Tamm, T., Seddon, P., & Shanks, G. (2013). Pathways to Value From Business 
  Analytics. Paper presented at ICIS 2013, Milan.
- Tufte, E. (2001). The Visual Display of Quantitative Information. Cheshire,
  CT: Graphics Press, Ltd.


