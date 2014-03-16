CS-6210
=======

Plan
----
- Videos: 
	Watching 2h 18m
		   + 0h 46m
		   + 2h 40m
		   + 1h 53m
		   + 5h 34m
		   + 3h 20m
		   = 16h 27m

	watch @ 2x speed                        =   16h 27m
	go back and write notes for same time.  = + 16h 27m

- Papers: 19
	reading: 1 hr
	writing comprehension of paper: 1 hr    = + 20h  
- Quizzes: 36 x 5 mins  = 180 min           = +  3h
- Research online and piazza: ? hrs

Total estimate                              = ~ 40h by 4/30 ie 7 weekends
              								= ~ 6h per weekend

L0. Refresher
-------------

### Memory Systems
Naive Memory Model: there's a cpu connected to main memory and disk
Main memory is:
	- fast
	- random access
	- temporary
disk is:
	- slow
	- sequential access
	- durable

We will add 2 things to this picture:
1. Cache systems
2. Virtual memory

Motivation for cache:
A program without cache must always go to main memory when it needs data. This is of order 100ms, which is slow.
hence the cache cocept.

A cache is a faster 10x, smaller memory close to teh cpu.
a request processed via cache is called a hit and those that dont, called "miss"

99% hit rate with 4c for hit and 100c for miss => avg cycles = .99*4 + .01 * 100 = 4.96
90% hit rate with same  values                 => avg cycles = .90*4 + .10 * 100 = 13.96

Dramatic increase is surprising, but explained by fact that hit rate is want controls the average.

### Memory Hierarchy
MOst imp consideration in cache design is tradeoff bw speed and capacity. We can either have a small but fast cache or a large but slow cache. But we dont have to make a single choice. We can choose to progressively cache things from small+fast to slow+large. This is called the cache hierarchy. Starting at registers we can go L1,L2, L3 and finally main memory, which can itself be considered cache for disk or the network.

### Locality and Cache blocks

What to put in cache?
	- ideally what the program wants next. 
but we dont know that, so we assume that its something close enough and store the neighboring data as well.
This is called a cache block

Two types of locality:
	- temporal: refering to the same memory close in time.
	- spatial: referring to the same memory close physically.

caches exploit both. temporal by putting data in cache right after you use it and pu

L1. Intro to AOS
-----------------

### Principle of abstraction: no major notes here

### L01b Hardware resources

any system consists of:
	- cpu
	- bus
	- connected to bus are:
		- memory
		- disk w/ controller
		- network w/ controller
		- other peripherals with a controller each
	- some controllers can talk directly to memory using DMA

elaborate organization:
	- cpu, memory on system bus.
	- system bus is fast and hi b/w
	- all other peripherals/devices on data bus.
	- i/o bus is for devices to comm with cpu.slow and low b/w
	- bridge connects the two and allows two way comm.
		- could be an io processor that schedules devices' comm with cpu
		- mini processor in itself.
	- could also have frame buffer on system bus. needed to refresh screen rapidly.

nutshell: 1 or more cpu, bunch of memory, whole lot of io devices and their controllers, system and io buses to link everything as conduits of data.

internal ogranization same throughout the spectrum.

Specifics:

### L01c OS functioality:

An OS is:
	- code that provides protected access to hardware resourcs
	- manages requests from applications 

#### L01d Managing CPU and memory
- No hogging
- No overriding data/code from one another.

- OS should cater to cpu, memory and peripheral device demand from apps
- know how to launch apps, ie read from disk and create running image.
- handle dynamic resource request

Job of modern OS: give access to resources asked for and get out quickly as possible.

Processor related terms:
- Program: memory footprint created of code. static image loaded into memory
- Process: a program in execution = program + state
		state evolves as time goes
- Thread: A line of control coursing through the program with no data of its own.

process: program + state of all threads coursing thru it.

Memory related abstractions:
	- distinct addr spc for each app
	- implement using h/w capabilities


