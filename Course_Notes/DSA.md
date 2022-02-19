# Day 1

## Review  OO

* Class called **instances,** has state & behaviors of an obj

  * An object is called an **instance** of the class
  * Obj has function called **methods.**(behavior of obj**)** 
  * Variables called **field** or **instance variables** (state of obj)

* Calling obj function w/o (), the expression evaluates to the func itself instead of calling it

* Print

  * `Def __str__(self): return 'hello world'` is called when conversion to string like ‘print’.
  * `Def __repr__(self): return self.__str__()` called in interactive mode

* Method call `b.sell(100)` == function `Book.sell(b.100)`

* Inherits: 

  * `super().__init__(parent_x, parent_y)`inherit all methods in the superclass. 
    * To overwrite just simply define a function has a same name as parent class 

  * If super class’s method got overwritten by subclass, when we init an instance from subclass only the **overwritten method will be invoked** not the one in super class

## Reading code

* Why?
  * commincate with code
  * express our thoughts to computer
  * learn from others
  * reading code is part of process
    * from google or stackoverlow
    * as a documentation: variable/function name is not enough, look as source code
    * bugs are everywehre
* How to read?
  * look for key elements and code pattern
  * imagine the original goal of author
  * Look for patterns of high level operations, don't try run loop on paper
* get the gist of program
  * program's files and name of functions, like books' content and overview
    * function name, say its utility
    * function comments, sometimes outdated and misleading
    * parameter and return
      * *python doesn't have explicit types, without knowing types, we cannot know what the function does, e.g. + can be used both for numeric and for merging list*
    * reading the function itself
      * **patterns** of high-level operations in the code: 
        * accumulating: `sum = sum + x`
        * map: `foo.append(bar*2)`
        * Matrix or image: 'nested loop'`C[i][j] = A[i][j] + B[i][j]`
        * search, filter: `if x > blort: blort = x`
      * Analogy: remember as a whole
  * where is the main program
  * look at the input and output for functionality

# Part 2

* What is Data Structure

  * for what?
    * orgranize, group, encode relationship between data elements
      * How to organize: position in RAM; pointers
    * implementation data type: array, hashtable...
    * Algorithms operated on data structure: sorting algorithm..
  * How to choose?
    * use simplest, because don't know how long it survise
    * trade off between time and space: make faster by using more memory
    * some sophisticated data structure can help: hash table..

* Elemental data in memory RAM

  * Type: int, float, string.. while int can be diff size like float32 float64
  * data value: int can be number or categorical; string can also for cate, but more space

* Pointer/references

  * integer varible of memory address, `id(p)`
    * python knows varible that reference to memory
  * How much space does list of strings take?
    * n pointers and space for chars of all n strings

* Data Structure

  * Array
    * most common implementation of list (list is ordered but not necessarily sortable)
    * **contiguous** memory locations
    * `a = [9, 2, 45]` has a pointer to contiguous block of cells
  * Linked lists
    * `a = (9,(2,(45,None)))`

  * Queue/ordered list
    * First in first out (FIFO), operation: enqueue, dequeue
    * an array implementation
  * Stacks:
    * First in last out (FILO), operation: push, pop
    * used as work list for non-recursive tree walking
  * Set
    * unordered, unique collection, Operation: add, delete, contains, union, intersection
    * typical implementation is hash table, contains take O(1) for hash table
    * ~~hashtable implement maps key to 1, True, or similar (value is ignored)~~
      * Dictionary abstract data structure
        * map key-value
        * Look up value by key
        * Hash table and hashcode for get bucket index

* Binary tree ≠ binary search tree

  * Attribute
    * directed graph with internal nodes and leaves, no cycle 
    * each node has at most 1 parent, at most 2 child node
    * n node, n-1 edges; n internal node full binary tree is about log2(n) height
  * Use contiguous array: left child is 2i+1, right child is 2i+2
  * indexes as pointers, sklearn doesn't use pointers which is expensive, it use node ID and parallel arrays

* Binary search tree

  * nodes has value, left smaller, right greater

  * recursive

    * search

      ```python
      def search(p:TreeNode, x:object): 
        if p is None: 
          return None
      	if x < p.value:
      		return search(p.left, x) 
        if x > p.value:
      		return search(p.right, x) 
        return p
      ```

    * Construct

      ```python
      def add(p:TreeNode, value) -> TreeNode :
      	if p is None: 
          	return TreeNode(value)
      	if value < p.value: 
          	p.left = add(p.left, value) 
      	elif value > p.value: 
          	p.right = add(p.right, value) 
        return p # do nothing if equal (already there)
      ```

