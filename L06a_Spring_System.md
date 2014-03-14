L06a Spring System
===================

How to innovate OS in the commercial space?
-------------------------------------------

In a commercial space, the choice becomes whether to build a new OS or to build a better implementation of an existing OS.
Due to the installed base of applications, moving to a new OS is not a teneable option. Sun, therefore too the latter approach with Spring.

The space that Spring was intended for was large scale commercial applications - the staple for Sun's customer base. The OS therefore had to run existing applications unchanged and yet allow for them to be scaled to larger capacities via clusters.

It also had to allow for 3rd party vendors being able to use the API exposed optimally without breaking anything.

Ie, keep all the good things, but add innovation and flexiblity.

OO is a good choice for this by keeping this "under the covers".

Procedural vs OO Design
-----------------------

Monolithic kernels are built with state strewn all over the subsystems that make up the kernel. With OO design, state is contained within objects and methods inside the object manipulate the state. Only the methods are visible externally.

Thus you get strong interfaces and isloation of state.

![Procedural vs OO design](vlcsnap-00001.png)

However, what about the border crossing cost? Ans TBD to later.

Spring built kernel components using OO Technology.

Spring Approach
---------------


Unorganized notes
------------------

[2:57] Sun wanted to build a network OS during the time when the industry was moving to services provided on the network. So they wanted to build an OS that worked on one machine and more using the same Unix API.

Spring came out as Sun Solaris' MC product. See [Khalidi's paper](https://www.usenix.org/legacy/publications/library/proceedings/sd96/full_papers/khalidi.ps)

Issue that Spring saw: Innovate using brand new OS or better implementation of a known OS.

hardware vendors dont change things that often, so software follows suit. Seems like this is a big thing.


![Spring system](vlcsnap-00002.png)

![Nucleus microkernel](vlcsnap-00003.png)

Resources used
---------------

1. Vidoes from Udacity, downloaded and watched in 2x speed on VLC
1. spring.pdf - in required reading.
1. [Khalidi's paper on Spring MC](https://www.usenix.org/legacy/publications/library/proceedings/sd96/full_papers/khalidi.ps)
1. Hamilton et al's Paper on Subcontracts - in reqd reading.
1. [Wikipedia page on Spring](http://en.wikipedia.org/wiki/Spring_(operating_system))

Todos
-----
- watch lectures again after papers are read.
- Finish up notes on lectures
- finish reading up halidi's page
- answer questions:
	* How is border crossing cost handled in Spring?
- Practice drawing the diagrams

Journal
-------

**Thu Mar 13 02:20:59 2014** : 

- Started watching the videos today at about 10 PM CST. 
- Switched to the "Overview of Spring" pdf, but couldnt understand the why behind such a system.
- So searched on the net for Spring MC and came across the Khalidi paper (see below) which explained the commercial reasons better as well as the subsystems that were actually built. This paper also explained how doors actually work.
- Also found the [wikipedia page on Spring](http://en.wikipedia.org/wiki/Spring_(operating_system)) useful in explaining how it is similar to Mach as well as the business rationale.
- Then started reading the Hamilton paper to understand how Subcontracts work.

**Mar-13-2014 08:38 :** Watching vidoes again.

**Mar-13-2014 08:52 :** Some thoughts on note taking:
- in a blank md file, start with h1 lecture title, h2 resources, h2 todos, h2 plan and h2 journal
- log start of lecture in journal
- update resources list
	- add videos to resource list
	- find all papers that may apply and add to resource list
	- find all piazza comments related to this topic and add to resource list (assuming you're late for this lecture in pacing)

- update plan
	- put estimate for video watching (based on duration reported in udacity wiki)
	- put estimate for paper reading
	- put estimate for quiz answering online
	- put estimate for question and answers - online and on piazza.
	- put estimate for note consolidation
	- ... and therefore total estimate of hours required.
- watch the videos	
	- Create h2 section for each main topic in video
	- watch video once without break
	- watch video again with breaks
	- write notes with timestamps for important notes
	- write answers to quizzes in line
	- organize notes into coherent set of bullets
	- review notes for all topics in lecture to date.
- read the papers
	- create h2 section for each paper
	- read paper once without break
	- write down why this paper is pertinent.
	- 
-