L2. OS Structures
-----------------
OS structure is the way it is organized wrt apps it serves and the hw it manages.

some of the basic services provided by an OS are scheduling, memory management and communication.
traditional OSs did these in a monolithic way: OS code ran in priviledged mode while apps ran in user mode and had to make a system call whenever it needed the OSs support.

So OS protected the app from vagaries of hardware but also prevented better performance because of it being monolithic.

Desirable properties in a Modern OS, thus:
- protection
- performance
- flexibility.

At the other end of the spectrum is a DOS-like OS that has a porous line between user and kernel space. Great for performance and flexilibity, not so much for protection. So what are our alternatives?

We want something in between that has a good mix of all three. The 3 attempts we'll review are:
- SPIN
- exoKernel
- L3

### Spin OS: main idea: extensions can be made efficient if they're compiled into the kernel, but using a safe and secure HLL. Spin OS uses Modula-3 bcos:

	- Classes/Modules provide logical protection domains
	- Typed pointers prevent misuse
	- Automatic storage management ensures stale pointer are not used.

Capablities are refs created using these features - unforgeable refs to objects. all actions are through capabilities.

It exposes 3 mechanisms to create extensions:
	- Create
	- resolve
	- combine

Using these we can create extensions and combine them to form aggregate extensions that can share subsystems, but still be separate Library OSs.

Once created, they're registered to Spin's event handler bcos every procedure represents an event tht can be called and the procedure is the event handler. Spin can do 1-1, 1-* and *-1 events. It also has preemption on an event to prevent hogging and also a default handler for all events. if the default handler refuses installation of a new handler it wont work and it can add guards as well.

All of this is fine, but it also has core services:
- Memory Service:
	- allocate
	- virtual addr svc: which gives a virtual addr
	- translation svc: xlates virtual <-> actual
- Strand: Thread abstraction
	- block
	- unblock
	- checkpoint
	- resume

these can be used by libraries to let spini control more.

networking is via a seriese of event handlers. ip and atm packets -> ip handler ->-> multiple places _. single place


### ExoKernel
Philosophy: expose the physical interface directly. separate permission from use. Mechanisms:
- secure binding
	- using hardware
	- using software tlb
	- by downloading code
- visible revocation
- abort process
	- reposession vector and assignment


### L3 microkernel
Philosphy: Debunk myths about exokernel by careful construction. 3 myths:
- Border crossing cost: Debunked by showing it is less.
- Addr spcSwitching cost: cost to switch from one process to another . true on smaller domains, not much on larger cos there its not switching cost both others that are higher. Adress tagged archtiecture helps. on arch that doesnt have this, use segment register.
	- share thsi spce for 
- Thread switches + IPC: shown to be competitive by construction.
- Memory effects: locailty loss cos multiple namespaces. not true, bcos the caches were warm.


L3. Virtualization
------------------
Basic idea: if a, b and c have usage of a system that varies over time, with their individual machines they'd have to pay for the peak usage. if they pooled resources, they could by a beefy system that handles the combined peak and share it to reduce total cost. that is the promise of utility computing and premise for virtualization.

essentially we're talking about virtualizing the entier OS instead of individual services inside it.

but who is in charge, then? for that we need an OS of OSs, or Virtual Machine manager or hypervisor.

VM=virtual machines or Guest OSs

virtualization started with vm 370. give illusion that the computer was theirs. 
micro kernels 80-90s
xen and vmware early 2000

Full virt: guest os is unchanged; doesnt know its running on a VMM. HV emulates every sys call as a trap. issue: some priv instr may fail silently. sometimes: vmm might look for binaries for this and ensure that issues can be fixed.

VMware uses full virt.

Para virt: modify source of guest os. thus avoid problems and add optimizations. nothing changes about the apps on each os - only the os changes. modified code is less < 2%.

proof of this by construction by xen. implemented linux and xp. 1.36%, .04% noice.

