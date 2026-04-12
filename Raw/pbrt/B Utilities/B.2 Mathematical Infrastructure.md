---
title: "Mathematical Infrastructure"
source: "https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## B.2 Mathematical Infrastructure
> ## B.2 数学基础设施

pbrt uses a wide range of mathematical routines.
> pbrt 使用了大量的数学例程。 Much of this functionality is implemented in the files [util/math.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/math.h) and [util/math.cpp](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/math.cpp); everything in this section is found there, with a few exceptions that will be noted when they are encountered.

A table of the first 1,000 prime numbers is provided via a global variable. Its main use in pbrt is for determining bases to use for the radical inverse based low-discrepancy points in Chapter [8](https://pbr-book.org/4ed/Sampling_and_Reconstruction.html#chap:sampling-reconstruction).

<<Prime Table Declarations>>=

static constexpr int PrimeTableSize = 1000; extern PBRT\_CONST int Primes\[PrimeTableSize\];

NextPrime() returns the next prime number after the provided one.

<<Math Function Declarations>>=

int NextPrime(int x);

### B.2.1 Basic Algebraic Functions

[Clamp()](#Clamp) clamps the given value to lie between the values low and high. For convenience, it allows the types of the values giving the extent to be different than the type being clamped (but its implementation requires that implicit conversion among the types involved is legal). By being implemented this way rather than requiring all to have the same type, the implementation allows calls like Clamp(floatValue, 0, 1) that would otherwise be disallowed by C++’s template type resolution rules.

template <typename T, typename U, typename V> constexpr T [Clamp](#Clamp) (T val, U low, V high) { if (val < low) return T(low); else if (val > high) return T(high); else return val; }

[Mod()](#Mod) computes the remainder of. pbrt has its own version of this (rather than using %) in order to provide the behavior that the modulus of a negative number is always positive or zero. Starting with C++11, the behavior of % has been specified to return a negative value or zero in this case, so that the identity (a/b)\*b + a%b == a holds.

template <typename T> T Mod(T a, T b) { T result = a - (a / b) \* b; return (T)((result < 0)? result + b: result); }

A specialization for Float s calls the corresponding standard library function.

template <> Float Mod(Float a, Float b) { return std::fmod(a, b); }

It can be useful to be able to invert the bilinear interpolation function, Equation ([2.25](https://pbr-book.org/4ed/Monte_Carlo_Integration/Transforming_between_Distributions.html#eq:bilinear-interp)). Because there are two unknowns in the result, values with at least two dimensions must be bilinearly interpolated in order to invert it. In that case, two equations with two unknowns can be formed, which in turn leads to a quadratic equation.

<<Point2 Inline Functions>>=

[Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) InvertBilinear([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p, pstd::span<const [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) > v);

A number of constants, most of them related to, are used enough that it is worth having them easily available.

<<Mathematical Constants>>+=

constexpr Float Pi = 3.14159265358979323846; constexpr Float InvPi = 0.31830988618379067154; constexpr Float Inv2Pi = 0.15915494309189533577; constexpr Float Inv4Pi = 0.07957747154594766788; constexpr Float PiOver2 = 1.57079632679489661923; constexpr Float PiOver4 = 0.78539816339744830961; constexpr Float Sqrt2 = 1.41421356237309504880;

Two simple functions convert from angles expressed in degrees to radians, and vice versa:

Float Radians(Float deg) { return (Pi / 180) \* deg; } Float Degrees(Float rad) { return (180 / Pi) \* rad; }

It is often useful to blend between two values using a smooth curve that does not have the first derivative discontinuities that a linear interpolant would. SmoothStep() takes a range and a value, returning 0 if, 1 if, and smoothly blends between 0 and 1 for intermediate values of using a cubic polynomial. Among other uses in the system, the [SpotLight](https://pbr-book.org/4ed/Light_Sources/Point_Lights.html#SpotLight) uses SmoothStep() to model the falloff to its edge.

Float SmoothStep(Float x, Float a, Float b) { if (a == b) return (x < a)? 0: 1; Float t = [Clamp](#Clamp) ((x - a) / (b - a), 0, 1); return t \* t \* (3 - 2 \* t); }

Finally, SafeSqrt() returns the square root of the given value, clamping it to zero in case of rounding errors being the cause of a slightly negative value. A second variant for double s is effectively the same and is therefore not included here.

float SafeSqrt(float x) { return std::sqrt(std::max(0.f, x)); }

### B.2.2 Integer Powers and Polynomials

Sqr() squares the provided value. Though it is not much work to write this operation out directly, we have often found this function to be helpful in making the implementations of formulae more succinct.

template <typename T> constexpr T Sqr(T v) { return v \* v; }

Pow() efficiently raises a value to a power if the power is a compile-time constant. Note that the total number of multiply operations works out to be logarithmic in the power n.

template <int n> constexpr float [Pow](#Pow) (float v) { if constexpr (n < 0) return 1 / [Pow](#Pow) <-n>(v); float n2 = [Pow](#Pow) <n / 2>(v); return n2 \* n2 \* [Pow](#Pow) <n & 1>(v); }

Specializations for and terminate the template function recursion.

template <> constexpr float Pow<1>(float v) { return v; } template <> constexpr float Pow<0>(float v) { return 1; }

EvaluatePolynomial() evaluates a provided polynomial using Horner’s method, which is based on the equivalence

This formulation both gives good numerical accuracy and is amenable to use of fused multiply add operations.

template <typename Float, typename C> constexpr Float EvaluatePolynomial(Float t, C c) { return c; }

template <typename Float, typename C, typename... Args> constexpr Float [EvaluatePolynomial](#EvaluatePolynomial) (Float t, C c, Args... cRemaining) { return [FMA](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#FMA) (t, [EvaluatePolynomial](#EvaluatePolynomial) (t, cRemaining...), c); }

### B.2.3 Trigonometric Functions

The function is used in multiple places in pbrt, including in the implementation of the [LanczosSincFilter](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Image_Reconstruction.html#LanczosSincFilter). It is undefined at and suffers from poor numerical accuracy if directly evaluated at nearby values. A robust computation of its value is possible by considering the power series expansion

If is small and rounds to 1, then also rounds to 1. The following function uses a slightly more conservative variant of that test, which is close enough for our purposes.

Float SinXOverX(Float x) { if (1 - x \* x == 1) return 1; return std::sin(x) / x; }

Similar to [SafeSqrt()](#SafeSqrt), pbrt also provides “safe” versions of the inverse sine and cosine functions so that if the provided value is slightly outside of the legal range, a reasonable result is returned rather than a not-a-number value. In debug builds, an additional check is performed to make sure that the provided value is not too far outside the valid range.

float SafeASin(float x) { return std::asin([Clamp](#Clamp) (x, -1, 1)); } float SafeACos(float x) { return std::acos([Clamp](#Clamp) (x, -1, 1)); }

### B.2.4 Logarithms and Exponentiation

Because the math library does not provide a base-2 logarithm function, we provide one here, using the identity.

Float Log2(Float x) { const Float invLog2 = 1.442695040888963387004650940071; return std::log(x) \* invLog2; }

If only the integer component of the base-2 logarithm of a float is needed, then the result is available (nearly) in the exponent of the floating-point representation. In the implementation below, we augment that approach by testing the significand of the provided value to the midpoint of significand values between the current exponent and the next one up, using the result to determine whether rounding the exponent up or rounding it down gives a more accurate result. A corresponding function for double s is not included here.

int [Log2Int](#Log2Int) (float v) { if (v < 1) return - [Log2Int](#Log2Int) (1 / v); // midsignif = [Significand](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#Significand) (std::pow(2., 1.5)) const uint32\_t midsignif = 0b00000000001101010000010011110011; return [Exponent](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#Exponent) (v) + (([Significand](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#Significand) (v) >= midsignif)? 1: 0); }

It is also often useful to be able to compute the base-2 logarithm of an integer. Rather than computing an expensive floating-point logarithm and converting to an integer, it is much more efficient to count the number of leading zeros up to the first one in the 32-bit binary representation of the value and then subtract this value from 31, which gives the index of the first bit set, which is in turn the integer base-2 logarithm. (This efficiency comes in part from the fact that most processors have an instruction to count these zeros.)

Though we generally eschew including target-specific code in the book text, we will make an exception here just as an illustration of the messiness that often results when it is necessary to leave the capabilities of the standard libraries to access features that have different interfaces on different targets.

int Log2Int(uint32\_t v) { #ifdef PBRT\_IS\_GPU\_CODE return 31 - \_\_clz(v); #elif defined(PBRT\_HAS\_INTRIN\_H) unsigned long lz = 0; if (\_BitScanReverse(&lz, v)) return lz; return 0; #else return 31 - \_\_builtin\_clz(v); #endif }

It is occasionally useful to compute the base-4 logarithm of an integer value. This is easily done using the identity.

template <typename T> int Log4Int(T v) { return [Log2Int](#Log2Int) (v) / 2; }

An efficient approximation to the exponential function comes in handy, especially for volumetric light transport algorithms where such values need to be computed frequently. Like [Log2Int()](#Log2Int), this value can be computed efficiently by taking advantage of the floating-point representation.

float FastExp(float x) { << [Compute such that](#fragment-Computexsuchthatromanex2x-0) >>

float xp = x \* 1.442695041f;

<< [Find integer and fractional components of](#fragment-Findintegerandfractionalcomponentsofx-0) >>

float fxp = pstd::floor(xp), f = xp - fxp; int i = (int)fxp;

<< [Evaluate polynomial approximation of](#fragment-Evaluatepolynomialapproximationof2f-0) >>

float twoToF = [EvaluatePolynomial](#EvaluatePolynomial) (f, 1.f, 0.695556856f, 0.226173572f, 0.0781455737f);

<< [Scale by and return final result](#fragment-Scale2fby2iandreturnfinalresult-0) >>

int exponent = [Exponent](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#Exponent) (twoToF) + i; if (exponent < -126) return 0; if (exponent > 127) return [Infinity](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#Infinity); uint32\_t bits = [FloatToBits](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#FloatToBits) (twoToF); bits &= 0b10000000011111111111111111111111u; bits |= (exponent + 127) << 23; return [BitsToFloat](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#BitsToFloat) (bits);

}

The first step is to convert the problem into one to compute a base-2 exponential; a factor of does so. This step makes it possible to take advantage of computers’ native floating-point representation.

<<Compute such that >>=

float xp = x \* 1.442695041f;

Next, the function splits the exponent into an integer and a fractional part, giving.

<<Find integer and fractional components of >>=

float fxp = pstd::floor(xp), f = xp - fxp; int i = (int)fxp;

Because is between 0 and 1, can be accurately approximated with a polynomial. We have fit a cubic polynomial to this function using a constant term of 1 so that is exact. The following coefficients give a maximum absolute error of less than over the range of.

<<Evaluate polynomial approximation of >>=

float twoToF = [EvaluatePolynomial](#EvaluatePolynomial) (f, 1.f, 0.695556856f, 0.226173572f, 0.0781455737f);

The last task is to apply the scale. This can be done by directly operating on the exponent in the twoToF value. It is necessary to make sure that the resulting exponent fits in the valid exponent range of 32-bit float s; if it does not, then the computation has either underflowed to 0 or overflowed to infinity. If the exponent is valid, then the existing exponent bits are cleared so that final exponent can be stored. (For the source of the value of 127 that is added to the exponent, see Equation ([6.17](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#eq:float-exponent-bias)).)

<<Scale by and return final result>>=

int exponent = [Exponent](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#Exponent) (twoToF) + i; if (exponent < -126) return 0; if (exponent > 127) return [Infinity](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#Infinity); uint32\_t bits = [FloatToBits](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#FloatToBits) (twoToF); bits &= 0b10000000011111111111111111111111u; bits |= (exponent + 127) << 23; return [BitsToFloat](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#BitsToFloat) (bits);

### B.2.5 Transcendental and Special Functions

Gaussian() evaluates the Gaussian function

Float Gaussian(Float x, Float mu = 0, Float sigma = 1) { return 1 / std::sqrt(2 \* [Pi](#Pi) \* sigma \* sigma) \* [FastExp](#FastExp) (- [Sqr](#Sqr) (x - mu) / (2 \* sigma \* sigma)); }

The integral of the Gaussian over a range can be expressed in terms of the error function, which is available from the standard library.

Float GaussianIntegral(Float x0, Float x1, Float mu = 0, Float sigma = 1) { Float sigmaRoot2 = sigma \* Float(1.414213562373095); return 0.5f \* (std::erf((mu - x0) / sigmaRoot2) - std::erf((mu - x1) / sigmaRoot2)); }

The logistic distribution takes a scale factor, which controls its width:

It has a generally similar shape to the Gaussian—it is symmetric and smoothly falls off from its peak—but it can be integrated in closed form. Evaluating the logistic is straightforward, though it is worth taking the absolute value of to avoid numerical instability for when the ratio is relatively large. (The function is symmetric around the origin, so this is mathematically equivalent.)

Float Logistic(Float x, Float s) { x = std::abs(x); return std::exp(-x / s) / (s \* [Sqr](#Sqr) (1 + std::exp(-x / s))); }

The logistic function is normalized so it is its own probability density function (PDF). Its cumulative distribution function (CDF) can be easily found via integration.

Float LogisticCDF(Float x, Float s) { return 1 / (1 + std::exp(-x / s)); }

The trimmed logistic function is the logistic limited to an interval and then renormalized using the technique introduced in Section [A.4.5](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#sec:sampling-function-over-interval).

Float TrimmedLogistic(Float x, Float s, Float a, Float b) { return [Logistic](#Logistic) (x, s) / ([LogisticCDF](#LogisticCDF) (b, s) - [LogisticCDF](#LogisticCDF) (a, s)); }

ErfInv() is the inverse to the error function std:erf(), implemented via a polynomial approximation. I0() evaluates the modified Bessel function of the first kind and LogI0() returns its logarithm.

Float ErfInv(Float a); Float I0(Float x); Float LogI0(Float x);

### B.2.6 Interval Search

FindInterval() is a helper function that emulates the behavior of std::upper\_bound() but uses a function object to get values at various indices instead of requiring access to an actual array. This way, it becomes possible to bisect arrays that are procedurally generated, such as those interpolated from point samples.

It generally returns the index such that is true and is false. However, since this function is primarily used to locate an interval for linear interpolation, it applies the following boundary conditions to prevent out-of-bounds accesses and to deal with predicates that evaluate to true or false over the entire domain:

- The returned index is no larger than sz-2, so that it is always legal to access both of the elements and.
- If there is no index such that the predicate is true, 0 is returned.
- If there is no index such that the predicate is false, sz-2 is returned.

template <typename Predicate> size\_t FindInterval(size\_t sz, const Predicate &pred) { using ssize\_t = std::make\_signed\_t<size\_t>; ssize\_t size = (ssize\_t)sz - 2, first = 1; while (size > 0) { << [Evaluate predicate at midpoint and update first and size](#fragment-Evaluatepredicateatmidpointandupdatemonofirstandmonosize-0) >>

size\_t half = (size\_t)size >> 1, middle = first + half; bool predResult = pred(middle); first = predResult? middle + 1: first; size = predResult? size - (half + 1): half;

} return (size\_t) [Clamp](#Clamp) ((ssize\_t)first - 1, 0, sz - 2); }

<<Evaluate predicate at midpoint and update first and size >>=

size\_t half = (size\_t)size >> 1, middle = first + half; bool predResult = pred(middle); first = predResult? middle + 1: first; size = predResult? size - (half + 1): half;

### B.2.7 Bit Operations

There are clever tricks that can be used to efficiently determine if a given integer is an exact power of 2, or round an integer up to the next higher (or equal) power of 2. (It is worthwhile to take a minute and work through for yourself how these two functions work.)

template <typename T> bool IsPowerOf2(T v) { return v &&!(v & (v - 1)); }

int32\_t RoundUpPow2(int32\_t v) { v--; v |= v >> 1; v |= v >> 2; v |= v >> 4; v |= v >> 8; v |= v >> 16; return v + 1; }

A variant of RoundUpPow2() for int64\_t is also provided but is not included in the text here.

The bits of an integer quantity can be efficiently reversed with a series of bitwise operations. The first line of the ReverseBits32() function, which reverses the bits of a 32-bit integer, swaps the lower 16 bits with the upper 16 bits of the value. The next line simultaneously swaps the first 8 bits of the result with the second 8 bits and the third 8 bits with the fourth. This process continues until the last line, which swaps adjacent bits. To understand this code, it is helpful to write out the binary values of the various hexadecimal constants. For example, 0xff00ff00 is 11111111000000001111111100000000 in binary; it is then easy to see that a bitwise or with this value masks off the first and third 8-bit quantities.

<<Bit Operation Inline Functions>>=

inline uint32\_t ReverseBits32(uint32\_t n) { n = (n << 16) | (n >> 16); n = ((n & 0x00ff00ff) << 8) | ((n & 0xff00ff00) >> 8); n = ((n & 0x0f0f0f0f) << 4) | ((n & 0xf0f0f0f0) >> 4); n = ((n & 0x33333333) << 2) | ((n & 0xcccccccc) >> 2); n = ((n & 0x55555555) << 1) | ((n & 0xaaaaaaaa) >> 1); return n; }

The bits of a 64-bit value can then be reversed by reversing the two 32-bit components individually and then interchanging them.

<<Bit Operation Inline Functions>>+=

inline uint64\_t ReverseBits64(uint64\_t n) { uint64\_t n0 = ReverseBits32((uint32\_t)n); uint64\_t n1 = ReverseBits32((uint32\_t)(n >> 32)); return (n0 << 32) | n1; }

#### Morton Indexing

To be able to compute 3D Morton codes, which were introduced in Section [7.3.3](https://pbr-book.org/4ed/Primitives_and_Intersection_Acceleration/Bounding_Volume_Hierarchies.html#sec:hlbvh), we will first define a helper function: LeftShift3() takes a 32-bit value and returns the result of shifting the th bit to be at the th bit, leaving zeros in other bits. Figure [B.1](#fig:morton-bits-3x) illustrates this operation.

[![](https://pbr-book.org/4ed/Utilities/phabbf01.svg)](https://pbr-book.org/4ed/Utilities/phabbf01.svg)

Figure B.1: Bit Shifts to Compute 3D Morton Codes. The LeftShift3() function takes a 32-bit integer value and for the bottom 10 bits, shifts the th bit to be in position —in other words, shifts it places to the left. All other bits are set to zero.

The most obvious approach to implement this operation, shifting each bit value individually, is not the most efficient. (It would require a total of 9 shifts, along with bitwise or operations to compute the final value.) Instead, we can decompose each bit’s shift into multiple shifts of power-of-two size that together shift the bit’s value to its final position. Then, all the bits that need to be shifted a given power-of-two number of places can be shifted together. The LeftShift3() function implements this computation, and Figure [B.2](#fig:morton-pow2-shifts) shows how it works.

[![](https://pbr-book.org/4ed/Utilities/phabbf02.svg)](https://pbr-book.org/4ed/Utilities/phabbf02.svg)

Figure B.2: Power-of-Two Decomposition of Morton Bit Shifts. The bit shifts to compute the Morton code for each 3D coordinate are performed in a series of shifts of power-of-two size. First, bits 8 and 9 are shifted 16 places to the left. This places bit 8 in its final position. Next bits 4 through 7 are shifted 8 places. After shifts of 4 and 2 places (with appropriate masking so that each bit is shifted the right number of places in the end), all bits are in the proper position. This computation is implemented by the [LeftShift3()](#LeftShift3) function.

<<Bit Operation Inline Functions>>+=

inline uint32\_t LeftShift3(uint32\_t x) { if (x == (1 << 10)) --x; x = (x | (x << 16)) & 0b00000011000000000000000011111111; x = (x | (x << 8)) & 0b00000011000000001111000000001111; x = (x | (x << 4)) & 0b00000011000011000011000011000011; x = (x | (x << 2)) & 0b00001001001001001001001001001001; return x; }

EncodeMorton3() takes a 3D coordinate value where each component is a floating-point value between 0 and. It converts these values to integers and then computes the Morton code by expanding the three 10-bit quantized values so that their th bits are at position, then shifting the bits over one more, the bits over two more, and computing the bitwise or of the result (Figure [B.3](#fig:morton-final-interleave)).

[![](https://pbr-book.org/4ed/Utilities/phabbf03.svg)](https://pbr-book.org/4ed/Utilities/phabbf03.svg)

Figure B.3: Final Interleaving of Coordinate Values. Given interleaved values for,, and computed by LeftShift3(), the final Morton-encoded value is computed by shifting and one and two places, respectively, and then computing the bitwise OR of the results.

<<Bit Operation Inline Functions>>+=

uint32\_t EncodeMorton3(float x, float y, float z) { return (LeftShift3(z) << 2) | (LeftShift3(y) << 1) | LeftShift3(x); }

Support for 2D Morton encoding is provided by the EncodeMorton2() function, which takes a pair of 32-bit integer values and follows an analogous approach. It is not included here.

### B.2.8 Hashing and Random Permutations

A handful of hashing functions are provided. Their implementations are in the file [\[EntityList: util/cmd: -: hash.h\]](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/[EntityList:%20util/cmd:%20-:hash.h]).

The first, MixBits(), takes an integer value and applies a so-called *finalizer*, which is commonly found at the end of hash function implementations. A good hash function has the property that flipping a single bit in the input causes each of the bits in the result to flip with probability; a finalizer takes values where this may not be the case and shuffles them around in a way that increases this likelihood.

MixBits() is particularly handy for tasks like computing unique seeds for a pseudo-random number generator at each pixel: depending on the RNG implementation, the naive approach of converting the pixel coordinates into an index and giving the RNG successive integer values as seeds may lead to correlation between values it generates at nearby pixels. Running such an index through MixBits() first is good protection against this.

<<Hashing Inline Functions>>=

uint64\_t MixBits(uint64\_t v);

There are also complete hash functions for arbitrary data. HashBuffer() hashes a region of memory of given size using *MurmurHash64A*, which is an efficient and high-quality hash function.

template <typename T> uint64\_t HashBuffer(const T \*ptr, size\_t size, uint64\_t seed = 0) { return MurmurHash64A((const unsigned char \*)ptr, size, seed); }

For convenience, pbrt also provides a Hash() function that can be passed an arbitrary sequence of values, all of which are hashed together.

template <typename... Args> uint64\_t Hash(Args... args);

It is sometimes useful to convert a hash to a floating-point value between 0 and 1; the HashFloat() function handles the details of doing so.

<<Hashing Inline Functions>>+=

template <typename... Args> Float HashFloat(Args... args) { return uint32\_t([Hash](#Hash) (args...)) \* 0x1p-32f; }

PermutationElement() returns the i th element of a random permutation of n values based on the provided seed. Remarkably, it is able to do so without needing to explicitly represent the permutation. The key idea underlying its implementation is the insight that any invertible hash function of bits represents a permutation of the values from 0 to —otherwise, it would not be invertible.

Such a hash function can be used to define a permutation over a non-power-of-two number of elements using the permutation for the next power-of-two number of elements and then repermuting any values greater than until a valid one is reached.

<<Permutation Inline Function Declarations>>=

int PermutationElement(uint32\_t i, uint32\_t n, uint32\_t seed);

### B.2.9 Error-Free Transformations

It is possible to increase the accuracy of some floating-point calculations using an approach known as *error-free transformations* (EFT). The idea of them is to maintain the accumulated error that is in a computed floating-point value and to then make use of that error to correct later computations. For example, we know that the rounded floating-point value is in general not equal to the true product. Using EFTs, we also compute an error term such that

A clever use of fused multiply add (FMA) makes it possible to compute without resorting to higher-precision floating-point numbers. Consider the computation FMA(-a, b, a \* b): on the face of it, it computes zero, adding the negated product of and to itself. In the context of the FMA operation, however, it gives the rounding error, since the product of and is not rounded before, which is rounded, is added to it.

TwoProd() multiplies two numbers and determines the error, returning both results using the CompensatedFloat structure.

CompensatedFloat TwoProd(Float a, Float b) { Float ab = a \* b; return {ab, FMA(a, b, -ab)}; }

CompensatedFloat is a small wrapper class that holds the results of EFT-based computations.

<<CompensatedFloat Definition>>=

struct CompensatedFloat { public: << [CompensatedFloat Public Methods](#fragment-CompensatedFloatPublicMethods-0) >>

CompensatedFloat(Float v, Float err = 0): v(v), err(err) {} explicit operator float() const { return v + err; } explicit operator double() const { return double(v) + double(err); }

Float v, err; };

It provides the expected constructor and conversion operators, which are qualified with explicit to force callers to express their intent to use them.

<<CompensatedFloat Public Methods>>=

CompensatedFloat(Float v, Float err = 0): v(v), err(err) {} explicit operator float() const { return v + err; } explicit operator double() const { return double(v) + double(err); }

It is also possible to compute a compensation term for floating-point addition of two values:.

CompensatedFloat TwoSum(Float a, Float b) { Float s = a + b, delta = s - a; return {s, (a - (s - delta)) + (b - delta)}; }

It is not in general possible to compute exact compensation terms for sums or products of more than two values. However, maintaining them anyway, even if they carry some rounding error, makes it possible to implement various algorithms with lower error than if they were not used.

A similar trick based on FMA can be applied to the difference-of-products calculation of the form. To understand the challenge involved in this computation, consider computing this difference as. There are two rounding operations, one after computing and then another after the FMA. If, for example, all of,,, and are positive and the products and are of similar magnitudes, then catastrophic cancellation may result: the rounding error from, though small with respect to the product, may be large with respect to the final result.

The following DifferenceOfProducts() function uses FMA in a similar manner to [TwoProd()](#TwoProd), finding an initial value for the difference of products as well as the rounding error from. The rounding error is then added back to the value that is returned, thus fixing up catastrophic cancellation after the fact. It has been shown that this gives a result within 1.5 ulps of the correct value; see the “Further Reading” section for details.

template <typename Ta, typename Tb, typename Tc, typename Td> inline auto DifferenceOfProducts(Ta a, Tb b, Tc c, Td d) { auto cd = c \* d; auto differenceOfProducts = [FMA](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#FMA) (a, b, -cd); auto error = [FMA](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#FMA) (-c, d, cd); return differenceOfProducts + error; }

pbrt also provides a [SumOfProducts()](#SumOfProducts) function that reliably computes in a similar manner.

Compensation can also be used to compute a sum of numbers more accurately than adding them together directly. An algorithm to do so is implemented in the CompensatedSum class.

<<CompensatedSum Definition>>=

template <typename Float> class CompensatedSum { public: << [CompensatedSum Public Methods](#fragment-CompensatedSumPublicMethods-0) >>

CompensatedSum() = default; PBRT\_CPU\_GPU explicit CompensatedSum(Float v): sum(v) {} PBRT\_CPU\_GPU CompensatedSum &operator=(Float v) { sum = v; c = 0; return \*this; } CompensatedSum &operator+=(Float v) { Float delta = v - c; Float newSum = sum + delta; c = (newSum - sum) - delta; sum = newSum; return \*this; } explicit operator Float() const { return sum; } std::string ToString() const;

private: Float sum = 0, c = 0; };

The value added to the sum, delta, is the difference between the value provided and the accumulated error in c. After the addition is performed, the compensation term is updated appropriately.

<<CompensatedSum Public Methods>>=

CompensatedSum &operator+=(Float v) { Float delta = v - c; Float newSum = sum + delta; c = (newSum - sum) - delta; sum = newSum; return \*this; }

<<CompensatedSum Public Methods>>+=

explicit operator Float() const { return sum; }

### B.2.10 Finding Zeros

The Quadratic() function finds solutions of the quadratic equation; the Boolean return value indicates whether solutions were found.

bool Quadratic(float a, float b, float c, float \*t0, float \*t1) { << [Handle case of for quadratic solution](#fragment-Handlecaseofa0forquadraticsolution-0) >>

if (a == 0) { if (b == 0) return false; \*t0 = \*t1 = -c / b; return true; }

<< [Find quadratic discriminant](#fragment-Findquadraticdiscriminant-0) >>

float discrim = [DifferenceOfProducts](#DifferenceOfProducts) (b, b, 4 \* a, c); if (discrim < 0) return false; float rootDiscrim = std::sqrt(discrim);

<< [Compute quadratic t values](#fragment-Computequadraticmonotvalues-0) >>

float q = -0.5f \* (b + pstd::copysign(rootDiscrim, b)); \*t0 = q / a; \*t1 = c / q; if (\*t0 > \*t1) pstd::swap(\*t0, \*t1);

return true; }

If a is zero, then the caller has actually specified a linear function. That case is handled first to avoid not-a-number values being generated via the usual execution path. (Our implementation does not handle the case of all coefficients being equal to zero, in which case there are an infinite number of solutions.)

<<Handle case of for quadratic solution>>=

if (a == 0) { if (b == 0) return false; \*t0 = \*t1 = -c / b; return true; }

The discriminant is computed using DifferenceOfProducts(), which improves the accuracy of the computed value compared to computing it directly using floating-point multiplication and subtraction. If the discriminant is negative, then there are no real roots and the function returns false.

<<Find quadratic discriminant>>=

float discrim = [DifferenceOfProducts](#DifferenceOfProducts) (b, b, 4 \* a, c); if (discrim < 0) return false; float rootDiscrim = std::sqrt(discrim);

The usual version of the quadratic equation can give poor numerical accuracy when due to cancellation error. It can be rewritten algebraically into a more stable form:

where

The implementation uses pstd::copysign() in place of an if test for the condition on, setting the sign of the square root of the discriminant to be the same as the sign of, which is equivalent. This micro-optimization does not meaningfully affect pbrt ’s performance, but it is a trick that is worth being aware of.

<<Compute quadratic t values>>=

float q = -0.5f \* (b + pstd::copysign(rootDiscrim, b)); \*t0 = q / a; \*t1 = c / q; if (\*t0 > \*t1) pstd::swap(\*t0, \*t1);

NewtonBisection() finds a zero of an arbitrary function over a specified range using an iterative root-finding technique that is guaranteed to converge to the solution so long as brackets a root and and differ in sign.

In each iteration, bisection search splits the interval into two parts and discards the subinterval that does not bracket the solution—in this way, it can be interpreted as a continuous extension of binary search. The method’s robustness is clearly desirable, but its relatively slow (linear) convergence can still be improved. We therefore use *Newton-bisection*, which is a combination of the quadratically converging but potentially unsafe Newton’s method with the safety of bisection search as a fallback.

The provided function f should return a std::pair<Float, Float> where the first value is the function’s value and the second is its derivative. Two “epsilon” values control the accuracy of the result: xEps gives a minimum distance between the values that bracket the root, and fEps specifies how close to zero is sufficient for.

template <typename Func> Float NewtonBisection(Float x0, Float x1, Func f, Float xEps = 1e-6f, Float fEps = 1e-6f) { << [Check function endpoints for roots](#fragment-Checkfunctionendpointsforroots-0) >>

Float fx0 = f(x0).first, fx1 = f(x1).first; if (std::abs(fx0) < fEps) return x0; if (std::abs(fx1) < fEps) return x1; bool startIsNegative = fx0 < 0;

<< [Set initial midpoint using linear approximation of f](#fragment-Setinitialmidpointusinglinearapproximationofmonof-0) >>

Float xMid = x0 + (x1 - x0) \* -fx0 / (fx1 - fx0);

while (true) { << [Fall back to bisection if xMid is out of bounds](#fragment-FallbacktobisectionifmonoxMidisoutofbounds-0) >>

if (!(x0 < xMid && xMid < x1)) xMid = (x0 + x1) / 2;

<< [Evaluate function and narrow bracket range \[x0, x1\]](#fragment-Evaluatefunctionandnarrowbracketrangemonox0x1-0) >>

std::pair<Float, Float> fxMid = f(xMid); if (startIsNegative == (fxMid.first < 0)) x0 = xMid; else x1 = xMid;

<< [Stop the iteration if converged](#fragment-Stoptheiterationifconverged-0) >>

if ((x1 - x0) < xEps || std::abs(fxMid.first) < fEps) return xMid;

<< [Perform a Newton step](#fragment-PerformaNewtonstep-0) >>

xMid -= fxMid.first / fxMid.second;

} }

Before the iteration begins, a check is performed to see if one of the endpoints is a zero. (For example, this case comes up if a zero-valued function is specified.) If so, there is no need to do any further work.

<<Check function endpoints for roots>>=

Float fx0 = f(x0).first, fx1 = f(x1).first; if (std::abs(fx0) < fEps) return x0; if (std::abs(fx1) < fEps) return x1; bool startIsNegative = fx0 < 0;

The number of required Newton-bisection iterations can be reduced by starting the algorithm with a good initial guess. The function uses a heuristic that assumes that the function is linear and finds the zero crossing of the line between the two endpoints.

<<Set initial midpoint using linear approximation of f >>=

Float xMid = x0 + (x1 - x0) \* -fx0 / (fx1 - fx0);

The first fragment in the inner loop checks if the current proposed midpoint is inside the bracketing interval. Otherwise, it is reset to the interval center, resulting in a standard bisection step (Figure [B.4](#fig:newton-bisection)).

[![](https://pbr-book.org/4ed/Utilities/phabbf04.svg)](https://pbr-book.org/4ed/Utilities/phabbf04.svg)

Figure B.4: The Robustness of Newton-Bisection. (a) This function increases monotonically and contains a single root on the shown interval, but a naive application of Newton’s method diverges. (b) The bisection feature of the robust root-finder enables recovery from the third Newton step, which jumps far away from the root (the bisection interval is highlighted). The method converges a few iterations later.

<<Fall back to bisection if xMid is out of bounds>>=

if (!(x0 < xMid && xMid < x1)) xMid = (x0 + x1) / 2;

The function can now be evaluated and the bracket range can be refined. Either x0 or x1 is set to xMid in a way that maintains the invariant that the function has different signs at and.

<<Evaluate function and narrow bracket range \[x0, x1\] >>=

std::pair<Float, Float> fxMid = f(xMid); if (startIsNegative == (fxMid.first < 0)) x0 = xMid; else x1 = xMid;

The iteration stops either if the function value is close to 0 or if the bracketing interval has become sufficiently small.

<<Stop the iteration if converged>>=

if ((x1 - x0) < xEps || std::abs(fxMid.first) < fEps) return xMid;

If the iteration is to continue, an updated midpoint is computed using a Newton step. The next loop iteration will detect the case of this point being outside the bracket interval.

<<Perform a Newton step>>=

xMid -= fxMid.first / fxMid.second;

### B.2.11 Robust Variance Estimation

One problem with computing the sample variance using Equation ([2.11](https://pbr-book.org/4ed/Monte_Carlo_Integration/Monte_Carlo_Basics.html#eq:sample-variance-basic)) is that doing so requires storing all the samples. The storage requirements for this may be unacceptable—for example, for a Film implementation that is estimating per-pixel variance with thousands of samples per pixel. Equation ([2.9](https://pbr-book.org/4ed/Monte_Carlo_Integration/Monte_Carlo_Basics.html#eq:variance)) suggests another possibility: if we accumulate estimates of both and, then the sample variance could be estimated as

which only requires storing two values. This approach is numerically unstable, however, due to having a much larger magnitude than. Therefore, the following VarianceEstimator class, which computes an online estimate of variance without storing all the samples, uses *Welford’s algorithm*, which is numerically stable. Its implementation in pbrt is parameterized by a floating-point type so that, for example, double precision can be used even when pbrt is built to use single-precision [Float](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#Float) s.

<<VarianceEstimator Definition>>=

template <typename Float = Float> class VarianceEstimator { public: << [VarianceEstimator Public Methods](#fragment-VarianceEstimatorPublicMethods-0) >>

void Add(Float x) { ++ [n](#VarianceEstimator::n); Float delta = x - [mean](#VarianceEstimator::mean); [mean](#VarianceEstimator::mean) += delta / [n](#VarianceEstimator::n); Float delta2 = x - [mean](#VarianceEstimator::mean); [S](#VarianceEstimator::S) += delta \* delta2; } Float [Mean](#VarianceEstimator::Mean) () const { return [mean](#VarianceEstimator::mean); } Float [Variance](#VarianceEstimator::Variance) () const { return ([n](#VarianceEstimator::n) > 1)? [S](#VarianceEstimator::S) / ([n](#VarianceEstimator::n) - 1): 0; } Float RelativeVariance() const { return ([n](#VarianceEstimator::n) < 1 || [mean](#VarianceEstimator::mean) == 0)? 0: [Variance](#VarianceEstimator::Variance) () / [Mean](#VarianceEstimator::Mean) (); } void Merge(const VarianceEstimator &ve) { if (ve.[n](#VarianceEstimator::n) == 0) return; [S](#VarianceEstimator::S) = [S](#VarianceEstimator::S) + ve.[S](#VarianceEstimator::S) + Sqr(ve.[mean](#VarianceEstimator::mean) - [mean](#VarianceEstimator::mean)) \* [n](#VarianceEstimator::n) \* ve.[n](#VarianceEstimator::n) / ([n](#VarianceEstimator::n) + ve.[n](#VarianceEstimator::n)); [mean](#VarianceEstimator::mean) = ([n](#VarianceEstimator::n) \* [mean](#VarianceEstimator::mean) + ve.[n](#VarianceEstimator::n) \* ve.[mean](#VarianceEstimator::mean)) / ([n](#VarianceEstimator::n) + ve.[n](#VarianceEstimator::n)); [n](#VarianceEstimator::n) += ve.[n](#VarianceEstimator::n); }

private: << [VarianceEstimator Private Members](#fragment-VarianceEstimatorPrivateMembers-0) >>

Float mean = 0, S = 0; int64\_t n = 0;

};

Welford’s algorithm computes two quantities: the sample mean and the sum of squares of differences between the samples and the sample mean,. In turn, gives the sample variance.

<<VarianceEstimator Private Members>>=

Float mean = 0, S = 0; int64\_t n = 0;

Both of these quantities can be computed incrementally. First, if is the sample mean of the first samples, then given an additional sample, the updated sample mean is

([B.18](#eq:welford-mean))

Next, if is the sum of squares of differences from the current mean,

then consider the difference, which is the quantity that when added to gives:

After some algebraic manipulation, this can be found to be equal to

([B.18](#eq:welford-S))

which is comprised of quantities that are all readily available. The implementation of the [VarianceEstimator](#VarianceEstimator) Add() method is then just a matter of applying Equations ([B.18](#eq:welford-mean)) and ([B.18](#eq:welford-S)).

<<VarianceEstimator Public Methods>>=

void Add(Float x) { ++ [n](#VarianceEstimator::n); Float delta = x - [mean](#VarianceEstimator::mean); [mean](#VarianceEstimator::mean) += delta / [n](#VarianceEstimator::n); Float delta2 = x - [mean](#VarianceEstimator::mean); [S](#VarianceEstimator::S) += delta \* delta2; }

Given these two quantities, [VarianceEstimator](#VarianceEstimator) can provide a number of useful statistical quantities.

<<VarianceEstimator Public Methods>>+=

Float [Mean](#VarianceEstimator::Mean) () const { return [mean](#VarianceEstimator::mean); } Float [Variance](#VarianceEstimator::Variance) () const { return ([n](#VarianceEstimator::n) > 1)? [S](#VarianceEstimator::S) / ([n](#VarianceEstimator::n) - 1): 0; } Float RelativeVariance() const { return ([n](#VarianceEstimator::n) < 1 || [mean](#VarianceEstimator::mean) == 0)? 0: [Variance](#VarianceEstimator::Variance) () / [Mean](#VarianceEstimator::Mean) (); }

It is also possible to merge two [VarianceEstimator](#VarianceEstimator) s so that the result stores the same mean and variance estimates (modulo minor floating-point rounding error) as if a single [VarianceEstimator](#VarianceEstimator) had processed all the values seen by the two of them. This capability is particularly useful in parallel implementations, where separate threads may separately compute sample statistics that are merged later.

The Merge() method implements this operation, which we will not include here; see the “Further Reading” section for details of its derivation.

### B.2.12 Square Matrices

The [SquareMatrix](#SquareMatrix) class provides a representation of square matrices with dimensionality set at compile time via the template parameter N. It is an integral part of both the [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) class and pbrt ’s color space conversion code.

<<SquareMatrix Definition>>=

template <int N> class SquareMatrix { public: << [SquareMatrix Public Methods](#fragment-SquareMatrixPublicMethods-0) >>

static SquareMatrix Zero() { SquareMatrix [m](#SquareMatrix::m); for (int i = 0; i < N; ++i) for (int j = 0; j < N; ++j) [m](#SquareMatrix::m).[m](#SquareMatrix::m) \[i\]\[j\] = 0; return [m](#SquareMatrix::m); } PBRT\_CPU\_GPU SquareMatrix() { for (int i = 0; i < N; ++i) for (int j = 0; j < N; ++j) [m](#SquareMatrix::m) \[i\]\[j\] = (i == j)? 1: 0; } PBRT\_CPU\_GPU SquareMatrix(const Float mat\[N\]\[N\]) { for (int i = 0; i < N; ++i) for (int j = 0; j < N; ++j) [m](#SquareMatrix::m) \[i\]\[j\] = mat\[i\]\[j\]; } PBRT\_CPU\_GPU SquareMatrix(pstd::span<const Float> t); template <typename... Args> PBRT\_CPU\_GPU SquareMatrix(Float v, Args... args) { static\_assert(1 + sizeof...(Args) == N \* N, "Incorrect number of values provided to SquareMatrix constructor"); init<N>([m](#SquareMatrix::m), 0, 0, v, args...); } template <typename... Args> PBRT\_CPU\_GPU static SquareMatrix Diag(Float v, Args... args) { static\_assert(1 + sizeof...(Args) == N, "Incorrect number of values provided to SquareMatrix::Diag"); SquareMatrix [m](#SquareMatrix::m); initDiag<N>([m](#SquareMatrix::m).[m](#SquareMatrix::m), 0, v, args...); return [m](#SquareMatrix::m); } SquareMatrix operator+(const SquareMatrix & [m](#SquareMatrix::m)) const { SquareMatrix r = \*this; for (int i = 0; i < N; ++i) for (int j = 0; j < N; ++j) r.[m](#SquareMatrix::m) \[i\]\[j\] += [m](#SquareMatrix::m).[m](#SquareMatrix::m) \[i\]\[j\]; return r; } PBRT\_CPU\_GPU SquareMatrix operator\*(Float s) const { SquareMatrix r = \*this; for (int i = 0; i < N; ++i) for (int j = 0; j < N; ++j) r.[m](#SquareMatrix::m) \[i\]\[j\] \*= s; return r; } PBRT\_CPU\_GPU SquareMatrix operator/(Float s) const { DCHECK\_NE(s, 0); SquareMatrix r = \*this; for (int i = 0; i < N; ++i) for (int j = 0; j < N; ++j) r.[m](#SquareMatrix::m) \[i\]\[j\] /= s; return r; } PBRT\_CPU\_GPU bool operator==(const SquareMatrix<N> &m2) const { for (int i = 0; i < N; ++i) for (int j = 0; j < N; ++j) if ([m](#SquareMatrix::m) \[i\]\[j\]!= m2.[m](#SquareMatrix::m) \[i\]\[j\]) return false; return true; } PBRT\_CPU\_GPU bool operator!=(const SquareMatrix<N> &m2) const { for (int i = 0; i < N; ++i) for (int j = 0; j < N; ++j) if ([m](#SquareMatrix::m) \[i\]\[j\]!= m2.[m](#SquareMatrix::m) \[i\]\[j\]) return true; return false; } PBRT\_CPU\_GPU bool operator<(const SquareMatrix<N> &m2) const { for (int i = 0; i < N; ++i) for (int j = 0; j < N; ++j) { if ([m](#SquareMatrix::m) \[i\]\[j\] < m2.[m](#SquareMatrix::m) \[i\]\[j\]) return true; if ([m](#SquareMatrix::m) \[i\]\[j\] > m2.[m](#SquareMatrix::m) \[i\]\[j\]) return false; } return false; } bool IsIdentity() const; std::string ToString() const; pstd::span<const Float> operator\[\](int i) const { return [m](#SquareMatrix::m) \[i\]; } pstd::span<Float> operator\[\](int i) { return pstd::span<Float>([m](#SquareMatrix::m) \[i\]); }

private: Float m\[N\]\[N\]; };

The default constructor initializes the identity matrix. Other constructors (not included here) allow providing the values of the matrix directly or via a two-dimensional array of values. Alternatively, Zero() can be used to get a zero-valued matrix or Diag() can be called with N values to get the corresponding diagonal matrix.

<<SquareMatrix Public Methods>>=

static SquareMatrix Zero() { SquareMatrix [m](#SquareMatrix::m); for (int i = 0; i < N; ++i) for (int j = 0; j < N; ++j) [m](#SquareMatrix::m).[m](#SquareMatrix::m) \[i\]\[j\] = 0; return [m](#SquareMatrix::m); }

All the basic arithmetic operations between matrices are provided, including multiplying them or dividing them by scalar values. Here is the implementation of the method that adds two matrices together.

SquareMatrix operator+(const SquareMatrix & [m](#SquareMatrix::m)) const { SquareMatrix r = \*this; for (int i = 0; i < N; ++i) for (int j = 0; j < N; ++j) r.[m](#SquareMatrix::m) \[i\]\[j\] += [m](#SquareMatrix::m).[m](#SquareMatrix::m) \[i\]\[j\]; return r; }

The IsIdentity() checks whether the matrix is the identity matrix via a simple loop over its elements.

bool IsIdentity() const;

Indexing operators are provided as well. Because these methods return span s, the syntax for multidimensional indexing is the same as it is for regular C++ arrays: m\[i\]\[j\].

<<SquareMatrix Public Methods>>+=

pstd::span<const Float> operator\[\](int i) const { return m\[i\]; } pstd::span<Float> operator\[\](int i) { return pstd::span<Float>(m\[i\]); }

The [SquareMatrix](#SquareMatrix) class provides a matrix–vector multiplication function based on template classes to define the types of both the vector that is operated on and the result. It only requires that the result type has a default constructor and that both types allow element indexing via operator\[\]. Thus it can, for example, be used in pbrt ’s color space conversion code to convert from [RGB](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGB) to [XYZ](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#XYZ) via a call of the form Mul<XYZ>(m, rgb), where m is a SquareMatrix and rgb is of type RGB.

<<SquareMatrix Inline Functions>>=

template <typename Tresult, int N, typename T> Tresult Mul(const SquareMatrix<N> &m, const T &v) { Tresult result; for (int i = 0; i < N; ++i) { result\[i\] = 0; for (int j = 0; j < N; ++j) result\[i\] += m\[i\]\[j\] \* v\[j\]; } return result; }

The Determinant() function returns the value of the matrix’s determinant using the standard formula. Specializations for and matrices are carefully written to use [DifferenceOfProducts()](#DifferenceOfProducts) for intermediate calculations of matrix minors in order to maximize accuracy in the result for those common cases.

template <int N> Float Determinant(const SquareMatrix<N> &m);

Finally, there are both Transpose() and Inverse() functions. Like Determinant(), Inverse() has specializations for N up to 4 and then a general implementation for matrices of larger dimensionality.

template <int N> SquareMatrix<N> Transpose(const SquareMatrix<N> &m); template <int N> pstd::optional<SquareMatrix<N>> Inverse(const SquareMatrix<N> &);

The regular Inverse() function returns an unset optional value if the matrix has no inverse. If no recovery is possible in that case, InvertOrExit() can be used, allowing calling code to directly access the matrix result.

<<SquareMatrix Inline Functions>>+=

template <int N> SquareMatrix<N> InvertOrExit(const SquareMatrix<N> &m) { pstd::optional<SquareMatrix<N>> inv = [Inverse](#SquareMatrix::Inverse) (m); CHECK(inv.has\_value()); return \*inv; }

Given the SquareMatrix definition, it is easy to implement a LinearLeastSquares() function that finds a matrix that minimizes the least squares error of a mapping from one set of vectors to another. This function is used as part of pbrt ’s infrastructure for modeling camera response curves.

<<Math Inline Functions>>+=

template <int N> pstd::optional<SquareMatrix<N>> LinearLeastSquares(const Float A\[\]\[N\], const Float B\[\]\[N\], int rows);

### B.2.13 Bézier Curves

Bézier curves, first introduced in Section [6.7](https://pbr-book.org/4ed/Shapes/Curves.html#sec:curves) with the [Curve](https://pbr-book.org/4ed/Shapes/Curves.html#Curve) shape, are polynomial functions that are widely used in graphics. They are specified by a number of control points that have the useful property that the curve passes through the first and last of them. Cubic Béziers, which are specified by four control points, are commonly used. pbrt ’s functions for working with them are defined in the file [util/splines.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/splines.h).

They are commonly defined using polynomial basis functions called the *Bernstein basis functions*, though here we will focus on them through an approach called *blossoming*. The blossom of a cubic Bézier is defined by three stages of linear interpolation, starting with the original control points:

([B.18](#eq:blossoming))

BlossomBezier() implements this computation, which we will see has a variety of uses. The type P of the control point is a template parameter, which makes it possible to call this function with any type for which a Lerp() function is defined.

<<Bezier Inline Functions>>=

template <typename P> P BlossomCubicBezier(pstd::span<const P> p, Float u0, Float u1, Float u2) { P a\[3\] = { [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (u0, p\[0\], p\[1\]), [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (u0, p\[1\], p\[2\]), [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (u0, p\[2\], p\[3\]) }; P b\[2\] = { [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (u1, a\[0\], a\[1\]), [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (u1, a\[1\], a\[2\]) }; return [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (u2, b\[0\], b\[1\]); }

The blossom gives the curve’s value at position. (To verify this for yourself, expand Equation ([B.18](#eq:blossoming)) using, simplify, and compare to Equation ([6.16](https://pbr-book.org/4ed/Shapes/Curves.html#eq:bezier-basic)).) Thus, implementation of the EvaluateCubicBezier() function is trivial. It too is a template function of the type of control point.

template <typename P> P EvaluateCubicBezier(pstd::span<const P> cp, Float u) { return [BlossomCubicBezier](#BlossomCubicBezier) (cp, u, u, u); }

A second variant of EvaluateCubicBezier() also optionally returns the curve’s derivative at the evaluation point. This and the following Bézier functions could also be template functions based on the type of control point; for pbrt ’s uses, however, only [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) variants are required. We therefore implement them in terms of Point3f, if only to save the verbosity and slight obscurity of the templated variants.

[Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) EvaluateCubicBezier(pstd::span<const [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) > cp, Float u, [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) \*deriv) { [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) cp1\[3\] = { [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (u, cp\[0\], cp\[1\]), [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (u, cp\[1\], cp\[2\]), [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (u, cp\[2\], cp\[3\]) }; [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) cp2\[2\] = { [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (u, cp1\[0\], cp1\[1\]), [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (u, cp1\[1\], cp1\[2\]) }; if (deriv) { << [Compute Bézier curve derivative at](#fragment-ComputeBeziercurvederivativeatu-0) >>

if ([LengthSquared](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#LengthSquared) (cp2\[1\] - cp2\[0\]) > 0) \*deriv = 3 \* (cp2\[1\] - cp2\[0\]); else \*deriv = cp\[3\] - cp\[0\];

} return [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (u, cp2\[0\], cp2\[1\]); }

With blossoming, the final two control points that are linearly interpolated to compute the curve value define a line that is tangent to the curve.

One edge case must be handled here: if, for example, the first three control points are coincident, then the derivative of the curve is legitimately 0 at. However, returning a zero-valued derivative in that case would be problematic since pbrt uses the derivative to compute the tangent vector of the curve. Therefore, this function returns the difference between the first and last control points in such cases.

<<Compute Bézier curve derivative at >>=

if ([LengthSquared](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#LengthSquared) (cp2\[1\] - cp2\[0\]) > 0) \*deriv = 3 \* (cp2\[1\] - cp2\[0\]); else \*deriv = cp\[3\] - cp\[0\];

SubdivideCubicBezier() splits a Bézier curve into two Bézier curves that together are equivalent to the original curve. The last control point of the first subdivided curve is the same as the first control point of the second one and the 7 total control points are specified by the blossoms,,,,,, and. There is no need to call [BlossomCubicBezier()](#BlossomCubicBezier) to evaluate them, however, as each one works out to be a simple combination of existing control points.

pstd::array< [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f), 7> SubdivideCubicBezier(pstd::span<const [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) > cp) { return {cp\[0\], (cp\[0\] + cp\[1\]) / 2, (cp\[0\] + 2 \* cp\[1\] + cp\[2\]) / 4, (cp\[0\] + 3 \* cp\[1\] + 3 \* cp\[2\] + cp\[3\]) / 8, (cp\[1\] + 2 \* cp\[2\] + cp\[3\]) / 4, (cp\[2\] + cp\[3\]) / 2, cp\[3\]}; }

![](https://pbr-book.org/4ed/Utilities/phabbf05.png)

Figure B.5: Blossoming to Find Control Points for a Segment of a Bézier Curve. The four blossoms in Equation ([B.18](#eq:blossom-4-cp)) give the control points for the curve from to. Blossoming provides an elegant method to compute the Bézier control points of the curve that represent a subset of the overall curve.

More generally, the four control points for the curve segment over the range to are given by the blossoms:

([B.18](#eq:blossom-4-cp))

(see Figure [B.5](#fig:bezier-blossom)). CubicBezierControlPoints() implements this computation.

pstd::array< [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f), 4> CubicBezierControlPoints(pstd::span<const [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) > cp, Float uMin, Float uMax) { return { BlossomCubicBezier(cp, uMin, uMin, uMin), BlossomCubicBezier(cp, uMin, uMin, uMax), BlossomCubicBezier(cp, uMin, uMax, uMax), BlossomCubicBezier(cp, uMax, uMax, uMax) }; }

Bounding boxes of Curve s can be efficiently computed by taking advantage of the *convex hull property*, a property of Bézier curves that says that they must lie within the convex hull of their control points. Therefore, the bounding box of the control points gives a conservative bound of the underlying curve. This bounding box is returned by the [BoundCubicBezier()](#BoundCubicBezier) function.

[Bounds3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds3f) BoundCubicBezier(pstd::span<const [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) > cp) { return [Union](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds3::Union) ([Bounds3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds3f) (cp\[0\], cp\[1\]), [Bounds3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds3f) (cp\[2\], cp\[3\])); }

A second variant of this function bounds a Bézier curve over a specified parametric range via a call to CubicBezierControlPoints().

<<Bezier Inline Functions>>+=

[Bounds3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds3f) [BoundCubicBezier](#BoundCubicBezier) (pstd::span<const [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) > cp, Float uMin, Float uMax) { if (uMin == 0 && uMax == 1) return [BoundCubicBezier](#BoundCubicBezier) (cp); auto cpSeg = [CubicBezierControlPoints](#CubicBezierControlPoints) (cp, uMin, uMax); return [BoundCubicBezier](#BoundCubicBezier) (pstd::span<const [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) >(cpSeg)); }

### B.2.14 Pseudo-Random Number Generation

pbrt uses an implementation of the PCG pseudo-random number generator (O’Neill [2014](https://pbr-book.org/4ed/Utilities/Further_Reading.html#cite:ONeill2014)) to generate pseudo-random numbers. This generator not only passes a variety of rigorous statistical tests of randomness, but its implementation is also extremely efficient.

We wrap its implementation in a small random number generator class, RNG, which can be found in the files [util/rng.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/rng.h) and [util/rng.cpp](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/rng.cpp). Random number generator implementation is an esoteric art; therefore, we will not include or discuss the implementation here but will describe the interfaces provided.

<<RNG Definition>>=

class RNG { public: << [RNG Public Methods](#fragment-RNGPublicMethods-0) >>

[RNG](#RNG) (): state(PCG32\_DEFAULT\_STATE), inc(PCG32\_DEFAULT\_STREAM) {} [RNG](#RNG) (uint64\_t seqIndex, uint64\_t offset) { [SetSequence](#RNG::SetSequence) (seqIndex, offset); } [RNG](#RNG) (uint64\_t seqIndex) { [SetSequence](#RNG::SetSequence) (seqIndex); } void [SetSequence](#RNG::SetSequence) (uint64\_t sequenceIndex, uint64\_t offset); void [SetSequence](#RNG::SetSequence) (uint64\_t sequenceIndex) { [SetSequence](#RNG::SetSequence) (sequenceIndex, [MixBits](#MixBits) (sequenceIndex)); } template <typename T> T Uniform(); template <typename T> typename std::enable\_if\_t<std::is\_integral\_v<T>, T> Uniform(T b) { T threshold = (~b + 1u) % b; while (true) { T r = Uniform<T>(); if (r >= threshold) return r % b; } } void Advance(int64\_t idelta); std::string ToString() const;

private: <<RNG Private Members>>

uint64\_t state, inc;

};

The RNG class provides three constructors. The first, which takes no arguments, sets the internal state to reasonable defaults. The others allow providing values that seed its state. The PCG random number generator actually allows the user to provide two 64-bit values to configure its operation: one chooses from one of different sequences of random numbers, while the second effectively selects a starting point within such a sequence. Many pseudo-random number generators only allow this second form of configuration, which alone is not as useful: having independent non-overlapping sequences of values rather than different starting points in a single sequence provides greater nonuniformity in the generated values.

<<RNG Public Methods>>=

[RNG](#RNG) (): state(PCG32\_DEFAULT\_STATE), inc(PCG32\_DEFAULT\_STREAM) {} [RNG](#RNG) (uint64\_t seqIndex, uint64\_t offset) { [SetSequence](#RNG::SetSequence) (seqIndex, offset); } [RNG](#RNG) (uint64\_t seqIndex) { [SetSequence](#RNG::SetSequence) (seqIndex); }

RNG s should not be used in pbrt without either providing an initial sequence index via the constructor or a call to the SetSequence() method; otherwise, there is risk that different parts of the system will inadvertently use correlated sequences of pseudo-random values, which in turn could cause surprising errors.

void [SetSequence](#RNG::SetSequence) (uint64\_t sequenceIndex, uint64\_t offset); void [SetSequence](#RNG::SetSequence) (uint64\_t sequenceIndex) { [SetSequence](#RNG::SetSequence) (sequenceIndex, [MixBits](#MixBits) (sequenceIndex)); }

The RNG class defines a template method Uniform() that returns a uniformly distributed random value of the specified type. A variety of specializations of this method are provided for basic arithmetic types.

template <typename T> T Uniform();

The default implementation of Uniform() attempts to ensure that a useful error message is issued if it is invoked with an unsupported type.

<<RNG Inline Method Definitions>>=

template <typename T> T [RNG](#RNG)::Uniform() { return T::unimplemented; }

A specialization for uint32\_t uses the PCG algorithm to generate a 32-bit value. We will not include its implementation here, as it would be impenetrable without an extensive discussion of the details of the pseudo-random number generation approach it implements.

<<RNG Inline Method Definitions>>+=

template <> uint32\_t [RNG](#RNG)::Uniform<uint32\_t>();

Given a source of pseudo-randomness, a variety of other specializations of Uniform() can be provided. For example, a uniform 64-bit unsigned integer can be generated by using the bits from two 32-bit random numbers.

<<RNG Inline Method Definitions>>+=

template <> uint64\_t [RNG](#RNG)::Uniform<uint64\_t>() { uint64\_t v0 = Uniform<uint32\_t>(), v1 = Uniform<uint32\_t>(); return (v0 << 32) | v1; }

Generating a uniformly distributed signed 32-bit integer requires surprisingly tricky code. The issue is that in C++, it is undefined behavior to assign a value to a signed integer that is larger than it can represent. Undefined behavior does not just mean that the result is undefined, but that, in principle, no further guarantees are made about correct program execution after it occurs. Therefore, the following code is carefully written to avoid integer overflow. In practice, a good compiler can be expected to optimize away the extra work.

<<RNG Inline Method Definitions>>+=

template <> int32\_t [RNG](#RNG)::Uniform<int32\_t>() { uint32\_t v = Uniform<uint32\_t>(); if (v <= (uint32\_t)std::numeric\_limits<int32\_t>::max()) return int32\_t(v); return int32\_t(v - std::numeric\_limits<int32\_t>::min()) + std::numeric\_limits<int32\_t>::min(); }

A similar method returns pseudo-random int64\_t values.

It is often useful to generate a value that is uniformly distributed in the range given a bound. The first two versions of pbrt effectively computed Uniform<int32\_t>() % b to do so. That approach is subtly flawed—in the case that b does not evenly divide, there is higher probability of choosing any given value in the sub-range.

Therefore, the implementation here first computes the above remainder efficiently using 32-bit arithmetic and stores it in the variable threshold. Then, if the value returned by Uniform() is less than threshold, it is discarded and a new value is generated. The resulting distribution of values has a uniform distribution after the modulus operation, giving a uniformly distributed sample value.

The tricky declaration of the return value ensures that this variant of Uniform() is only available for integral types.

template <typename T> typename std::enable\_if\_t<std::is\_integral\_v<T>, T> Uniform(T b) { T threshold = (~b + 1u) % b; while (true) { T r = Uniform<T>(); if (r >= threshold) return r % b; } }

A specialization of Uniform() for float s generates a pseudo-random floating-point number in the half-open interval by multiplying a 32-bit random value by. Mathematically, this value is always less than one; it can be at most. However, some values still round to 1 when computed using floating-point arithmetic. That case is handled here by clamping to the largest representable float less than one. Doing so introduces a tiny bias, but not one that is meaningful for rendering applications.

<<RNG Inline Method Definitions>>+=

template <> float [RNG](#RNG)::Uniform<float>() { return std::min<float>([OneMinusEpsilon](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#OneMinusEpsilon), Uniform<uint32\_t>() \* 0x1p-32f); }

An equivalent method for double s is provided but is not included here.

With this random number generator, it is possible to step forward or back to a different spot in the sequence without generating all the intermediate values. The Advance() method provides this functionality.

<<RNG Public Methods>>+=

void Advance(int64\_t idelta);

### B.2.15 Interval Arithmetic

Interval arithmetic is a technique that can be used to reason about the range of a function over some range of values and also to bound the round-off error introduced by a series of floating-point calculations. The Interval class provides functionality for both of these uses.

To understand the basic idea of interval arithmetic, consider, for example, the function. If we have an interval of values, then we can see that, over the interval, the range of is the interval. In other words,. More generally, all the basic operations of arithmetic have *interval extensions* that describe how they operate on intervals of values. For example, given two intervals and,

In other words, if we add together two values where one is in the range and the second is in, then the result must be in the range. Interval arithmetic has the important property that the intervals that it gives are conservative. For example, if and if, then we know for sure that no value in causes to be negative.

When implemented in floating-point arithmetic, interval operations can be defined so that they result in intervals that bound the true value. Given a function that rounds a value that cannot exactly be represented as a floating-point value down to the next lower floating-point value and that similarly rounds up, interval addition can be defined as

([B.18](#eq:interval-fp-addition))

Performing a series of floating-point calculations in this manner is the basis of running error analysis, which was described in Section [6.8.1](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#sec:ieee-fp).

pbrt uses interval arithmetic to compute error bounds for ray intersections with quadrics and also uses the interval-based Point3i class to store computed ray intersection points on surfaces. The zero-finding method used to find the extrema of moving bounding boxes in AnimatedTransform::BoundPointMotion() (included in the online edition) is also based on interval arithmetic.

The Interval class provides interval arithmetic capabilities using operator overloading to make it fairly easy to switch existing regular floating-point computations over to be interval-based.

<<Interval Definition>>=

class Interval { public: << [Interval Public Methods](#fragment-IntervalPublicMethods-0) >>

explicit Interval(Float v): [low](#Interval::low) (v), [high](#Interval::high) (v) {} Interval(Float [low](#Interval::low), Float [high](#Interval::high)): [low](#Interval::low) (std::min([low](#Interval::low), [high](#Interval::high))), [high](#Interval::high) (std::max([low](#Interval::low), [high](#Interval::high))) {} static Interval FromValueAndError(Float v, Float err) { Interval i; if (err == 0) i.[low](#Interval::low) = i.[high](#Interval::high) = v; else { i.[low](#Interval::low) = SubRoundDown(v, err); i.[high](#Interval::high) = [AddRoundUp](#AddRoundUp) (v, err); } return i; } PBRT\_CPU\_GPU Interval &operator=(Float v) { [low](#Interval::low) = [high](#Interval::high) = v; return \*this; } Float UpperBound() const { return [high](#Interval::high); } Float LowerBound() const { return [low](#Interval::low); } Float [Midpoint](#Interval::Midpoint) () const { return ([low](#Interval::low) + [high](#Interval::high)) / 2; } Float Width() const { return [high](#Interval::high) - [low](#Interval::low); } Float operator\[\](int i) const { return (i == 0)? [low](#Interval::low): [high](#Interval::high); } explicit operator Float() const { return [Midpoint](#Interval::Midpoint) (); } bool Exactly(Float v) const { return [low](#Interval::low) == v && [high](#Interval::high) == v; } PBRT\_CPU\_GPU bool operator==(Float v) const { return Exactly(v); } Interval operator-() const { return {- [high](#Interval::high), - [low](#Interval::low) }; } Interval operator+(Interval i) const { return { [AddRoundDown](#AddRoundDown) ([low](#Interval::low), i.[low](#Interval::low)), [AddRoundUp](#AddRoundUp) ([high](#Interval::high), i.[high](#Interval::high))}; } PBRT\_CPU\_GPU Interval operator-(Interval i) const { return {SubRoundDown([low](#Interval::low), i.[high](#Interval::high)), SubRoundUp([high](#Interval::high), i.[low](#Interval::low))}; } Interval operator\*(Interval i) const { Float lp\[4\] = { [MulRoundDown](#MulRoundDown) ([low](#Interval::low), i.[low](#Interval::low)), [MulRoundDown](#MulRoundDown) ([high](#Interval::high), i.[low](#Interval::low)), [MulRoundDown](#MulRoundDown) ([low](#Interval::low), i.[high](#Interval::high)), [MulRoundDown](#MulRoundDown) ([high](#Interval::high), i.[high](#Interval::high))}; Float hp\[4\] = { [MulRoundUp](#MulRoundUp) ([low](#Interval::low), i.[low](#Interval::low)), [MulRoundUp](#MulRoundUp) ([high](#Interval::high), i.[low](#Interval::low)), [MulRoundUp](#MulRoundUp) ([low](#Interval::low), i.[high](#Interval::high)), [MulRoundUp](#MulRoundUp) ([high](#Interval::high), i.[high](#Interval::high))}; return {std::min({lp\[0\], lp\[1\], lp\[2\], lp\[3\]}), std::max({hp\[0\], hp\[1\], hp\[2\], hp\[3\]})}; } PBRT\_CPU\_GPU Interval operator/(Interval i) const; PBRT\_CPU\_GPU bool operator==(Interval i) const { return [low](#Interval::low) == i.[low](#Interval::low) && [high](#Interval::high) == i.[high](#Interval::high); } PBRT\_CPU\_GPU bool operator!=(Float f) const { return f < [low](#Interval::low) || f > [high](#Interval::high); } std::string ToString() const; PBRT\_CPU\_GPU Interval &operator+=(Interval i) { \*this = Interval(\*this + i); return \*this; } PBRT\_CPU\_GPU Interval &operator-=(Interval i) { \*this = Interval(\*this - i); return \*this; } PBRT\_CPU\_GPU Interval &operator\*=(Interval i) { \*this = Interval(\*this \* i); return \*this; } PBRT\_CPU\_GPU Interval &operator/=(Interval i) { \*this = Interval(\*this / i); return \*this; } PBRT\_CPU\_GPU Interval &operator+=(Float f) { return \*this += Interval(f); } PBRT\_CPU\_GPU Interval &operator-=(Float f) { return \*this -= Interval(f); } PBRT\_CPU\_GPU Interval &operator\*=(Float f) { if (f > 0) \*this = Interval([MulRoundDown](#MulRoundDown) (f, [low](#Interval::low)), [MulRoundUp](#MulRoundUp) (f, [high](#Interval::high))); else \*this = Interval([MulRoundDown](#MulRoundDown) (f, [high](#Interval::high)), [MulRoundUp](#MulRoundUp) (f, [low](#Interval::low))); return \*this; } PBRT\_CPU\_GPU Interval &operator/=(Float f) { if (f > 0) \*this = Interval(DivRoundDown([low](#Interval::low), f), DivRoundUp([high](#Interval::high), f)); else \*this = Interval(DivRoundDown([high](#Interval::high), f), DivRoundUp([low](#Interval::low), f)); return \*this; } #ifndef PBRT\_IS\_GPU\_CODE static const Interval Pi; #endif

private: << [Interval Private Members](#fragment-IntervalPrivateMembers-0) >>

Float low, high;

};

Before we go further with Interval, we will define some supporting utility functions for performing basic arithmetic with specified rounding. Recall that the default with floating-point arithmetic is that results are rounded to the nearest representable floating-point value, with ties being rounded to the nearest even value (i.e., with a zero-valued low bit in its significand). However, in order to compute conservative intervals like those in Equation ([B.18](#eq:interval-fp-addition)), it is necessary to specify different rounding modes for different operations, rounding down when computing the value at the lower range of the interval and rounding up at the upper range.

The IEEE floating-point standard requires capabilities to control the rounding mode, but unfortunately it is expensive to change it on modern CPUs. Doing so generally requires a flush of the execution pipeline, which may cost many tens of cycles. Therefore, pbrt provides utility functions that perform various arithmetic operations where the final value is then nudged up or down to the next representable float. This will lead to intervals that are slightly too large, sometimes nudging when it is not necessary, but for pbrt ’s purposes it is preferable to paying the cost of changing the rounding mode.

Some GPUs provide intrinsic functions to perform these various operations directly, with the rounding mode specified as part of the instruction and with no performance cost. Alternative implementations of these functions, not included here, use those when they are available.

<<Floating-point Inline Functions>>+=

Float AddRoundUp(Float a, Float b) { return [NextFloatUp](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#NextFloatUp) (a + b); } Float AddRoundDown(Float a, Float b) { return [NextFloatDown](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#NextFloatDown) (a + b); }

Beyond addition, there are equivalent methods that are not included here for subtraction, multiplication, division, the square root, and FMA.

An interval can be initialized with a single value or a pair of values that specify an interval with nonzero width.

<<Interval Public Methods>>=

<<Interval Private Members>>=

Float low, high;

It can also be specified by a value and an error bound. Note that the implementation uses rounded arithmetic functions to ensure a conservative interval.

static Interval FromValueAndError(Float v, Float err) { Interval i; if (err == 0) i.[low](#Interval::low) = i.[high](#Interval::high) = v; else { i.[low](#Interval::low) = SubRoundDown(v, err); i.[high](#Interval::high) = AddRoundUp(v, err); } return i; }

A number of accessor methods provide information about the interval. An implementation of operator\[\], not included here, allows indexing the two bounding values.

Float UpperBound() const { return [high](#Interval::high); } Float LowerBound() const { return [low](#Interval::low); } Float Midpoint() const { return ([low](#Interval::low) + [high](#Interval::high)) / 2; } Float Width() const { return [high](#Interval::high) - [low](#Interval::low); }

An interval can be converted to a Float approximation to it, but only through an explicit cast, which ensures that intervals are not accidentally reduced to Float s in the middle of a computation, thus causing an inaccurate final interval.

explicit operator Float() const { return [Midpoint](#Interval::Midpoint) (); }

InRange() method implementations check whether a given value is in the interval and whether two intervals overlap.

<<Interval Inline Functions>>=

bool InRange(Float v, Interval i) { return v >= i.[LowerBound](#Interval::LowerBound) () && v <= i.[UpperBound](#Interval::UpperBound) (); } bool InRange(Interval a, Interval b) { return a.[LowerBound](#Interval::LowerBound) () <= b.[UpperBound](#Interval::UpperBound) () && a.[UpperBound](#Interval::UpperBound) () >= b.[LowerBound](#Interval::LowerBound) (); }

Negation of an interval is straightforward, as it does not require any rounding.

Interval operator-() const { return {- [high](#Interval::high), - [low](#Interval::low) }; }

The addition operator just requires implementing Equation ([B.18](#eq:interval-fp-addition)) with the appropriate rounding.

The subtraction operator and the += and \-= operators follow the same pattern, so they are not included in the text.

Interval multiplication and division are slightly more involved: which of the low and high bounds of each of the two intervals is used to determine each of the bounds of the result depends on the signs of the values involved. Rather than incur the overhead of working out exactly which pairs to use, [Interval](#Interval) ’s implementation of the multiply operator computes all of them and then takes the minimum and maximum.

<<Interval Public Methods>>+=

Interval operator\*(Interval i) const { Float lp\[4\] = { [MulRoundDown](#MulRoundDown) ([low](#Interval::low), i.[low](#Interval::low)), [MulRoundDown](#MulRoundDown) ([high](#Interval::high), i.[low](#Interval::low)), [MulRoundDown](#MulRoundDown) ([low](#Interval::low), i.[high](#Interval::high)), [MulRoundDown](#MulRoundDown) ([high](#Interval::high), i.[high](#Interval::high))}; Float hp\[4\] = { [MulRoundUp](#MulRoundUp) ([low](#Interval::low), i.[low](#Interval::low)), [MulRoundUp](#MulRoundUp) ([high](#Interval::high), i.[low](#Interval::low)), [MulRoundUp](#MulRoundUp) ([low](#Interval::low), i.[high](#Interval::high)), [MulRoundUp](#MulRoundUp) ([high](#Interval::high), i.[high](#Interval::high))}; return {std::min({lp\[0\], lp\[1\], lp\[2\], lp\[3\]}), std::max({hp\[0\], hp\[1\], hp\[2\], hp\[3\]})}; }

The division operator follows a similar form, though it must check to see if the divisor interval spans zero. If so, an infinite interval must be returned.

The interval Sqr() function is more than a shorthand; it is sometimes able to compute a tighter bound than would be found by multiplying an interval by itself using operator\*. To see why, consider two independent intervals that both happen to have the range. Multiplying them together results in the interval. However, if we are multiplying an interval by itself, we know that there is no way that squaring it would result in a negative value. Therefore, if an interval with the bounds is multiplied by itself, it is possible to return the tighter interval instead.

<<Interval Inline Functions>>+=

Interval Sqr(Interval i) { Float alow = std::abs(i.[LowerBound](#Interval::LowerBound) ()), ahigh = std::abs(i.[UpperBound](#Interval::UpperBound) ()); if (alow > ahigh) pstd::swap(alow, ahigh); if ([InRange](#Interval::InRange) (0, i)) return Interval(0, [MulRoundUp](#MulRoundUp) (ahigh, ahigh)); return Interval([MulRoundDown](#MulRoundDown) (alow, alow), [MulRoundUp](#MulRoundUp) (ahigh, ahigh)); }

A variety of additional arithmetic operations are provided by the Interval class, including Abs(), Min(), Max(), Sqrt(), Floor(), Ceil(), Quadratic(), and assorted trigonometric functions. See the pbrt source code for their implementations.

pbrt provides 3D vector and point classes that use [Interval](#Interval) for the coordinate values. Here, the “fi” at the end of [Vector3fi](#Vector3fi) denotes “float interval.” These classes are easily defined thanks to the templated definition of the [Vector3](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3) and [Point3](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3) classes and the underlying [Tuple3](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple3) class from Section [3.2](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#sec:ntuple).

<<Vector3fi Definition>>=

class Vector3fi: public Vector3<Interval> { public: << [Vector3fi Public Methods](#fragment-Vector3fiPublicMethods-0) >>

using Vector3<Interval>::x; using Vector3<Interval>::y; using Vector3<Interval>::z; using Vector3<Interval>::HasNaN; using Vector3<Interval>::operator+; using Vector3<Interval>::operator+=; using Vector3<Interval>::operator\*; using Vector3<Interval>::operator\*=; Vector3fi(Float x, Float y, Float z): Vector3<Interval>(Interval(x), Interval(y), Interval(z)) {} Vector3fi(Interval x, Interval y, Interval z): Vector3<Interval>(x, y, z) {} Vector3fi([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) p): Vector3<Interval>(Interval(p.x), Interval(p.y), Interval(p.z)) {} template <typename T> PBRT\_CPU\_GPU explicit Vector3fi(Point3<T> p): Vector3<Interval>(Interval(p.x), Interval(p.y), Interval(p.z)) {} PBRT\_CPU\_GPU Vector3fi(Vector3<Interval> pfi): Vector3<Interval>(pfi) {} Vector3fi([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) v, [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) e): Vector3<Interval>(Interval::FromValueAndError(v.x, e.x), Interval::FromValueAndError(v.y, e.y), Interval::FromValueAndError(v.z, e.z)) {} [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) Error() const { return {x.Width() / 2, y.Width() / 2, z.Width() / 2}; } bool IsExact() const { return x.Width() == 0 && y.Width() == 0 && z.Width() == 0; }

};

In addition to the usual constructors, Vector3fi can be initialized by specifying a base vector and a second one that gives error bounds for each component.

<<Vector3fi Public Methods>>=

Vector3fi([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) v, [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) e): Vector3<Interval>(Interval::FromValueAndError(v.x, e.x), Interval::FromValueAndError(v.y, e.y), Interval::FromValueAndError(v.z, e.z)) {}

Helper methods return error bounds for the vector components and indicate if the value stored has empty intervals.

<<Vector3fi Public Methods>>+=

[Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) Error() const { return {x.Width() / 2, y.Width() / 2, z.Width() / 2}; } bool IsExact() const { return x.Width() == 0 && y.Width() == 0 && z.Width() == 0; }

The Point3fi class, not included here, similarly provides the capabilities of a [Point3](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3) using intervals for its coordinate values. It, too, provides Error() and IsExact() methods.