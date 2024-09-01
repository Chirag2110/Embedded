Chapter 1: Introduction

Hierarchy of memory

1. CPU Register Files  -> SRAM
2. Cache               -> SRAM
3. Main memory         -> DDR/DRAM
4. Disk/Storage memory -> HDD/SSD (Flash drivers)

Cost of momory reduces as we move from 1 to 4; however, transaction cost of accessing it increases.

Morden commodity hardware aka computers uses combination of all in it's design to make them more economical. (Ofcourse besided design challenges like die size).

An idea behind cache is CPU doesn't access all the memory content at same time.
Only subset of program/data is needed.

So, Next memory cell access is tightly coupled to a memory cell which CPU accessed last.

This relation is exploited by computer architectures via introducing a small and fast memory between CPU and main memory which is called cache.

A meat of the idea:
  1. program doesn't access memory in random pattern.
  2. Caches take copy of subset of main memory contnet and keep it close to CPU.

Now, be carefull! Accessing only next memory cell and keeping it by cache is not
advantegeous as it increases power impact and CPU stall.

Hence, it is a good idea to take copy of a chunck of main memory. (Analogy from
day to day life would be grocery shopping, we generally don't buy a single item.)

This chunk of memory is an unit of cache, called block or cache line.

This idea could be exploited at multi level by hw designer. That is why in morden HW, we have multiple level of cache. Like L1, L2, and L3.

Size of this cache increases from L1 to L3.

Operations in Cache:
  1. CPU request access to particular address in memory to cache.
  2. Caches check if it has this address covered ?
  3. If yes, it means "CACHE HIT". -> Shared data with CPU do nothing.
  4. If no, it means "CACHE MISS"  -> Cache controller (HW that manages cache) 
                                      request main memory and copies data.
                                      Once, copied CPU can access same address now.
  
Done ??
  No, Cache is limited in size. So, once, it is fully filled a cache controller has to copy a new block from main memory to Cache.

  It has to find a cache block which it could replace.

  Replacing existing cache line throws a few challenges.
    1. Which cache like to remove?
    2. If it is data chache? Is it dirty?

  Finding and removing a cache line is called Replacement/Eviction.


  *********************************************************************************
  Chapter 2: Definitions

  Let's define a few keywords evalute cache performaces.

  1. Locality:
     
     CPU access same or near by address most of the time. This is called locality.

     a). Temoral locality:
         Use came address for next operation.
     b). Spatial Locality:
         Use near by address for next operation.

  2. Working Set:

     Cache always brings in a subset of main memory whenever cache miss happens.
     This subset also known as Working Set.

     Size of a working set impacts performace of a program.
       a). If the working set is big enough to hold whole program program runs very fast.
       b). If the working set is too small runs very slow (due to frequency cache misses).

       Also, a big working set means bigger caches which means more cost and big die size.

       We can say Working Set is similar as cache line or cache block.

  3. Stride:
      It is more of how a program access memory/cache.

      For example:
      Let's assume cache line/block size = 4 dword.

      below program as stride of 1. It is sequntially increase index by 1.
      With cache line size of 4. the below program will face a cache miss every
      4th element access in array.

      So this programm will have 3 cache miss in it's life time.

      int array[10];
      int sum = 0

      for (int index = 0; index < 10; index++)
      {
        sum += arry[index];
      }

      Cache Miss = ~33%
      Cache Hit  = ~66%

      On contrary, program with index moving by 4 will have cache miss 3 times consecutively.
      
      for (int index = 0; index < 10; index = index + 4)
      {
        sum += arry[index];
      }

      Cache Miss = 100%
      Cache Hit  = 0%
      You can imagine performace degradation if stride greater than cache line size.