What needs to be done?
Virtualize hw
	- memory
	- cpu
	- devices
	- effect data and control b/w guest and host

### Memory Virtn
Each guest os has its own Page Table, which is mapped to machne memory by hv.

so diff vms on one box could have different regions of machine mem allocated to them.

normal mapping is VPN -PT-> PPN

in virt, there's a shadow pt , so process is VPN -PT-> PPN -SPT-> MPN

full virt: mapping kept in host
para: either can, but guest does.

how to make this efficient?
when guest tries vpn -> ppn, this traps to hv, which installs vpn->mpn mapping.

in para v, can be pushed into guest as it is aware. eg vmware hypercall: createPT, update(), swtichPT()

how to dnyamically increase memory of a vm?
basic idea: if existing allocated memory can be freed up, it can be given to other guest.

Balloon technique. Ballon device driver installed in every vm. priv channel b/w it and driver.
bdd keeps asking for more memory and holds on to it.

when house needs more memory for G2, it asks G1 to request more memory. when the demand increases, P1 is paged out to disk and main memory is freed up.

later when house has more memory and can bring G1 back, it asks G2 to deflate. thus more main memory is again free, so P1 is paged back.

this assumes cooperation with guest os.
applicable to both full and para

how to share memory across vms? possible if we share the code pages.(draw pic of ff in linux, 2 copies, linked to memory array). mark pages as copy-on-write so chagnes can be done.

vm oblivious paging: has content hash of memory pages. as long as the pages are same, only one copy is kept.
hash table has: hash, vm id, refs, mpn.
even with match, we cannot directly use it. cos there could be copies.
match and scan is resource intensive, so do it slow time. applys to both full and para.

Memory Allocation policies:
- share based
- working set based
- dynamic idle-adjusted shares. tax idle pages more than active. esx 3 does this.
- reclaim most idle moemory

### CPU and device virt.
this is more explicit.
- illusion that it owns the cpu
- handle program discontinuities

cpu virt:
	- first, need to give illusion that they own it. easily done in full vit. just need to make sure they keep to timeslices. can be done with standard scheduling as hv doesnt care about guest processes/threads.
	- make sure accounting of time is right, eg, processing of interrupts should nt count to total time in cpu.

program discont:
	- syscalls from running program
	- interrupts
	- exceptions
	- page faults

	all fo these have to be packaged as events sent up to the guest os.

	some priv instrs may fail silently. might require b rewites.

for para virt, api can be written to handle such issues.

### Device virt

Full virt: trap and emulate. not much scope for innovation.
para virt: much more interesting. hv can expose simple apis and buffers for data axfer. determine:
- how to xfer data?

Control xfer:
Full: implicit (traps) and events(interrupts)
para: explicit(hypercalls) and sw interrupts (events)

Data xfer:
Full: implicit
para: explicit. time issue in handling buffering of data. space issue in keeping buffered data around. xen has async io rings. shared data struc bw xen and guest. 
any number can be created for any use
items are req descriptors, or rsp with same descriptor
rq prod and rq consumer have ptrs.
rs ptr and consumer have ptrs 
diff bw ptrs is # rqs outstanding or rs available.

io rings for nw virt:
two rings: xmit and recv.
guest puts msg ptr in desc and puts into ring.
rr pkt sched picks up and sends to 


L4. Parallel Systems
-------------------

- basic algos for snyc, communication and scheduling for shared memory || systems. 
- leading up to: structure of OS for || machines

Outline:

- machine model
- synchronization
- communication
- scheduling
- || OS case studies.

### L04a. Shared Memory Machines

aka Shared Memory multiprocessor. 3 structures:

- Dance hall arch: cpus on one side, memory on the other side of an interconnection network.
- SMP: Symmetric multiprocessor achitecture: simple system bus that connects all cpus to main memory. symmetric because access time from any cpu to memory is the same.
- DSM: Distributed shared memory architecture: each has its own memory, but can access the other memories the same. access to other memories is slower than the one associated with a particular cpu. 

