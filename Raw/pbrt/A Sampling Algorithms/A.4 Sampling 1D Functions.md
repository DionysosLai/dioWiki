---
title: "Sampling 1D Functions"
source: "https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## A.4 Sampling 1D Functions
> ## A.4 一维函数采样

Throughout the implementation of pbrt we have found it useful to draw samples from a wide variety of functions.
> 在 pbrt 的整个实现过程中，我们发现从各种各样的函数中抽取样本非常有用。 This section therefore presents the implementations of additional functions for sampling in 1D to augment the ones in Section [2.3.2](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#sec:inversion-method-continuous). All are based on the inversion method and most introduce useful tricks for sampling that are helpful to know when deriving new sampling algorithms.

### A.4.1 Sampling the Tent Function

SampleTent() uses [SampleLinear()](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#SampleLinear) to sample the “tent” function with radius,

The sampling algorithm first uses the provided uniform sample u with the [SampleDiscrete()](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#SampleDiscrete) function to choose whether to sample a value greater than or less than zero, with each possibility having equal probability. Note the use of SampleDiscrete() ’s capability of returning a new uniform random sample here, overwriting u ’s original value. In turn, one of the two linear functions is sampled, with the result scaled so that the interval is sampled.

One thing to note in this function is that the cases and expressions have been carefully crafted so that u==0 maps to \-r and then as u increases, the sampled value increases monotonically until u==1 maps to r, without any jumps or reversals. This property is helpful for preserving well-distributed sample points (e.g., if they have low discrepancy).

Float SampleTent(Float u, Float r) { if ([SampleDiscrete](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#SampleDiscrete) ({0.5f, 0.5f}, u, nullptr, &u) == 0) return -r + r \* [SampleLinear](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#SampleLinear) (u, 0, 1); else return r \* [SampleLinear](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#SampleLinear) (u, 1, 0); }

The tent function is easily normalized to find its PDF.

Float TentPDF(Float x, Float r) { if (std::abs(x) >= r) return 0; return 1 / r - std::abs(x) / [Sqr](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (r); }

The inversion function is based on [InvertLinearSample()](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#InvertLinearSample).

inline Float InvertTentSample(Float x, Float r) { if (x <= 0) return (1 - [InvertLinearSample](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#InvertLinearSample) (-x / r, 1, 0)) / 2; else return 0.5f + [InvertLinearSample](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#InvertLinearSample) (x / r, 1, 0) / 2; }

### A.4.2 Sampling Exponential Distributions

Sampling the transmittance function when rendering images with participating media often requires samples from a distribution. As before, the first step is to find a constant that normalizes this distribution so that it integrates to one. In this case, we will assume for now that the range of values we’d like the generated samples to cover is rather than, so

Thus, and our PDF is.

Float ExponentialPDF(Float x, Float a) { return a \* std::exp(-a \* x); }

We can integrate to find:

([A.17](#eq:exponential-cdf))

which gives a function that is easy to invert:

Therefore, we can draw samples using

([A.17](#eq:exponential-sampling-distance))

Float SampleExponential(Float u, Float a) { return -std::log(1 - u) / a; }

It may be tempting to simplify the log term from to, under the theory that because, these are effectively the same and a subtraction can thus be saved. The problem with this idea is that may have the value 0 but never has the value 1. With the simplification, it is possible that we would try to take the logarithm of 0, which is undefined; this danger is avoided with the first formulation. While a value of 0 may seem very unlikely, it is possible, especially in the world of floating-point arithmetic and not the real numbers. Sample generation algorithms based on the radical inverse function (Section [8.6.1](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Halton_Sampler.html#sec:hammersley-halton)) are particularly prone to generating the value 0.

As before, the inverse sampling function is given by evaluating.

Float InvertExponentialSample(Float x, Float a) { return 1 - std::exp(-a \* x); }

### A.4.3 Sampling the Gaussian

The Gaussian function is parameterized by its center and standard deviation:

([A.17](#eq:gaussian-function))

The probability distribution it defines is called the *normal distribution*. The Gaussian is already normalized, so the PDF follows directly.

Float NormalPDF(Float x, Float mu = 0, Float sigma = 1) { return [Gaussian](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Gaussian) (x, mu, sigma); }

However, the Gaussian’s CDF cannot be expressed with elementary functions. It is

where is the error function. If we equate and solve, we find that:

The inverse error function can be well approximated with a polynomial, which in turn gives a sampling technique.

Float SampleNormal(Float u, Float mu = 0, Float sigma = 1) { return mu + [Sqrt2](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqrt2) \* sigma \* [ErfInv](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#ErfInv) (2 \* u - 1); }

InvertNormalSample(), not included here, evaluates.

The *Box-Muller transform* is an alternative sampling technique for the normal distribution; it takes a pair of random samples and returns a pair of normally distributed samples. It makes use of the fact that if two normally distributed variables are considered as a 2D point and transformed to 2D polar coordinates, then and.

[Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) SampleTwoNormal([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) u, Float mu = 0, Float sigma = 1) { Float r2 = -2 \* std::log(1 - u\[0\]); return {mu + sigma \* std::sqrt(r2 \* std::cos(2 \* [Pi](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Pi) \* u\[1\])), mu + sigma \* std::sqrt(r2 \* std::sin(2 \* [Pi](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Pi) \* u\[1\]))}; }

### A.4.4 Sampling the Logistic Function

The *logistic function* is shaped similarly to the Gaussian, but can be sampled directly. It is therefore useful in cases where a distribution similar to the Gaussian is useful but an exact Gaussian is not needed. (It is used, for example, in the implementation of pbrt ’s scattering model for hair.) The logistic function centered at the origin is

([A.17](#eq:logistic-function))

where is a parameter that controls its rate of falloff similar to in the Gaussian. Figure [A.4](#fig:plot-logistic-gaussian) shows a plot of the logistic and Gaussian functions with parameter values that lead to curves with similar shapes.

[![](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf04.svg)](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf04.svg)

Figure A.4: Plots of the logistic function, Equation ([A.17](#eq:logistic-function)) with, and the Gaussian, Equation ([A.17](#eq:gaussian-function)), with. ( was found via a least-squares fit to minimize error over the domain of the plot.)

The logistic function is normalized by design, so the PDF evaluation function follows directly.

Float LogisticPDF(Float x, Float s) { x = std::abs(x); return std::exp(-x / s) / (s \* [Sqr](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (1 + std::exp(-x / s))); }

Its CDF,

is easily found, and can be inverted to derive a sampling routine. The result is implemented in [SampleLogistic()](#SampleLogistic).

Float SampleLogistic(Float u, Float s) { return -s \* std::log(1 / u - 1); }

As usual in 1D, the sample inversion method is performed by evaluating the CDF.

Float InvertLogisticSample(Float x, Float s) { return 1 / (1 + std::exp(-x / s)); }

### A.4.5 Sampling a Function over an Interval

It is sometimes useful to sample from a function’s distribution over a specified interval. It turns out that this is easy to do if we are able to evaluate the function’s CDF. We will use the logistic function as an example here, though the underlying technique applies more generally.

First consider the task of finding the PDF of the function limited to the interval,: we need to renormalize it. Doing so requires being able to integrate, which is otherwise known as finding its CDF:

([A.17](#eq:function-interval-pdf))

The function to evaluate the PDF follows directly. Here we have wrapped a call to InvertLogisticSample() in a simple lambda expression in order to make the relationship to Equation ([A.17](#eq:function-interval-pdf)) more clear.

Float TrimmedLogisticPDF(Float x, Float s, Float a, Float b) { if (x < a || x > b) return 0; auto P = \[&\](Float x) { return [InvertLogisticSample](#InvertLogisticSample) (x, s); }; return [Logistic](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Logistic) (x, s) / (P(b) - P(a)); }

Next, consider sampling using the inversion method. Following the definition of, we can see that the CDF associated with is

([A.17](#eq:trimmed-logistic-cdf))

Setting and solving for, we have

Thus, if we compute a new value (that, in a slight abuse of notation, is not between 0 and 1) by using to linearly interpolate between and and then apply the original sampling algorithm, we will generate a sample from the distribution over the interval.

Float SampleTrimmedLogistic(Float u, Float s, Float a, Float b) { auto P = \[&\](Float x) { return [InvertLogisticSample](#InvertLogisticSample) (x, s); }; u = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (u, P(a), P(b)); Float x = [SampleLogistic](#SampleLogistic) (u, s); return [Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp) (x, a, b); }

The inversion routine follows directly from Equation ([A.17](#eq:trimmed-logistic-cdf)).

Float InvertTrimmedLogisticSample(Float x, Float s, Float a, Float b) { auto P = \[&\](Float x) { return [InvertLogisticSample](#InvertLogisticSample) (x, s); }; return (P(x) - P(a)) / (P(b) - P(a)); }

### A.4.6 Sampling Non-Invertible CDFs

It was not possible to invert the normal distribution’s CDF to derive a sampling technique, so there we used a polynomial approximation of the inverse CDF. In cases like that, another option is to use numerical root–finding techniques. We will demonstrate that approach using the *smoothstep* function as an example.

Smoothstep defines an s-shaped curve based on a third-degree polynomial that goes from zero to one starting at a point and ending at a point. It is zero for values and one for values. Otherwise, it is defined as

with. In pbrt the smoothstep function is used to define the falloff at the edges of a spotlight.

We will consider the task of sampling the function within the range. First, it is easy to show that the PDF is

Float SmoothStepPDF(Float x, Float a, Float b) { if (x < a || x > b) return 0; return (2 / (b - a)) \* [SmoothStep](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SmoothStep) (x, a, b); }

Integrating the PDF is also easy; the resulting CDF is

The challenge in sampling is evident: doing so requires solving a fourth-degree polynomial.

The sampling task can be expressed as a zero-finding problem: to apply the inversion method, we would like to solve for. Doing so is equivalent to finding the value such that. The [SampleSmoothStep()](#SampleSmoothStep) function below uses a Newton-bisection solver that is defined in Section [B.2.10](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#sec:math-finding-zeros) to do this. That function takes a callback that returns the value of the function and its derivative at a given point; these values are easily computed given the equations derived so far.

Float SampleSmoothStep(Float u, Float a, Float b) { auto cdfMinusU = \[=\](Float x) -> std::pair<Float, Float> { Float t = (x - a) / (b - a); Float P = 2 \* [Pow](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Pow) <3>(t) - [Pow](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Pow) <4>(t); Float PDeriv = [SmoothStepPDF](#SmoothStepPDF) (x, a, b); return {P - u, PDeriv}; }; return [NewtonBisection](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#NewtonBisection) (a, b, cdfMinusU); }

Sample inversion can be performed following the same approach as was used earlier in Equation ([A.17](#eq:trimmed-logistic-cdf)) for the logistic over an interval.

Float InvertSmoothStepSample(Float x, Float a, Float b) { Float t = (x - a) / (b - a); auto P = \[&\](Float x) { return 2 \* Pow<3>(t) - Pow<4>(t); }; return (P(x) - P(a)) / (P(b) - P(a)); }

### A.4.7 Sampling Piecewise-Constant 1D Functions

The inversion method can also be applied to tabularized functions; in this section, we will consider piecewise-constant functions defined over. The algorithms described here will provide the foundation for sampling piecewise-constant 2D functions, used in multiple parts of pbrt to sample from distributions defined by images.

Assume that the 1D function’s domain is split into equal-sized pieces of size. These regions start and end at points, where ranges from 0 to, inclusive. Within each region, the value of the function is a constant (Figure [A.5](#fig:piecewise-constant) (a)).

[![](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf05.svg)](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf05.svg)

Figure A.5: (a) Probability density function for a piecewise-constant 1D function and (b) cumulative distribution function defined by this PDF.

The value of is then

The function need not always be positive, though its PDF must be. Therefore, the absolute value of the function is taken to define its PDF. The integral is

([A.17](#eq:piecewise-step-integral))

and so it is easy to construct the PDF for as. By direct application of the relevant formulae, the CDF is a piecewise-linear function defined at points by

Between two points and, the CDF is linearly increasing with slope.

Recall that in order to sample we need to invert the CDF to find the value such that

Because the CDF is monotonically increasing, the value of must be between the and such that. Given an array of CDF values, this pair of values can be efficiently found with a binary search.

The PiecewiseConstant1D class brings these ideas together to provide methods for efficient sampling and PDF evaluation of this class of functions.

<<PiecewiseConstant1D Definition>>=

class PiecewiseConstant1D { public: << [PiecewiseConstant1D Public Methods](#fragment-PiecewiseConstant1DPublicMethods-0) >>

PBRT\_CPU\_GPU size\_t BytesUsed() const { return ([func](#PiecewiseConstant1D::func).capacity() + [cdf](#PiecewiseConstant1D::cdf).capacity()) \* sizeof(Float); } static void TestCompareDistributions(const PiecewiseConstant1D &da, const PiecewiseConstant1D &db, Float eps = 1e-5); std::string ToString() const { return StringPrintf("\[ PiecewiseConstant1D [func](#PiecewiseConstant1D::func): %s [cdf](#PiecewiseConstant1D::cdf): %s " " [min](#PiecewiseConstant1D::min): %f [max](#PiecewiseConstant1D::max): %f funcInt: %f \]", [func](#PiecewiseConstant1D::func), [cdf](#PiecewiseConstant1D::cdf), [min](#PiecewiseConstant1D::min), [max](#PiecewiseConstant1D::max), funcInt); } PiecewiseConstant1D() = default; PiecewiseConstant1D(Allocator alloc): [func](#PiecewiseConstant1D::func) (alloc), [cdf](#PiecewiseConstant1D::cdf) (alloc) {} PiecewiseConstant1D(pstd::span<const Float> f, Allocator alloc = {}): PiecewiseConstant1D(f, 0., 1., alloc) {} PiecewiseConstant1D(pstd::span<const Float> f, Float [min](#PiecewiseConstant1D::min), Float [max](#PiecewiseConstant1D::max), Allocator alloc = {}): [func](#PiecewiseConstant1D::func) (f.begin(), f.end(), alloc), [cdf](#PiecewiseConstant1D::cdf) (f.size() + 1, alloc), [min](#PiecewiseConstant1D::min) ([min](#PiecewiseConstant1D::min)), [max](#PiecewiseConstant1D::max) ([max](#PiecewiseConstant1D::max)) { << [Take absolute value of func](#fragment-Takeabsolutevalueofmonofunc-0) >>

for (Float &f: [func](#PiecewiseConstant1D::func)) f = std::abs(f);

<< [Compute integral of step function at](#fragment-Computeintegralofstepfunctionatx_i-0) >>

[cdf](#PiecewiseConstant1D::cdf) \[0\] = 0; size\_t n = f.size(); for (size\_t i = 1; i < n + 1; ++i) [cdf](#PiecewiseConstant1D::cdf) \[i\] = [cdf](#PiecewiseConstant1D::cdf) \[i - 1\] + func\[i - 1\] \* (max - min) / n;

<< [Transform step function integral into CDF](#fragment-TransformstepfunctionintegralintoCDF-0) >>

[funcInt](#PiecewiseConstant1D::funcInt) = [cdf](#PiecewiseConstant1D::cdf) \[n\]; if ([funcInt](#PiecewiseConstant1D::funcInt) == 0) for (size\_t i = 1; i < n + 1; ++i) [cdf](#PiecewiseConstant1D::cdf) \[i\] = Float(i) / Float(n); else for (size\_t i = 1; i < n + 1; ++i) [cdf](#PiecewiseConstant1D::cdf) \[i\] /= [funcInt](#PiecewiseConstant1D::funcInt);

} Float Integral() const { return funcInt; } size\_t size() const { return [func](#PiecewiseConstant1D::func).size(); } Float Sample(Float u, Float \*pdf = nullptr, int \*offset = nullptr) const { << [Find surrounding CDF segments and offset](#fragment-FindsurroundingCDFsegmentsandmonooffset-0) >>

int o = [FindInterval](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#FindInterval) ((int) [cdf](#PiecewiseConstant1D::cdf).size(), \[&\](int index) { return [cdf](#PiecewiseConstant1D::cdf) \[index\] <= u; }); if (offset) \*offset = o;

<< [Compute offset along CDF segment](#fragment-ComputeoffsetalongCDFsegment-0) >>

Float du = u - [cdf](#PiecewiseConstant1D::cdf) \[o\]; if ([cdf](#PiecewiseConstant1D::cdf) \[o + 1\] - [cdf](#PiecewiseConstant1D::cdf) \[o\] > 0) du /= [cdf](#PiecewiseConstant1D::cdf) \[o + 1\] - [cdf](#PiecewiseConstant1D::cdf) \[o\];

<< [Compute PDF for sampled offset](#fragment-ComputePDFforsampledoffset-0) >>

if (pdf) \*pdf = ([funcInt](#PiecewiseConstant1D::funcInt) > 0)? [func](#PiecewiseConstant1D::func) \[o\] / [funcInt](#PiecewiseConstant1D::funcInt): 0;

<< [Return corresponding to sample](#fragment-Returnxcorrespondingtosample-0) >>

return [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) ((o + du) / [size](#PiecewiseConstant1D::size) (), [min](#PiecewiseConstant1D::min), [max](#PiecewiseConstant1D::max));

} pstd::optional<Float> Invert(Float x) const { << [Compute offset to CDF values that bracket](#fragment-ComputeoffsettoCDFvaluesthatbracketx-0) >>

if (x < [min](#PiecewiseConstant1D::min) || x > [max](#PiecewiseConstant1D::max)) return {}; Float c = (x - [min](#PiecewiseConstant1D::min)) / ([max](#PiecewiseConstant1D::max) - [min](#PiecewiseConstant1D::min)) \* [func](#PiecewiseConstant1D::func).size(); int offset = [Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp) (int(c), 0, [func](#PiecewiseConstant1D::func).size() - 1);

<< [Linearly interpolate between adjacent CDF values to find sample value](#fragment-LinearlyinterpolatebetweenadjacentCDFvaluestofindsamplevalue-0) >>

Float delta = c - offset; return [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (delta, [cdf](#PiecewiseConstant1D::cdf) \[offset\], [cdf](#PiecewiseConstant1D::cdf) \[offset + 1\]);

}

<< [PiecewiseConstant1D Public Members](#fragment-PiecewiseConstant1DPublicMembers-0) >>

pstd::vector<Float> func, cdf; Float min, max; Float funcInt = 0;

};

The PiecewiseConstant1D constructor takes n values of a piecewise-constant function f defined over a range. (The generalization to a non- interval simply requires remapping returned samples to the specified range and renormalizing the PDF based on its extent.)

<<PiecewiseConstant1D Public Methods>>=

PiecewiseConstant1D(pstd::span<const Float> f, Float [min](#PiecewiseConstant1D::min), Float [max](#PiecewiseConstant1D::max), Allocator alloc = {}): [func](#PiecewiseConstant1D::func) (f.begin(), f.end(), alloc), [cdf](#PiecewiseConstant1D::cdf) (f.size() + 1, alloc), [min](#PiecewiseConstant1D::min) ([min](#PiecewiseConstant1D::min)), [max](#PiecewiseConstant1D::max) ([max](#PiecewiseConstant1D::max)) { << [Take absolute value of func](#fragment-Takeabsolutevalueofmonofunc-0) >>

for (Float &f: [func](#PiecewiseConstant1D::func)) f = std::abs(f);

<< [Compute integral of step function at](#fragment-Computeintegralofstepfunctionatx_i-0) >>

[cdf](#PiecewiseConstant1D::cdf) \[0\] = 0; size\_t n = f.size(); for (size\_t i = 1; i < n + 1; ++i) [cdf](#PiecewiseConstant1D::cdf) \[i\] = [cdf](#PiecewiseConstant1D::cdf) \[i - 1\] + func\[i - 1\] \* (max - min) / n;

<< [Transform step function integral into CDF](#fragment-TransformstepfunctionintegralintoCDF-0) >>

[funcInt](#PiecewiseConstant1D::funcInt) = [cdf](#PiecewiseConstant1D::cdf) \[n\]; if ([funcInt](#PiecewiseConstant1D::funcInt) == 0) for (size\_t i = 1; i < n + 1; ++i) [cdf](#PiecewiseConstant1D::cdf) \[i\] = Float(i) / Float(n); else for (size\_t i = 1; i < n + 1; ++i) [cdf](#PiecewiseConstant1D::cdf) \[i\] /= [funcInt](#PiecewiseConstant1D::funcInt);

}

The constructor makes its own copy of the function values and computes the function’s CDF. Note that the constructor allocates n+1 Float s for the cdf array because if has step values, then there are values that define the CDF. Storing the final CDF value of 1 is redundant but simplifies the sampling code later.

<<PiecewiseConstant1D Public Members>>=

pstd::vector<Float> func, cdf; Float min, max;

Because the specified function may be negative, the absolute value of it is taken here first. (There is no further need for the original function in the PiecewiseConstant1D implementation.)

<<Take absolute value of func >>=

for (Float &f: [func](#PiecewiseConstant1D::func)) f = std::abs(f);

Next, the integral of at each point is computed using Equation ([A.17](#eq:piecewise-step-integral)), with the result stored in the cdf array for now.

<<Compute integral of step function at >>=

[cdf](#PiecewiseConstant1D::cdf) \[0\] = 0; size\_t n = f.size(); for (size\_t i = 1; i < n + 1; ++i) [cdf](#PiecewiseConstant1D::cdf) \[i\] = [cdf](#PiecewiseConstant1D::cdf) \[i - 1\] + func\[i - 1\] \* (max - min) / n;

With the value of the integral stored in cdf\[n\], this value can be copied into funcInt and the CDF can be normalized by dividing through all entries by this value. The case of a zero-valued function is handled by defining a linear CDF, which leads to uniform sampling. That case occurs more frequently than one might expect due to the use of this class when sampling piecewise-constant 2D functions; when that is used with images, images with zero-valued scanlines lead to zero-valued functions here.

<<Transform step function integral into CDF>>=

[funcInt](#PiecewiseConstant1D::funcInt) = [cdf](#PiecewiseConstant1D::cdf) \[n\]; if ([funcInt](#PiecewiseConstant1D::funcInt) == 0) for (size\_t i = 1; i < n + 1; ++i) [cdf](#PiecewiseConstant1D::cdf) \[i\] = Float(i) / Float(n); else for (size\_t i = 1; i < n + 1; ++i) [cdf](#PiecewiseConstant1D::cdf) \[i\] /= [funcInt](#PiecewiseConstant1D::funcInt);

<<PiecewiseConstant1D Public Members>>+=

Float funcInt = 0;

The integral of the absolute value of the function is made available via a method and the size() method returns the number of tabularized values.

Float Integral() const { return funcInt; } size\_t size() const { return [func](#PiecewiseConstant1D::func).size(); }

The [PiecewiseConstant1D::Sample()](#PiecewiseConstant1D::Sample) method uses the given random sample u to sample from its distribution. It returns the corresponding value and the value of the PDF. If the optional offset parameter is not nullptr, it returns the offset into the array of function values of the largest index where the CDF was less than or equal to u. (In other words, cdf\[\*offset\] <= u < cdf\[\*offset+1\].)

Float Sample(Float u, Float \*pdf = nullptr, int \*offset = nullptr) const { << [Find surrounding CDF segments and offset](#fragment-FindsurroundingCDFsegmentsandmonooffset-0) >>

int o = [FindInterval](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#FindInterval) ((int) [cdf](#PiecewiseConstant1D::cdf).size(), \[&\](int index) { return [cdf](#PiecewiseConstant1D::cdf) \[index\] <= u; }); if (offset) \*offset = o;

<< [Compute offset along CDF segment](#fragment-ComputeoffsetalongCDFsegment-0) >>

Float du = u - [cdf](#PiecewiseConstant1D::cdf) \[o\]; if ([cdf](#PiecewiseConstant1D::cdf) \[o + 1\] - [cdf](#PiecewiseConstant1D::cdf) \[o\] > 0) du /= [cdf](#PiecewiseConstant1D::cdf) \[o + 1\] - [cdf](#PiecewiseConstant1D::cdf) \[o\];

<< [Compute PDF for sampled offset](#fragment-ComputePDFforsampledoffset-0) >>

if (pdf) \*pdf = ([funcInt](#PiecewiseConstant1D::funcInt) > 0)? [func](#PiecewiseConstant1D::func) \[o\] / [funcInt](#PiecewiseConstant1D::funcInt): 0;

<< [Return corresponding to sample](#fragment-Returnxcorrespondingtosample-0) >>

return [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) ((o + du) / [size](#PiecewiseConstant1D::size) (), [min](#PiecewiseConstant1D::min), [max](#PiecewiseConstant1D::max));

}

Mapping u to an interval matching the above criterion is carried out using the efficient binary search implemented in [FindInterval()](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#FindInterval).

<<Find surrounding CDF segments and offset >>=

int o = [FindInterval](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#FindInterval) ((int) [cdf](#PiecewiseConstant1D::cdf).size(), \[&\](int index) { return [cdf](#PiecewiseConstant1D::cdf) \[index\] <= u; }); if (offset) \*offset = o;

Given the pair of CDF values that straddle u, we can compute. First, we determine how far u is between cdf\[o\] and cdf\[o+1\]. We denote this value with du, where du is 0 if u == cdf\[o\] and goes up to 1 if u == cdf\[o+1\]. Because the CDF is piecewise-linear, the sample value is the same offset between and (Figure [A.5](#fig:piecewise-constant) (b)).

<<Compute offset along CDF segment>>=

Float du = u - [cdf](#PiecewiseConstant1D::cdf) \[o\]; if ([cdf](#PiecewiseConstant1D::cdf) \[o + 1\] - [cdf](#PiecewiseConstant1D::cdf) \[o\] > 0) du /= [cdf](#PiecewiseConstant1D::cdf) \[o + 1\] - [cdf](#PiecewiseConstant1D::cdf) \[o\];

The PDF for this sample is easily computed since we have the function’s integral in funcInt. (Note that the offset o into the CDF array has been computed in a way so that func\[o\] gives the value of the function in the CDF range that the sample landed in.)

<<Compute PDF for sampled offset>>=

if (pdf) \*pdf = ([funcInt](#PiecewiseConstant1D::funcInt) > 0)? [func](#PiecewiseConstant1D::func) \[o\] / [funcInt](#PiecewiseConstant1D::funcInt): 0;

Finally, the appropriate value of is computed and returned. Here is where the sampled value in is remapped to the user-specified range.

<<Return corresponding to sample>>=

return [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) ((o + du) / [size](#PiecewiseConstant1D::size) (), [min](#PiecewiseConstant1D::min), [max](#PiecewiseConstant1D::max));

As with the other sampling routines so far, PiecewiseConstant1D provides an inversion method that takes a point in the range and returns the sample value that maps to it. As before, this is a matter of evaluating the CDF at the given position.

<<PiecewiseConstant1D Public Methods>>+=

pstd::optional<Float> Invert(Float x) const { << [Compute offset to CDF values that bracket](#fragment-ComputeoffsettoCDFvaluesthatbracketx-0) >>

if (x < [min](#PiecewiseConstant1D::min) || x > [max](#PiecewiseConstant1D::max)) return {}; Float c = (x - [min](#PiecewiseConstant1D::min)) / ([max](#PiecewiseConstant1D::max) - [min](#PiecewiseConstant1D::min)) \* [func](#PiecewiseConstant1D::func).size(); int offset = [Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp) (int(c), 0, [func](#PiecewiseConstant1D::func).size() - 1);

<< [Linearly interpolate between adjacent CDF values to find sample value](#fragment-LinearlyinterpolatebetweenadjacentCDFvaluestofindsamplevalue-0) >>

Float delta = c - offset; return [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (delta, [cdf](#PiecewiseConstant1D::cdf) \[offset\], [cdf](#PiecewiseConstant1D::cdf) \[offset + 1\]);

}

Because the CDF is tabularized at regular steps over, if we remap to lie within, scale by the number of CDF values, and take the floor of that value, we have the offset to the entry in the cdf array that precedes.

<<Compute offset to CDF values that bracket >>=

if (x < [min](#PiecewiseConstant1D::min) || x > [max](#PiecewiseConstant1D::max)) return {}; Float c = (x - [min](#PiecewiseConstant1D::min)) / ([max](#PiecewiseConstant1D::max) - [min](#PiecewiseConstant1D::min)) \* [func](#PiecewiseConstant1D::func).size(); int offset = [Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp) (int(c), 0, [func](#PiecewiseConstant1D::func).size() - 1);

Given those two points, we linearly interpolate between their values to evaluate the CDF.

<<Linearly interpolate between adjacent CDF values to find sample value>>=

Float delta = c - offset; return [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (delta, [cdf](#PiecewiseConstant1D::cdf) \[offset\], [cdf](#PiecewiseConstant1D::cdf) \[offset + 1\]);