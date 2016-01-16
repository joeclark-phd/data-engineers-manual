# Atoms, Bytes, and Databases

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
needs some means of saving its state or its output to persistent storage.
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
and "zeroes" which encode meaning. Eight of these digits or bits taken
together make up a byte. We refer to 2^10^ bytes as a kilobyte, 2^20^ bytes as
a megabyte, and so on.[^1] Table 1.1 provides the common terms for various
measures of data.[^2]

[^1]: It's not exactly the metric system. 2^10^ is 1024, close but not quite 
    1000.

[^2]: There's a petition going around to officially name 2^90^---about a 
    nonillion bytes---a "hellabyte".  I'm all for it.  We're going to have to \
    start calling it *something* soon.

![Fig. 1.1: Measures of Data](/images/kilomegagigabytes.png)

The bits and bytes on your disk would simply be electronic gibberish if
your operating system didn't know how to read them. Fortunately, these
bytes are structured according to a file system so your operating system
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

## References & Recommended Reading

- "What is the Internet, really?", TED talk by Andrew Blum, author of "Tubes".
  https://youtu.be/XE_FPEFpHt4. (Or see this longer version of the talk: 
  https://youtu.be/28hzKbcLlWg.)
- "The Secret Life of Big Data", keynote talk at Supercomputing 2013 by 
  Genevieve Bell of Intel.  https://youtu.be/CNoi-XqwJnA