**********************************************************************************
Chapter 3: Cache Write and Coherency.

    There are two type of caches in morden computer.

    1. Intruction Cache and
    2. Data Cache.

    It is advantageous to differentiate cahche for intruction and data because intstuction are part of code which is ready only.

    Meaning, no need to take care of cache line write back on eviction/replacement.

    On other hand, Data Cache lines needs to be written back on eviction.
    Reason, CPU might have update some part of cache line. Which is not reflected back to main memory.

    There are different scenarios under which a cache line write back shold be done.

    1. Write-hit:
      A scenario where CPU writes to memory and cache controller finds it in cached line.

      1. Write - Through:
        Immediately update block of cache line in main memory. In short keep a cache line and main memory in sync.
      
      2. Write - Back:
        For now update cache line and mark cache line as dirty. On eviction dirty cache line would be flushed to main memory.
    
    2. Write-miss:
      A scenario where CPU writes to memory and cache controller doesn't find it in cached line.

      a). Write-No-Allocate:

          Write updated data to main memory directly and don't allocate a cache line for miss.
          Means: No cache line eviction.

      b). Write-Allocate:

          Evict a cache line and update offset in block.

Typical approach with on Write

    1. Write-hit -> Write-back:
        - Update a cache line.
        - Mark current line dirty
        - On eviction write complete cache line to memory.
    
    2. Write-miss -> Write-Allocate:
        - Evict LRU cache line (if dirty write back whole line to memory)
        - Bring the missed cache line to cache.
        - Update cache line, mark it dirty.
        - On eviction write complete cache line to memory.

    Generally good approach for where memory is not shared between CPUs.
    Multiple write is expected.

Another approach:
    1. Write-hit  -> Write-through
    2. Write-miss -> Write-no-allocate

    Good approach for shared memory scenario. But it gets complex and slow.
    But there are better design available.

***********************************************************************************
Chapter-4: How Caching works?

Suppose
Address size is 32-bits
Cache line size 64 bytes
Number of cache lines 16

Total Cache size = 64*16 = 1 kB

 ----------------------------------------------------------------------
|                26-bit                       |       6-bit            |
 -----------------------------------------------------------------------

 Address will be divided in two parts: Tag-Key and Offset respectively

 Tag-key will be used by cache controller to decide to which cache line out of 16
 entries it matches.

----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------
| Tag-Key         |D|V|                       Data                               |
----------------------------------------------------------------------------------

If it matches Value will be written/read based on access request.
If not we will have different option based on cache design. (Direct-Mapped/Associative).

As you can see it is not just Tag-key and data on cache line but other important fields like Dirty-D, and Valid-V.

So in reality total cache size > 1 kB.

The above cache is Fully associative that means any cache line can hold any block of cache line from main memory.

***********************************************************************************
Chapter: 5 Direct-Mapped Caches

Each memory block can goto single cache line.

For example:
Imagine we have 16 cache lines
  1. 0th, 16th, 32th, ... blocks of memory will goto 0th cache line.
  2. 1st, 17th, 33th, ... blocks of memory will goto 1th cache line.
  3. 2nd, 18th, 34th, ... blocks of memory will goto 2nd cache line.

So, there will be multiple block mapped to single cache line

How it works??
Total Main Memory / Cache size = total number of mapping per chache lines

With direct mapping search logic for mapped cache line is limited to particular cache line.

How ??

------------------------------------------------------------------------
|       22-bits                        |  4-bits   |       6-bits      |
------------------------------------------------------------------------

32 bit address is split into
Tag    : 22 bits
Index  :  4 bits
Offset :  6 bits

         ------------------------------------------------------
   0     | Tag-Key         |D|V|                       Data    |
         -------------------------------------------------------
         ------------------------------------------------------
   1     | Tag-Key         |D|V|                       Data    |
         -------------------------------------------------------
         ------------------------------------------------------
   2     | Tag-Key         |D|V|                       Data    |
         -------------------------------------------------------
         ------------------------------------------------------
   3     | Tag-Key         |D|V|                       Data    |
         -------------------------------------------------------
         ------------------------------------------------------
   4     | Tag-Key         |D|V|                       Data    |
         -------------------------------------------------------
         -------------------------------------------------------
   5     | Tag-Key         |D|V|                       Data    |
         -------------------------------------------------------
         -------------------------------------------------------
   6     | Tag-Key         |D|V|                       Data    |
         -------------------------------------------------------
         .
         .
         .
         .
         -------------------------------------------------------_
   15    | Tag-Key         |D|V|                       Data    |
         -------------------------------------------------------

