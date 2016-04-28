# Chapter 5: Opening Your Data to the World

This chapter introduces the HTTP protocol and its implications for 
communicating with data through the Internet.  I discuss the challenges of 
statelessness and unreliability, safe and unsafe HTTP methods, and show how 
a RESTful architecture enables us to cope with them.  This leads into a 
section on API design.

## The language of the Internet

Many courses on databases or data management begin by assuming that you have 
certain software available on a certain type of computer, and proceed to tell 
you how to use it.  On the contrary, we will assume that at some point you 
are going to have to communicate data over the Internet, whether you are 
providing data *to* others, collecting it *from* others, or both.  You 
will want them to be able to build applications that interact with your 
systems, but you cannot know exactly what software they will use, and 
they won't be able to directly access whatever database system you're 
using.  The Internet itself determines some limitations on how you can 
communicate in data.

The language of the Internet which we are concerned with is the HyperText 
Transfer Protocol, **HTTP**.  What it means to call it a "protocol" is that 
HTTP defines a structure for messages to be sent between client machines and 
server machines.  It is not a programming language, or a specification for 
the underlying networking technologies.[^netwk] Instead, HTTP gives us a 
format for how to write a data message so that the person or computer on the 
other end of the line will know how to read it.  HTTP was invented around 
1990 by Tim Berners-Lee and his team along with HTML, the web server, and 
the first web browser---together creating the World Wide Web.

[^netwk]: Networking is a huge topic, including aspects of hardware and 
    software, beyond the scope of this book.  Assume that the infrastructure 
    exists for sending messages between computers.  What HTTP does is tell us 
    how to structure those messages so the machine on the other end knows how 
    to read them.

Two types of messages in HTTP are requests and responses.  Typically, a 
client (such as your web browser) sends a request to a server (such as 
Google's or Amazon's 
web server) and the server sends back a response (such as a web page, 
image, or PDF).

<todo: A diagram of the request/response process.>

Every HTTP message has an initial line, which is different for requests and 
responses, optional "headers" (which are metadata about the message), and 
then the optional main body of the message, which may be any type of 
data.  In the message body you can send text data like HTML (a web page), 
XML, or JSON, or you can send binary data like pictures in PNG format, 
documents in DOC or PDF format, and more.

<todo: Illustration of typical request message>

The initial line for an HTTP request includes a method, a URL, and the HTTP 
version, for example:

    GET /path/to/file/index.html HTTP/1.1

[^url]: URL stands for "Uniform Resource Locator" and is the address of the 
    resource (such as a file) that you are requesting.

The most common method you use when browsing the web is `GET`, which simply 
says, "send me this resource", and doesn't change data or have other side 
effects on the server.  You sometimes use `POST` for submitting forms, 
commenting on blogs and so forth---this is a method that adds data to 
the server.  You might use `PUT` when updating a profile, and there are 
several other methods that are used from time to time.

The initial line for a response from a server includes the HTTP version, a 
response code, and an English "reason phrase", for example:

    HTTP/1.0 200 OK

or

    HTTP/1.0 404 Not Found

Most communication on the internet occurs in this pattern of requests and 
responses.  The requests often include metadata ("headers") which may 
identify the person who made the request and the browser version he is 
using.  Response headers may include the date of the response, and if 
there is a message body such as a web page, will tell you the type of 
data to expect, and its length.  Several other headers may be useful for 
different kinds of tasks.

##Communicating in Data

One problem we face in sharing data over the Internet is that the networks 
are unreliable.  Sometimes a message will not reach its intended 
recipient.  In other cases, the response may be delayed and the requester may 
repeat his request, so the server receives the same message twice.  These 
kinds of problems can have severe side effects---think of a credit card being 
charged twice for one purchase---and in designing for data communications 
over the Internet we need to consider them.

Another problem is that communication via HTTP is **stateless**.  If I make 
two requests to a web server, the server doesn't automatically know that the 
two requests came from the same person, or whether they were part of the same 
"session".  This makes it difficult to know if a person is "logged in" when 
they are trying to access sensitive data, and it complicates the 
implementation of something you see on a lot of web sites: a "next page" or 
"more results" button.  If the server doesn't remember what I searched for, 
or which page of results I saw last, it won't know which data is "next".

Many solutions have been used to overcome these and other problems in the 
design of web applications.  Some simple solutions are easy to imagine, but 
they have the feeling of rowing a boat upstream---they fight against the 
nature of the Internet rather than adapting to it.  For example:

- The web developer could encode a session ID or page number in the URL for
  each subsequent link from the initially-requested page.  This leads to the
  long, ugly URLs which you have undoubtedly seen around the web (*ahem*, 
  Amazon).  This method could fail if one user e-mails one of these URLs to
  another user, so the session ID is re-used and cannot tell you about a
  unique user's visit to the site.