Similar to all: entire memory is accessible to any cpu, ie its fully shared; there's acache for each cpu.

Shared memory and caches:

Cache serves the same purpose in an MP system as it does in a uniprocessor setup, ie when a cpu needs some memory location's value, it looks in cache and if its not there goes to main memory and then caches it for future.

From diagram:
	cache speed: ~2 cycles
	memory speed: 100+ cycles

Unique problem with MP: Cache is cpu-specific, main memory is shared. Eg:

Lets say there's a memory location y in all caches. its a hot emmory lacation.
Let's say processor P1 decides to write to y, making it y'.

What should happen to y in all the other caches.

This is called the CACHE CHOERENCE PROBLEM.




### L04c. Communication

Barriers are the other type of sync primitive. It is used in multiprocessor architectures to allow multi-stage computations that need threads to wait at each stage for the others before proceeding.

**Central or counting Barrier**: Simplest barrier. A central count counts down until the last thread arrives at the barrier, at which point all the threads are woken up.

barrier:
	//arrive
	decrement(count)	// atomic
	if(count==0)
		count=N

	// wakeup
	while(count!=0)
		//spin
	while(count!=N)
		//spin

The second spin is to ensure that threads that are spinning on n becoming 0 do not race forward and go past the next barrier before it is reset back to N.

Adv:
	- simple
Disadv:
	- high contention; all threads are notified, so broadcast msg on bus/interconnect nw
	- 2 spin episodes

**Sense reversal barrier**: The idea here is to avoid the 2 spin episodes. Instead of spining on n, each thread spins on the last thread notifying them when its completely done.

init:
	sense=false
barrier:
	//arrive
	currentSense=sense
	decrement(count)	//atomic
	if(count==0)
		count=N
		sense=!sense
	while(sense==currentSense)
		//spin

Adv:
	- reduces spin episodes
Disadv:
	- still high contention, broadcast

**Tree barrier**: The idea here is to reduce the contention by reducing the number of peers that each thread waits on or notifies. The threads are broken into groups of size k, k<N, and there's one set of sync vars for each such group. The sense variable sets are then organized into a tree where each group points to the next level until there's a single root set. 

init:
	struct node:
		count : k
		sense : false
		parent: // ref to parent

private sense = true
private myself = new node
barrier(node):
	decrement(count)	//atomic
	if(count==0)
		if(parent exists)
			barrier(parent)
		end if
		node.count=k
		node.sense=!node.sense
	end if
	while (sense = ! node.sense)
		// spin

Adv:
	- contention reduced to log(N)
Disadv
	- wait location not statically fixed
	- as k increases, contention does too.
	- bcos of non static wait location, bad performance on NUMA machines. on NCC m/cs, spin may be on a remote memory.
	- n needs to be a power of 2

**4-ary or MCS Barrier**: The idea here is to fix both the static location problem and the problem with large value for k. There are 2 trees: arrival and wakeup. The arrival tree is a 4-ary tree and the wakeup is a binary tree, both values determined by experiments to be optimal.

init:
	struct node:
		hasChildren: bitarray[4]	// false initially
		childDone: bitarray[4]		// arranged on child cachelines
		imDone : ^childDone 		// initialized to the specific bit array location in parent for this child.
		wakeupList: bitarray[2]		// initialized to the specific bit array location in the children
		wakeup: false				// this is the spin for wakeup.

	setupTree(processors)		//sets up the tree and assigns hasChildren and childNotDone values

barrier(node):
	// arrival
	if(node.hasChildren)
		while(node.hasChildren xor node.childDone = 0)
			// spin
		loop i=0..wakeupList.size 
			wakeupList[i] = true
	end if
	node.imDone=true	// notify parent

	// wakeup
	// now wait on wakeup
	while(node.wakeup==false)
		// spin