* Graphs

  * Many edges: label, direction, point, cycle

    ```python
    class LLNode:
      def __init__(self, value, next=None):
            self.value = value
            self.next = next
          
    class TreeNode:
      def __init__(self, value, left=None, right=None):
          self.value = value
          self.left = left
          self.right = right
        
    class Node:
      def __init__(self, value):
          self.value = value
          self.edges = [] # outgoing edges    
    ```



# Day 3

## Complexity

* What

  * Performance: get a feel for algorithm time and space performance on specific data strucutre
  * compare: Compare algorithm performance based on various input size
  * Behavior: best/ worst situation
  * reduce algorithm complexity

* Why

  * Factors that effect execution time:
    * data structure and algorithm
    * computer situation: processor, memory, disk
    * Single input: best and worst case
    * different input size

  * measure exec time is still usefull
  * Benefits:
    * know the performance across a wide variety of input content and size
    * know future, with company grow and user increase
    * compare two program without implement them, independent of machine property (speed, language...)

* Space v.s. Time: trade off

  * Space: storage necessary as a function of input size

  * Time: amount of work necessary <= focus

    * count fundamental operations of work: floating points, visiting nodes, traversing edges, swapping array elements

      * e.g. sort: counter the number of comparison

    * Interest is growth: how many more operations for each increases in input size

    * Define T(n) = total operations required to operate on size n

      * array e.g.

        ```python
        s = 0.0
        n = len(a)
        for i in range(n):
        s = s + a[i]
        
        # a+b is 1 operation
        # a[i] is another 1 operation
        # so for each loop step, takes 2 operation
        # the T(n) = 2n
        ```

    * $log(n) < n < nlog(n) < n^2 < 2^n < n!$, should better bellow $O(nlog(n))$

* Asymptotic behavior O(n)

  * growth in effort given growth in input; what is the marginal cost 

  * best picture: n is very big and input is the worst, e.g. only last one is what we want to search

  * O(n): ignore constants
    $$
    T(n) = 2n \Rightarrow O(n) \\
    T(n) = n^3 + kn^2 + nlog(n) \Rightarrow O(n^3) \\
    T(n) = k \Rightarrow O(1) \\ 
    T(n) = 3n! \ and \ T(n) = 10 n! \ are \ both \ O(n!)
    $$
    

* process:

  * unit of work 
  * key indicator
    * input size n
    * both input n and m, then  n x m
  * T(n) closed form
  * O(n) upper bound
  * Tips
    * Max loop iterations
      * look for loops and recursion
      * **verify loops step by constant**
      * look for patterns: binary search, sorting, traversing tree...
    * what is the maximum amount of work? focus on behavior
      * touch every elemnt is $O(n)$
      * Touch n x m matrix means $O(nm) = O(n^2)$
      * touch every element of a tree with n nodes is $O(n)$, but tracing from root to leaf is $O(log(n))$ in balanced tree; if trinary tree $T(log_3(n)) = O(log(n))$

