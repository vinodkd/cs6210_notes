L09 Internet Computing 1
========================
Review material
---------------
### Video content
### Paper Content
### Previous Exam questions and answers

#### CS 6210 Spring 2013 Final Solution Name:__________Kishore___GT Number: _________
6. Internet Scale Computing (20 mins, 20 points)
(a) (4 points) (Map/Reduce)
Mention at least 4 important “heavy lifting” work done by the map-reduce infrastructure unbeknownst to the user of the programming model (use a figure if it makes easier to get your points across).
	 Automatic splitting of input into key-value pairs
	 Spawning worker threads to execute parallel maps
	 Spawning worker threads to execute parallel reduce
	 Managing the scheduling of the worker threads
	 Plumbing intermediate results from the map to reduce worker threads
	 Managing machine failures and re-execution of failed map/reduce functions
(+1 for each of any of the above points upto 4 max; other points also considered if valid)

(b) (6 points) (Giant-scale services)
Explain the pros and cons of the three approaches to online evolution of giant- scale services:
(i) Fast reboot
Pro: whole datacenter up in a short amount of time; especially works well in exploiting diurnal server property for geographical datacenters across the globe (+1)
Con: No service (DQ = 0) for the duration of the reboot time (+1)
(ii) Rolling upgrade
Pro: graceful degradation of service (DQ never goes to zero) (+1)
Con: takes a long time for the datacenter to be fully functional again (+1)
(iii) Big flip
Pro: better than fast reboot in terms of availability (DQ is exactly 50% for the duration of total reboot time) (+1)
Con: worse than fast reboot in the time it takes for the datacenter to be fully operational (+1)
(c) (10 points) (Coral)
(i) (2 points) Explain “full” and “loaded” attributes of nodes participating in Coral
Full: the node already has a predefined number of “values” associated with a given key (basically helps in statically dispersing the meta data management for popular content)
(+1)
Loaded: the node is already handling a predefines number of queries per unit time for a given key (basically helps in dynamically dispersing the meta data management for popular content)
(+1)

#### CS 6210 Fall 2011 Final Solution with Grading Rubric Name:__________Kishore_________GT Number: _________
6. Internet Scale Computing (25 min, 20 points)
(a) (5 min, 5 points) (Giant-scale services) What do “D” and “Q” stand for in the DQ principle? Explain how the DQ principle helps in handling graceful degradation of giant scale services.
￼
o D is the data in the giant-scale service
o Q is the“Yield”definedasQ=Qc/Qo,whereQcisthequeriescompleted,andQoisthequeries offered to the giant-scale service in a given time period
o Gracefuldegradation:
o DQisaconstantforasystemcapacity
o Ifsystemcapacitygoesdown(duetofailure,routinemaintenance,etc.)
 Keep harvest (D) unchanged and reduce the Yield (Q)
 Reduce harvest (D) and keep the Yield unchanged (Q
 Allows system administrator to make an explicit choice as to which to preserve
and which to sacrifice
￼￼￼
“Harvest” defined as D = Dv/Df, where Dv is the available data, and Df is the complete
           +1 for each highlighted phrase or equivalent
           +1 for the two bullets identified above
(b) (5 min, 5 points) (Coral CDN) Explain the metadata server overload that can happen in a traditional DHT with an example.
o Assume 10 nodes each generate content. Assume that the hashed keys for all the 10 generated content are all clustered around the IP-address hash of one particular node say N1. In this case the key-value pairs, <key1, node-id1>, <key2, node-id2>, ..., <key10, node-id10> will all be stored in the same node N1. If all these 10 generated content are the most popular content in the CDN, then, even though the content themselves are hosted on distinct nodes, the discovery of the content location requires accessing the metadata stored in N1. This leads to an overload of the metadata server N1.
++++
+5 if the answer says that a lot of hashed content keys are clustered
   around the hash of the IP address

(c) (15 min, 10 points) (Map-Reduce) Show pictorially a Map-Reduce structure (you don’t have to write the code for map and reduce functions) for ranking the pages on the web. Be explicit in identifying the <key, value> of the map and reduce functions both for input and output.
￼
o <Key,value>inputtomap function:
o <sourceURL,contentof the source page>
o <Key,value>outputofMapand input to Reduce
o <targetURL,URLof source page where found>
o <Key,value>outputofReduce o <targetURL,rankofthe
target URL>

10. (10 min, 5 points) (Map/Reduce)Write a Map/Reduce program that counts the URL access frequency. The input to the map/reduce system are web logs of page requests. The final output should be <URL, total-count>

11. (5 min, 5 points) (Giant Scale Service)
DQ Principle: If D is data per query and Q is the number of queries per second, then the product D*Q tends towards a constant for a fully utilized system. How can the DQ principle be used to come up with policies for graceful degradation of the system under excess load?

Resources
---------

1. Videos : 2 h 34 m
2. Papers: 7 (adding the 2 papers from "Web technologies" to this lesson)
	update: 4/2 removed 3 addnl and 2 from original list = 5 reduced.
	thus papers = 2
3. Quizzes: TBD
4. Exam Review Questions: TBD

Plan
----
- Videos:
	Suggested pacing: 2w
	Watching 2h 34 m = 154 mins = 11 mins/day for 14 days
		ie 	watch @ 2x speed
			go back and write notes for same time.
- Papers:
	reading: 1 hr
	writing comprehension of paper: 1 hr
	= 2 x 2 hrs = 4 hrs. 
- Quizzes: TBD x 5 mins  = ? min
- Research online and piazza: ? hrs
- Review Exam questions : ?

Total: 2 x 2 hrs + 2.5hrs (ie 154 mins) = 6h 34 min

Todos
-----

Journal
-------
**Fri Apr  4 01:30:53 2014** : Started watching videos this evening.
**Fri Apr  4 01:42:37 2014** : Looking at previous exams now.
**Fri Apr  4 01:55:19 2014** : Did a quick persual of the Map reduce paper. Need to go look at the details but it is as i remembered it from before. Also looked at the Coral paper. Need to read that paper fully, esp about Distributed Sloppy Hash table.