Adv:
	- static locations on both arrival and wakeup
	- k optimized to 4, wakeup optimized to 2
	- ok for NCC machines

Disadv:
	- Higher use of space
	- n needs to be a power of 2

**Tournament Barrier**: Idea here is to arrange the nodes in a tournament. So its not necessarily an improvement on MCS, but its is an improvement on regular tree barrier. N processors/threads are arranged in a tournament with fixed winners and losers. This means at each level log N to base k losers will notify winners when they are done, and on the wake up cycle, the winners will notify the same losers that the barrier is done.

init:
	struct node:
		role: (winner, loser)
		sense: boolean

		winnersToNotify: ^node[]		//size log(N,k) mapped to a specific array element in losers array of parent
		losersToWakeup : ^node[]		//same size and similarly mapped.
	end struct

	setupTree(processors)	// sets up the tree and assigns roles and the two notification lists.

	N=...
	k=...
	ROUNDS=log(N,k)

barrier(node):
	for round = 0..ROUNDS
		switch node.role
			case winner:
				while(node.losersToWakeup[round].sense = ! node.losersToWakeup[round].sense)
					//spin
			case loser:
				winnersToNotify[round].sense = ! winnersToNotify[round].sense
		end switch
	end for

	// wake up
	for round = round .. 0, -1
		switch role
			case winner:
				node.losersToWakeup[round].sense = ! node.losersToWakeup[round].sense
		end switch
	end for

Adv:
	- Statically assigned locations for each thread to spin on
	- contention reduced as number of sync vars is O(log N)
	- no fetch and phi operations required
	- can be used on a cluster as well because all notifications are atomic reads and writes. So can use message passing.
	- All communications can be parallel.

Disadv:
	- n might need to be a power of 2.

Cluster: set of nodes in the multiprocessor do not have shared memory, they communicate by message passing.

**Dissemination Barrier**: The idea here is to have news of the barrier being reached via communication, which means no shared memory is required. However, communiction does need to happen from all nodes to all other nodes, so this is suited for message passing architectures, but it can take advantage of any parallel data paths.

If there are N threads (N not necessarily a power of 2), communication of the barrier being reached is made in rounds. In each round k, a node communicates to it's kth neighbor and passes the message that the barrier has been reached. When ciel(log(k,N)) rounds are done, everyone has communicated with everyone else and the barrier has been reached. 
So there is no distinction between arrival and wakeup. 

Adv:
	- doesnt need sync vars
	- doesnt need fetch and phi operations
	- can be used on message passing architectures ie clusters
	- can be parallelized messages
	- N need not be a power of 2

Disadv:
	- O(N) communication events per round, it total is O(N log N), compared to O(log N) for others.

Link that helped understand how to code MPI:
http://stackoverflow.com/questions/21626423/how-is-barrier-implemented-in-message-passing-systems

###L04d Scheduling

Consider cache for scheduling. Policies:

FCFS: fair
Thread centric:
	Fixed Processor Ti on Pfixed
	Last Processor Ti on Plast
Processor Centric:
	Min Intervening : Ti on P where number of intervening threads is the least.
	Min Intervening + q: Ti on P where num. intervening is least + queue is least.

Implementation issues:
Could have a global qu or processor specific affinity based queues

Ti's priority = Born Priority + Age + Affinity

Figures of merit
Thruput
Response time
Variance

Cache affinity and multi core
in these m/cs hardware itself will schedule threads. OS can help by scheduleing threads in cores and making sure that the working set of the threads are in L1$ and look at the universe of threads and make sure their working set is in L2.

Use analysis to find cache frugal and cache hungry threads and make sure sume of them is les than L2 cache size.

be careful about monitoring overhead.


L5. Distributed Systems
-----------------------

### Dist systems defn:
- A system where the nodes are connected via a network of fiber satellite or ethernet.
- No physical memory shared between nodes
- A system where the messaging time is much more than the event communiction time.

