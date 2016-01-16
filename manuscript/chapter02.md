# Chapter 2: Data in the Wild

Data must be communicated in order to be useful, and in almost all nontrivial
applications, this means communication over a network. In order to
be communicated, it must be structured in a way that is intelligible. This
chapter considers the possibilities for structuring data files and discusses 
the strengths and weaknesses of commonly-used formats such as CSV, XML,
and JSON.

## The Context of Data Sharing

In Chapter 1, I argued that data ought to be managed independently of the
programs that create and use it, and typically this means it is stored in some
kind of a database. If we accept that argument, it is obvious that data must
be communicated between systems in order to be useful. In the simplest
case, an application sends a request for data (a **query**) to the database and
the database sends a response that includes the requested data. Data may
also be communicated from one database to another database, or from one
application to another application. Moreover it is often imperative upon
system developers, in this age of open data, to make their data available to
the public *without knowing who will use it or for what purpose*. And likewise,
as application developers we may need to consume data from sources over
which we have no control.

There are two major problems for data engineers to consider here. The
first is how to make data available to other systems and to send, and receive
data "messages" between them. I don't want to spoil Chapter 4 for you, but
I'll tell you the short answer to the first one is "the Internet". 
The other major problem,
and the subject of this chapter, is how to structure those data messages so
that they accurately represent the data and are intelligible to the systems
that receive them.

## Formats for Data Interchange

Raw data comes in a sequence of logical ones and zeroes, and ones and zeroes
do not themselves communicate any meaning unless we have a "decoder ring"
to translate them into words, numbers, audio, video, or something else.[^ch2_1]
Early on in computing, various **encodings** were developed, including 
ASCII[^ch2_2]. In ASCII, a sequence of seven bits would be taken to represent 
a number between 0 and 127, which could be looked up in a table to transform 
it into a letter or a number. The capital letter "J", for example, 
corresponds to binary 1001010 (or the decimal number 74) in the ASCII encoding.

[^ch2_1]: Something else, such as smells! See 
    https://en.wikipedia.org/wiki/Digital_scent_technology.

[^ch2_2]: the American Standard Code for Information Interchange
    
Data that is encoded in one of the standard character sets, such as
ASCII, UTF-8, or Unicode is commonly called "text" data, not because other
formats couldn't represent text, but because these are open standards that
can be interpreted by almost any system, transmitted over the Web, sent as
e-mail, and so on. Every mainstream operating system has a built-in text
editor (such as Notepad on Windows, or vi on most Unix platforms) that
can read and write text files.

Not every piece of data is communicated in a text encoding. Non-textual
data such as pictures, sounds, and video must be stored in different encodings
(such as PNG, WAV, and MPEG), and there are also many proprietary
encodings such as those used by Microsoft Word, Adobe Photoshop, and
other commercial software. You may think of a word processor as storing
text, but remember that a word processor would also need to keep track
of other data such as page layouts, fonts, styles, and other non-text data.
These proprietary formats may be designed to be more compact, or more
secure, than plain text files, but the cost of these innovations is that 
files of these types can only be read by certain programs.

Because text encoding is so universal, and the format is so simple to use,
text is therefore used for a great deal of the data communication over the
Internet. Even limiting our discussion to text files, though, there are still
a number of ways that we might structure a text message to communicate
data. In choosing a data interchange format, we need to consider (1) the
logical structure of the data we need to represent, (2) who the users are and
what they need, and (3) the efficiency of the format in transmitting data.

The biggest constraint in data communication is that *networks are slow*.
Even with relatively fast Internet connections, communication between remote
computers is far slower than processing local data within a single box.
Therefore, efficient formats that pack more data into fewer bytes become
more and more important as data sets become bigger.

### CSV: Tabular data with low overhead

One of the most commonly-used text formats is **CSV**, which stands for
"comma-separated values". Just like it sounds, each data record is structured
as a single row of text, with commas between the various values. A single
CSV file can transmit a number of records, each on a row of its own. An
optional header row may identify the columns. Instead of commas, sometimes
tabs, semicolons, or other delimiters are used.

A list of the cast and crew of the movie *Interstellar* in CSV format might
appear like so:

    FIRSTNAME,LASTNAME,TYPE,ROLE
    Christopher,Nolan,crew,Director
    Hans,Zimmer,crew,Composer
    Matthew,McConaghey,cast,Coop
    Anne,Hathaway,cast,Brand
    Bill,Irwin,cast,TARS
    Matt,Damon,cast,The Remarkable Doctor Mann
    ...
    
