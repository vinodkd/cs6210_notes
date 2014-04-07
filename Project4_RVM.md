Project4: RVM
=============

Requirements
------------

Implement a recoverable virtual memory library. Using the library, programs must be able to:

- store data durably, ie data stored in memory must be persisted and available across program instantiations and despite crashes.
- modify the data in memory with changes persisted transparently and durably to storage.
- the changes will be made transactionally, ie, all changes must be stored accurately and completely or not at all.
- changes need not be persisted immediately to disk, but must be updated such that the next read from disk should be consistent with all the updates.

Todos
-----

- Create test plan for all scenarios using Cat-Partition technique from sdp
	- plan for BVT vagaries.
- Write tests for each scenario and build the code incrementally.
	- Decide on unit test tool for c.
- Collect BVT test cases as well.
- Build in the following iterations:
	- library saves to memory, mocks save to disk
	- library saves to memory and disk, but does full segment writes
	- library saves to memory and disk, does only affected areas, but non overlapping ones.
	- library saves to memory and disk, does only affected areas, and handles overlapping segments.
- Test on BVT

Plan
----


Resources
---------

Journal
-------
**Sun Apr  6 12:27:16 2014** : Started on this project. Watched the vidoes on Lesson 8 and watched Leslie Lamport's talk on Thinking for programmers at the same time. This inspired me to:

- consider writing the spec for the program in TLA+. After spending the last hour or so decided that this was too much to take on now.
- consider using the Category Partition method from SDP course to come up with the test cases for this program. Might still do that.

**Sun Apr  6 15:23:01 2014** : Starting to create test cases in cs6210_share/project4/rvm/
**Sun Apr  6 15:54:00 2014** : Created a basic tsl file, but its not working. Will fix it, sleepy now.
