---
title: "Parallelism"
source: "https://pbr-book.org/4ed/Utilities/Parallelism"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## B.6 Parallelism
> ## B.6 并行性

As improvements in the performance of single processing cores have slowed over the past fifteen years, it has become increasingly important to write parallel programs in order to reach the full computational capabilities of a system.
> 随着过去十五年来单处理核心性能提升的放缓，编写并行程序以充分发挥系统的计算能力变得越来越重要。 Fortunately, ray tracing offers abundant independent work, which makes it easier to distribute work across processing cores. This section discusses some important principles of parallelism, focusing on CPUs, and introduces assorted classes and functions that pbrt uses for parallelism. (See Section [15.1](https://pbr-book.org/4ed/Wavefront_Rendering_on_GPUs/Mapping_Path_Tracing_to_the_GPU.html#sec:mapping-path-tracing-to-gpu) for discussion of parallelism on GPUs and how pbrt is parallelized on those processors.)

One of the biggest challenges with parallel ray tracing is the impact of nonparallel phases of computation. For example, it is not as easy to effectively parallelize the construction of many types of acceleration structure while the scene is being constructed as it is to parallelize rendering. While this may seem like a minor issue, *Amdahl’s law*, which describes the speedup of a workload that has both serial and parallel phases, points to the challenge. Given cores performing computation and a workload where the fraction of its overall computation is inherently serial, the maximum speedup then possible is

Thus, even with an infinite number of cores, the maximum speedup is. If, for example, a seemingly innocuous of the run time is spent in a serial phase of parsing the scene file and building acceleration structures, the maximum speedup possible is, no matter how quickly the parallel phase executes.

We experienced the impact of Amdahl’s law as we brought pbrt ’s GPU rendering path to life: it was often the case that it took longer to parse the scene description and to prepare the scene for rendering than it took to render the image, even at high sampling rates! This led to more attention to parallelizing parsing and creating the objects that represent the scene. (See Section [C.3](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#sec:basic-scene) for further discussion of this topic.)

### B.6.1 Data Races and Coordination

When pbrt is running on the CPU, we assume that the computation is running on processors that provide *coherent shared memory*. The main idea of coherent shared memory is that all threads can read and write to a common set of memory locations and that changes to memory made by one thread will eventually be seen by other threads. These properties greatly simplify the implementation of the system, as there is no need to explicitly communicate data between cores.

Although coherent shared memory relieves the need for separate threads to explicitly communicate data with each other, they still need to *coordinate* their access to shared data; a danger of coherent shared memory is *data races*. If two threads modify the same memory location without coordination between the two of them, the program will almost certainly compute incorrect results or even crash. Consider the example of two processors simultaneously running the following innocuous-looking code, where globalCounter starts with a value of two:

extern int globalCounter; if (--globalCounter == 0) printf("done\\n");

Because the two threads do not coordinate their reading and writing of globalCounter, it is possible that “done” will be printed zero, one, or even two times. For example, if both threads simultaneously load globalCounter, decrement it in a local register, and then write the result simultaneously, both will write a value of 1 and “done” will never be printed.

Two main mechanisms are used for this type of synchronization: mutual exclusion and atomic operations. Mutual exclusion is implemented with std::mutex objects in pbrt. A std::mutex can be used to protect access to some resource, ensuring that only one thread can access it at a time:

extern int globalCounter; extern std::mutex globalCounterMutex; globalCounterMutex.lock(); if (--globalCounter == 0) printf("done\\n"); globalCounterMutex.unlock();

*Atomic memory operations* (or *atomics*) are the other option for correctly performing this type of memory update with multiple threads. Atomics are machine instructions that guarantee that their respective memory updates will be performed in a single transaction. (*Atomic* in this case refers to the notion that the memory updates are indivisible.) The implementations of atomic operations in pbrt are from the C++ standard library. Using atomics, the computation above could be written to use the std::atomic<int> type, which has overloaded add, subtract, increment, and decrement operations, as below:

extern std::atomic<int> globalCounter; if (--globalCounter == 0) printf("done\\n");

The std::atomic – operator subtracts one from the given variable, globalCounter, and returns the new value of the variable. Using an atomic operation ensures that if two threads simultaneously try to update the variable, then not only will the final value of the variable be the expected value, but each thread will be returned the value of the variable after its update alone. In this example, then, globalCounter will end up with a value of zero, as expected, with one thread guaranteed to have the value one returned from the atomic subtraction and the other thread guaranteed to have zero returned.

Another useful atomic operation is “compare and swap,” which is also provided by the C++ standard library. It takes a memory location and the value that the caller believes the location currently stores. If the memory location still holds that value when the atomic compare and swap executes, then a new value is stored and true is returned; otherwise, memory is left unchanged and false is returned.

Compare and swap is a building block that can be used to build many other atomic operations. For example, the code below could be executed by multiple threads to compute the maximum of values computed by all the threads. (For this particular case, the specialized atomic maximum function would be a better choice, but this example helps convey the usage.)

std::atomic<int> maxValue; int localMax =...; int currentMax = maxValue; while (localMax > currentMax) { if (maxValue.compare\_exchange\_weak(currentMax, localMax)) break; }

If only a single thread is trying to update the memory location and the local value is larger, the loop is successful the first time through; the value loaded into currentMax is still the value stored by maxValue when compare\_exchange\_weak() executes and so localMax is successfully stored and true is returned. If multiple threads are executing concurrently, then another thread may update the value in maxValue between the thread’s read of maxValue and the execution of compare\_exchange\_weak(). In that case, the compare and swap fails, memory is not updated, and another pass is taken through the loop to try again. In the case of a failure, compare\_exchange\_weak() updates currentMax with the new value of maxValue.

An important application of atomic compare and swap is for the construction of data structures. Consider, for example, a tree data structure where each node has child node pointers initially set to nullptr. If code traversing the tree wants to create a new child at a node, code could be written like:

// atomic<Type \*> node->firstChild if (!node->firstChild) { Type \*newChild = new Type... Type \*current = nullptr; if (node->firstChild.compare\_exchange\_weak(current, newChild) == false) delete newChild; } // node->firstChild!= nullptr now

The idea is that if the child has the value nullptr, the thread speculatively creates and fully initializes the child node into a local variable, not yet visible to the other threads. Atomic compare and swap is then used to try to initialize the child pointer; if it still has the value nullptr, then the new child is stored and made available to all threads. If the child pointer no longer has the value nullptr, then another thread has initialized the child in the time between the current thread first seeing that it was nullptr and later trying to update it. In this case, the work done in the current thread turns out to have been wasted, but it can delete the locally created child node and continue execution, using the node created by the other thread.

This method of tree construction is an example of a *lock-free* algorithm. This approach has a few advantages compared to, for example, using a single mutex to manage updating the tree. First, there is no overhead of acquiring the mutex for regular tree traversal. Second, multiple threads can naturally concurrently update different parts of the tree. The “Further Reading” section at the end of this appendix has pointers to more information about lock-free algorithms.

### B.6.2 Atomic Floating-Point Values

The std::atomic template cannot be used with floating-point types. One of the main reasons that atomic operations are not supported with it is that floating-point operations are generally not associative: as discussed in Section [6.8.1](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#sec:ieee-fp), when computed in floating-point, the value of the sum (a+b)+c is not necessarily equal to the sum a+(b+c). In turn, if a multi-threaded computation used atomic floating-point addition operations to compute some value, then the result computed would not be the same across multiple program executions. (In contrast, with integer types all the supported operations are associative, and so atomic operations give consistent results no matter which order threads perform them in.)

For pbrt ’s needs, these inconsistencies are generally tolerable, and being able to use atomic operations on Float s is preferable in some cases to using a lock. (One example is splatting pixel contributions in the [RGBFilm::AddSplat()](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#RGBFilm::AddSplat) and GBufferFilm::AddSplat() methods.) For these purposes, we provide a small AtomicFloat class.

<<AtomicFloat Definition>>=

class AtomicFloat { public: << [AtomicFloat Public Methods](#fragment-AtomicFloatPublicMethods-0) >>

explicit AtomicFloat(float v = 0) { bits = [FloatToBits](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#FloatToBits) (v); } operator float() const { return [BitsToFloat](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#BitsToFloat) (bits); } Float operator=(float v) { bits = [FloatToBits](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#FloatToBits) (v); return v; } void Add(float v) { FloatBits oldBits = bits, newBits; do { newBits = [FloatToBits](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#FloatToBits) ([BitsToFloat](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#BitsToFloat) (oldBits) + v); } while (!bits.compare\_exchange\_weak(oldBits, newBits)); } std::string ToString() const;

private: << [AtomicFloat Private Members](#fragment-AtomicFloatPrivateMembers-0) >>

std::atomic< [FloatBits](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#FloatBits) > bits;

};

An AtomicFloat can be initialized from a provided floating-point value. In the implementation here, floating-point values are actually represented as their unsigned integer bitwise values, as returned by the [FloatToBits()](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#FloatToBits) function.

<<AtomicFloat Public Methods>>=

explicit AtomicFloat(float v = 0) { bits = [FloatToBits](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#FloatToBits) (v); }

Using an integer type to represent the value allows us to use a std::atomic type to store it in memory, which in turn allows the compiler to be aware that the value in memory is being updated atomically.

<<AtomicFloat Private Members>>=

std::atomic< [FloatBits](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#FloatBits) > bits;

Assigning the value or returning it as a Float is just a matter of converting to or from the unsigned integer representation.

operator float() const { return [BitsToFloat](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#BitsToFloat) (bits); } Float operator=(float v) { bits = [FloatToBits](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#FloatToBits) (v); return v; }

Atomic floating-point addition is implemented via an atomic compare and exchange operation. In the do loop below, we convert the in-memory bit representation of the value to a Float, add the provided difference in v, and attempt to atomically store the resulting bits. If the in-memory value has been changed by another thread since the value from bits was read from memory, the implementation continues retrying until the value in memory matches the expected value (in oldBits), at which point the atomic update succeeds.

<<AtomicFloat Public Methods>>+=

void Add(float v) { FloatBits oldBits = bits, newBits; do { newBits = [FloatToBits](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#FloatToBits) ([BitsToFloat](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#BitsToFloat) (oldBits) + v); } while (!bits.compare\_exchange\_weak(oldBits, newBits)); }

pbrt does not currently need to perform any other operations on AtomicFloat s, so we do not provide any additional methods. An AtomicDouble class, not included here, provides an equivalent Add() method for atomic addition with double s.

### B.6.3 Memory Coherence Models and Performance

Cache coherence is a feature of all modern multicore CPUs; with it, memory writes by one processor are automatically visible to other processors. This is an incredibly useful feature; being able to assume it in the implementation of a system like pbrt is extremely helpful to the programmer. Understanding the subtleties and performance characteristics of this feature is important, however.

One potential issue is that other processors may not see writes to memory in the same order that the processor that performed the writes issued them. This can happen for two main reasons: the compiler’s optimizer may have reordered write operations to improve performance, and the CPU hardware may write values to memory in a different order than the stream of executed machine instructions. When only a single thread is running, both of these are innocuous; by design, the compiler and hardware, respectively, ensure that it is impossible for a single thread of execution running the program to detect when these cases happen. This guarantee is not provided for multi-threaded code, however; doing so would impose a significant performance penalty, so hardware architectures leave requiring such ordering, when it matters, to software.

*Memory barrier* instructions can be used to ensure that all write instructions before the barrier are visible in memory before any subsequent instructions execute. In practice, we generally do not need to issue memory barrier instructions explicitly, since both C++ atomic and the thread synchronization calls used to build multi-threaded algorithms can include them in their operation.

Although cache coherence is helpful to the programmer, it can sometimes impose a substantial performance penalty for data that is frequently modified and accessed by multiple processors. Read-only data has little penalty; copies of it can be stored in the local caches of all the processors that are accessing it, allowing all of them the same performance benefits from the caches as in the single-threaded case. To understand the downside of taking too much advantage of cache coherence for read–write data, it is useful to understand how cache coherence is typically implemented on processors.

CPUs implement a *cache coherence protocol*, which is responsible for tracking the memory transactions issued by all the processors in order to provide cache coherence. A classic such protocol is *MESI*, where the acronym represents the four states that each cache line can be in. Each processor stores the current state for each cache line in its local caches:

- *Modified* —The current processor has written to the memory location, but the result is only stored in the cache—it is *dirty* and has not been written to main memory. No other processor has the location in its cache.
- *Exclusive* —The current processor is the only one with the data from the corresponding memory location in its cache. The value in the cache matches the value in memory.
- *Shared* —Multiple processors have the corresponding memory location in their caches, but they have only performed read operations.
- *Invalid* —The cache line does not hold valid data.

At system startup time, the caches are empty and all cache lines are in the invalid state. The first time a processor reads a memory location, the data for that location is loaded into cache and its cache line marked as being in the “exclusive” state. If another processor performs a memory read of a location that is in the “exclusive” state in another cache, then both caches record the state for the corresponding memory location to instead be “shared.”

When a processor writes to a memory location, the performance of the write depends on the state of the corresponding cache line. If it is in the “exclusive” state and already in the writing processor’s cache, then the write is cheap; the data is modified in the cache and the cache line’s state is changed to “modified.” (If it was already in the “modified” state, then the write is similarly efficient.) In these cases, the value will eventually be written to main memory, at which point the corresponding cache line returns to the “exclusive” state.

However, if a processor writes to a memory location that is in the “shared” state in its cache or is in the “modified” or “exclusive” state in another processor’s cache, then expensive communication between the cores is required. All of this is handled transparently by the hardware, though it still has a performance impact. In this case, the writing processor must issue a *read for ownership* (RFO), which marks the memory location as invalid in the caches of any other processors; RFOs can cause stalls of tens or hundreds of cycles—a substantial penalty for a single memory write.

In general, we would therefore like to avoid the situation of multiple processors concurrently writing to the same memory location as well as unnecessarily reading memory that another processor is writing to. An important case to be aware of is “false sharing,” where a single cache line holds some read-only data and some data that is frequently modified. In this case, even if only a single processor is writing to the part of the cache line that is modified but many are reading from the read-only part, the overhead of frequent RFO operations will be unnecessarily incurred. pbrt uses alignas in the declaration of classes that are modified during rendering and are susceptible to false sharing in order to ensure that they take entire cache lines for themselves. A macro makes the system’s cache line size available.

<<Define Cache Line Size Constant>>=

#ifdef PBRT\_BUILD\_GPU\_RENDERER #define PBRT\_L1\_CACHE\_LINE\_SIZE 128 #else #define PBRT\_L1\_CACHE\_LINE\_SIZE 64 #endif

### B.6.4 Thread Pools and Parallel Jobs

Although C++ provides a portable abstraction for CPU threads via its std::thread class, creating and then destroying threads each time there is parallel work to do is usually not a good approach. Thread creation requires calls to the operating system, which must allocate and update data structures to account for each thread; this work consumes processing cycles that we would prefer to devote to rendering. Further, unchecked creation of threads can overwhelm the processor with many more threads than it is capable of executing concurrently. Flooding it with more work than it can handle may be detrimental to its ability to get through it.

A widely used solution to both of these issues is *thread pools*. With a thread pool, a fixed number of threads are launched at system startup time. They persist throughout the program’s execution, waiting for parallel work to help out with and sleeping when there is no work for them to do. In pbrt, the call to [InitPBRT()](https://pbr-book.org/4ed/Utilities/System_Startup,_Cleanup,_and_Options.html#InitPBRT) creates a pool of worker threads (generally, one for each available CPU core). A further advantage of this implementation approach is that providing work to the threads is a fairly lightweight operation, which encourages the use of the thread pool even for fine-grained tasks.

<<ThreadPool Definition>>=

class ThreadPool { public: <<ThreadPool Public Methods>>

explicit ThreadPool(int nThreads); ~ThreadPool(); size\_t size() const { return threads.size(); } std::unique\_lock<std::mutex> AddToJobList(ParallelJob \*job); void RemoveFromJobList(ParallelJob \*job); void WorkOrWait(std::unique\_lock<std::mutex> \*lock, bool isEnqueuingThread); bool WorkOrReturn(); void Disable(); void Reenable(); void ForEachThread(std::function<void(void)> func); std::string ToString() const;

private: <<ThreadPool Private Methods>>

void Worker();

<< [ThreadPool Private Members](#fragment-ThreadPoolPrivateMembers-0) >>

std::vector<std::thread> threads; mutable std::mutex mutex; bool shutdownThreads = false; bool disabled = false; ParallelJob \*jobList = nullptr; std::condition\_variable jobListCondition;

};

pbrt ’s main thread of execution also participates in executing parallel work, so the ThreadPool constructor launches one fewer than the requested number of threads.

<<ThreadPool Method Definitions>>=

ThreadPool::ThreadPool(int nThreads) { for (int i = 0; i < nThreads - 1; ++i) [threads](#ThreadPool::threads).push\_back(std::thread(&ThreadPool::[Worker](#ThreadPool::Worker), this)); }

<<ThreadPool Private Members>>=

std::vector<std::thread> threads;

The worker threads all run the ThreadPool ’s Worker() method, which acquires a mutex and calls WorkOrWait() until system shutdown, at which point shutdownThreads will be set to true to signal the worker threads to exit. When we get to the implementation of WorkOrWait(), we will see that this mutex is only held briefly, until the thread is able to determine whether or not there is more work for it to perform.

void ThreadPool::Worker() { std::unique\_lock<std::[mutex](#ThreadPool::mutex) > lock([mutex](#ThreadPool::mutex)); while (![shutdownThreads](#ThreadPool::shutdownThreads)) [WorkOrWait](#ThreadPool::WorkOrWait) (&lock, false); }

mutable std::mutex mutex; bool shutdownThreads = false;

Before we get to the implementation of the WorkOrWait() method, we will discuss the ParallelJob class, which specifies an abstract interface for work that is executed by the thread pool and defines a few member variables that the ThreadPool will use to keep track of work. Because it is only used for CPU parallelism and is not used on the GPU, we will use regular virtual functions for dynamic dispatch in its implementation.

<<ParallelJob Definition>>=

class ParallelJob { public: << [ParallelJob Public Methods](#fragment-ParallelJobPublicMethods-0) >>

virtual ~ParallelJob() { DCHECK(removed); } virtual bool [HaveWork](#ParallelJob::HaveWork) () const = 0; virtual void RunStep(std::unique\_lock<std::mutex> \*lock) = 0; bool Finished() const { return![HaveWork](#ParallelJob::HaveWork) () && [activeWorkers](#ParallelJob::activeWorkers) == 0; } virtual std::string ToString() const = 0;

<< [ParallelJob Public Members](#fragment-ParallelJobPublicMembers-0) >>

static ThreadPool \*threadPool;

private: << [ParallelJob Private Members](#fragment-ParallelJobPrivateMembers-0) >>

friend class ThreadPool; int activeWorkers = 0; ParallelJob \*prev = nullptr, \*next = nullptr;

};

All the parallel work in pbrt is handled by a single thread pool managed by ParallelJob.

<<ParallelJob Public Members>>=

static ThreadPool \*threadPool;

Each job may consist of one or more independent tasks. The two key methods that ParallelJob implementations must provide are HaveWork() and RunStep(). The former indicates whether there is any remaining work that has not yet commenced, and when the latter is called, some of the remaining work should be done. The implementation can assume that none of its methods will be called concurrently by multiple threads—in other words, that the calling code uses a mutex to ensure mutual exclusion.

RunStep() is further passed a pointer to a lock that is already held when the method is called. It should be unlocked at its return.

<<ParallelJob Public Methods>>=

virtual bool HaveWork() const = 0; virtual void RunStep(std::unique\_lock<std::mutex> \*lock) = 0;

ParallelJob carries along a few member variables that are purely for the use of the ThreadPool. Including them in the ParallelJob class here saves the thread pool from needing to dynamically allocate any per-job storage. One is activeWorkers, which the thread pool uses to track how many threads are currently working on the job.

<<ParallelJob Private Members>>=

friend class ThreadPool; int activeWorkers = 0;

In turn, a job is only finished if there is no more work to be handed out and if no threads are currently working on it.

<<ParallelJob Public Methods>>+=

bool Finished() const { return![HaveWork](#ParallelJob::HaveWork) () && [activeWorkers](#ParallelJob::activeWorkers) == 0; }

Returning to the [ThreadPool](#ThreadPool) implementation now, we will consider how work to be done is managed. The ThreadPool maintains a doubly linked list of jobs where its jobList member variable points to the list’s head. [ThreadPool::mutex](#ThreadPool::mutex) must always be held when accessing jobList or values stored in the [ParallelJob](#ParallelJob) objects held in it.

ParallelJob \*jobList = nullptr;

The link pointers are stored as [ParallelJob](#ParallelJob) member variables that are just for the use of the ThreadPool and should not be accessed by the ParallelJob implementation.

<<ParallelJob Private Members>>+=

ParallelJob \*prev = nullptr, \*next = nullptr;

AddToJobList() acquires the mutex and adds the provided job to the work list before using a condition variable to signal the worker threads so that they wake up and start taking work from the list. The mutex lock is returned to the caller so that it can do any further job-related setup, assured that work will not start until it releases the lock.

std::unique\_lock<std::[mutex](#ThreadPool::mutex) > ThreadPool::AddToJobList(ParallelJob \*job) { std::unique\_lock<std::[mutex](#ThreadPool::mutex) > lock([mutex](#ThreadPool::mutex)); << [Add job to head of jobList](#fragment-AddmonojobtoheadofmonojobList-0) >> [jobListCondition](#ThreadPool::jobListCondition).notify\_all(); return lock; }

Jobs are added to the front of the work list. In this way, if some parallel work enqueues additional work, the additional work will be processed before more is done on the initial work. This corresponds to depth-first processing of the work if dependent jobs are considered as a tree, which can avoid an explosion in the number of items in the work list.

<<Add job to head of jobList >>=

When there is no available work, worker threads wait on the jobListCondition condition variable.

<<ThreadPool Private Members>>+=

std::condition\_variable jobListCondition;

We can finally return to the WorkOrWait() method that all threads execute. The lock provided to it is of the mutex member variable, so it is safe to access other ThreadPool members as long as it is held. Its second parameter, isEnqueuingThread, is used when the thread pool has been temporarily disabled to ensure that only the thread that submits work performs computation in that case. (That capability is needed for an arcane situation in the implementation of some of pbrt ’s GPU code, so it is not discussed further here.)

The method implementation starts by walking through the job list in search of a ParallelJob that still has work left.

void ThreadPool::WorkOrWait(std::unique\_lock<std::mutex> \*lock, bool isEnqueuingThread) { <<Return if this is a worker thread and the thread pool is disabled>>

if (!isEnqueuingThread && disabled) { jobListCondition.wait(\*lock); return; }

ParallelJob \*job = [jobList](#ThreadPool::jobList); while (job &&!job-> [HaveWork](#ParallelJob::HaveWork) ()) job = job->next; if (job) { << [Execute work for job](#fragment-Executeworkformonojob-0) >> } else << [Wait for new work to arrive or the job to finish](#fragment-Waitfornewworktoarriveorthejobtofinish-0) >>

[jobListCondition](#ThreadPool::jobListCondition).wait(\*lock);

}

If an unfinished job is found, then its active worker count is incremented and its RunStep() method is called with the lock passed along.

<<Execute work for job >>=

Recall that RunStep() methods should release the lock before they do their actual work, so the lock will not be held by this thread after that call returns. Thus, the lock must be reacquired before this thread can update activeWorkers and check to see if the job is completed. If it is, the condition variable must be signaled again: the thread that initially spawned the work may be waiting on the condition variable for other threads to finish their work on the job.

<<Handle post-job-execution details>>=

Threads wait on the condition variable if there is no work to be done. The semantics of condition variables are such that the lock is released upon the call to wait(), but when the call returns due to the thread having been woken up, it will again hold the lock.

<<Wait for new work to arrive or the job to finish>>=

[jobListCondition](#ThreadPool::jobListCondition).wait(\*lock);

Removing a job from the list just requires rewiring the pointers of adjacent list nodes, if present, and updating the list head pointer if the job is at the head.

<<ThreadPool Method Definitions>>+=

The ThreadPool::WorkOrReturn() method is very similar to WorkOrWait() with the differences that it acquires a lock to the mutex itself rather than expecting it to be passed in and that it returns if there is no work available. (Its implementation is therefore elided.) This method will be useful with the forthcoming [AsyncJob](#AsyncJob) class, which opportunistically helps out with parallel work when it would otherwise be blocked.

The thread pool also provides a ForEachThread() function that takes a function to be executed on each of the threads in the thread pool as well as the main thread. In pbrt, it is used by the statistics system to collect statistics that are stored in per-thread variables.

### B.6.5 Parallel for Loops

Much of the multi-core parallelism when pbrt is running on the CPU is expressed through parallel for loops using the ParallelFor() and ParallelFor2D() functions, which implement the [ParallelJob](#ParallelJob) interface. These functions take the loop body in the form of a function that is called for each iteration as well as a count of the total number of loop iterations to execute. Multiple iterations can thus run in parallel on different CPU cores. Calls to these functions return only after all the loop iterations have finished.

Here is an example of using ParallelFor(). The first two arguments give the range of values for the loop index and a C++ lambda expression is used to define the loop body; the loop index is passed to it as an argument. The lambda has access to the local array variable and doubles each array element in its body.

Float array\[1024\] = {... }; ParallelFor(0, 1024, \[array\](int index) { array\[index\] \*= 2; });

While it is also possible to pass a function pointer to ParallelFor(), lambdas are generally much more convenient, given their ability to capture locally visible variables and make them available in their body.

ParallelForLoop1D implements the ParallelJob interface, for use in the [ParallelFor()](#ParallelFor) functions.

<<ParallelForLoop1D Definition>>=

class ParallelForLoop1D: public [ParallelJob](#ParallelJob) { public: << [ParallelForLoop1D Public Methods](#fragment-ParallelForLoop1DPublicMethods-0) >>

ParallelForLoop1D(int64\_t startIndex, int64\_t [endIndex](#ParallelForLoop1D::endIndex), int [chunkSize](#ParallelForLoop1D::chunkSize), std::function<void(int64\_t, int64\_t)> [func](#ParallelForLoop1D::func)): [func](#ParallelForLoop1D::func) (std::move([func](#ParallelForLoop1D::func))), [nextIndex](#ParallelForLoop1D::nextIndex) (startIndex), [endIndex](#ParallelForLoop1D::endIndex) ([endIndex](#ParallelForLoop1D::endIndex)), [chunkSize](#ParallelForLoop1D::chunkSize) ([chunkSize](#ParallelForLoop1D::chunkSize)) {} bool HaveWork() const { return [nextIndex](#ParallelForLoop1D::nextIndex) < [endIndex](#ParallelForLoop1D::endIndex); } void RunStep(std::unique\_lock<std::mutex> \*lock); std::string ToString() const { return StringPrintf("\[ ParallelForLoop1D [nextIndex](#ParallelForLoop1D::nextIndex): %d [endIndex](#ParallelForLoop1D::endIndex): %d " " [chunkSize](#ParallelForLoop1D::chunkSize): %d \]", [nextIndex](#ParallelForLoop1D::nextIndex), [endIndex](#ParallelForLoop1D::endIndex), [chunkSize](#ParallelForLoop1D::chunkSize)); }

private: << [ParallelForLoop1D Private Members](#fragment-ParallelForLoop1DPrivateMembers-0) >>

std::function<void(int64\_t, int64\_t)> func; int64\_t nextIndex, endIndex; int chunkSize;

};

In addition to the callback function for the loop body, the constructor takes the range of values the loop should cover via the startIndex and endIndex parameters. For loops with relatively large iteration counts where the work done per iteration is small, it can be worthwhile to have the threads running loop iterations do multiple iterations before getting more work. (Doing so helps amortize the overhead of determining which iterations should be assigned to a thread.) Therefore, ParallelFor() also takes an optional chunkSize parameter that controls the granularity of the mapping of loop iterations to processing threads.

<<ParallelForLoop1D Public Methods>>=

The nextIndex member variable tracks the next loop index to be executed. It is incremented by workers as they claim loop iterations to execute in their threads.

<<ParallelForLoop1D Private Members>>=

std::function<void(int64\_t, int64\_t)> func; int64\_t nextIndex, endIndex; int chunkSize;

The HaveWork() method is easily implemented.

<<ParallelForLoop1D Public Methods>>+=

bool HaveWork() const { return [nextIndex](#ParallelForLoop1D::nextIndex) < [endIndex](#ParallelForLoop1D::endIndex); }

RunStep() determines which loop iterations to run and does some housekeeping before releasing the provided lock and executing loop iterations.

<<ParallelForLoop1D Method Definitions>>=

void ParallelForLoop1D::RunStep(std::unique\_lock<std::mutex> \*lock) { << [Determine the range of loop iterations to run in this step](#fragment-Determinetherangeofloopiterationstoruninthisstep-0) >>

int64\_t indexStart = [nextIndex](#ParallelForLoop1D::nextIndex); int64\_t indexEnd = std::min(indexStart + [chunkSize](#ParallelForLoop1D::chunkSize), [endIndex](#ParallelForLoop1D::endIndex)); [nextIndex](#ParallelForLoop1D::nextIndex) = indexEnd;

<< [Remove job from list if all work has been started](#fragment-Removejobfromlistifallworkhasbeenstarted-0) >> << [Release lock and execute loop iterations in \[indexStart, indexEnd)](#fragment-ReleaselockandexecuteloopiterationsinmonoindexStartindexEnd-0) >>

lock->unlock(); [func](#ParallelForLoop1D::func) (indexStart, indexEnd);

}

Recall that the [ThreadPool](#ThreadPool) ensures that no other threads will concurrently call any of the other ParallelForLoop1D methods as long as the provided lock is held. Therefore, the method implementation here is free to access and modify member variables without needing to worry about mutual exclusion or atomic updates. Here, it is a simple matter to determine the range of iterations to run next, given a starting iteration and the chunk size. Note, however, that it is important to copy the nextIndex member variable into a local variable here while the lock is held, as that value will be accessed later when the lock is not held.

<<Determine the range of loop iterations to run in this step>>=

int64\_t indexStart = [nextIndex](#ParallelForLoop1D::nextIndex); int64\_t indexEnd = std::min(indexStart + [chunkSize](#ParallelForLoop1D::chunkSize), [endIndex](#ParallelForLoop1D::endIndex)); [nextIndex](#ParallelForLoop1D::nextIndex) = indexEnd;

If all the work for a job has begun, there is no need for it to be in the list of unfinished jobs that the ThreadPool maintains. Therefore, we immediately remove it from the list in that case. Note that just because a job is not in the work list does not mean that its work is completed.

<<Remove job from list if all work has been started>>=

Finally, the thread can release the lock and get to work executing the specified loop iterations.

<<Release lock and execute loop iterations in \[indexStart, indexEnd) >>=

lock->unlock(); [func](#ParallelForLoop1D::func) (indexStart, indexEnd);

The ParallelFor() function pulls all the pieces together to create a ParallelForLoop1D object, provide it to the thread pool, and then execute loop iterations in the thread that specified the loop. This function does not return until all the specified loop iterations are complete.

<<Parallel Function Definitions>>=

void ParallelFor(int64\_t start, int64\_t end, std::function<void(int64\_t, int64\_t)> func) { if (start == end) return; << [Compute chunk size for parallel loop](#fragment-Computechunksizeforparallelloop-0) >>

int64\_t chunkSize = std::max<int64\_t>(1, (end - start) / (8 \* RunningThreads()));

<< [Create and enqueue ParallelForLoop1D for this loop](#fragment-CreateandenqueuemonoParallelForLoop1Dforthisloop-0) >>

ParallelForLoop1D loop(start, end, chunkSize, std::move(func)); std::unique\_lock<std::mutex> lock = ParallelJob::[threadPool](#ParallelJob::threadPool) -> [AddToJobList](#ThreadPool::AddToJobList) (&loop);

<< [Help out with parallel loop iterations in the current thread](#fragment-Helpoutwithparallelloopiterationsinthecurrentthread-0) >>

while (!loop.[Finished](#ParallelJob::Finished) ()) ParallelJob::threadPool-> [WorkOrWait](#ThreadPool::WorkOrWait) (&lock, true);

}

The first step is to compute the chunk size—how many loop iterations are performed each time a thread gets another block of work to do. On one hand, the larger this value is, the less often threads will need to acquire the mutex to get more work. If its value is too small, parallel speedup may be inhibited by worker threads being stalled while they wait for other threads to release the mutex. On the other hand, if it is too large, then load balancing may be poor: all the threads but one may have finished the available work and be stalled, waiting for the last thread still working. Here the value is set inversely proportional to the number of threads in an effort to balance these two factors.

<<Compute chunk size for parallel loop>>=

int64\_t chunkSize = std::max<int64\_t>(1, (end - start) / (8 \* RunningThreads()));

(The RunningThreads() function, which is not included in the book, returns the total number of available threads for pbrt.)

A ParallelForLoop1D object can now be initialized and provided to the thread pool. Because this ParallelFor() call does not return until all work for the loop is done, it is safe to allocate loop on the stack—no dynamic memory allocation is required.

<<Create and enqueue ParallelForLoop1D for this loop>>=

ParallelForLoop1D loop(start, end, chunkSize, std::move(func)); std::unique\_lock<std::mutex> lock = ParallelJob::[threadPool](#ParallelJob::threadPool) -> [AddToJobList](#ThreadPool::AddToJobList) (&loop);

After adding the job, the thread that called ParallelFor() (be it the main thread or one of the worker threads) starts work on the loop. By finishing the loop before allowing the thread that submitted it to do any more work, the implementation keeps the amount of enqueued work limited and allows subsequent code in the caller to proceed knowing the loop’s work is done after its call to ParallelFor() returns.

Because a held lock to the ThreadPool ’s mutex is returned from the call to AddToJobList(), it is safe to call both Finished() and WorkOrWait().

<<Help out with parallel loop iterations in the current thread>>=

while (!loop.[Finished](#ParallelJob::Finished) ()) ParallelJob::threadPool-> [WorkOrWait](#ThreadPool::WorkOrWait) (&lock, true);

There is a second variant of ParallelFor() that calls a callback that only takes a single loop index. This saves a line or two of code in implementations that do not care to know about the chunk’s \[start, end) range.

<<Parallel Inline Functions>>=

void [ParallelFor](#ParallelFor) (int64\_t start, int64\_t end, std::function<void(int64\_t)> func) { [ParallelFor](#ParallelFor) (start, end, \[&func\](int64\_t start, int64\_t end) { for (int64\_t i = start; i < end; ++i) func(i); }); }

ParallelFor2D(), not included here, takes a [Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) to specify the loop domain and then calls a function that either takes a Bounds2i or one that takes a Point2i, along the lines of the two ParallelFor() variants.

### B.6.6 Asynchronous Jobs

Parallel for loops are useful when the parallel work is easily expressed as a loop of independent iterations; it is just a few lines of changed code to parallelize an existing for loop. The fact that ParallelFor() and ParallelFor2D() ensure that all loop iterations have finished before they return is also helpful since subsequent code can proceed knowing any values set in the loop are available. However, not all work fits that form. Sometimes one thread of execution may produce independent work that could be done concurrently by a different thread. In this case, we would like to be able to provide that work to the thread pool and then continue on in the current thread, harvesting the result of the independent work some time later. pbrt therefore provides a second mechanism for parallel execution in the form of *asynchronous jobs* that execute a given function (often, a lambda function). The following code shows an example of their use.

extern Result func(float x); AsyncJob<Result> \*job = RunAsync(func, 0.5f);... Result r = job->GetResult();

The RunAsync() function takes a function as its first parameter as well as any arguments that the function takes. It returns an AsyncJob to the caller, which can then continue execution. When the AsyncJob ’s GetResult() method is subsequently called, the call will only return after the asynchronous function has executed, be it by another thread in the thread pool or by the calling thread. The value returned by the asynchronous function is then returned to the caller.

The AsyncJob class implements the [ParallelJob](#ParallelJob) interface. It is templated on the return type of the function it manages.

<<AsyncJob Definition>>=

template <typename T> class AsyncJob: public [ParallelJob](#ParallelJob) { public: << [AsyncJob Public Methods](#fragment-AsyncJobPublicMethods-0) >>

AsyncJob(std::function<T(void)> w): func(std::move(w)) {} bool HaveWork() const { return![started](#AsyncJob::started); } void RunStep(std::unique\_lock<std::[mutex](#AsyncJob::mutex) > \*lock) { [threadPool](#ParallelJob::threadPool) -> [RemoveFromJobList](#ThreadPool::RemoveFromJobList) (this); [started](#AsyncJob::started) = true; lock->unlock(); << [Execute asynchronous work and notify waiting threads of its completion](#fragment-Executeasynchronousworkandnotifywaitingthreadsofitscompletion-0) >>

T r = [func](#AsyncJob::func) (); std::unique\_lock<std::[mutex](#AsyncJob::mutex) > ul([mutex](#AsyncJob::mutex)); [result](#AsyncJob::result) = r; [cv](#AsyncJob::cv).notify\_all();

} bool [IsReady](#AsyncJob::IsReady) () const { std::lock\_guard<std::[mutex](#AsyncJob::mutex) > lock([mutex](#AsyncJob::mutex)); return [result](#AsyncJob::result).has\_value(); } T GetResult() { [Wait](#AsyncJob::Wait) (); std::lock\_guard<std::[mutex](#AsyncJob::mutex) > lock([mutex](#AsyncJob::mutex)); return \* [result](#AsyncJob::result); } pstd::optional<T> TryGetResult(std::[mutex](#AsyncJob::mutex) \*extMutex) { { std::lock\_guard<std::[mutex](#AsyncJob::mutex) > lock([mutex](#AsyncJob::mutex)); if ([result](#AsyncJob::result)) return [result](#AsyncJob::result); } extMutex->unlock(); [DoParallelWork](#DoParallelWork) (); extMutex->lock(); return {}; } void [Wait](#AsyncJob::Wait) () { while (![IsReady](#AsyncJob::IsReady) () && [DoParallelWork](#DoParallelWork) ()); std::unique\_lock<std::[mutex](#AsyncJob::mutex) > lock([mutex](#AsyncJob::mutex)); if (![result](#AsyncJob::result).has\_value()) cv.wait(lock, \[this\]() { return [result](#AsyncJob::result).has\_value(); }); } void DoWork() { T r = func(); std::unique\_lock<std::[mutex](#AsyncJob::mutex) > l([mutex](#AsyncJob::mutex)); [result](#AsyncJob::result) = r; cv.notify\_all(); } std::string ToString() const { return StringPrintf("\[ AsyncJob [started](#AsyncJob::started): %s \]", [started](#AsyncJob::started)); }

private: << [AsyncJob Private Members](#fragment-AsyncJobPrivateMembers-0) >>

std::function<T(void)> func; bool started = false; pstd::optional<T> result; mutable std::mutex mutex; std::condition\_variable cv;

};

The constructor, not included here, takes the asynchronous function and stores it in the func member variable. started is used to record whether some thread has begun running the function.

<<AsyncJob Private Members>>=

std::function<T(void)> func; bool started = false;

An AsyncJob represents a single quantum of work; only one thread can help, so once one has started running the function, there is nothing for any other thread to do. Implementation of the HaveWork() method for the [ParallelJob](#ParallelJob) interface follows.

<<AsyncJob Public Methods>>=

bool HaveWork() const { return![started](#AsyncJob::started); }

The RunStep() method starts with some minor bookkeeping before calling the provided function; it is worth removing the AsyncJob from the job list at this point, as there is no reason for other threads to consider it when they iterate through the list.

void RunStep(std::unique\_lock<std::mutex> \*lock) { [threadPool](#ParallelJob::threadPool) -> [RemoveFromJobList](#ThreadPool::RemoveFromJobList) (this); [started](#AsyncJob::started) = true; lock->unlock(); << [Execute asynchronous work and notify waiting threads of its completion](#fragment-Executeasynchronousworkandnotifywaitingthreadsofitscompletion-0) >>

T r = [func](#AsyncJob::func) (); std::unique\_lock<std::[mutex](#AsyncJob::mutex) > ul([mutex](#AsyncJob::mutex)); [result](#AsyncJob::result) = r; [cv](#AsyncJob::cv).notify\_all();

}

The asynchronous function is called without the AsyncJob ’s mutex being held so that its execution does not stall other threads that may want to quickly check whether the function has finished running; the mutex is only acquired when a value is available to store in result. Note also the use of a condition variable after result is set: other threads that are waiting for the result wait on this condition variable, so it is important that they be notified.

<<Execute asynchronous work and notify waiting threads of its completion>>=

T r = [func](#AsyncJob::func) (); std::unique\_lock<std::[mutex](#AsyncJob::mutex) > ul([mutex](#AsyncJob::mutex)); [result](#AsyncJob::result) = r; [cv](#AsyncJob::cv).notify\_all();

Using optional to store the function’s result simplifies keeping track of whether the function has been executed.

<<AsyncJob Private Members>>+=

pstd::optional<T> result; mutable std::mutex mutex; std::condition\_variable cv;

A convenience IsReady() method that indicates whether the function has run and its result is available is easily implemented.

bool IsReady() const { std::lock\_guard<std::[mutex](#AsyncJob::mutex) > lock([mutex](#AsyncJob::mutex)); return [result](#AsyncJob::result).has\_value(); }

The GetResult() method starts by calling Wait(), which only returns once the function’s return value is available. The value of \*result can therefore then be returned with no further checks.

T GetResult() { [Wait](#AsyncJob::Wait) (); std::lock\_guard<std::[mutex](#AsyncJob::mutex) > lock([mutex](#AsyncJob::mutex)); return \* [result](#AsyncJob::result); }

AsyncJob also provides a TryGetResult() method that takes an already-locked std::mutex as a parameter. It then returns the asynchronous function’s return value if it is available, with the lock still held, or unlocks the lock, performs some work via a call to [DoParallelWork()](#DoParallelWork), and then relocks the mutex. (The definition of DoParallelWork() is not included in the text; it takes a single work item from the parallel job queue, performs the associated work, and then returns.) This variant is useful when multiple threads are waiting for the value returned by an asynchronous function, since it allows them to perform other useful work rather than stalling as they wait.

So long as the asynchronous function has not yet finished, Wait() calls DoParallelWork() to help out with work enqueued in the thread pool (including, at some point, the current AsyncJob, if another thread has not yet taken care of it). If the result is not available and there is no work to run, then some other thread must be running the asynchronous job; the current thread then waits for the condition variable to be signaled.

<<AsyncJob Public Methods>>+=

void Wait() { while (![IsReady](#AsyncJob::IsReady) () && [DoParallelWork](#DoParallelWork) ()); std::unique\_lock<std::[mutex](#AsyncJob::mutex) > lock([mutex](#AsyncJob::mutex)); if (![result](#AsyncJob::result).has\_value()) cv.wait(lock, \[this\]() { return [result](#AsyncJob::result).has\_value(); }); }

For the simplicity of the AsyncJob implementation, there is some complexity in RunAsync(), which takes care of creating an [AsyncJob](#AsyncJob) and making it available to the thread pool. That complexity starts with the function using a variadic template to capture the function’s argument values.

<<Asynchronous Task Launch Function Definitions>>=

template <typename F, typename... Args> auto RunAsync(F func, Args &&...args) { << [Create AsyncJob for func and args](#fragment-CreatemonoAsyncJobformonofuncandmonoargs-0) >>

auto fvoid = std::bind(func, std::forward<Args>(args)...); using R = typename std::invoke\_result\_t<F, Args...>; AsyncJob<R> \*job = new AsyncJob<R>(std::move(fvoid));

<< [Enqueue job or run it immediately](#fragment-Enqueuemonojoborrunitimmediately-0) >>

std::unique\_lock<std::mutex> lock; if ([RunningThreads](#RunningThreads) () == 1) job-> [DoWork](#AsyncJob::DoWork) (); else lock = ParallelJob::[threadPool](#ParallelJob::threadPool) -> [AddToJobList](#ThreadPool::AddToJobList) (job);

return job; }

The AsyncJob class assumes that the function to execute does not take any arguments, though RunAsync() allows the provided function to take arguments. Therefore, it starts by using std::bind() to create a new callable object with the arguments bound and no arguments remaining. An alternative design might generalize AsyncJob to allow arguments, though at a cost of added complexity that we think is better left to std::bind. Given the new function fvoid, its return type R can be found, which allows for creating an AsyncJob of the correct type. Dynamic allocation is necessary for the AsyncJob here since it must outlast the call to RunAsync().

<<Create AsyncJob for func and args >>=

auto fvoid = std::bind(func, std::forward<Args>(args)...); using R = typename std::invoke\_result\_t<F, Args...>; AsyncJob<R> \*job = new AsyncJob<R>(std::move(fvoid));

If there is no thread pool (e.g., due to the user specifying that no additional threads should be used), then the work is performed immediately via a call to DoWork() (the implementation of which is not included here), which immediately invokes the function and saves its result in [AsyncJob::result](#AsyncJob::result). Otherwise, it is added to the job list.

<<Enqueue job or run it immediately>>=

std::unique\_lock<std::mutex> lock; if ([RunningThreads](#RunningThreads) () == 1) job-> [DoWork](#AsyncJob::DoWork) (); else lock = ParallelJob::[threadPool](#ParallelJob::threadPool) -> [AddToJobList](#ThreadPool::AddToJobList) (job);

### B.6.7 Thread-Local Variables

It is often useful to have local data associated with each executing thread that it can access without concern of mutual exclusion with other threads. For example, per-thread [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) s and [ScratchBuffer](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#ScratchBuffer) s were used by the [ImageTileIntegrator](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#ImageTileIntegrator) in Section [1.3.4](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#sec:image-tile-integrator). The ThreadLocal template class handles the details of such cases, creating per-thread instances of a managed object type T on demand as threads require them.

<<ThreadLocal Definition>>=

template <typename T> class ThreadLocal { public: << [ThreadLocal Public Methods](#fragment-ThreadLocalPublicMethods-0) >>

[ThreadLocal](#ThreadLocal) (): hashTable(4 \* [RunningThreads](#RunningThreads) ()), create(\[\]() { return T(); }) {} [ThreadLocal](#ThreadLocal) (std::function<T(void)> &&c): hashTable(4 \* [RunningThreads](#RunningThreads) ()), create(c) {} T &Get(); template <typename F> void ForAll(F &&func);

private: <<ThreadLocal Private Members>>

struct Entry { std::thread::id tid; T value; }; std::shared\_mutex mutex; std::vector<pstd::optional<Entry>> hashTable; std::function<T(void)> create;

};

ThreadLocal uses a hash table to manage the objects. It allocates a fixed-size array for the hash table in order to avoid the complexity of resizing the hash table at runtime. For pbrt ’s use, where the number of running threads is fixed, this is a reasonable simplification. If the caller provides a function that returns objects of the type T, then it is used to create them; otherwise, the object’s default constructor is called.

<<ThreadLocal Public Methods>>=

[ThreadLocal](#ThreadLocal) (): hashTable(4 \* [RunningThreads](#RunningThreads) ()), create(\[\]() { return T(); }) {} [ThreadLocal](#ThreadLocal) (std::function<T(void)> &&c): hashTable(4 \* [RunningThreads](#RunningThreads) ()), create(c) {}

The Get() method returns the instance of the object that is associated with the calling thread. It takes care of allocating the object and inserting it into the hash table when needed.

T &Get();

It is useful to be able to iterate over all the per-thread objects managed by ThreadLocal. That capability is provided by the ForAll() method.

<<ThreadLocal Public Methods>>+=

template <typename F> void ForAll(F &&func);