As the example shows, CSV is very well suited to **tabular** data, the kind
where there are a fixed number of columns and every row has the same type
of data. Thus CSV is quite compatible with spreadsheets and other data
analysis software. (Tableau and Excel can both easily import CSV files.) If the
data represent a long list of records with a consistent structure, such as stock
prices or weather data, and if your users are going to use the data as a table,
to compute averages or produce charts and graphs, the CSV format may be
a very good fit. In addition, CSV uses bytes very efficiently. Except for the
commas, and the optional header row, everything else in the file is data.

However, the CSV format is limited in its ability to represent data with
a more complex or flexible structure. If you wanted to transmit data about
movies as well as their cast members, you'd probably have to send two
different CSV files, because a data table can only represent one type of thing,
or **entity type**, well. To hold data about *movies*, you'd need a 
different set
of columns, and probably a different number of columns, from those you
would use to hold data about *actors*. Additionally, CSV files treat the 
rows as
independent, and cannot represent relationships or ordering among them.
In the example, there's no way to indicate that the cast members and crew
members are two sub-sets of the data, except to created a column called
"TYPE" and hope that whoever receives the data can use that column to sort
the list.

Since data often has a complex structure and relationships that we want
to communicate, a number of new data interchange formats have proliferated.
One of the best known is XML.

### XML: A document-oriented data format

**XML**, or eXtensible Markup Language, is a relative of HTML, the language
in which web pages are encoded. It allows the encoding of "documents"
with metadata and complex structure, such as internal lists, which can be
validated according to established standards. There are standards for music
data, math equation data, and many other types of documents.

An example of some movie data in XML:

    <movie>
      <title>Interstellar</title>
      <year>2014</year>
      <rating>PG-13</rating>
      <crew>
        <crewmember>
          <name>Christopher Nolan</name>
          <role>Director</role>
        </crewmember>
        <crewmember>
          <name>Hans Zimmer</name>
          <role>Composer</name>
        </crewmember>
      </crew>
      <cast>
        <castmember>
          <name>Matthew McConaghey</name>
          <role>Coop</role>
        </castmember>
        <castmember>
          <name>Anne Hathaway</name>
          <role>Brand</role>
        </castmember>
      </cast>
    </movie>

You can see that XML is hierarchical and that we can "nest" objects and lists.
The main drawback to XML is that it is extremely "wordy" resulting
in large files that are difficult for humans to read, and not really ideal for
computers to read, either. The best cases for using XML are when
you are transmitting an established type of document---and not when
creating your own ad hoc data structures.

### JSON: The programmer's favorite

Until a few years ago, XML was the only game in town, but recently **JSON**,
the JavaScript Object Notation, has begun to eclipse its popularity. As the
name suggests, data is simply described the same way it would be described in
a JavaScript program. This enables the same sort of complex data structures
that XML can represent, but with a lot less overhead. An example of the
movie data above, in JSON:

    {
      "title": "Interstellar",
      "year": 2014,
      "rating": "PG-13",
      "crew": [
                {"name": "Chrisopher Nolan", "role": "Director"},
                {"name": "Hans Zimmer", "role": "Composer"},
              ],
      "cast": [
                {"name": "Matthew McConaghey", "role": "Coop"},
                {"name": "Anne Hathaway", "role": "Brand"},
              ]
    }

Lightweight JSON messages are more efficient than XML (although less
efficient than CSV) and are also very easy for programmers to use, since
the data can simply be "dropped into" a JavaScript program without any
changes, and into other programming languages with very minor adjustments.
Thus, JSON is becoming the preferred data format for most applications
where software is talking to software, and CSV may still be dominant when
data is intended for humans to use in spreadsheets and the like.

## New Technologies for Data Serialization

There's a term, **serialization**, for when data in computer memory or in a 
database is exported into text files for transmission.  In addition to CSV,
XML, and JSON, there are a few new formats aimed at allowing the 
structural flexibility of XML or JSON while minimizing wasted bytes.  When
sending or receiving millions of records (or billions!), the small amount
of "overhead"---the punctuation, whitespace, and repetitions of attribute 
names---can add up to major costs in time and computing resources.

Although these alternative serialization techniques are beyond the scope of 
this course, you should be familiar with a couple of names: **Avro** and 
**Thrift**.  Either of these may be something you find yourself working
with in the future.


## References & Recommended Reading

- A *hilarious* student-produced video on data serialization with 
  XML. https://youtu.be/kfVPLSj6Rqw
  
- "Character Encoding", Wikipedia. https://en.wikipedia.org/wiki/Character_encoding

- Manoochehri, M. (2014). Data Just Right: Introduction to Large-Scale Data &
  Analytics.  Addison-Wesley.