As shown in above table
  1. Index will be used to find right index. Hence, search logic is simplified.
  2. Tag is compared with Tag-key in cache table
  3. if it matches cache line is found
  4. if not cache controller will evict the cache line and replace it with correct
     cache line.

Pros:
  1. Fast and Simple

Cons:
  1. impacts performace if data being access is with stride of 1kB. with 100% miss rate. This is also called Cache Trashing.
  Not likely but possible.

**********************************************************************************
Chapter: 6 combination of direct and associative cache.

Good and bad feature of direct mapped cache is multiple blocks maps to a single cache line.

Good and bad feature of fully associative cache is any block can map to any cache line. Now this makes cache controller logic complext as finding and managing cache line becomes difficult.

We can take combination of both direct and associative cache to handle this challenge.

Idea is to divide whole cache memory into different sets.

For example 4 way associative set.

32-bit address will be split like below.

------------------------------------------------------------------------
|       24-bits                        |  2-bits   |       6-bits      |
------------------------------------------------------------------------

Tag    :  24-bits
Index  :   2-bits
Offset :   6-bits

Total Cache size = No of set * Cache lines per set * offset
                 = 4 * 4 * 64
                 = 1 kB


A Set view:

-----------------------------------
| Tag  |V|D|    DATA              |
-----------------------------------
| Tag  |V|D|    DATA              |
-----------------------------------
| Tag  |V|D|    DATA              |
-----------------------------------
| Tag  |V|D|    DATA              |
-----------------------------------

Cache Memory view:
_________________________________________________________________
| ____________________________    ___________________________   |
||      SET - 0               |  |       SET-1               |  |
||____________________________|  |___________________________|  |
| ____________________________    ___________________________   |
||      SET - 2               |  |       SET-4               |  |
||____________________________|  |___________________________|  |
|_______________________________________________________________|

Now, 0th, 16th, 32th, ... blocks of memory maps to set 0 but it has 4 cache line
avaiblable.

Conflicts is much less likely.
**********************************************************************************
Chapter 7: Cache misses

1. Compulsory Miss: Boot time cache misses. Also know as cold cache.
2. Conflict Miss:   Cache is too small cann't contain entire working set.
3. Capacity Miss:   Plenty of room in cache but two blocks in working set
                    cannot be kept at same place.

Trashing:
  Both block X and Y is needed
  If both block X and Y maps to same cache line

  Load X, Evict Y
  Load Y, Evict X
  Load X, Evict Y

  Performace takes a nosedive. "BAD"

  Pathological Case:
      No locality: A spagetti code.
      Neural Network Simulation.
      Computer generated codes.
      Graph like data structure.
*********************************************************************
Chapter: 8 Different types of tag and index

Till now, we have classified an address type.

Address from CPU can be classified in two category.
  1. Virtual or Logical Address
  2. Physical Address.

When address from CPU is physical cache access steps are as discuss previously, but
when address is a virtual, it adds one more layer.

1. PIPT:
    Physically indexed physically Tag

    Logical address first need to be converted to Physical address as followed
    1. Check TLB if address is present get physical address.
    2. If not get physical address by accessing page table entry from main memory
       Access cache with it.
       This will result into page fault, page will be brough from secondary storage
       to main memory. TLB get upadted. Also update cache line.

       A lot of penalty.

    Cons:
    1. Every address access gets translation from TLB.
    2. Not good choice for inner level cache.

2. VIVT:

    Virtually indexed virtually Tag

    1. Access related set from virtual index.
    2. Match Tag
    3. If Match fails access TLB, get physicall address bring data into Cache

   Tagged and indexed through virtual tag and indexed. Doesn't require access to
   TLB for translation.
   Better compare to PIPT.

   Lots of cache misses on context switch:
   Since the cache is specific to logical address and each process has its own logical address space, two process can use the same address but refer to different data.
   Remember that this is the same reason for having a page table for every process. This means that for every context switch, the cache needs to be flushed and every context switch follows with a lot of cache misses both of which is time-consuming and adds to the hit time.

3. VIPT:

    Virtually Indexed Physically Tag

    Get Tag related to Virtual indexed from Cache
    Get Logical to physical address translation from TLB.

    Compare both Tag.

    1. If TLB doesn't have corresponding physical address.  
  