* Examples:

  * Nested loop example

    ```python
    # first, must assuming a = ... cost c operations
    
    # T(n) = c*n*n => O(n^2)
    for i in range(n): 
    	for j in range(n):
    		a = ...
        
    # T(n) = c*n*n*n => O(n^3)  
    for i in range(n): 
      for j in range(n):
    		for k in range(n): 
          a = ...    
    ```

    * focus on real operation, not #lines, https://wiki.python.org/moin/TimeComplexity

    * `if` can be tricky, cause the operation inside isn't awlays called, but we can take it as worst situation.

    * **use while loop,` while n<0` 's `n` can shrink**

  * Recursive - trickier

    ```python
    def sum(a): # recursive sum array 
    	if len(a)==0:
      	return 0
      return a[0] + sum(a[1:])
    # Assumption: a[1:] takes constant time (not true in Python)
    ```

    * initial condition $T(0) = 0$

    * define recurrence relation:
      $$
      T(n) = 1 + T(n-1)\\
      T(n) = 1 + 1 + T(n-2)\\
      T(n) = 1 + 1 + 1 + T(n-3)\\
      T(n) = n + T(0) = n + 0 = n
      $$

  * Linear search

    ```python
    def find(a,x): # find x in a 
      n = len(a)
      for i in range(n):
        if a[i]==x: 
          return i
      return -1
    ```

    * count comparison: n, each comparison take 1 operation
    * Tuition: touch every element of the input in the worst case

    ```python
    def findw(words, doc): 
      c=0
    	for i in range(len(words)): 
        if words[i] in doc:
          c += 1 
      return c
    
    # inside each loop step:
    # words[i] is 1 operation
    # in doc is k operation
    # T(n) = n(1 + k)
    ```

    * ```python
      # T(n) = n(1 + k)def findw(words:list, doc:set): 
        c=0
        for i in range(len(words)): 
          if words[i] in doc:
            c += 1 
        return c
      
      # now we know doc is a set
      # words[i] is 1 operation
      # in doc is 1 operation
      # T(n) = n(1 + 1) = 2n => O(n)
      ```

    * ```python
      def findw(words:list, doc:list): 
        c=0
        for i in range(len(words)): 
          	if words[i] in doc:
              c += 1 
        return c
      
      # now we know doc is a set
      # words[i] is 1 operation
      # in doc is 1 operation
      # T(n) = n(1 + m) = 2n + mn => O(n^2)
      ```

  * loop iteration

    ```python
    def intlog2(n): # for n>=1 
      if n == 1: 
        return 0 
      count = 0
    	while n > 0:
    		n = n // 2
        count += 1
      return count-1
    
    # T(1) = 0
    # T(n) = 2 + T(n/2)
    # T(n) = 2 + 2 + T(n/(2^2))
    # T(n) = 2log(n)+ T(1) = 2log(n)
    # Ask how many times you can divide n by 2? log(n) times
    ```

  * binary search tree

    * Definition: left < current node, right > current node

    * n nodes, count comparison: different from binary tree, it only goes one direction, everytime half the data

      ```python
      p = root
      while p is not None:
        if p.value==x: 
          return p
        if x < p.value: 
          p = p.left 
        else: 
          p = p.right
          
      # common case: T(n) = 2 + T(n/2) => O(log(n))
      # worse case: like a list O(n)
      ```

    * the height of tree is $log_2(n)$

      * what is the average height? $ \approx \frac{1+log_2(n)}{2}$
      * what is the highest height? $log_2(n)$

  * ```sql
    -- all worst case
    copy -- O(n) avg O(n)
    append(1) -- O(1)
    pop last -- O(1)
    pop intermediate -- O(k)
    insert -- O(n)
    get item -- O(n) avg O(1)
    set item -- O(n) avg O(1)
    delete item -- O(n) avg O(1)
    iteration -- O(n) avg O(n)
    get slice -- O(k)
    set slice -- O(k+n)
    sort -- O(nlog(n))
    multiply -- O(nk)
    x in s -- O(n)
    min/max(s) -- O(n)
    get length -- O(1)
    
    ```

* some cheatsheet from professor Parr

<img src="/Users/caoyanan/Library/Mobile Documents/com~apple~CloudDocs/Yena/Github/Memory_Baguette/Course_Notes/image/complexity (dragged)1.png" alt="complexity (dragged)1" style="zoom:;" />

<img src="/Users/caoyanan/Library/Mobile Documents/com~apple~CloudDocs/Yena/Github/Memory_Baguette/Course_Notes/image/complexity (dragged)2.png" alt="complexity (dragged)2" style="zoom:;" />

\* *if we know more about data, we can be faster*

* summary
  * O(...) is (tight) upper-bound on work done for given input size
  * independent of language machine, algorithm
  * process: unit, size indicator, T(n), O(n)


## Recursion

* divide a big problem into small similar ones

  * factorial math: easy to understand

    ```python
    # recursion
    def fact(n):
        if n==0: 
          return 1
        return n * fact(n-1)
        
    # non-recursion    
    def factloop(n): 
      r=1
      for i in range(1,n+1): 
        r *= i
    	return r   
    ```

  * Fibonacci sequence: inefficient,  redundant, linear time

    ```python
    def fib(n):
      if n==0 or n==1: 
        return 1 
      return fib(n-1) + fib(n-2)
    ```

* Difference comes down to **two versus one** subproblem! 

  * Key idea: recursion traces out a tree of function calls (implies recursion is most natural way to walk trees)

  * fib(n-1) and fib(n-2) overlap $O(𝑘^n)$
    $$
    Assume\ T(i)=2^i \ holds\ for\ all\ 𝑖 < 𝑛 \\
     
     T(n) = k + T(n-1) + T(n-2)\\
     T(n+2) = k + T(n+1) + T(n)\\
     T(n+2)=𝑘+2^{n+1}+2^n = k + 2^1 2^n + 2^n = 𝑘+ 2^n(2 + 1) \\
     T(n) = 𝑘 + 3 ∗ 2^n \Rightarrow O(2!)
    $$

  * dynamic programming => `a,b = 0,1, a,b = b, a+b...`.

