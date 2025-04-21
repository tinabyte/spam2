- Everything you've learned until now assumes that there's only one user
- obviously there are multiple users in the real world
- You need to ensure consistency in the case of failures / crashes
## Types of Inconsistency
- Attribute-level
	- multiple users update the same attribute constantly (guy has 99 in his bank account. setting A: add 5, setting B: add 5. if you run A and B at the same time, you get 104 instead of 109 sometimes because they read and write in incomplete states)
- Tuple-level
	- concurrent updates to different attributes of the same tuple 
	- the values now don't make sense
	- Ex: 
		- EmployeeID: 101
		- DateHired: 2023-05-15
		- DateBirth: 2010-03-20
		- Position: Senior Manager
	- JK He's just Chinese and has been working since he was in the womb
- multi-statement inconsistency
	- multiple database queries that are related run without concurrency control and screw it up
	- if a student who is rejected goes into archive, and starts in apply, a function that inserts Rstudents into archive from apply when they are rejected but does not do it atomically might cause a student to appear in apply and archive when both tables are selected
	- in the same way, if a query involves multiple select statements, other queries can muck it up
## ACID (Throwback)
- (resilience to system failure and enhances concurrency)
- Atomicity
	- Transactions cannot be split further. They either happen or they don't.
	- Failure = roll back completely
- Consistency
	- Database must transition from valid state to valid state
- Isolation
	- Transactions that don't mess with each other cannot run concurrently and must run sequentially
- Durability
	- Failure-resilient

## Levels of Isolation
- Serializability
	- Highest level of isolation
	- High consistency, low concurrency, high overhead
	- literally one at a time
- Read uncomitted
	- (can dirty read)
	- basically can read whenever
- Read comitted
	- no dirty read, but values can change between reads. you can read after written to (problem called interleaved reads)
- repeatable read 
	- take a snapshot of the state that it's read, and use that data to r/w info 
	- issue: what if you have two queries that run at the same time, and run on the same valid state? the one that writes first is lost

• Describe the ACID properties and their significance.  
- A (atomicity)
	- Transactions cannot be split further. They are atomic. They either happen or they don't
- C (Consistency)
	- The database transitions from one valid state to another. Important to make sure that you don't have system-breaking errors
- I (Isolation)
	- Transactions don't interfere with each other. different levels of isolation 
- D (Durability)
	- System can survive failure / not being on (memory is stored physically somewhere). When a transaction happens, it survives system failure
• Define and explain the differences between the isolation levels: Read  
Uncommitted, Read Committed, Repeatable Read, and Serializable.  
- Read uncomitted: you can read transitional data (being written to). Most concurrent state. also most dangerous (dirty reads)
- Read comitted: you can only read data that has been committed (cannot read data in transition.) Has the issue of interleaved reads / writes (queries run concurrently, but one reads from a transitional state that has been written to and comitted but hasn't finished the query.)
- Repeatable read: take a snapshot at the start of reads so that it doesn't read transitional data
- Serializable: has to be verifiably equivalent to running sequentially
• What is the difference between a committed and an uncommitted trans-  
action?
- committed: transaction is finished and gives go-ahead to read in read-comitted
- uncommitted: literally in transitional state. not finished yet
• What is a dirty read? Provide an example scenario where a dirty read  
might occur.  
- reading from data that is actively being written to
- read uncommitted isolation level + reading from data that has a write op ongoing from another user
• In the event of a system failure, how do databases ensure durability?
- write-ahead logging 


First Normal Form - Indivisible non-composite values in cells, no duplicate rows and no duplicate columns

2nd Normal Form - requires everything from 1st normal form but you also need to decompose table into smaller tables where each table's composite key only owns keys that it needs all of its attributes to identify. for example: 

ssn + passport number: trip_id, bank_account_number

ssn + passport_number is needed to find trip_id, but it is not needed to find bank_account_number (you only need 1 key for that)

instead, it should just be 

ssn + passport_number: trip_id
ssn: bank_account_number

3rd normal form: 2nd normal form, and that attributes cannot be derived from each other

a->b and b->c cannot be 

a-> b,c, it has to be 
a-> b 
b-> c

BCNF: 3NF, and X must be a superkey for the table 
guide for decomposing: 
- for each FD, check if X is a superkey
- if it's note, compute the closure of the failed key, remove those attributes from the "glob" into its own table, and iterate over until each table's key is its own superkey


• Check for 2NF and 3NF compliance
- 2nf: check to see if any attributes belong to composite keys that can be computed with part of the composite key (and see if it's in 1nf)
- 3nf: check to see if any attributes can be computed with each other (and see if it's in 2nf)
• Identify partial dependencies and decompose to achieve 2NF
- if it belongs to a part of a composite key, pull it out along with the part of the composite key
• For the following relation, list all FDs that satisfy 2NF and 3NF.
- explained before, just check for compliance
• Determine whether the relation with the this dependency requires nor-  
malization.  
- read the previous things bruh
• Given FDs, decompose to BCNF and justify the stopping point.  
- check if X is a superkey. if not, reduce Y until it is. then, pick the first element of y as the new x, and compute closure for that. go until you have a set of FDs which are all superkeys
• Identify violations of BCNF in the following schema and normalize as  
needed  
- THIS IS THE SAME THING {EXPLETIVE} PRIMARY KEY IS X
• Explain why a certain relation is not in 2NF and describe the process  
to achieve 2NF.  
- not in 2nf because some attribute in Y can be determined by a partial selection of X
• Demonstrate how to convert the relation R to BCNF given this set of  
functional dependencies S
- 


vtable

virtual view - not stored physically

materialized view - stored and refreshed periodically

use them like a table 😩💦

just say "CREATE VIEW viewname AS (query)"
optional: rename columns "CREATE VIEW viewname (column1, column2) AS (query)"
- Let you view without having to constantly load from storage
- Updatable Views can be CRUDDED
	- must not
		- have select distinct
		- have where with subquery referencing base relation

Index - Data structure stored on disk. Speeds up tuple access by reducing full-table scans
- Choose frequently queried attributes, not rarely used ones. Primary keys and unique constraints are automatically indexed
- Fast with efficient joins but has overhead in space and dev time (no fucking shit holy shit why does this even have to be stated hehe)

Materialized views
- Views with pre-computed results, stored physically on disk used for complex aggregations / joins
- saves CPU power, uses storage

Tuning advisors take in Query history, table metadata, and sample workloads and spit out reccomended indices

What is a constraint?
- Forced rule / relationship between elements
Types of constraints
- Foreign Key constraint
	- When you insert a new entry into the table with a foreign key, it must have a corresponding entry in the table that the foreign key connects to 
- CHECK constraint
	- programmable (you set it yourself)
	- side_hoes INT check (count >= 2)
		- list of playas
- Tuple based check
	- CHECK (side_hoes >= 2 or baby_mamas >= 2)
		- list of playas and retired playas
	- must be done at the end of the declaration
- Assertions
	- maintain constraints on the entire schema
	- CREATE ASSERTION cheaters CHECK (NOT EXISTS(SELECT * FROM BROTHERS WHERE side_hoes >= 1)))
- Handling violations
	- Foreign key table
		- Reject if not exists connected FK 
	- Referenced table
		- Reject action
		- Propogate change to referencing rows
			- copy the changes to rows referencing it
		- set Referencer to null
			- requires allowing null
- Triggers
	- Execute on certain events

- BEFORE CRUD
	- check if meets all requirements (won't fuck anything up)
- After CRUD
	- check to see if there's anything you need to update as a result of the CRUD
