---
Title: DBS101 Unit_seven class7
categories: [DBS101 Unit_Seven_class7]
tags: [DBS101]
---
# Introduction
- A transaction in a database is a collection of operations (such as read, write, update, or delete) that are treated as a single logical unit of work. The purpose of a transaction is to ensure that either all the operations are executed successfully, or none of them are this maintains the integrity and consistency of the database.
- In databases, it refers to a unit of execution that accesses and possibly updates data items.

### Marked by:
- BEGIN TRANSACTION
- END TRANSACTION, COMMIT, or ROLLBACK

## ACID Properties
### Atomicity:
- All operations are completed successfully, or none are.
### Durability:
- Once a transaction is committed, changes persist even after system failure.
![transaction](/assets/unit7/Transaction.png)

### Consistency:
- The database remains in a consistent state before and after the transaction.
### Isolation:
- Concurrent transactions don’t interfere with each other.

### Example Transaction (Transfer $50 from A to B)
- BEGIN;
- UPDATE accounts SET balance = balance - 50 WHERE account_name = 'A';
- UPDATE accounts SET balance = balance + 50 WHERE account_name = 'B';
- COMMIT;

## Transaction Failures and Recovery
- Failure after partial execution leads to inconsistent state.
- Logging is used to restore the previous state (atomicity).
- Durability is ensured by writing changes to stable storage.

## Storage Structures
- Volatile Storage:
- Fast but loses data on crash (RAM, cache).

### Non-Volatile Storage:
- Survives crashes (disk, SSD, flash).
## Stable Storage:
- Achieved by replication (e.g., RAID), minimizes data loss.

## Transaction States
- Active – Transaction is executing.
- Partially Committed – Last statement executed.
- Failed – Error detected, can't proceed.
- Aborted – Rolled back.
- Committed – Successfully completed.

## Concurrency in Transactions
- Multiple transactions can run concurrently.
### Benefits:
- Increased throughput
- Reduced waiting time
- Challenge: Ensuring isolation and consistency.

## Transaction Schedules
### Serial Schedule:
- Transactions run one after another. Safe and consistent.
### Non-Serial Schedule:
- Operations are interleaved. Can lead to inconsistency.
### Serializable Schedule:
- Equivalent to some serial schedule; maintains consistency.

## Serializability
- Conflict Serializability:
- Based on detecting conflicting operations:
- Different transactions
- Same data item
- At least one is a write
### Precedence Graph:
- Used to test for conflict serializability.
- Cycle in graph = Not serializable
- No cycle = Serializable

## Recoverability in Schedules
### Recoverable Schedule:
- A transaction that reads from another should commit after the writer commits.
### Cascadeless Schedule:
- A transaction reads only after the writer commits → prevents cascading rollbacks.

## Transaction Isolation Levels
- Serializable – Fully isolated and consistent.
- Repeatable Read – Committed reads; same rows can't change.
- Read Committed – Only committed data can be read; data can change between reads.
- Read Uncommitted – Allows dirty reads (inconsistent).

## Isolation Techniques
### Locking:
- Two-Phase Locking (2PL) to ensure serializability.
### Timestamps:
- Transactions ordered by timestamps.
- Multi-Version Concurrency Control (MVCC):
- Uses snapshot isolation.

## Snapshot Isolation
- Transactions read data as it was at the start.
- Prevents interference, but not fully serializable.

## Locks in Databases
- Locks ensure isolation and prevent conflicts between concurrent transactions.
- Data should be accessed mutually exclusively to avoid inconsistency.
- A transaction must request a lock before accessing a data item.

## Types of Locks
![type](/assets/unit7/type.png)

- Multiple S locks can coexist.
- X lock requires exclusive access.

### Lock Compatibility
- S is compatible with S
- S is not compatible with X
- X is not compatible with any other lock

## Two-Phase Locking Protocol (2PL)
- Growing Phase: Can acquire locks, cannot release any.
- Shrinking Phase: Can release locks, cannot acquire any.

## Lock Implementation
- Managed by a Lock Manager
- Uses a lock table and linked lists per data item.
- Grants locks based on arrival order to prevent starvation.

## Deadlocks
- Occur when a cycle of transactions waits for each other’s locks.
1. Detection:
- Use a Waits-For Graph
- Find and break cycles
![detection](/assets/unit7/deadlock.png)

2. Prevention:
- Use timestamps for priority
![prevention](/assets/unit7/prevention.png)

### Schemes:
- Wait-Die (Older waits, younger aborts)
- Wound-Wait (Older aborts younger)

### Rollback Types
- Full rollback
- Partial rollback using savepoints

## Lock Granularity
- Lock at different levels: attribute → tuple → page → table
- Trade-off between parallelism and overhead

### Database Lock Hierarchy
![Hierarchy](/assets/unit7/Hierarchy.png)

### Intention Locks
![lock](/assets/unit7/lock.png)

