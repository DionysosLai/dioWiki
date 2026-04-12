---
title: "The Alias Method"
source: "https://pbr-book.org/4ed/Sampling_Algorithms/The_Alias_Method"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## A.1 The Alias Method
> ## A.1 别名方法

If many samples need to be generated from a discrete distribution, using the approach implemented in the [SampleDiscrete()](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#SampleDiscrete) function would be wasteful: each generated sample would require computation.
> 如果需要从离散分布生成大量样本，使用 [SampleDiscrete()](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#SampleDiscrete) 函数中实现的方法会很浪费：每个生成的样本都需要计算。 That approach could be improved to time by computing a cumulative distribution function (CDF) table once and then using binary search to generate each sample, but there is another option that is even more efficient, requiring just time for each sample; that approach is the *alias method*.

To understand how the alias method works, first consider the task of sampling from discrete outcomes, each with equal probability. In that case, computing the value gives a uniformly distributed index between 0 and and the corresponding outcome can be selected—no further work is necessary. The alias method allows a similar searchless sampling method if the outcomes have arbitrary probabilities.

The alias method is based on creating bins, one for each outcome. Bins are sampled uniformly and then two values stored in each bin are used to generate the final sample: if the th bin was sampled, then gives the probability of sampling the th outcome, and otherwise the *alias* is chosen; it is the index of a single alternative outcome. Though we will not include the proof here, it can be shown that this representation—the th bin associated with the th outcome and no more than a single alias per bin—is sufficient to represent arbitrary discrete probability distributions.

With the alias method, if the probabilities are all the same, then each bin’s probability is one, and it reduces to the earlier example with uniform probabilities. Otherwise, for outcomes where the associated probability is greater than the average probability, the outcome will be stored as the alias in one or more of the other bins. For outcomes where the associated is less than the average probability, will be less than one and the alias will point to one of the higher-probability outcomes.

For a specific example, consider the probabilities. A corresponding alias table is shown in Table [A.1](#table:alias-table). It is possible to see that, for example, the first sample is chosen with probability: there is a probability of choosing the first table entry, in which case the first sample is always chosen. Otherwise, there is a probability of choosing the second and third table entries, and for each, there is a chance of choosing the alias, giving in sum an additional probability of choosing the first sample. The other probabilities can be verified similarly.

Table A.1: A Simple Alias Table. This alias table makes it possible to generate samples from the distribution of discrete probabilities. To generate a sample, an entry is first chosen with uniform probability. Given an entry, its corresponding sample is chosen with probability and the sample corresponding to its alias index is chosen with probability.

| Index |  | Alias Index |
| --- | --- | --- |
| 1 |  | n/a |
| 2 |  | 1 |
| 3 |  | 1 |
| 4 |  | 2 |

One way to interpret an alias table is that each bin represents of the total probability mass function. If outcomes are first allocated to their corresponding bins, then the probability mass of outcomes that are greater than must be distributed to other bins that have associated probabilities less than. This idea is illustrated in Figure [A.1](#fig:alias-table), which corresponds to the example of Table [A.1](#table:alias-table).

[![](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf01.svg)](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf01.svg)

Figure A.1: Graphical Representation of the Alias Table in Table [A.1](#table:alias-table). One bin is allocated for each outcome and is filled by the outcome’s probability, up to. Excess probability is allocated to other bins that have probabilities less than and thus extra space.

The AliasTable class implements algorithms for generating and sampling from alias tables. As with the other sampling code, its implementation is found in [util/sampling.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/sampling.h) and [util/sampling.cpp](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/sampling.cpp).

<<AliasTable Definition>>=

class AliasTable { public: << [AliasTable Public Methods](#fragment-AliasTablePublicMethods-0) >>

AliasTable() = default; AliasTable(Allocator alloc = {}): [bins](#AliasTable::bins) (alloc) {} AliasTable(pstd::span<const Float> weights, Allocator alloc = {}); PBRT\_CPU\_GPU int Sample(Float u, Float \*pmf = nullptr, Float \*uRemapped = nullptr) const; std::string ToString() const; size\_t size() const { return [bins](#AliasTable::bins).size(); } Float PMF(int index) const { return [bins](#AliasTable::bins) \[index\].[p](#AliasTable::Bin::p); }

private: << [AliasTable Private Members](#fragment-AliasTablePrivateMembers-0) >>

struct Bin { Float q, p; int alias; }; pstd::vector<Bin> bins;

};

Its constructor takes an array of weights, not necessarily normalized, that give the relative probabilities for the possible outcomes.

<<AliasTable Method Definitions>>=

AliasTable::AliasTable(pstd::span<const Float> weights, Allocator alloc): [bins](#AliasTable::bins) (weights.size(), alloc) { << [Normalize weights to compute alias table PDF](#fragment-NormalizemonoweightstocomputealiastablePDF-0) >>

Float sum = std::accumulate(weights.begin(), weights.end(), 0.); for (size\_t i = 0; i < weights.size(); ++i) [bins](#AliasTable::bins) \[i\].[p](#AliasTable::Bin::p) = weights\[i\] / sum;

<< [Create alias table work lists](#fragment-Createaliastableworklists-0) >>

struct Outcome { Float pHat; size\_t index; }; std::vector<Outcome> under, over; for (size\_t i = 0; i < [bins](#AliasTable::bins).size(); ++i) { << [Add outcome i to an alias table work list](#fragment-Addoutcomemonoitoanaliastableworklist-0) >>

Float pHat = [bins](#AliasTable::bins) \[i\].[p](#AliasTable::Bin::p) \* [bins](#AliasTable::bins).size(); if (pHat < 1) under.push\_back(Outcome{pHat, i}); else over.push\_back(Outcome{pHat, i});

}

<< [Process under and over work item together](#fragment-Processunderandoverworkitemtogether-0) >>

while (!under.empty() &&!over.empty()) { << [Remove items un and ov from the alias table work lists](#fragment-Removeitemsmonounandmonoovfromthealiastableworklists-0) >>

Outcome un = under.back(), ov = over.back(); under.pop\_back(); over.pop\_back();

<< [Initialize probability and alias for un](#fragment-Initializeprobabilityandaliasformonoun-0) >> << [Push excess probability on to work list](#fragment-Pushexcessprobabilityontoworklist-0) >>

Float pExcess = un.pHat + ov.pHat - 1; if (pExcess < 1) under.push\_back(Outcome{pExcess, ov.index}); else over.push\_back(Outcome{pExcess, ov.index});

}

<<Handle remaining alias table work items>>

while (!over.empty()) { Outcome ov = over.back(); over.pop\_back(); bins\[ov.index\].q = 1; bins\[ov.index\].alias = -1; } while (!under.empty()) { Outcome un = under.back(); under.pop\_back(); bins\[un.index\].q = 1; bins\[un.index\].alias = -1; }

}

The Bin structure represents an alias table bin. It stores the probability, the corresponding outcome’s probability, and an alias.

<<AliasTable Private Members>>=

struct Bin { Float q, p; int alias; }; pstd::vector<Bin> bins;

We have found that with large numbers of outcomes, especially when the magnitudes of their weights vary significantly, it is important to use double precision to compute their sum so that the alias table initialization algorithm works correctly. Therefore, here std::accumulate takes the double-precision value 0. as its initial value, which in turn causes all its computation to be in double precision. Given the sum of weights, the normalized probabilities can be computed.

<<Normalize weights to compute alias table PDF>>=

Float sum = std::accumulate(weights.begin(), weights.end(), 0.); for (size\_t i = 0; i < weights.size(); ++i) [bins](#AliasTable::bins) \[i\].[p](#AliasTable::Bin::p) = weights\[i\] / sum;

The first stage of the alias table initialization algorithm is to split the outcomes into those that have probability less than the average and those that have probability higher than the average. Two std::vector s of the Outcome structure are used for this.

<<Create alias table work lists>>=

struct Outcome { Float pHat; size\_t index; }; std::vector<Outcome> under, over; for (size\_t i = 0; i < [bins](#AliasTable::bins).size(); ++i) { << [Add outcome i to an alias table work list](#fragment-Addoutcomemonoitoanaliastableworklist-0) >>

Float pHat = [bins](#AliasTable::bins) \[i\].[p](#AliasTable::Bin::p) \* [bins](#AliasTable::bins).size(); if (pHat < 1) under.push\_back(Outcome{pHat, i}); else over.push\_back(Outcome{pHat, i});

}

Here and in the remainder of the initialization phase, we will scale the individual probabilities by the number of bins, working in terms of. Thus, the average value is 1, which will be convenient in the following.

<<Add outcome i to an alias table work list>>=

Float pHat = [bins](#AliasTable::bins) \[i\].[p](#AliasTable::Bin::p) \* [bins](#AliasTable::bins).size(); if (pHat < 1) under.push\_back(Outcome{pHat, i}); else over.push\_back(Outcome{pHat, i});

To initialize the alias table, one outcome is taken from under and one is taken from over. Together, they make it possible to initialize the element of bins that corresponds to the outcome from under. After that bin has been initialized, the outcome from over will still have some excess probability that is not yet reflected in bins. It is added to the appropriate work list and the loop executes again until under and over are empty. This algorithm runs in time.

It is not immediately obvious that this approach will successfully initialize the alias table, or that it will necessarily terminate. We will not rigorously show that here, but informally, we can see that at the start, there must be at least one item in each work list unless they all have the same probability (in which case, initialization is trivial). Then, each time through the loop, we initialize one bin, which consumes worth of probability mass. With one less bin to initialize and that much less probability to distribute, we have the same average probability over the remaining bins. That brings us to the same setting as the starting condition: some of the remaining items in the list must be above the average and some must be below, unless they are all equal to it.

<<Process under and over work item together>>=

while (!under.empty() &&!over.empty()) { << [Remove items un and ov from the alias table work lists](#fragment-Removeitemsmonounandmonoovfromthealiastableworklists-0) >>

Outcome un = under.back(), ov = over.back(); under.pop\_back(); over.pop\_back();

<< [Initialize probability and alias for un](#fragment-Initializeprobabilityandaliasformonoun-0) >> << [Push excess probability on to work list](#fragment-Pushexcessprobabilityontoworklist-0) >>

Float pExcess = un.pHat + ov.pHat - 1; if (pExcess < 1) under.push\_back(Outcome{pExcess, ov.index}); else over.push\_back(Outcome{pExcess, ov.index});

}

<<Remove items un and ov from the alias table work lists>>=

Outcome un = under.back(), ov = over.back(); under.pop\_back(); over.pop\_back();

The probability of un must be less than one. We can initialize its bin’s q with, as that is equal to the probability it should be sampled if its bin is chosen. In order to allocate the remainder of the bin’s probability mass, the alias is set to ov. Because, it certainly has enough probability to fill the remainder of the bin—we just need of it.

<<Initialize probability and alias for un >>=

In initializing bins\[un.index\], we have consumed worth of the scaled probability mass. The remainder, un.pHat + ov.pHat - 1, is the as-yet unallocated probability for ov.index; it is added to the appropriate work list based on how much is left.

<<Push excess probability on to work list>>=

Float pExcess = un.pHat + ov.pHat - 1; if (pExcess < 1) under.push\_back(Outcome{pExcess, ov.index}); else over.push\_back(Outcome{pExcess, ov.index});

Due to floating-point round-off error, there may be work items remaining on either of the two work lists with the other one empty. These items have probabilities slightly less than or slightly greater than one and should be given probability in the alias table. The fragment that handles this, <<Handle remaining alias table work items>>, is not included in the book.

Given an initialized alias table, sampling is easy. As described before, an entry is chosen with uniform probability and then either the corresponding sample or its alias is returned. As with the [SampleDiscrete()](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#SampleDiscrete) function, a new uniform random sample derived from the original one is optionally returned.

<<AliasTable Method Definitions>>+=

int AliasTable::Sample(Float u, Float \*pmf, Float \*uRemapped) const { << [Compute alias table offset and remapped random sample up](#fragment-Computealiastablemonooffsetandremappedrandomsamplemonoup-0) >>

int offset = std::min<int>(u \* [bins](#AliasTable::bins).size(), [bins](#AliasTable::bins).size() - 1); Float up = std::min<Float>(u \* [bins](#AliasTable::bins).size() - offset, [OneMinusEpsilon](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#OneMinusEpsilon));

if (up < [bins](#AliasTable::bins) \[offset\].[q](#AliasTable::Bin::q)) { << [Return sample for alias table at offset](#fragment-Returnsampleforaliastableatmonooffset-0) >>

if (pmf) \*pmf = [bins](#AliasTable::bins) \[offset\].[p](#AliasTable::Bin::p); if (uRemapped) \*uRemapped = std::min<Float>(up / [bins](#AliasTable::bins) \[offset\].[q](#AliasTable::Bin::q), [OneMinusEpsilon](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#OneMinusEpsilon)); return offset;

} else { << [Return sample for alias table at alias\[offset\]](#fragment-Returnsampleforaliastableatmonoaliasoffset-0) >> } }

The index for the chosen entry is found by multiplying the random sample by the number of entries. Because u was only used for the discrete sampling decision of selecting an initial entry, it is possible to derive a new uniform random sample from it. That computation is done here to get an independent uniform sample up that is used to decide whether to sample the alias at the current entry.

<<Compute alias table offset and remapped random sample up >>=

int offset = std::min<int>(u \* [bins](#AliasTable::bins).size(), [bins](#AliasTable::bins).size() - 1); Float up = std::min<Float>(u \* [bins](#AliasTable::bins).size() - offset, [OneMinusEpsilon](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#OneMinusEpsilon));

If the initial entry is selected, the various return values are easily computed.

<<Return sample for alias table at offset >>=

if (pmf) \*pmf = [bins](#AliasTable::bins) \[offset\].[p](#AliasTable::Bin::p); if (uRemapped) \*uRemapped = std::min<Float>(up / [bins](#AliasTable::bins) \[offset\].[q](#AliasTable::Bin::q), [OneMinusEpsilon](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#OneMinusEpsilon)); return offset;

Otherwise the appropriate values for the alias are returned.

<<Return sample for alias table at alias\[offset\] >>=

Beyond sampling, it is useful to be able to query the size of the table and the probability of a given outcome. These two operations are easily provided.

<<AliasTable Public Methods>>=

size\_t size() const { return [bins](#AliasTable::bins).size(); } Float PMF(int index) const { return [bins](#AliasTable::bins) \[index\].[p](#AliasTable::Bin::p); }

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[采样与重建]]
- [[蒙特卡洛积分]]

**同章节**:
- [[A Sampling Algorithms]]
- [[A.2 Reservoir Sampling]]
- [[A.3 The Rejection Method]]
- [[A.4 Sampling 1D Functions]]
- [[A.5 Sampling Multidimensional Functions]]
