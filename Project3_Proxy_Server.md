Project3: Proxy Server
======================

Todos
-----

- DONE: Understand the overall task
	- make RPC using rpcgen
		- understand rpcgen
		- use it
	- get files from inet via cURL
		- understand cURL
		- use it
		- setup project in vbox
		- write code
		- upload to bvt.
	- read up on caching algorithms
	- Implement LFU
	- Implement LRU
	- Implement Random
	- Implement LRU-min

- DONE: List out deliverables
	- 6 C programs
	- 4 request pattern files

- DONE: Estimate time for deliverables
- Research how to do the work
- WIP: Implement code and submit
	- DONE: make rpc using rpcgen
	- DONE: get files from inet via curl

- Create request patterns and submit


Plan
----

| Task													| Due Date | Est total hrs | Est hrs/day |
|-------------------------------------------------------|----------|---------------|-------------| 
| Read up on Caching algorithms                         |  3/14    |    5h         |             |
| Make RPC using rpcgen                                 |  3/15    |    3h         |   5 hrs     |
| Get files from inet using cURL                        |  3/16    |    3h         |             |
| Implement LFU                                         |  3/22    |   10h         |             |
| Implement LRU                                         |  3/22    |   10h         |             |
| Implement Random                                      |  3/23    |   10h         |             |
| Implement LRU-min                                     |  3/23    |   10h         |             |
| Create data for Random beats LRU                      |  3/24    |    1h         |             |
| Create data for LFU beats LRU                         |  3/24    |    1h         |             |
| Create data for LRU beats LFU                         |  3/24    |    1h         |             |
| Create data for LRU-min beats LRU                     |  3/25    |    1h         |             |
| -----Total-----------                                 |          |   55h         |             |

55h by 3/25, ie 11 days from 3/14, ie 5 hrs per day.

Better: Implement programs in weekends of 15-15 and 22-23.
Try to finish off data before implementation.
Also see if LRU beats LFU can be done after the 2 programs are implemented.

Resources
---------

- [rpcgen programming guide](http://docs.oracle.com/cd/E19683-01/816-1435/6m7rrfn7f/index.html)

Questions
---------

1. XDR seems exactly like protobufs. Find out if there's any comparison.
2. RCPL is exactly like IDL and its ilk. Hmm.. Why was it not continued beyond C? Maybe because Sun wanted control via the program registration bit.


Journal
-------
**Sat Mar 15 16:42:49 2014** : Starting rpcgen task
**Sat Mar 15 17:49:54 2014** : Finished reading tutorial and spec for XDR and RPCL.
**Sat Mar 15 19:28:08 2014** : Got past the unknown protocol by changing visible to tcp. link: http://www.linuxmisc.com/16-linux-development/0c1aca4e67e963cf.htm. Also has usage of rpcinfo that shows how to check if the server came up.
**Sun Mar 16 03:03:19 2014** : Finally figured out why my server was dying after the first request: its the -K 0 setting in the make file. This causes it to exit after servicing the first request. Ref: http://www.lehman.cuny.edu/cgi-bin/man-cgi?rpcgen+1. 
**Sun Mar 16 03:22:51 2014** : Submitted task#2 - get files from inet using curl.
**Sun Mar 16 04:05:36 2014** : Was trying to figure out how rpc servers would actually stay up and service multiple calls. To that end, I first looked at the generated source because -K is an rpcgen option, so it must be influencing the behavior. Didnt find anything there, so went looking for the reason online. I got a clue in rpcgen's man page that said: 
	
	 When monitoring for a server, some portmoni-
     tors,  like  listen(1M),  always spawn a new
     process in response to a service request. If
     it  is known that a server will be used with
     such  a  monitor,  the  server  should  exit
     immediately on completion. For such servers,
     rpcgen should be used with -K 0.

So that's why -K is 0 and depending on the portmonitor, a new instance would be spawned every time. This however, seemed inefficient for use with cURL because it explicitly asks for init operations to be done once and handles to be reused. Seems like a waste to do the init and destroy on every call. So I looked around a little bit more to find out how the -K value was used. Couldnt find the source code for rpcgen, so couldnt see how it did it.

So if I changed the value of K, the server would stay up and service calls past the first one. But how can it be stopped? I thought maybe rpcbind's -d option would help, but it didnt. All it did was remove the server's registration; it didnt kill the server itself - if it was running.

So I finally changed the value of -K just to see how it would work. I set it to 500 and did a clean build. Now _srv.c got regenerated and all the lines that had exit(0) were replaced with return. So that's how the server stays up - by not exiting :). I tested the server with multiple calls from the client and immediately had a segfault - I was initializing cURL once, but trying to cleanup multiple times. Fixed that and the server worked fine with multiple calls; inefficiently, but it worked.

I'm guessing that building the server code by hand allows us to abstract out common functions better, but that requires understanding the various levels of RPC interfaces. Hopefully task#3's cache_perform.c has the smarts to keep the server up :).

