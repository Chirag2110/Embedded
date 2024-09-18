Bentley's rule for an optimization work

Classified in 4 categories:
  1. Data Structure
  2. Loops
  3. Functions
  4. Logic

1. Data Structure:
    a. Encoding and Packaging
    b. Agumentation
    c. Pre-computation
    d. Compile time initialization
    e. Caching
    f. Lazy evaluation
    g. sparsity

2. Loops:
    a. Hoisting
    b. sentinels
    c. Loop Unrolling
    d. Loop fusion
    e. Eleminate wasted iterations

3. Functions:
    a. Tail recursion elimination
    b. Inlining
    c. Coarse recursion.

4. Logic:
    a. Constant folding and propogation
    b. common sub-expression elimination
    c. Algebric Identities
    d. Short circuting
    e. Ordering tests
    d. Creating Fast path
    f. Combining tests


1. Data structure:
    a. Packing and encoding:
        i.e: How to store date?
        
        String Representation:
        "September, 7, 2019" --> 18 bytes of data.
        Now consider 4 bytes DWORD machine. It requires 5 DWORDs.

        How to optimize this?
        We can do encoding. 
        Let's say we want to keep 4096 years record BC and AC.

        No days = 365.25 * 4096 * 2 ~= 3 million days

        No of bits = log2(3 * 10^6) = 22 bits

        But it requires an extra work get an extact month with string representation.

        Better approach:

        typdef struct _day
        {
            int year:13; // 2^13 = 4096 * 2
            int month: 4;
            int day: 5;
        } day;

        this also requires 22 bits and less work.
        This also requires extra padding of 9 bit which accumultes to 1 DWORD.

    2. Agumentation:
       
       i.e appending an element to a list.

        HEAD-> [0] -> [1] -> [2] -> [3] -> NULL
        
        It requires to travel to whole list.

        With extra tail pointer.

        HEAD-> [0] -> [1] -> [2] -> [3] -> NULL;
                                    Tail
    
    3. Pre-computaion:
       
       Prepare Look up table with static information.
       Or prepare at initial stage of program.

       i.e. Band and power limit mapping table.
       i.e. Pascal's triangle.
    
    4. Compile time optimization:
       Use static tables.
       Use scripts to generate tables and make it part of build flow.

    5. Caching:
        Store recently derives results in memory so
        it doesn't required to derive again.
    
    6. Sparsity:
        Exploit sparsity to avoid storing and computing on zeros.

        -> Matrix vector multiplication:

        {3. 0, 0, 0, 1, 0
         0, 4, 1, 0, 5, 9
         0, 0, 0, 2, 0, 6
         5, 0, 0, 3, 0, 0
         0, 0, 0, 0, 5, 0
         0, 0, 0, 8, 9, 7}

        -> Compressed Sparse Row (CSR)
        
        Index: 0    1   2   3   4   5   6   7   8   9   10  11  12  13
        Rows:  0    2   6   8   10  11  14

        Cols:  0    4   1   2   4   5   3   5   0   3   4   3   4   5
        Vals:  3    1   4   1   5   9   2   6   5   3   5   8   9   7

        Storage O(n + nnz)
        n = #rows
        nnz = #non-zero elements

        Works only for sparsely dense matrix.

        -> Store sparse graph

        VertexId    0   1   2   3   4   5
        Offset      0   2   5   5   6   7

        Edges       1   3   2   3   4   2   2

        runs many graphy algrotithms efficiently with this representation.
        e.g. breadth-first-search, PageRank.

2. Loops:
    a. Hoisting:

    Hoisting == loop invariant code motion.

    Avoid computing loop invariant code each time through the body of a loop.

    void scale(double *x, double *y, int N)
    {
        for (int i = 0; i < N; i++)
        {
            y[i] = x[i] * exp(sqrt(M_PI/2));
        }
    }

    it would be better if it computed intially outside loop or use macros to define at compile time.

    void scale(double *x, double *y, int N)
    {
        double factor = exp(sqrt(M_PI/2);

        for (int i = 0; i < N; i++)
        {
            y[i] = x[i] * factor);
        }
    }

    b. sentinels:

    A special dummy values placed in a data structure to simplify the logic of boudry conditions, and in particular, the handling of loop-exit tests.

    

4. Logic:
    a. Constant Folding and Propagation:

    idea ??
        "evaluate constant expression and substitute the result into further expressions." All during compilation.

        In simple wors use combination of macros
        which calls many othe macros to derive end result.

        For example:
        calculate sum of 1 to 5 at compile time.

        #define SUM_OF_1  (1)
        #define SUM_OF_2  (2 + (SUM_OF_1))
        #define SUM_OF_3  (3 + (SUM_OF_2))
        #define SUM_OF_4  (4 + (SUM_OF_3))
        #define SUM_OF_5  (5 + (SUM_OF_4))

    b. common-subexpression elimintation:

        avoid computing the same expression multiple times by evaluating the expression once and storing the result for later use.

        1. a = b + c
        2. b = a - d
        3. c = b + c
        4. d = a - d

        1 and 3 as well as 2 and 4 does same calculation.
    
    c. Algebric Identities
    d. Short-Circuting:
    
    Idea??
        Stop evaluating as soon as you know the answer.

        like adding explit check and breaking out of loop.

        && and || are short-circuting operators in C/C++.
    
    e. Ordering Tests:

        Consider code that executes a sequence of logical tests.

        The Idea is to perform those tests that are more often successful.

        For example:

        bool is_whiteSpaceChar(char c)
        {
            if (c == '\r' || c == '\t' || c == ' ' || c == '\n')
            {
                return true;
            }

            return false;
        }

        In any text file has # of ' ' > '\t' > '\n' > '\r'.

        Better to short-circuit like below.
        bool is_whiteSpaceChar(char c)
        {
            if (c == ' ' || c == '\t' || c == '\n' || c == '\r')
            {
                return true;
            }

            return false;
        }
    f. creating a fast path
        like base case or special case handling

        perform basic check first and proceed further base on result or return.
    
    g. Combining Tests:
        Remove if else if else and try to use switch cases.

    










