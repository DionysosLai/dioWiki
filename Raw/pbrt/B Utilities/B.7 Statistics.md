---
title: "Statistics"
source: "https://pbr-book.org/4ed/Utilities/Statistics"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## B.7 Statistics
> ## B.7 统计

Collecting data about the runtime behavior of the system can provide a substantial amount of insight into its behavior and opportunities for improving its performance.
> 收集系统运行时行为的数据可以提供大量关于其行为和性能改进机会的洞察。 For example, we might want to track the average number of primitive intersection tests performed for all the rays; if this number is surprisingly high, then there may be a latent bug somewhere in the system. pbrt ’s statistics system makes it possible to measure and aggregate this sort of data in a variety of ways. The statistics system is only available with the CPU renderer; an exercise at the end of this appendix discusses how it might be brought to the GPU.

It is important to make it as easy as possible to add new measurements to track the system’s runtime behavior; the easier it is to do this, the more measurements end up being added to the system, and the more likely that “interesting” data will be discovered, leading to new insights and improvements. Therefore, it is fairly easy to add new measurements to pbrt. For example, the following lines declare two counters that can be used to record how many times the corresponding events happen.

STAT\_COUNTER("Integrator/Regular ray intersection tests", nIsectTests); STAT\_COUNTER("Integrator/Shadow ray intersection tests", nShadowTests);

As appropriate, counters can be incremented with simple statements like

++nIsectTests;

With no further intervention from the developer, the preceding is enough for the statistics system to be able to automatically print out nicely formatted results like the following when rendering completes:

Integrator Regular ray intersection tests 752982 Shadow ray intersection tests 4237165

The statistics system supports the following aggregate measurements:

- STAT\_COUNTER("name", var): A count of the number of instances of an event. The counter variable var can be updated as if it was a regular integer variable; for example, ++var and var += 10 are both valid.
- STAT\_MEMORY\_COUNTER("name", var): A specialized counter for recording memory usage. In particular, the values reported at the end of rendering are in terms of kilobytes, megabytes, or gigabytes, as appropriate. The counter is updated the same way as a regular counter: var += count \* sizeof(MyStruct) and so forth.
- STAT\_INT\_DISTRIBUTION("name", dist): Tracks the distribution of some value; at the end of rendering, the minimum, maximum, and average of the supplied values are reported. Call dist << value to include value in the distribution.
- STAT\_PERCENT("name", num, denom): Tracks how often a given event happens; the aggregate value is reported as the percentage num/denom when statistics are printed. Both num and denom can be incremented as if they were integers—for example, one might write if (event) ++num; or ++denom.
- STAT\_RATIO("name", num, denom): This tracks how often an event happens but reports the result as a ratio num/denom rather than a percentage. This is often a more useful presentation if num is often greater than denom. (For example, we might record the percentage of ray–triangle intersection tests that resulted in an intersection but the ratio of triangle intersection tests to the total number of rays traced.)