## Lock Escalation
- DBMS may escalate many fine-grained locks into one coarse-grained lock to reduce overhead.

## Insert/Delete Operations
### Insert: 
- It needs exclusive lock
### Delete:
- Conflicts with read/write/delete → needs exclusive lock

### Phantom Phenomenon
- Occurs in predicate reads (e.g., WHERE conditions)
- Resolved by locking index entries or predicate locking

## Timestamp Ordering
- Uses transaction timestamps to ensure serializable execution

### Thomas Write Rule:
- Skip outdated writes if they don’t affect current reads
- Aborts only if it violates read consistency

## Multi-Version Concurrency Control (MVCC)
- Maintains multiple versions of a data item
- Readers don’t block writers, and vice versa
- Good for read-heavy systems
- Supports snapshot isolation

### Snapshot Isolation (SI)
- Each transaction sees a consistent snapshot of the DB
- First writer wins
- Vulnerable to write skew anomaly

## Advanced Concurrency Control Techniques
- Weak consistency levels:
- Degree 2 consistency
- Cursor stability
- Optimistic CC
### Main-Memory DBs:
- Prefer latch-free data structures
### Operation-based CC:
- E.g., INCREMENT rather than READ + WRITE
### Real-Time DBs:
- Prioritize transactions with deadlines

## Why Recovery Systems Are Important
### Recovery systems in DBMS are essential due to:
- System/transaction failures
- Human errors
- Security breaches
- Hardware upgrades
- Natural disasters
- Compliance needs
- Data corruption
- Goal: Ensure transactions are atomic (all-or-nothing).

## Logging and Log Records
- The log keeps a sequence of records for all DB changes.
- Typical update log format: <Ti, Xj, V1, V2>
- Ti: Transaction ID
- Xj: Data item
- V1: Old value
- V2: New value
- Other log types: <Ti start>, <Ti commit>, <Ti abort>

### Deferred vs. Immediate Modification
- Deferred: Changes occur only at commit.
- Immediate: Changes can occur before commit (used in practice).

## Undo and Redo
### Undo: 
- The process to reverse the effects of a transaction Ti that is being rolled back.
- Restores the value of all data items updated by Ti to their old values using the log records.
- Writes special redo-only log records to document the updates performed during the undo process

### Redo: 
- The process of reapplying the effects of a transaction Ti to ensure that all its changes are reflected in the database after a crash or failure.
- Sets the value of all data items updated by Ti to their new values using the log records.
- Maintains the original order of updates, as redoing them out of order may result in incorrect final values

### Crash handling:
- Before commit → Undo
- After commit → Redo

## Checkpoints
- Reduce log scanning during recovery.
- A checkpoint saves current system state + active transactions.
- Fuzzy checkpoints allow ongoing transactions during checkpointing.

## Write-Ahead Logging (WAL)
- Log must be written to stable storage before the DB is modified.
- Requires STEAL (allow dirty pages) and NO-FORCE (no immediate writes) policies.

## ARIES Recovery Algorithm
- ARIES = Algorithms for Recovery and Isolation Exploiting Semantics
### Three Passes:
- Analysis: Determine dirty pages and active/incomplete transactions.
- Redo: Reapply all actions to restore pre-crash state.
- Undo: Rollback incomplete transactions.

## Main-Memory Databases
- It Must persist data to non-volatile storage due to volatility.
- It Use parallel recovery and redo-only logging for performance.

## High Availability
- Remote backups replicate logs.
- Hot-spare systems can take over during failures.
- Distributed databases and load balancing improve availability.

# What i have learned 
- In this unit, I have learned that a transaction in a database is a logical unit of work that must follow the ACID properties—Atomicity, Consistency, Isolation, and Durability. These properties ensure that database operations are reliable, consistent, and recoverable even in the case of failures. I explored how transactions move through various states like active, partially committed, committed, or aborted, and how systems use logs to recover from failures. Techniques such as undo and redo operations help restore the database to a consistent state. Logging mechanisms like write-ahead logging (WAL) ensure that changes are recorded before they are applied, supporting crash recovery. Recovery techniques, including checkpointing and the ARIES algorithm, are crucial for restoring database integrity after system crashes. ARIES, in particular, uses three phases—analysis, redo, and undo—to handle incomplete or failed transactions efficiently.

- I also learned how concurrency control is essential in multi-user environments to ensure that multiple transactions can be executed simultaneously without interfering with each other. This is managed through various isolation levels, locking mechanisms (like two-phase locking), and advanced strategies like timestamp ordering and multi-version concurrency control (MVCC). Locking ensures that data is accessed safely, while deadlock detection and prevention methods are necessary to handle resource contention. Concepts like serializability and recoverable schedules help maintain consistency during concurrent execution.  Overall, this unit has deepened my understanding of how modern databases maintain data integrity, ensure high performance, and recover from errors or crashes effectively.