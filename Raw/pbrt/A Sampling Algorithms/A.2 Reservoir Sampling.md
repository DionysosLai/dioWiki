---
title: "Reservoir Sampling"
source: "https://pbr-book.org/4ed/Sampling_Algorithms/Reservoir_Sampling"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## A.2 Reservoir Sampling
> ## A.2 蓄水池采样

To perform the sampling operation, both [SampleDiscrete()](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#SampleDiscrete) and alias tables require the number of outcomes being sampled from as well as all their probabilities to be stored in memory.
> 要执行采样操作，[SampleDiscrete()](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#SampleDiscrete) 和别名表都需要将被采样的结果数量及其所有概率存储在内存中。 Often this is not a problem, but for cases where we would like to draw a sample from a large number of events, or cases where each event requires a large amount of memory, it is useful to be able to generate samples without storing all of them at once.

A family of algorithms based on a technique called *reservoir sampling* makes this possible, by taking a stream of candidate samples one at a time and randomly keeping just one of them in a way that ensures that the sample that is kept is from the distribution defined by the samples that have been seen so far. Reservoir sampling algorithms date to the early days of computer tape drives, where data could only be accessed sequentially and there was often more of it than could be stored in main memory. Reservoir sampling made it possible to draw random samples from data stored on tape while only reading the tape once.

The basic reservoir sampling algorithm is easily expressed. Each candidate sample is stored in the reservoir with probability equal to one over the number of candidates that have been considered:

The correctness of this algorithm can be shown using induction. For the base case, it is clear that if there is a single sample, it will be stored in the reservoir, and the reservoir has successfully drawn a sample with the appropriate probability from the sample distribution.

Now consider the case where samples have been considered and assume that the sample stored in the reservoir has been kept with probability. When a new sample is considered, it will be kept with probability, which is clearly the correct probability for it. The existing sample is kept with probability; the product of the probability of keeping the existing sample and its probability of being stored in the reservoir gives the correct probability,, as well.

*Weighted reservoir sampling* algorithms generalize the basic algorithm by making it possible to associate a nonnegative weight with each sample. Samples are then kept with probability given by the ratio of their weight to the sum of weights of all of the candidate samples that have been seen so far. The WeightedReservoirSampler class implements this algorithm. It is parameterized by the type of object being sampled T.

<<WeightedReservoirSampler Definition>>=

template <typename T> class WeightedReservoirSampler { public: << [WeightedReservoirSampler Public Methods](#fragment-WeightedReservoirSamplerPublicMethods-0) >>

WeightedReservoirSampler() = default; WeightedReservoirSampler(uint64\_t rngSeed): [rng](#WeightedReservoirSampler::rng) (rngSeed) {} void Seed(uint64\_t seed) { [rng](#WeightedReservoirSampler::rng).[SetSequence](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG::SetSequence) (seed); } void [Add](#WeightedReservoirSampler::Add) (const T &sample, Float weight) { [weightSum](#WeightedReservoirSampler::weightSum) += weight; << [Randomly add sample to reservoir](#fragment-Randomlyaddmonosampletoreservoir-0) >>

Float p = weight / [weightSum](#WeightedReservoirSampler::weightSum); if (rng.[Uniform](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG::UniformFloat) <Float>() < p) { [reservoir](#WeightedReservoirSampler::reservoir) = sample; [reservoirWeight](#WeightedReservoirSampler::reservoirWeight) = weight; }

} template <typename F> void [Add](#WeightedReservoirSampler::Add) (F func, Float weight) { <<Process weighted reservoir sample via callback>>

weightSum += weight; Float p = weight / weightSum; if (rng.Uniform<Float>() < p) { reservoir = func(); reservoirWeight = weight; }

} void Copy(const WeightedReservoirSampler &wrs) { [weightSum](#WeightedReservoirSampler::weightSum) = wrs.[weightSum](#WeightedReservoirSampler::weightSum); [reservoir](#WeightedReservoirSampler::reservoir) = wrs.[reservoir](#WeightedReservoirSampler::reservoir); [reservoirWeight](#WeightedReservoirSampler::reservoirWeight) = wrs.[reservoirWeight](#WeightedReservoirSampler::reservoirWeight); } int [HasSample](#WeightedReservoirSampler::HasSample) () const { return [weightSum](#WeightedReservoirSampler::weightSum) > 0; } const T &GetSample() const { return [reservoir](#WeightedReservoirSampler::reservoir); } Float SampleProbability() const { return [reservoirWeight](#WeightedReservoirSampler::reservoirWeight) / [weightSum](#WeightedReservoirSampler::weightSum); } Float WeightSum() const { return [weightSum](#WeightedReservoirSampler::weightSum); } void Reset() { [reservoirWeight](#WeightedReservoirSampler::reservoirWeight) = [weightSum](#WeightedReservoirSampler::weightSum) = 0; } void Merge(const WeightedReservoirSampler &wrs) { if (wrs.[HasSample](#WeightedReservoirSampler::HasSample) ()) [Add](#WeightedReservoirSampler::Add) (wrs.[reservoir](#WeightedReservoirSampler::reservoir), wrs.[weightSum](#WeightedReservoirSampler::weightSum)); } std::string ToString() const { return StringPrintf("\[ WeightedReservoirSampler [rng](#WeightedReservoirSampler::rng): %s " " [weightSum](#WeightedReservoirSampler::weightSum): %f [reservoir](#WeightedReservoirSampler::reservoir): %s [reservoirWeight](#WeightedReservoirSampler::reservoirWeight): %f \]", [rng](#WeightedReservoirSampler::rng), [weightSum](#WeightedReservoirSampler::weightSum), [reservoir](#WeightedReservoirSampler::reservoir), [reservoirWeight](#WeightedReservoirSampler::reservoirWeight)); }

private: << [WeightedReservoirSampler Private Members](#fragment-WeightedReservoirSamplerPrivateMembers-0) >>

[RNG](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG) rng; Float weightSum = 0; Float reservoirWeight = 0; T reservoir;

};

WeightedReservoirSampler stores an [RNG](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG) object that provides the random numbers that are used in deciding whether to add each sample to the reservoir. The constructor correspondingly takes a seed value that is passed on to the [RNG](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG).

<<WeightedReservoirSampler Public Methods>>=

WeightedReservoirSampler() = default; WeightedReservoirSampler(uint64\_t rngSeed): [rng](#WeightedReservoirSampler::rng) (rngSeed) {}

<<WeightedReservoirSampler Private Members>>=

[RNG](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG) rng;

If an array of WeightedReservoirSampler s is allocated, then the default constructor runs instead. In that case, the [RNG](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG) s in individual samplers can be seeded via the Seed() method.

void Seed(uint64\_t seed) { rng.[SetSequence](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG::SetSequence) (seed); }

The Add() method takes a single sample and a nonnegative weight value and updates the reservoir so that the stored sample is from the expected distribution.

void Add(const T &sample, Float weight) { [weightSum](#WeightedReservoirSampler::weightSum) += weight; << [Randomly add sample to reservoir](#fragment-Randomlyaddmonosampletoreservoir-0) >>

Float p = weight / [weightSum](#WeightedReservoirSampler::weightSum); if (rng.[Uniform](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG::UniformFloat) <Float>() < p) { [reservoir](#WeightedReservoirSampler::reservoir) = sample; [reservoirWeight](#WeightedReservoirSampler::reservoirWeight) = weight; }

}

Float weightSum = 0;

The probability p for storing the sample candidate in the reservoir is easily found given weightSum.

<<Randomly add sample to reservoir>>=

Float p = weight / [weightSum](#WeightedReservoirSampler::weightSum); if (rng.[Uniform](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG::UniformFloat) <Float>() < p) { [reservoir](#WeightedReservoirSampler::reservoir) = sample; [reservoirWeight](#WeightedReservoirSampler::reservoirWeight) = weight; }

The weight of the sample stored in the reservoir is stored in reservoirWeight; it is needed to compute the value of the probability mass function (PMF) for the sample that is kept.

<<WeightedReservoirSampler Private Members>>+=

Float reservoirWeight = 0; T reservoir;

A second Add() method takes a callback function that returns a sample. This function is only called when the sample is to be stored in the reservoir. This variant is useful in cases where the sample’s weight can be computed independently of its value and where its value is relatively expensive to compute. The fragment that contains its implementation, <<Process weighted reservoir sample via callback>>, otherwise follows the same structure as the first Add() method, so it is not included here.

template <typename F> void Add(F func, Float weight) { <<Process weighted reservoir sample via callback>>

weightSum += weight; Float p = weight / weightSum; if (rng.Uniform<Float>() < p) { reservoir = func(); reservoirWeight = weight; }

}

A number of methods provide access to the sample and the probability that it was stored in the reservoir.

int HasSample() const { return [weightSum](#WeightedReservoirSampler::weightSum) > 0; } const T &GetSample() const { return [reservoir](#WeightedReservoirSampler::reservoir); } Float SampleProbability() const { return [reservoirWeight](#WeightedReservoirSampler::reservoirWeight) / [weightSum](#WeightedReservoirSampler::weightSum); } Float WeightSum() const { return [weightSum](#WeightedReservoirSampler::weightSum); }

It is sometimes useful to reset a WeightedReservoirSampler and restart from scratch with a new stream of samples; the Reset() method handles this task.

void Reset() { [reservoirWeight](#WeightedReservoirSampler::reservoirWeight) = [weightSum](#WeightedReservoirSampler::weightSum) = 0; }

Remarkably, it is possible to merge two reservoirs into one in such a way that the stored sample is kept with the same probability as if a single reservoir had considered all of the samples seen by the two. Merging two reservoirs is a matter of randomly taking the sample stored by the second reservoir with probability defined by its sum of sample weights divided by the sum of both reservoirs’ sums of sample weights, which in turn is exactly what the Add() method does.

<<WeightedReservoirSampler Public Methods>>+=

void Merge(const WeightedReservoirSampler &wrs) { if (wrs.[HasSample](#WeightedReservoirSampler::HasSample) ()) [Add](#WeightedReservoirSampler::Add) (wrs.[reservoir](#WeightedReservoirSampler::reservoir), wrs.[weightSum](#WeightedReservoirSampler::weightSum)); }

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[采样与重建]]
- [[蒙特卡洛积分]]

**同章节**:
- [[A Sampling Algorithms]]
- [[A.1 The Alias Method]]
- [[A.3 The Rejection Method]]
- [[A.4 Sampling 1D Functions]]
- [[A.5 Sampling Multidimensional Functions]]