- "Cookies" are encrypted data passed back and forth with the request-response
  cycle via headers instead of as part of the URL.  Although they don't have
  the side effects of ugly URLs, cookies can be ineffective because many users
  turn off this feature of their browsers, knowing that cookies are often 
  used by advertisers to track
  their activity.
- The server could attempt to infer from the user's IP address (the equivalent
  of a postal "return address" whether a user is new or returning.  This can 
  be inaccurate because multiple users may appear to have the same IP address 
  if they are on a private network behind a firewall, and in other ways.

##REST to the Rescue

A more mature solution to the Internet's challenges is found in the 
Representational State Transfer or **REST** architecture, first proposed 
by Roy Fielding in a dissertation at U.C. Irvine in 2000.  Instead of fighting 
against the characteristics of HTTP and the Internet, this architecture is 
designed to take advantage of them.

According to a white paper by IBM's Alex Rodriguez, a modern implementation 
of REST architecture for a web service follows four basic design principles:

- Use directory-structure-like URLs mapped to resources.
- Map HTTP methods to CRUD functions.
- Design your applications to be stateless.
- Communicate in XML, JSON, or both.

The first of these principles requires that we think of our data as 
"resources" to be accessed, rather than programs to access them, and use 
URLs that seem to be addresses to those resources.  The old, 
non-RESTful[^rl] way to implement comments on a blog (to give an example), 
might be to write a piece of code called `comment.php` and the URL to read 
the comments might be:

    http://www.myblog.com/comment.php?post=42&action=read

[^rl]: RESTless?

The RESTful way to address the comments for this article would be to use a 
URL like the following:

    http://www.myblog.com/articles/42/comments

This implies that there is a folder or directory called "articles", and a 
folder for article #42 within it, and a folder of "comments" within that.  On 
the server side, these folders need not actually exist, but the server should 
be programmed to receive a URL that looks like a folder structure and respond 
with the proper data.  The simulated folder structure has a couple of 
benefits.  First, it hides the details of how the site is programmed, and 
this means we could change the programming without changing the URL 
structure.[^pdi]  Second, and more importantly, it allows our consumers to 
use the HTTP methods directly on the data (aka "resources") that they want, 
instead of indirectly by using coded "actions".

[^pdi]: Again with the program-data independence!

The RESTful architecture maps the four main HTTP methods `POST`, `GET`, 
`PUT`, and `DELETE` to the four "CRUD" operations which appear in almost 
any application that operates on data.  See Table 5.1.  CRUD stands for 
Create, Read, Update, and Delete.  It is almost universal that we will want 
to add new data to our system, read the data we have stored, change existing 
data, and remove data from storage.  Consider the contacts list on your 
phone---a simple database.  You need to be able to add new contacts, browse 
them to find one you want to call, update them when friends get new phone 
numbers, and delete the old ones you never used.

{title="Table 5.1: CRUD methods in HTTP"}
| HTTP method | CRUD operation | Meaning |
|----------------------------------------|
| `GET` | Read | Fetch data without altering it. |
| `POST` | Create | Add this new data to the server, creating the resource. |
| `PUT` | Update | Replace this existing resource with this (updated) data. |
| `DELETE` | Delete | Remove this resource from the server. |
 
Allowing our users (humans as well as software applications) to directly 
access data resources also helps us to achieve statelessness, and to 
mitigate some of the risks inherent in unreliable networks.  Of the four 
main HTTP methods, one (`GET`) is "safe" meaning that by design it has no 
side effects on the server.[^harmless] Two of the other methods (`PUT` 
and `DELETE`) are **idempotent**, meaning that they have the same 
effect---and no unintended side effects---whether they are executed once, 
twice, or a dozen times.  There can still beside effects when using the 
`POST` method, such as adding the same blog comment twice, but we are at 
least removing the problems that occurred in the past when `GET` and 
`POST` were used haphazardly for *everything* instead of only the operations 
proper to them.

[^harmless]: Except harmless ones like logs of web traffic, advertisements 
    served, and so on.

Finally, web services must communicate in a standard data interchange 
format, usually JSON or XML, although the other formats discussed in 
Chapter 2 may also be good candidates for the reasons stated there.

In sum, if we want people to be able to access our data over the Internet, 
we need to make it available via the HTTP methods, using sensible URLs that 
correspond to the data resources themselves.  We need to receive requests 
in standard data formats, and send responses in standard data formats.  If 
we are merely sharing our data, we can start by implementing the `GET` 
method.  The other HTTP methods are available if we want to create a 
truly interactive data application.