* How to do recursion

  > def f(input):
  > 1. check termination condition
  > 2. process the active input region / current node, etc... 
  > 3. invoke f on subregion or subregions
  > 4. combine and return results
  >
  > Terminology: currently-active region or element is what the function is currently trying to process. Here, that is argument n

  * Step 1: break into smaller subproblems until subproblems are so small that we can solve in constant time
  * Step 2: climb back
  * Examples: merge sort, quick sort, closest pair of (x,y) points

* attribute

  * recursion is easiest way to describe algorithms
  * single tail recursive call can convert to loops, which is more efficient: Factorial

* Divide and conquer

  * Binary search: Divide and conquer algorithms make 2 or more recursive calls where each subproblem is a **fraction** of the size of the currently active problem

    * cost of any recursive algorithm
      * number of recursive subproblem
      * size of the subproblems
      * work required for each active region

  * Example

  * merging two sorted lists is **O(n)**

    ```python
    def msort(A):
    	if len(A)==1: # sort one element in O(1)
    		return A
    	if len(A)==2: # sort two elements in O(1)
    		return A if A[0]<A[1] else [A[1],A[0]] 
      mid = int(len(A)/2)
    	left = msort(A[:mid])
    	right = msort(A[mid:])
    	return sorted(left + right)
    ```

  * write pow2(n) ****

    ```python
    # recursion O(n)
    def pow2(n):
      if n==0: 
        return 1
      return 2 * pow2(n-1)
        
    # iterative O(n)    
    def pow2(n): 
      v=1
      for i in range(n): 
        v *= 2 
      return v    
    
    # Divide and conquer O(n/2)
    def pow2(n):
    	if n==0: 
        return 1
      half = pow2(n//2)
    	if n % 2==0: # if even
    		return half * half 
      return 2 * half * half
    ```

* * 

### Dynamic Programming

* we can alway decompose problem into subproblems, if there are **repeated computation**

* caching or memoizing partial results, reuse to avoid redundant computation

* save former record in memory
  * Fibonacci only cares the former two numbers, so just need to remember those two
  * use it when there is redundant computation
  * check how many number should be memories
  
* `a,b = 0,1, a,b = b, a+b...`

* Fibonacci

  ```python
  def cachefib(n): # non-recursive version
  	F = [0 for i in range(n+1)] # cache
  	F[0] = F[1] = 1 # init cache
  	for i in range(2,n+1): # work up not down
      F[i] = F[i-1] + F[i-2]
    return F[n]
  ```

​		\* *Algorithmic changes matter much more than code optimizations!!!*

* combined with recursion

  ```python
  def memofib(n, memo = {}):
    if n==0 or n==1: 
      return 1
    if n in memo: # return if already computed
      return memo[n]
    f = memofib(n-1, memo) + memofib(n-2, memo) 
    memo[n] = f # memoize result
  	return f
  ```

  > def f(input):
  > 1. check termination condition as usual
  > 2. **return memoized result if available**
  > 3. process the active input region / current node, etc... 4. invoke f on subregion(s)
  > 5. combine results and memoize
  > 6. return results


* Leetcode Example

  * maximum depth: always can use recursion O(n)

  * Reverse listed link:

    * head pointer, subproblem

      ```
      h ->(1 ->2....->4)
      h' ->(2 ->3 ...->4) ->1
      h'' ->(3 ...->4) ->2 ->1
      
      reverse h -> next -> h'
      h.next = None
      find last of h' -> x
      x.next = h
      return h'
      ```

      



# Day 4 

## Walking Data Structure

* most of algorithm walks data structures

* walking arrays

* Matrix walking 

* Visualize

* Trees

  * Depth-first search in perticular visitation order

  * *Traversal* (pre-, in-, post-) order depends on action location

    * Preorder => depth-first: parents first

      ```
      def walk_tree(p:TreeNode):
      if p is None: return print(p.value) # preorder walk_tree(p.left) walk_tree(p.right)
      ```

    * Postoder: children fist

      ```
      def walk_tree(p:TreeNode):
      if p is None: return walk_tree(p.left) walk_tree(p.right) print(p.value) # postorder
      ```

  * Seach in binary tree

    * exhaustive walk

      ```
      def walk(p:TreeNode): if p is None: return walk(p.left) walk(p.right)
      ```

    * restricted walk 

      ```
      def search(p:TreeNode, x:object): if p is None: return None
      if x==p.value: return p
      q = search(p.left, x)
      q = search(p.right, x)
      if q is not None: return q
      return q
      ```

  * Compare binary tree walk with BST search

    * Conditional recursion: only recurse to **ONE** child not both

      ```
      # T(n) = k + 2T(n/2)
      def walk_tree(p:TreeNode): if p is None: return print(p.value) walk_tree(p.left) walk_tree(p.right)
      
      
      # T(n) = k + T(n/2)
      def search(p:TreeNode, x:object): if p is None: return None
      if x<p.value:
      return search(p.left, x) if x>p.value:
      return search(p.right, x) return p
      ```

    * Graphs: Manual construction

  * *Depth-first* graph walk*, compare to tree

    * *Depth-first* graph walk avoiding cycles

      ```
      # naughty but simple
      def walk_graph2(p:Node) -> None:
      if p is None or p in visited: return
      visited.add(p) 
      # must be before recursion step
      print(p.value)
      for q in p.edges:
      walk_graph2(q)
      ```