[![](https://pbr-book.org/4ed/Utilities/phabbf08.svg)](https://pbr-book.org/4ed/Utilities/phabbf08.svg)

Figure B.8: Visualization of Average Path Length at Each Pixel. Each pixel’s value is based on the number of rays traced to compute the pixel’s shaded value. Not only it is evident that longer paths are traced at pixels with specular surfaces like the glasses on the tables, but it is also possible to see the effect of Russian roulette terminating paths more quickly at darker surfaces. This image was generated using STAT\_PIXEL\_COUNTER and only required adding two lines of code to an integrator. *(Scene courtesy of Guillermo M. Leal Llaguno.)*

In addition to statistics that are aggregated over the entire rendering, pbrt can also measure statistics at each pixel and generate images with their values. Two variants are supported: STAT\_PIXEL\_COUNTER and STAT\_PIXEL\_RATIO, which are used in the same way as the corresponding aggregate statistics. Per-pixel statistics are only measured if the –pixelstats command line option is provided to pbrt. Figure [B.8](#fig:stat-pixel-counter) shows an image generated using STAT\_PIXEL\_COUNTER.

All the macros to define statistics trackers can only be used at file scope and should only be used in \*.cpp files (for reasons that will become apparent as we dig into their implementations). They specifically should not be used in header files or function or class definitions.

Note also that the string names provided for each measurement should be of the form “category/statistic.” When values are reported, everything under the same category is reported together (as in the preceding example).

### B.7.1 Implementation

There are a number of challenges in making the statistics system both efficient and easy to use. The efficiency challenges stem from pbrt being multi-threaded: if there was not any parallelism, we could associate regular integer or floating-point variables with each measurement and just update them like regular variables. In the presence of multiple concurrent threads of execution, however, we need to ensure that two threads do not try to modify these variables at the same time (recall the discussion of mutual exclusion in Section [B.6.1](https://pbr-book.org/4ed/Utilities/Parallelism.html#sec:atomic-ops)).

While atomic operations like those described in Section [B.6.1](https://pbr-book.org/4ed/Utilities/Parallelism.html#sec:atomic-ops) could be used to safely increment counters without using a mutex, there would still be a performance impact from multiple threads modifying the same location in memory. Recall from Section [B.6.3](https://pbr-book.org/4ed/Utilities/Parallelism.html#sec:multi-thread-memory-perf) that the cache coherence protocols can introduce substantial overhead in this case. Because the statistics measurements are updated so frequently during the course of rendering, we found that an atomics-based implementation caused the renderer to be 10–15% slower than the following implementation, which avoids the overhead of multiple threads frequently modifying the same memory location.

The implementation here is based on having separate counters for each running thread, allowing the counters to be updated without atomics and without cache coherence overhead (since each thread increments its own counters). This approach means that in order to report statistics, it is necessary to merge all of these per-thread counters into final aggregate values, which we will see is possible with a bit of trickiness.

To see how this all works, we will dig into the implementation for regular counters; the other types of measurements are all along similar lines. First, here is the STAT\_COUNTER macro, which packs three different things into its definition.

<<Statistics Macros>>=

#define STAT\_COUNTER(title, var) \\ static thread\_local int64\_t var; \\ static StatRegisterer STATS\_REG##var(\[\](StatsAccumulator &accum) { \\ accum.ReportCounter(title, var); \\ var = 0; \\ });

First, and most obviously, the macro defines a 64-bit integer variable named var, the second argument passed to the macro. The variable definition has the thread\_local qualifier, which indicates that there should be a separate copy of the variable for each executing thread. This variable can then be incremented directly as appropriate to report results. However, given these per-thread instances, we need to be able to sum together the per-thread values and to aggregate all the individual counters into the final program output.

To this end, the macro next defines a static variable of type StatRegisterer, giving it a (we hope!) unique name derived from var. A lambda function is passed to the StatRegisterer constructor, which stores a copy of it. When called, the lambda passes the current thread’s counter value to a ReportCounter() method and then resets the counter. Evidently, all that is required is for this lambda to be called by each thread and for ReportCounter() to sum up the values provided and then report them. (We will gloss over the implementation of the StatsAccumulator class and methods like ReportCounter(), as there is nothing very interesting about them.)

Recall that in C++, constructors of global static objects run when program execution starts; thus, each static instance of the StatRegisterer class runs its constructor before main() starts running. This constructor, which is not included here, adds the lambda passed to it to a std::vector that holds all such lambdas for all the statistics.

At the end of rendering, the [ForEachThread()](https://pbr-book.org/4ed/Utilities/Parallelism.html#ForEachThread) function is used to cause each thread to loop over the registered lambdas and call each of them. In turn, the StatsAccumulator will have all the aggregate values when they are done. The PrintStats() function can then be called to print all the statistics that have been accumulated in StatsAccumulator.

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**同章节**:
- [[B Utilities]]
- [[B.1 System Startup, Cleanup, and Options]]
- [[B.2 Mathematical Infrastructure]]
- [[B.3 User Interaction]]
- [[B.4 Containers and Memory Management]]
- [[B.5 Images]]
- [[B.6 Parallelism]]