messaging time: time taken for a message from node a to reach node b as measured from a
event time: time taken to process an event on node a or b

Lamport defn: a system is dist if the message transmission time tm is not negligible compared to the time between events in a single process.

=> clusters are distributed systems.

Beliefs on event seq:
ref series of events related to booking airline ticket.

beliefs:
- processes are sequential.
- all message sends happen before they're received

Happened before relationship:
a->b => a and b are in the same process or a sent a message to b.

Transitivity of -> : a->b and b->c => a->c

Concurrent events: a || b if there is no ordering between them.
Happened before gives a partial ordering of events

Qn: id all -> and || events:
a->h->i->l
b->c->f->g->m
d->e->j->k

a->b,c->d,e->f,g->h,i->j,k->l

m || h,i,l,j,k

### Lamport's logical clock

Each node in a dist system knows:
- its own events
- its communication events.

Given this, Lamports logical clock is thus:
- each node counts its events via a clock that is monotonically increasing
	so if there are two events a,b in Ci such that a->b,
		Ci(a) < Ci(b)
- message receipt timestamp should be greater than message send timestamp
	so if there are two events a,d, a from Pi and d from Pj such that a->d
		Ci(a) < Cj(d)
	 => Cj(d) = max(Ci(a)++,Cj)
- Does not handle concurrent events
- C(x)<C(y) =/=> x->y

This clock gives only partial order

Need for total order. when required, we need a tie breaker policy when timestamps match

### Lamport's total order clock:
a => b iff	Ci(a) < Cj(b) or
			Ci(a) = Cj(b) and Pi << Pj where << is an arbitrary well known tie breaker function.

Tehre is no single total order

Once we get a partial order to total order the tstamps dont matter.

Qn:
happened before: a->b->c->e, a->d, f->e
concurrent events: d || b, c, e, f, f || a, b, c , d
timestamps: a:1,b:2,c:3,d:5,f:4,e:5
Total order:  
	d vs b: 5 vs 2 ie b->d
	d vs c: 5 vs 3 ie c->d
	d vs e: 5 vs 5, ie, p1< p3, ie d->e
	d vs f: 5 vs 4, ie f->d

	f vs a: 4 vs 1, ie a->f
	f vs b: 4 vs 2, ie b->f
	f vs c: 4 vs 3, ie c->f

Total order therefore: a=>b=>c=>f=>d=>e 

### Lamports Dist ME Lock Algo
In a dist system with n processors, the algo to gain a lock uses:
each processor has a queue of events that is sorted by timestamp, smallest at top.

when a node wants to get the lock, it:
- inserts the current ts into its own q
- sends out a message to every other node
- waits for an ack.

each node on receipt of msg, sends an ack back with their current ts

lock gained when:
- my req is at top of q
- all nodes ack back
		or
- there are no nodes in my q with earlier ts, or all ts in my q are after me.

unlock:
	send unlock msg
	all nodes remove my event from their qs

correctness ensured by:
- msgs in order
- no msg loss
- events totally ordered using lamport's total order clock.

#events: 3(n-1)

improve by defering the ack if we know that our req is before the next lock req.
thus unlock becomes the ack and #events becomes 2(N-1)

### Real world scenarios and Lamport's physical clock
Clocks can drift away from the real time and result in anomalies.

Thus we have Lamport's Physical clock:

a |-> b => Ci(a)<Cj(b) iff

	PC1: for all i, individual drift should be very small, ie d(Ci(t))/dt - 1 < k for all i, k << i
	PC2: for all i,j mutual drift should be bounded, ie Ci(t)-Cj(t) < epsilon.

pc1: Ci(t2) - Ci(t1)/ t2-t1 -1 < k
 Ci(t2) - Ci(t1)/ t2-t1 > 1 - k
 Ci(t2)-Ci(t1) > (t2-t1) (1-k)
 mu = t2-t1

Thus the formulation for total ordering of events in a dist network is:

If we have a network of n processors, with 

