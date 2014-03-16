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