**Sun Mar 16 11:59:40 2014** : Starting on LFU replacement.
**Sun Mar 16 12:44:28 2014** : Starting to get the hang of the overall algorithm without eviction:
	
	init:
		create an array to hold the cache entries
		create a hashtable to map urls to array indexes
		set maxentries to some value

	get:
		check hashtable if url is already present.
		if so, 
			return it
		else,
			return failure

	set:
		check if hashtable already has url.
		if yes,
			replace contents with new contents
		if no,
			check if there's space to add a new entry (ie, entries < maxentries)
			if not,
				return failure
			else,
				store it in the next available slot
				entries++

this has to be modified to include evictions thus:

	init:
		create an array to hold the cache entries
		create a hashtable to map urls to array indexes
		create a stack of available slots
		create a priority queue with <id,criteria> as contents.
		set maxentries to some value

	get:
		check hashtable if url is already present.
		if so,
			update stats required for eviction
			return it
		else,
			return failure

	set:	
		check if hashtable already has url.
		if yes,
			replace contents with new contents
		if no,
			check if there's space to add a new entry (ie, free slots in stack > 0)
			if not,
				evict an entry
				use the newly freed slot for the new entry
			else,
				store it in the next available slot
				entries++

	evict an entry:
		index = pick a candidate based on policy
		free up index by putting it into stack of free slots

	update stats:
		if policy = LFU    insert into pqueue with criteria = #requests, sorted by least on top
		if policy = LRU    insert into pqueue with criteria = lasttimestamp, sorted by highest on top
		if policy = Rand   insert into pqueue with criteria = none, no sort
		if policy = LRUMin insert into pqueue with criteria = <band(#requests)#requests>, <sorted by band, sorted by least on top>. for lrumin, each entry in pqueue is itself a pqueue for that band or level.

**Sun Mar 30 11:42:31 2014** : Got Random policy to work on BVT. Yay!

**Sun Mar 30 11:43:02 2014** : Rethinking the data submission assignments.
| Create data for Random beats LRU                      |  3/24    |    1h         |             |

Since there are 20 levels, for one policy to beat another, it has to "win" in at least 11 levels, ie, at the 5.2 MB level.

LRU = least recently used. So strato:
fill up the cache with largest items - 2nd largest first. This will ensure that the eviction policy will kick in.
Then fill with largest. This will ensure that:
	- the 2nd largest is evicted
	- the 3rd largest becomes LRU
	- largest is the most recently used.
Then request each of the original list in order. This will ensure that:
	- every new request will evict the N-1th largest item and make the Nth largest the MRU.
	- once the whole cycle is done, the process will repeat.

So we need K items starting from 2nd largest >= 5.2 MB


| Create data for LFU beats LRU                         |  3/24    |    1h         |             |

LFU = Least frequently used
LRU = Least recently used

For LFU to succeed, most frequently used items must continue to be requested
For LRU to succeed, most recently used items must continue to be requested.
	ie for it to fail, least recently used items must continue to be requested. So same requests as rnd vs lru can be used, but to seal the deal, LFU must also succeed.
That is, least recently used items must continue to be requested, but requested with more frequency.

So:
	- fill the cache up with the largest items starting from 2nd largest and totaling > 5.2mb.
	- Request the largest so LRU fails.
	- Repeat request for largest again but 2 times so it becomes MFU, then request second largest 3 times so it becomes MFU and so forth.

| Create data for LRU beats LFU                         |  3/24    |    1h         |             |

LFU = Least frequently used
LRU = Least recently used

For LFU to succeed, most frequently used items must continue to be requested
	ie for it to fail, least frequently used items must continue to be requested.
For LRU to succeed, most recently used items must continue to be requested.

at the same time the biggest item must be the most recent and the least frequent.

So:
	- fill the cache up with the k largest items totaling > 5.2mb starting from kth largest to the 2nd largest.
	- as we fill, request each item multiple times: kth largest k time, k-1th largest k-1 times and so forth so that 2nd largest is the LFU and 3rd largest is 2nd LFU and so forth. Eventually the kth item will be the MFU and the 2nd largest item will be the ideal candidate for eviction, and it has to be evicted because the request will be for an item larger than the largest item in cache.
	- now add largest item. This should evict the 2nd largest cause a miss. LFU fails.
	- OTOH, LRU will evict the least recently used, which will be the items starting from k that add up to sizeof(largest item)
	- now toggle between the largest and 2nd largest for some time. Thus the largest item in cache will be at the same time the most recent and least frequent.


| Create data for LRU-min beats LRU                     |  3/25    |    1h         |             |

LRU-min: of all the least used items, find the one item that's close enough to the new item's size and evict it
		 instead of evicting the least recent. That way many cache entries are not evicted.
LRU: continue evicting the least recent used items until there's place for the new item

So for LRU-min to suceed, new requests must be such that a single entry matches usually and its closer to the LRU item

And for LRU to succeed, new requests must be closer to the MRU.
So for LRU min to succeed, make biggish requests that are a few spots removed from MRU.

So:
	- fill up the cache with the k largest items totaling > 5.2mb such that the largest is the MRU and smallest is LRU.
	- add a new item not in the cache such that its size is just below MRU-3
	- LRU-min will replace MRU-3, while LRU will replace LRU+n more items to accomodate the new item.
	- repeat with a new item similarly sized with the new MRU-3


**Tue Apr  1 22:25:25 2014** : Finally got all algos working. lrumin was a bitch. now working on cleaning up submissions.
total time spent: sat, sun, mon: 2pm - 6 am, 4pm - 4 am, 6 pm - 10 pm : 14h + 12 h + 4 h = 30 hrs

since code is scattered in multiple places, heres a guide to it:
main sources: vbox
copied for submission to: project3/proxy-server/submit
multiple versions of working and non-working (in bvt) should abound in this directory.
the final working versions are called algo_bvtworkingversion.c. This is a direct copy paste from bvt after getting passed message.
finally, the cleaned up versions of these are in submit/final
These are cleaned up for:
	- removal of debug(), fprintf()
	- added explanatory comments.

**Wed Apr  2 06:50:02 2014** : Wanted to pen down all the issues with the bvt on this one:
- debug prints are not displayed by default, even to stderr
  - The way to force it was to fake a system error like a double free(), at which point other debug prints were shown.
	
		bvtDebug = 0;	// at start
		bvtDebug++;		// at the point where one iteration of the test program potentially ends, eg in xx_destroy()
		if(bvtDebug==SOMEVALUE) {free(alreadyFreedPtr);}

  - however, there was a limit to the output that the bvt would display. if the output went over the limit, it would not show anything at all. Even truncated output would be great.
  - also, the SOMEVALUE from above was a trial and error. Too less, and the test cases would not be executed. too more and the output would become blank as above. So finding the right value was by multiple trials in 
- submits on the page did not necessarily save your final input into the text field. so the defensive practice became:
	- make a change in the textfield
	- cmd-a, cmd-c it to save it for yourself
	- reload the page
	- cmd-v in the change again
	- hit submit

	