If we adhere to these principles, we gain a great deal of freedom!  Since 
our URLs refer to resources rather than to pieces of code, we're free to use 
any technology we want to code our application, and to change it at any time 
without disrupting our users.  Since our application is stateless and most 
of its methods are safe or idempotent, we can use cluster computing, caching, 
and other tricks on the back end to improve performance.  By designing the 
RESTful interface or API *first* and then coding the implementation, we 
save a lot of time in programming, testing, and supporting customers.

##Designing an API

A RESTful data service that communicates via HTTP is an example of a
**web service**.  You build one with the same server technology that you'd 
use to support a human-readable website, but instead of styled text you're
sending serialized data as JSON or XML.  More commonly, a web service may
be called an **API** or application programming interface.

Good API design begins with pencil and paper.  An API can be thought of as 
a contract between you (the developer and owner of a data product) and your 
consumers (the people and products that will use your data).  You are 
agreeing to make certain resources accessible in prescribed ways, and if 
you change the API unexpectedly, you may cause headaches for your consumers 
as their products and their businesses stop working.  So let's first think 
about API design from the point of view of the consumer---what might he need 
from your data?

Imagine, for example, that your application is the database behind a 
Twitter-like microblog.  The API should have a URL endpoint[^end] for each 
entity type in the 
microblog's universe.  These "entities" are its nouns; some are individual 
things: users, messages, hashtags, etc., and others are *collections*, 
such as the set of all messages posted by a certain user.  Your API 
should provide a way to address both individual things and collections 
of things.  It should use the four main HTTP methods in the correct 
way, as they are its verbs.

[^end]: The URL "root" is the part that stays the same for all API calls,
    for example, `www.mysite.com/api`.  The "endpoints" are the various
    addresses and address patterns that may follow the URL root to specify
    the user's objective.

An initial sketch of the API design might include the following:

> `GET /users`: List all users of the system (usernames and real names)
> 
> `POST /users`: Create a new user
> 
> `GET /users/{username}`: Retrieve all data about the specified user
> 
> `GET /users/{username}/posts`: Retrieve all posts by this user
> 
> `DELETE /users/{username}`: Delete the specified user account

And similar methods would be documented for URL endpoints for messages, 
hashtags, and other data in the application.  After the API endpoints, which 
correspond to resources, your API may provide a number of optional 
parameters to filter the response.  For example, instead of all messages, 
your user may only want the last 10, or only want messages posted during the 
last month.  In a URL, these parameters follow a `?` and are separated 
by `&`.  For example:

> `GET /messages?limit=10`: Retrieve the last 10 messages.
> 
> `GET /messages?limit=10&offset=10`: Retrieve 10 messages *after* the first 
> 10 (in other words, retrieve "page 2" of messages).
> 
> `GET /messages?month=January&year=2016`: Retrieve all messages from 
> January 2016.

Some other good practices in setting up your API are to choose a good 
URL root.  You could use something like `http://www.mysite.com/api/*` 
, or you could use `http://api.mysite.com/*`(the `*` is where your consumers 
put the  specified URL endpoints).  You may also want to include 
a version number, so that if you change the API later, consumers can still 
use the old version.  For example: `http://api.mysite.com/v1/*`.

Your API should also be well-documented so that your users don't have to 
figure it out for themselves.  Where should you put this documentation?  A 
good choice might be to put it in the form of a web page at the root URL, so 
that anyone with access to the API also has access to the documentation.  Be 
sure to include sample URLs, as many people find it easier to modify the 
sample than to build their own from scratch.

## Summary

Your database may have an elegant internal structure, but when you want to
send or receive data through the Internet, you'll probably be dealing with
JSON or XML transmitted to and from a stateless counterpart over a network
that cannot be guaranteed to be reliable.  A set of RESTful principles 
guide the design of data web services, or APIs, that are adapted to these
constraints.  RESTful services leverage the semantics of HTTP, for example,
by mapping the CRUD operations to the four main HTTP methods.  An API can 
be thought of as a contract between the data
provider and his customers, and it may be a valuable exercise to draw an
API up with pencil and paper in order to understand this contract better
before writing any code to implement it.

## References & Recommended Reading

- Fielding, R. (2000). Architectural Styles and the Design of Network-based 
  Software Architectures. [Dissertation]. Chapter 5: Representational State 
  Transfer (REST). [[Link]](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)

- Grinberg, M. (2014). Designing a RESTful API with Python and Flask. 
  [[Link]](http://blog.miguelgrinberg.com/post/designing-a-restful-api-with-python-and-flask)

- Hunter, T. (2014). Consumer-Centric API Design. [e-book]. 
  [[Link]](https://github.com/tlhunter/consumer-centric-api-design)

- Rodriguez, A. (2008). RESTful Web Services: The basics.  IBM 
  developerWorks. [[Link]](http://www.ibm.com/developerworks/library/ws-restful/ws-restful-pdf.pdf)
  
