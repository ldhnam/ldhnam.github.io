### Difference between Array and ArrayList
    * Array fixed size, ArrayList dynamic size

### Redis

Redis keys are expired in two ways: a passive way and an active way.
A passive way: Client tries to access it and the key is found to be timed out.
Redis does 10 times per second:
1. Random 20 keys from the set of keys with an associated expire.
2. Delete all keys found expired.
3. If more than 25% of key were expired, start from again step 1.

Redis complied with 32 target uses a lot less memory per key. To complie Redis as 32 bit binary use make 32bit. Redis uses hashes when possible

### Physical and Virtual Memory

As the size of physical RAM is limited, so each process runs in its own memory sandbox - "virtual address space" known as ***Virtual Memory***

***Address of a byte in this virtual address space is no longer the same as the address that processor places on the address bus.***

Virtual memory space is divided into smaller regions called pages (4kB on most OS can be changed). Virtual memory doesn't store anything, it simply maps a program's address space onto the underlying physical memory.

***Memory Allocator***
 > If there is enough space in the heap to satisfy a memory request from our code, Memory allocator can fulfill the request without kernel involvement, else it enlarges heap via a system (brk) call, usually requesting a large block of memory. (For malloc large mean > MMAP_THRESHOLD bytes -128 kB by default).


### Database

#### Replication

Database replication refers to the frequent copying of data from one node—a database on a server—into another.
The transaction can then choose to return a commit after writing to the WAL, but before writing to the main data files. Now, if the server crashes, on restart it can replay all changes on the WAL since the last checkpoint.

### Distributed System


### Amazon ElastiCache

#### For Redis

* Automatic detection and recovery from cache node failures
* Redis (cluster mode enabled) support partitioning your data across up to 90 shards


### API

API should be backward compatible, it's mean shoud not remove field just add more field.
URL must be stable.