mu = smallest time to send a message,
k = individual drift
eps = mutual drift

then the conditions required to ensure that there is no anomalies is that:
for event t,

1. Ci(t+mu) - Cj(t) > 0. This ensures that the receipt of a message is after the send.
2. Ci(t+mu) - Ci(t) > mu (1-k). This ensures that 

using 1 and 2:
Ci(t+mu) > Cj(t)
Ci(t+mu) > Ci(t) + mu (1-k)
thus Cj(t) is related to Ci(t) + mu(1-k)
We need Ci(t) < Cj(t). ie,
Cj(t) = Ci(t) + x
ie Ci(t) + x = Ci(t) + mu(1-k)
for this to hold mu = x/1-k

smallest time for msg > mutual drift / (1 - ind drift)

Old notes
---------
	4   cycles on a cache hit
	100 cycles on a cache miss

how many cycles does a processor with 99% hit rate spend on average?

99% hit rate means out of 100 calls to main memory, 99 were fulfilled from cache and 1 was from memory.

So time for each of the 99 calls = 4   cycles
   time for the  1 call  =  100 cycles
 Average time = sum of times / count of times =  (99 * 4 + 100 )/ 100 = 4.96

If the hit rate was 90%, then, out of 100 calls to main memory, 90 were fulfilled from cache and 10 were from memory

So time for each of the 90 calls = 4 cycles
   time for each of the 10 calls = 100 cycles

Average time = (90 * 4 + 10 * 100) / 100 = 13.6

qn3:
22 bit size address space
512 byte cache
cache block size of 64 byte.

so, on a 2 pow 22 byte sized memory, only 512 bytes of that will be cached. every time one memory location is hit, the 64 locations surrounding it will also be. So at a time, the cache can hold 512/64 = 2 pow 9/ 2 pow 6 = 2 pow 3 blocks.
so bits reqd to uniquely identify blocks in cache = 3 = index.

now cache block is 64 => offset is 2 pow 6 bytes => 6 bits are required to uniquely identify each memory location in a block.

=> tag = 22 - 3 - 6 = 13

32 bit physical and virtual addr space
pages are 4k and cache blocks are 64 bytes
what is the # entries in a virtually indexed, physically tagged cache?

32 bit physical with 4 k pages
1 page = 2 pow 12 => # pages possible = 2 pow 32/ 2 pow 12 = 2 pow 20.
ie 2 pow 20 entries in the page table.
ie offset = 12, index = 20

when the pages are stored in the cache of 64 blocks each, the cache itself has:
offset = 6 bits, index + tag = 6.

https://www.udacity.com/course/viewer#!/c-ud098/l-635358970/m-648758632


# Course video durations
refresher: 
	memory systems: 33 min - 
	file systems: 25 min
	multithreaded prgg: 26.5 min
	networking: 35 mins, 8 quizzes

L1: rewatch and make notes
	L01A: Abstraction: 9 min
	L01b: Hw resources: 9:15
	L01c: OS Functionality: 7 mins
	L01d: OS Abstractions: 13.33 mins

L2:
	LO2a: OS structure: 30.43 mins, 4 qz
	L02b: Spin OS: 36 mins,2 qz
	L02c: Exokernel: 40 mins, 2 qz
	L02d: L3 microkernel: 45.4 mins, 1 qz

P1: 17.75, 6 qz.

thu: mt prgg + 10 min of sdp
fri: networking + 10 min of sdp
sat: memory systems + 10 min of sdp
sun: file systems + 10 min of sdp
mon onwards: ~ 20 min of aos + 10 mins of sdp + 30 mins of project.


Got answer to where pthread_join's retval is got from if there's no pthread_exit is called from here.
http://www.informit.com/articles/article.aspx?p=2085690&seqNum=5

Got answer to can we join with main thread here: http://stackoverflow.com/questions/4228203/is-it-valid-to-call-pthread-join-on-the-main-thread