* Summary

  - Walking data structures is fundamental to most algorithms
  - You should be able to walk arrays, link lists, trees, and graphs
  - Algorithms tend to be restricted or repeated walks
  - In the context of walking data structures, dynamic programming or memoization means recording partial results to avoid revisiting parts of the structure
  - Binary tree and graph walks are very similar in code, but have to transition to more children and must deal with cycles
  - Use recursion to walk trees and graphs (can be slow in python)



## Sorting

* Dirty tricks to sort faster than *O(n log n)*

  * Traditional sorting algorithms: bubble sort, merge sort, quicksort
  * Dirty tricks: pigeonhole sort, bucket sort can often sort in O(n)
  * Really dirty trick: nested bucket sort
  * What is the fastest?
    * only comparison or not

* Bubble sort

  ```
  changed=True second_to_last_idx = len(A)-2 while changed:
  changed=False
  for i in range(second_to_last_idx+1):
  if A[i] > A[i+1]:
  A[i], A[i+1] = A[i+1], A[i] changed=True
  ```

  * O(n^2), look at the worst case
  * stable
  * look for out-of-order elements and swap

* Merge sort
  * *O(n log n)*
  * Simple, recursion/divide and conquer
  * table
  * not in place
  * sort half

* Quicksort

  * 𝑂(𝑛^2) worst-case behavior but O(*n log n*) typical behavior

  * pick min to left and larger to right

  * not stable

    ```
    def qsort(A, lo=0, hi=len(A)-1): if lo >= hi:
    return
        pivot_idx = partition(A,lo,hi)
        qsort(A, lo, pivot_idx-1)
        qsort(A, pivot_idx+1, hi)
        
        
    # many ways to do this; here’s a slow O(n) one 
    # breaks idea of in-place for qsort
    def partition(A,lo,hi):
    pivot = A[hi] # pick last element as pivot left = [a for a in A if a=<pivot]
    right = [a for a in A if a>pivot] A[lo:hi+1] = left+[pivot]+right # copy back return len(left) # return index of pivot   
    ```

* So much for traditional sorts

  * for generic elements and doing comparisons

  * what if we know the elements are ints or strings or floats?

  * Pigeonhole sort

    ```
    # fill holes
    size = max(A) + 1 holes = [0] * size for a in A:
    holes[a] += 1
    # pull out in order
    A_ = []
    for i in range(0,size): A_.extend([i] * holes[i])
    ```

    * when the range of keys, *m*, is similar to the number of elements, n
    * T(n,m) = n + m
    * some issue

  * Bucket sort (also called bin sort)

    ```
    x = max(A)
    ...
    for a in A:
        a_normalized = a / mx # get into 0..1
        # spread across buckets
        i = int(a_normalized * (nbuckets-1))
        buckets[i].append(a)
    ...
    for i in range(nbuckets):
        A_.extend( sorted(buckets[i]) )
    ```

    

    * sort inside bucket, when hashing to bucket, it is kind of sorte

      * T(n,m,k=n/m) = m * k^2 + n, O(n)

    * on string

      ```
      for s in A:
          i = ord(s[0])-ord('a’)
          holes[i].append(s)
      ...
      for i in range(ord('z')-ord('a') + 1):
          A_.extend( sorted(holes[i]) )
      ```

      * Use first letter as bucket key
      * Nested or recursive string bucket sort *(Called TRIEs)*

* Summary
  - If asked, sorting is O(n log n) (via comparisons)
  - Divide and conquer, merge and quicksort, are primary algorithms
    - Mergesort merges two sorted halves recursively; takes extra memory
    - Quicksort partitions instead of sorting halves; works in-place (usually better)
  - But, we can do better with pigeonhole sort, mapping each element to unique bucket based on the key; O(n)
  - If mapping to unique bucket is hard, as with floating-point numbers, use bin/bucket sort like a hash table; O(n) if reasonably evenly distributed and enough buckets
  - Use ord(char) for strings to bucket sort
  - Use all letters in strings to get nested bucket sort (called a TRIE)
