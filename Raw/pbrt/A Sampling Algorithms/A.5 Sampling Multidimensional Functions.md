---
title: "Sampling Multidimensional Functions"
source: "https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_Multidimensional_Functions"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## A.5 Sampling Multidimensional Functions
> ## A.5 多维函数采样

Multidimensional sampling is also common in pbrt, most frequently when sampling points on the surfaces of shapes and sampling directions after scattering at points.
> 多维采样在 pbrt 中也很常见，最常用于在形状表面上采样点以及在散射点后采样方向。 This section therefore works through the derivations and implementations of algorithms for sampling in a number of useful multidimensional domains. Some of them involve separable PDFs where each dimension can be sampled independently, while others use the approach of sampling from marginal and conditional density functions that was introduced in Section [2.4.2](https://pbr-book.org/4ed/Monte_Carlo_Integration/Transforming_between_Distributions.html#sec:mc-multidimensional-sampling).

### A.5.1 Sampling a Unit Disk

Uniformly sampling a unit disk can be tricky because it has an incorrect intuitive solution. The wrong approach is the seemingly obvious one of sampling its polar coordinates uniformly:. Although the resulting point is both random and inside the disk, it is *not* uniformly distributed; it actually clumps samples near the center of the disk. Figure [A.6](#fig:disk-sample-good-bad) (a) shows a plot of samples on the unit disk when this mapping was used for a set of uniform random samples. Figure [A.6](#fig:disk-sample-good-bad) (b) shows uniformly distributed samples resulting from the following correct approach.

[![](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf06.svg)](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf06.svg)

Figure A.6: (a) When the obvious but incorrect mapping of uniform random variables to points on the disk is used, the resulting distribution is not uniform and the samples are more likely to be near the center of the disk. (b) The correct mapping gives a uniform distribution of points.

Since we would like to sample uniformly with respect to area, the PDF must be a constant. By the normalization constraint,. If we transform into polar coordinates, we have given the relationship between probability densities in Cartesian coordinates and polar coordinates that was derived in Section [2.4.1](https://pbr-book.org/4ed/Monte_Carlo_Integration/Transforming_between_Distributions.html#sec:mc-transform-multiple-dimensions), Equation ([2.22](https://pbr-book.org/4ed/Monte_Carlo_Integration/Transforming_between_Distributions.html#eq:polar-cartesian-pdf-relation)).

We can now compute the marginal and conditional densities:

The fact that is a constant should make sense because of the symmetry of the disk. Integrating and inverting to find,,, and, we can find that the correct solution to generate uniformly distributed samples on a disk is

Taking the square root of effectively pushes the samples back toward the edge of the disk, counteracting the clumping referred to earlier.

[Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) SampleUniformDiskPolar([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) u) { Float r = std::sqrt(u\[0\]); Float theta = 2 \* [Pi](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Pi) \* u\[1\]; return {r \* std::cos(theta), r \* std::sin(theta)}; }

The inversion method, InvertUniformDiskPolarSample(), is straightforward and is not included here.

Although this mapping solves the problem at hand, it distorts areas on the disk; areas on the unit square are elongated or compressed when mapped to the disk (Figure [A.7](#fig:disk-mapping-distortion)). This distortion can reduce the effectiveness of stratified sampling patterns by making the strata less compact. A better approach that avoids this problem is a “concentric” mapping from the unit square to the unit disk. The concentric mapping takes points in the square to the unit disk by uniformly mapping concentric squares to concentric circles (Figure [A.8](#fig:square-to-sphere)).

[![](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf07.svg)](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf07.svg)

Figure A.7: The mapping from 2D random samples to points on the disk implemented in [SampleUniformDiskPolar()](#SampleUniformDiskPolar) distorts areas substantially. Each section of the disk here has equal area and represents of the unit square of uniform random samples in each direction. In general, we would prefer a mapping that did a better job at mapping nearby values to nearby points on the disk.

[![](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf08.svg)](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf08.svg)

Figure A.8: The concentric mapping maps squares to circles, giving a less distorted mapping than the first method shown for uniformly sampling points on the unit disk. It is based on mapping triangular wedges of the unit square to pie-shaped wedges of the disk, as shown here.

The mapping turns wedges of the square into slices of the disk. For example, points in the shaded area in Figure [A.8](#fig:square-to-sphere) are mapped to by

See Figure [A.9](#fig:wedgemap). The other seven wedges are handled analogously.

[![](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf09.svg)](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf09.svg)

Figure A.9: Triangular wedges of the square are mapped into pairs in pie-shaped slices of the disk in the SampleUniformDiskConcentric() function.

[Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) SampleUniformDiskConcentric([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) u) { << [Map u to and handle degeneracy at the origin](#fragment-Mapmonouto-112andhandledegeneracyattheorigin-0) >>

[Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) uOffset = 2 \* u - [Vector2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector2f) (1, 1); if (uOffset.x == 0 && uOffset.y == 0) return {0, 0};

<< [Apply concentric mapping to point](#fragment-Applyconcentricmappingtopoint-0) >>

Float theta, r; if (std::abs(uOffset.x) > std::abs(uOffset.y)) { r = uOffset.x; theta = [PiOver4](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#PiOver4) \* (uOffset.y / uOffset.x); } else { r = uOffset.y; theta = [PiOver2](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#PiOver2) - [PiOver4](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#PiOver4) \* (uOffset.x / uOffset.y); } return r \* [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (std::cos(theta), std::sin(theta));

}

For the following, the random samples are mapped to the square. The point is then handled specially so that the following code does not need to avoid dividing by zero.

<<Map u to and handle degeneracy at the origin>>=

[Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) uOffset = 2 \* u - [Vector2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector2f) (1, 1); if (uOffset.x == 0 && uOffset.y == 0) return {0, 0};

All the other points are transformed using the mapping from square wedges to disk slices by way of computing polar coordinates for them. The following implementation is carefully crafted so that the mapping is continuous across adjacent slices.

<<Apply concentric mapping to point>>=

Float theta, r; if (std::abs(uOffset.x) > std::abs(uOffset.y)) { r = uOffset.x; theta = [PiOver4](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#PiOver4) \* (uOffset.y / uOffset.x); } else { r = uOffset.y; theta = [PiOver2](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#PiOver2) - [PiOver4](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#PiOver4) \* (uOffset.x / uOffset.y); } return r \* [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (std::cos(theta), std::sin(theta));

The corresponding inversion function, InvertUniformDiskConcentricSample(), is not included in the text here.

### A.5.2 Uniformly Sampling Hemispheres and Spheres

The area of a unit hemisphere is, and thus the PDF for uniform sampling must be.

We will use spherical coordinates to derive a sampling algorithm. Using Equation ([2.23](https://pbr-book.org/4ed/Monte_Carlo_Integration/Transforming_between_Distributions.html#eq:spherical-cartesian-pdf-relation)) from Section [2.4.1](https://pbr-book.org/4ed/Monte_Carlo_Integration/Transforming_between_Distributions.html#sec:mc-transform-multiple-dimensions), we have. This density function is separable. Because ranges from 0 to and must have a constant PDF, and therefore. The two CDFs follow:

Inverting these functions is straightforward, and in this case we can tidy the result by replacing with, giving

Converting back to Cartesian coordinates, we get the final sampling formulae:

([A.17](#eq:uniform-hemi-sample))

This sampling strategy is implemented in the following code. Two uniform random numbers are provided in u, and a vector on the hemisphere is returned.

[Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) SampleUniformHemisphere([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) u) { Float z = u\[0\]; Float r = [SafeSqrt](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SafeSqrt) (1 - [Sqr](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (z)); Float phi = 2 \* [Pi](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Pi) \* u\[1\]; return {r \* std::cos(phi), r \* std::sin(phi), z}; }

For each PDF evaluation function, it is important to be clear which PDF is being evaluated—for example, we have already seen directional probabilities expressed both in terms of solid angle and in terms of. For hemispheres (and all other directional sampling in pbrt), these functions return probability with respect to solid angle. Thus, the uniform hemisphere PDF function is trivial and does not require that the direction be passed to it.

Float UniformHemispherePDF() { return [Inv2Pi](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Inv2Pi); }

The inverse sampling method can be derived starting from Equation ([A.17](#eq:uniform-hemi-sample)).

[Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) InvertUniformHemisphereSample([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) w) { Float phi = std::atan2(w.y, w.x); if (phi < 0) phi += 2 \* [Pi](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Pi); return [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (w.z, phi / (2 \* [Pi](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Pi))); }

Sampling the full sphere uniformly over its area follows almost exactly the same derivation, which we omit here. The end result is

[Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) SampleUniformSphere([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) u) { Float z = 1 - 2 \* u\[0\]; Float r = [SafeSqrt](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SafeSqrt) (1 - [Sqr](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (z)); Float phi = 2 \* [Pi](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Pi) \* u\[1\]; return {r \* std::cos(phi), r \* std::sin(phi), z}; }

The PDF is, one over the surface area of the unit sphere.

Float UniformSpherePDF() { return [Inv4Pi](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Inv4Pi); }

The sampling inversion method also follows directly.

[Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) InvertUniformSphereSample([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) w) { Float phi = std::atan2(w.y, w.x); if (phi < 0) phi += 2 \* [Pi](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Pi); return [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) ((1 - w.z) / 2, phi / (2 \* [Pi](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Pi))); }

### A.5.3 Cosine-Weighted Hemisphere Sampling

As we saw in the discussion of importance sampling (Section [2.2.2](https://pbr-book.org/4ed/Monte_Carlo_Integration/Improving_Efficiency.html#sec:importance-sampling)), it is often useful to sample from a distribution that has a shape similar to that of the integrand being estimated. Many light transport integrals include a cosine factor, and therefore it is useful to have a method that generates directions according to a cosine-weighted distribution on the hemisphere. Such a method gives samples that are more likely to be close to the top of the hemisphere, where the cosine term has a large value, rather than near the bottom, where the cosine term is small.

Mathematically, this means that we would like to sample directions from a PDF

Normalizing as usual,

Thus,

We could compute the marginal and conditional densities as before, but instead we can use a technique known as *Malley’s method* to generate these cosine-weighted points. The idea behind Malley’s method is that if we choose points uniformly from the unit disk and then generate directions by projecting the points on the disk up to the hemisphere above it, the result will have a cosine-weighted distribution of directions (Figure [A.10](#fig:malley)).

[![](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf10.svg)](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf10.svg)

Figure A.10: Malley’s Method. To sample direction vectors from a cosine-weighted distribution, uniformly sample points on the unit disk and project them up to the unit hemisphere.

Why does this work? Let be the polar coordinates of the point chosen on the disk (note that we are using instead of the usual for the polar angle here). From Section [A.5.1](#sec:unit-disk-sample), we know that the joint density gives the density of a point sampled on the disk.

Now, we map this point to the hemisphere. The vertical projection gives, which is easily seen from Figure [A.10](#fig:malley). To complete the transformation, we need the determinant of the Jacobian

Therefore,

which is exactly what we wanted! We have used the transformation method to prove that Malley’s method generates directions with a cosine-weighted distribution. Note that this technique works with any uniform disk sampling approach, so we can use the earlier concentric mapping just as well as the simpler method.

[Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) SampleCosineHemisphere([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) u) { [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) d = [SampleUniformDiskConcentric](#SampleUniformDiskConcentric) (u); Float z = [Safe](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SafeSqrt) [Sqrt](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (1 - [Sqr](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (d.x) - [Sqr](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (d.y)); return [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) (d.x, d.y, z); }

Because directional PDFs in pbrt are defined with respect to solid angle, the PDF function returns the value.

Float CosineHemispherePDF(Float cosTheta) { return cosTheta \* [InvPi](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#InvPi); }

Finally, a directional sample can be inverted purely from its coordinates on the disk.

[Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) InvertCosineHemisphereSample([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) w) { return [InvertUniformDiskConcentricSample](#InvertUniformDiskConcentricSample) ({w.x, w.y}); }

### A.5.4 Sampling Within a Cone

It is sometimes useful to be able to uniformly sample rays in a cone of directions. This distribution is separable in, with, and so we therefore need to derive a method to sample a direction up to the maximum angle of the cone,. Incorporating the term from the measure on the unit sphere from Equation ([4.8](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Working_with_Radiometric_Integrals.html#eq:sintheta-dtheta-dphi)), we have

So and.

Float UniformConePDF(Float cosThetaMax) { return 1 / (2 \* [Pi](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Pi) \* (1 - cosThetaMax)); }

The PDF can be integrated to find the CDF and the sampling technique for follows:

The following code samples a canonical cone around the axis; the sample can be transformed to cones with other orientations using the [Frame](https://pbr-book.org/4ed/Geometry_and_Transformations/Applying_Transformations.html#Frame) class.

<<Sampling Inline Functions>>+=

[Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) SampleUniformCone([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) u, Float cosThetaMax) { Float cosTheta = (1 - u\[0\]) + u\[0\] \* cosThetaMax; Float sinTheta = [SafeSqrt](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SafeSqrt) (1 - [Sqr](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (cosTheta)); Float phi = u\[1\] \* 2 \* [Pi](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Pi); return [SphericalDirection](https://pbr-book.org/4ed/Geometry_and_Transformations/Spherical_Geometry.html#SphericalDirection) (sinTheta, cosTheta, phi); }

The inversion function, InvertUniformConeSample(), is not included here.

### A.5.5 Piecewise-Constant 2D Distributions

Building on the approach for sampling piecewise-constant 1D distributions in Section [A.4.7](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#sec:piecewise-constant-1d), we can apply the marginal-conditional approach to sample from piecewise-constant 2D distributions. We will consider the case of a 2D function defined over a user-specified domain by a 2D array of sample values. This case is particularly useful for generating samples from distributions defined by image maps and environment maps.

Consider a 2D function defined by a set of values where,, and gives the constant value of over the range. Given continuous values, we will use to denote the corresponding discrete indices, with and so that.

Integrals of are sums of, so that, for example, the integral of over the domain is

Using the definition of the PDF and the integral of, we can find ’s PDF,

Recalling Equation ([2.24](https://pbr-book.org/4ed/Monte_Carlo_Integration/Transforming_between_Distributions.html#eq:2d-marginal-density)), the marginal density can be computed as a sum of values

([A.17](#eq:2d-discrete-marginal-density))

Because this function only depends on, it is thus itself a piecewise-constant 1D function,, defined by values. The 1D sampling machinery from Section [A.4.7](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#sec:piecewise-constant-1d) can be applied to sampling from its distribution.

Given a sample, the conditional density is then

([A.17](#eq:2d-discrete-conditional-density))

Note that, given a particular value of, is a piecewise-constant 1D function of that can be sampled with the usual 1D approach. There are such distinct 1D conditional densities, one for each possible value of.

Putting this all together, the PiecewiseConstant2D class provides functionality similar to [PiecewiseConstant1D](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D), except that it generates samples from piecewise-constant 2D distributions.

<<PiecewiseConstant2D Definition>>=

class PiecewiseConstant2D { public: << [PiecewiseConstant2D Public Methods](#fragment-PiecewiseConstant2DPublicMethods-0) >>

PiecewiseConstant2D() = default; PiecewiseConstant2D(Allocator alloc): [pConditionalV](#PiecewiseConstant2D::pConditionalV) (alloc), [pMarginal](#PiecewiseConstant2D::pMarginal) (alloc) {} PiecewiseConstant2D(pstd::span<const Float> data, int nx, int ny, Allocator alloc = {}): PiecewiseConstant2D(data, nx, ny, [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) ([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (0, 0), [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (1, 1)), alloc) {} explicit PiecewiseConstant2D(const Array2D<Float> &data, Allocator alloc = {}): PiecewiseConstant2D(pstd::span<const Float>(data), data.XSize(), data.YSize(), alloc) {} PiecewiseConstant2D(const Array2D<Float> &data, [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) [domain](#PiecewiseConstant2D::domain), Allocator alloc = {}): PiecewiseConstant2D(pstd::span<const Float>(data), data.XSize(), data.YSize(), [domain](#PiecewiseConstant2D::domain), alloc) {} PBRT\_CPU\_GPU size\_t BytesUsed() const { return [pConditionalV](#PiecewiseConstant2D::pConditionalV).[size](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::size) () \* ([pConditionalV](#PiecewiseConstant2D::pConditionalV) \[0\].BytesUsed() + sizeof([pConditionalV](#PiecewiseConstant2D::pConditionalV) \[0\])) + [pMarginal](#PiecewiseConstant2D::pMarginal).BytesUsed(); } PBRT\_CPU\_GPU [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) Domain() const { return [domain](#PiecewiseConstant2D::domain); } PBRT\_CPU\_GPU [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) Resolution() const { return {int([pConditionalV](#PiecewiseConstant2D::pConditionalV) \[0\].[size](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::size) ()), int([pMarginal](#PiecewiseConstant2D::pMarginal).[size](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::size) ())}; } std::string ToString() const { return StringPrintf("\[ PiecewiseConstant2D [domain](#PiecewiseConstant2D::domain): %s [pConditionalV](#PiecewiseConstant2D::pConditionalV): %s " " [pMarginal](#PiecewiseConstant2D::pMarginal): %s \]", [domain](#PiecewiseConstant2D::domain), [pConditionalV](#PiecewiseConstant2D::pConditionalV), [pMarginal](#PiecewiseConstant2D::pMarginal)); } static void TestCompareDistributions(const PiecewiseConstant2D &da, const PiecewiseConstant2D &db, Float eps = 1e-5); PiecewiseConstant2D(pstd::span<const Float> func, int nu, int nv, [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) [domain](#PiecewiseConstant2D::domain), Allocator alloc = {}): [domain](#PiecewiseConstant2D::domain) ([domain](#PiecewiseConstant2D::domain)), [pConditionalV](#PiecewiseConstant2D::pConditionalV) (alloc), [pMarginal](#PiecewiseConstant2D::pMarginal) (alloc) { for (int v = 0; v < nv; ++v) << [Compute conditional sampling distribution for](#fragment-Computeconditionalsamplingdistributionfortildev-0) >>

[pConditionalV](#PiecewiseConstant2D::pConditionalV).emplace\_back(func.subspan(v \* nu, nu), domain.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin) \[0\], domain.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax) \[0\], alloc);

<< [Compute marginal sampling distribution](#fragment-Computemarginalsamplingdistributionptildev-0) >>

pstd::vector<Float> marginalFunc; for (int v = 0; v < nv; ++v) marginalFunc.push\_back([pConditionalV](#PiecewiseConstant2D::pConditionalV) \[v\].[Integral](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Integral) ()); [pMarginal](#PiecewiseConstant2D::pMarginal) = PiecewiseConstant1D(marginalFunc, domain.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin) \[1\], domain.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax) \[1\], alloc);

} Float [Integral](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Integral) () const { return [pMarginal](#PiecewiseConstant2D::pMarginal).[Integral](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Integral) (); } [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) [Sample](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Sample) ([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) u, Float \*pdf = nullptr, [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) \*offset = nullptr) const { Float pdfs\[2\]; [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) uv; Float d1 = [pMarginal](#PiecewiseConstant2D::pMarginal).[Sample](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Sample) (u\[1\], &pdfs\[1\], &uv\[1\]); Float d0 = [pConditionalV](#PiecewiseConstant2D::pConditionalV) \[uv\[1\]\].[Sample](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Sample) (u\[0\], &pdfs\[0\], &uv\[0\]); if (pdf) \*pdf = pdfs\[0\] \* pdfs\[1\]; if (offset) \*offset = uv; return [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (d0, d1); } Float PDF([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) pr) const { [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p = [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) ([domain](#PiecewiseConstant2D::domain).[Offset](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::Offset) (pr)); int iu = [Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp) (int(p\[0\] \* [pConditionalV](#PiecewiseConstant2D::pConditionalV) \[0\].[size](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::size) ()), 0, [pConditionalV](#PiecewiseConstant2D::pConditionalV) \[0\].[size](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::size) () - 1); int iv = [Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp) (int(p\[1\] \* [pMarginal](#PiecewiseConstant2D::pMarginal).[size](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::size) ()), 0, [pMarginal](#PiecewiseConstant2D::pMarginal).[size](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::size) () - 1); return [pConditionalV](#PiecewiseConstant2D::pConditionalV) \[iv\].func\[iu\] / [pMarginal](#PiecewiseConstant2D::pMarginal).[Integral](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Integral) (); } pstd::optional< [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) > [Invert](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Invert) ([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p) const { pstd::optional<Float> mInv = [pMarginal](#PiecewiseConstant2D::pMarginal).[Invert](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Invert) (p\[1\]); if (!mInv) return {}; Float p1o = (p\[1\] - [domain](#PiecewiseConstant2D::domain).[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin) \[1\]) / ([domain](#PiecewiseConstant2D::domain).[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax) \[1\] - [domain](#PiecewiseConstant2D::domain).[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin) \[1\]); if (p1o < 0 || p1o > 1) return {}; int offset = [Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp) (p1o \* [pConditionalV](#PiecewiseConstant2D::pConditionalV).[size](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::size) (), 0, [pConditionalV](#PiecewiseConstant2D::pConditionalV).[size](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::size) () - 1); pstd::optional<Float> cInv = [pConditionalV](#PiecewiseConstant2D::pConditionalV) \[offset\].[Invert](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Invert) (p\[0\]); if (!cInv) return {}; return [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (\*cInv, \*mInv); }

private: << [PiecewiseConstant2D Private Members](#fragment-PiecewiseConstant2DPrivateMembers-0) >>

[Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) domain; pstd::vector<PiecewiseConstant1D> pConditionalV; PiecewiseConstant1D pMarginal;

};

Its constructor has two tasks. First, it computes a 1D conditional sampling density for each of the individual values using Equation ([A.17](#eq:2d-discrete-conditional-density)). It then computes the marginal sampling density with Equation ([A.17](#eq:2d-discrete-marginal-density)). (PiecewiseConstant2D provides a variety of additional constructors, not included here, including ones that take an [Array2D](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D) to specify the values. See the pbrt source code for details.)

<<PiecewiseConstant2D Public Methods>>=

PiecewiseConstant2D(pstd::span<const Float> func, int nu, int nv, [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) [domain](#PiecewiseConstant2D::domain), Allocator alloc = {}): [domain](#PiecewiseConstant2D::domain) ([domain](#PiecewiseConstant2D::domain)), [pConditionalV](#PiecewiseConstant2D::pConditionalV) (alloc), [pMarginal](#PiecewiseConstant2D::pMarginal) (alloc) { for (int v = 0; v < nv; ++v) << [Compute conditional sampling distribution for](#fragment-Computeconditionalsamplingdistributionfortildev-0) >>

[pConditionalV](#PiecewiseConstant2D::pConditionalV).emplace\_back(func.subspan(v \* nu, nu), domain.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin) \[0\], domain.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax) \[0\], alloc);

<< [Compute marginal sampling distribution](#fragment-Computemarginalsamplingdistributionptildev-0) >>

pstd::vector<Float> marginalFunc; for (int v = 0; v < nv; ++v) marginalFunc.push\_back([pConditionalV](#PiecewiseConstant2D::pConditionalV) \[v\].[Integral](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Integral) ()); [pMarginal](#PiecewiseConstant2D::pMarginal) = PiecewiseConstant1D(marginalFunc, domain.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin) \[1\], domain.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax) \[1\], alloc);

}

<<PiecewiseConstant2D Private Members>>=

[Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) domain;

[PiecewiseConstant1D](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D) can directly compute the distributions from each of the rows of function values, since they are laid out linearly in memory. The and terms from Equation ([A.17](#eq:2d-discrete-conditional-density)) do not need to be included in the values passed to [PiecewiseConstant1D](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D) since they have the same value for all the values and are thus just a constant scale that does not affect the normalized distribution that [PiecewiseConstant1D](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D) computes.

<<Compute conditional sampling distribution for >>=

[pConditionalV](#PiecewiseConstant2D::pConditionalV).emplace\_back(func.subspan(v \* nu, nu), domain.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin) \[0\], domain.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax) \[0\], alloc);

pstd::vector<PiecewiseConstant1D> pConditionalV;

Given the conditional densities for each value, we can find the 1D marginal density for sampling each one,. Because the PiecewiseConstant1D class has a method that provides the integral of its function, it is just necessary to copy these values to the marginalFunc buffer so they are stored linearly in memory for the PiecewiseConstant1D constructor.

<<Compute marginal sampling distribution >>=

pstd::vector<Float> marginalFunc; for (int v = 0; v < nv; ++v) marginalFunc.push\_back([pConditionalV](#PiecewiseConstant2D::pConditionalV) \[v\].[Integral](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Integral) ()); [pMarginal](#PiecewiseConstant2D::pMarginal) = PiecewiseConstant1D(marginalFunc, domain.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin) \[1\], domain.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax) \[1\], alloc);

<<PiecewiseConstant2D Private Members>>+=

PiecewiseConstant1D pMarginal;

The integral of the function over the domain is made available via the Integral() method. Because the marginal distribution is the integral of one dimension, its integral gives the function’s full integral.

Float [Integral](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Integral) () const { return [pMarginal](#PiecewiseConstant2D::pMarginal).[Integral](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Integral) (); }

As described previously, in order to sample from the 2D distribution, first a sample is drawn from the marginal distribution in order to find the coordinate of the sample. The offset of the sampled function value gives the integer value that determines which of the precomputed conditional distributions should be used for sampling the value. Figure [A.11](#fig:sample-2d-image) illustrates this idea using a low-resolution image as an example.

[![](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf11.svg)](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf11.svg)

Figure A.11: The Piecewise-Constant Sampling Distribution for a High-Dynamic-Range Environment Map. (a) The original environment map. (b) A low-resolution version of the marginal density function and the conditional distributions for rows of the image. First the marginal 1D distribution is used to select a value, giving a row of the image to sample. Rows with bright pixels are more likely to be sampled. Then, given a row, a value is sampled from that row’s 1D distribution.

[Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) [Sample](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Sample) ([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) u, Float \*pdf = nullptr, [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) \*offset = nullptr) const { Float pdfs\[2\]; [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) uv; Float d1 = [pMarginal](#PiecewiseConstant2D::pMarginal).[Sample](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Sample) (u\[1\], &pdfs\[1\], &uv\[1\]); Float d0 = [pConditionalV](#PiecewiseConstant2D::pConditionalV) \[uv\[1\]\].[Sample](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Sample) (u\[0\], &pdfs\[0\], &uv\[0\]); if (pdf) \*pdf = pdfs\[0\] \* pdfs\[1\]; if (offset) \*offset = uv; return [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (d0, d1); }

The value of the PDF for a given sample value is computed as the product of the conditional and marginal PDFs for sampling it.

<<PiecewiseConstant2D Public Methods>>+=

Float PDF([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) pr) const { [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p = [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (domain.[Offset](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::Offset) (pr)); int iu = [Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp) (int(p\[0\] \* [pConditionalV](#PiecewiseConstant2D::pConditionalV) \[0\].[size](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::size) ()), 0, [pConditionalV](#PiecewiseConstant2D::pConditionalV) \[0\].[size](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::size) () - 1); int iv = [Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp) (int(p\[1\] \* [pMarginal](#PiecewiseConstant2D::pMarginal).[size](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::size) ()), 0, [pMarginal](#PiecewiseConstant2D::pMarginal).[size](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::size) () - 1); return [pConditionalV](#PiecewiseConstant2D::pConditionalV) \[iv\].func\[iu\] / [pMarginal](#PiecewiseConstant2D::pMarginal).[Integral](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#PiecewiseConstant1D::Integral) (); }

The Invert() method, not included here, inverts the provided sample by inverting the sample using the marginal distribution and then inverting via the appropriate conditional distribution.

### A.5.6 Windowed Piecewise-Constant 2D Distributions

[WindowedPiecewiseConstant2D](#WindowedPiecewiseConstant2D) generalizes the [PiecewiseConstant2D](#PiecewiseConstant2D) class to allow the caller to specify a window that limits the sampling domain to a given rectangular subset of it. (This capability was key for the implementation of the PortalImageInfiniteLight in Section [12.5.3](https://pbr-book.org/4ed/Light_Sources/Infinite_Area_Lights.html#sec:portal-image-infinite-light).) Before going into its implementation, we will start with the [SummedAreaTable](#SummedAreaTable) class, which provides some capabilities that make it easier to implement. We have encapsulated them in a stand-alone class, as they can be useful in other settings as well.

In 2D, a *summed-area table* is a 2D array where each element stores a sum of values from another array:

([A.17](#eq:summed-area-table))

where here we have used C++’s zero-based array indexing convention.

Summed-area tables can be used to compute the sum of array values over rectangular regions of the original array in constant time. If the array is interpreted as samples of a function, a summed-area table can efficiently compute integrals over arbitrary rectangular regions in a similar fashion. (Summed-area tables are therefore sometimes referred to as *integral images*.) They have a straightforward generalization to higher dimensions, though two of them suffice for pbrt ’s needs.

<<SummedAreaTable Definition>>=

class SummedAreaTable { public: << [SummedAreaTable Public Methods](#fragment-SummedAreaTablePublicMethods-0) >>

SummedAreaTable(const Array2D<Float> &values, Allocator alloc = {}): [sum](#SummedAreaTable::sum) (values.[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) (), values.[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) (), alloc) { [sum](#SummedAreaTable::sum) (0, 0) = values(0, 0); << [Compute sums along first row and column](#fragment-Computesumsalongfirstrowandcolumn-0) >>

for (int x = 1; x < [sum](#SummedAreaTable::sum).XSize(); ++x) [sum](#SummedAreaTable::sum) (x, 0) = values(x, 0) + [sum](#SummedAreaTable::sum) (x - 1, 0); for (int y = 1; y < [sum](#SummedAreaTable::sum).YSize(); ++y) [sum](#SummedAreaTable::sum) (0, y) = values(0, y) + [sum](#SummedAreaTable::sum) (0, y - 1);

<< [Compute sums for the remainder of the entries](#fragment-Computesumsfortheremainderoftheentries-0) >>

for (int y = 1; y < [sum](#SummedAreaTable::sum).[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) (); ++y) for (int [x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) = 1; [x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) < [sum](#SummedAreaTable::sum).[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) (); ++ [x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator)) [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator), y) = (values([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator), y) + [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) - 1, y) + [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator), y - 1) - [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) - 1, y - 1));

} Float Integral([Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) extent) const { double s = (((double) [Lookup](#SummedAreaTable::Lookup) (extent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x, extent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y) - (double) [Lookup](#SummedAreaTable::Lookup) (extent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x, extent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y)) + ((double) [Lookup](#SummedAreaTable::Lookup) (extent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x, extent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y) - (double) [Lookup](#SummedAreaTable::Lookup) (extent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x, extent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y))); return std::max<Float>(s / ([sum](#SummedAreaTable::sum).[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) () \* [sum](#SummedAreaTable::sum).[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) ()), 0); } std::string ToString() const;

private: << [SummedAreaTable Private Methods](#fragment-SummedAreaTablePrivateMethods-0) >>

Float Lookup(Float x, Float y) const { << [Rescale to table resolution and compute integer coordinates](#fragment-Rescalexytotableresolutionandcomputeintegercoordinates-0) >>

x \*= sum.[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) (); y \*= sum.[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) (); int x0 = (int)x, y0 = (int)y;

<< [Bilinearly interpolate between surrounding table values](#fragment-Bilinearlyinterpolatebetweensurroundingtablevalues-0) >>

Float v00 = [LookupInt](#SummedAreaTable::LookupInt) (x0, y0), v10 = [LookupInt](#SummedAreaTable::LookupInt) (x0 + 1, y0); Float v01 = [LookupInt](#SummedAreaTable::LookupInt) (x0, y0 + 1), v11 = [LookupInt](#SummedAreaTable::LookupInt) (x0 + 1, y0 + 1); Float dx = x - int(x), dy = y - int(y); return (1 - dx) \* (1 - dy) \* v00 + (1 - dx) \* dy \* v01 + dx \* (1 - dy) \* v10 + dx \* dy \* v11;

} Float LookupInt(int x, int y) const { << [Return zero at lower boundaries](#fragment-Returnzeroatlowerboundaries-0) >>

if (x == 0 || y == 0) return 0;

<< [Reindex and return actual stored value](#fragment-Reindexxyandreturnactualstoredvalue-0) >>

[x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) = std::min([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) - 1, [sum](#SummedAreaTable::sum).[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) () - 1); y = std::min(y - 1, [sum](#SummedAreaTable::sum).[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) () - 1); return [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator), y);

}

<< [SummedAreaTable Private Members](#fragment-SummedAreaTablePrivateMembers-0) >>

[Array2D](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D) <double> sum;

};

The constructor takes a 2D array of values that are used to initialize its sum array, which holds the corresponding sums. The first entry is easy: it is just the entry from the provided values array.

<<SummedAreaTable Public Methods>>=

SummedAreaTable(const Array2D<Float> &values, Allocator alloc = {}): [sum](#SummedAreaTable::sum) (values.[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) (), values.[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) (), alloc) { [sum](#SummedAreaTable::sum) (0, 0) = values(0, 0); << [Compute sums along first row and column](#fragment-Computesumsalongfirstrowandcolumn-0) >>

for (int x = 1; x < [sum](#SummedAreaTable::sum).XSize(); ++x) [sum](#SummedAreaTable::sum) (x, 0) = values(x, 0) + [sum](#SummedAreaTable::sum) (x - 1, 0); for (int y = 1; y < [sum](#SummedAreaTable::sum).YSize(); ++y) [sum](#SummedAreaTable::sum) (0, y) = values(0, y) + [sum](#SummedAreaTable::sum) (0, y - 1);

<< [Compute sums for the remainder of the entries](#fragment-Computesumsfortheremainderoftheentries-0) >>

for (int y = 1; y < [sum](#SummedAreaTable::sum).[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) (); ++y) for (int [x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) = 1; [x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) < [sum](#SummedAreaTable::sum).[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) (); ++ [x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator)) [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator), y) = (values([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator), y) + [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) - 1, y) + [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator), y - 1) - [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) - 1, y - 1));

}

<<SummedAreaTable Private Members>>=

[Array2D](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D) <double> sum;

All the remaining entries in sum can be computed incrementally. It is easiest to start out by computing sums as varies with and vice versa.

<<Compute sums along first row and column>>=

for (int x = 1; x < [sum](#SummedAreaTable::sum).XSize(); ++x) [sum](#SummedAreaTable::sum) (x, 0) = values(x, 0) + [sum](#SummedAreaTable::sum) (x - 1, 0); for (int y = 1; y < [sum](#SummedAreaTable::sum).YSize(); ++y) [sum](#SummedAreaTable::sum) (0, y) = values(0, y) + [sum](#SummedAreaTable::sum) (0, y - 1);

The remainder of the sums are computed incrementally by adding the corresponding value from the provided array to two of the previous sums and subtracting a third. It is possible to use the definition from Equation ([A.17](#eq:summed-area-table)) to verify that this expression gives the desired value, but it can also be understood geometrically; see Figure [A.12](#fig:sat-previous-sums).

[![](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf12.svg)](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf12.svg)

Figure A.12: Computing a Value in a Summed-Area Table Based on Previous Sums. (a) A starting value for the sum at a location (filled circle) is given by the sum at (shaded region). To this value, we need to add the provided array’s value at. (b) What is left is the sum of values in the column beneath (lighter shaded region); that value can be found by taking the sum at and subtracting the sum at (darker shaded region).

<<Compute sums for the remainder of the entries>>=

for (int y = 1; y < [sum](#SummedAreaTable::sum).[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) (); ++y) for (int [x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) = 1; [x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) < [sum](#SummedAreaTable::sum).[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) (); ++ [x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator)) [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator), y) = (values([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator), y) + [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) - 1, y) + [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator), y - 1) - [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) - 1, y - 1));

[![](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf13.svg)](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf13.svg)

Figure A.13: Interpretation of sum Array Values. If the sample array values is interpreted as defining a piecewise-constant function over, then the values stored in sum represent the sums at the upper-right corner of each piecewise-constant region. The sums along and, all of which are 0, are not stored.

We will find it useful to be able to treat the sum as a continuous function defined over. In doing so, our implementation effectively treats the originally provided array of values as the specification of a piecewise-constant function. Under this interpretation, the stored sum values effectively represent the function’s value at the upper corners of the box-shaped regions that the domain has been discretized into. (See Figure [A.13](#fig:sat-sum-value-interpretation).)

This Lookup() method returns the interpolated sum at the given continuous coordinate values.

<<SummedAreaTable Private Methods>>=

Float Lookup(Float x, Float y) const { << [Rescale to table resolution and compute integer coordinates](#fragment-Rescalexytotableresolutionandcomputeintegercoordinates-0) >>

x \*= sum.[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) (); y \*= sum.[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) (); int x0 = (int)x, y0 = (int)y;

<< [Bilinearly interpolate between surrounding table values](#fragment-Bilinearlyinterpolatebetweensurroundingtablevalues-0) >>

Float v00 = [LookupInt](#SummedAreaTable::LookupInt) (x0, y0), v10 = [LookupInt](#SummedAreaTable::LookupInt) (x0 + 1, y0); Float v01 = [LookupInt](#SummedAreaTable::LookupInt) (x0, y0 + 1), v11 = [LookupInt](#SummedAreaTable::LookupInt) (x0 + 1, y0 + 1); Float dx = x - int(x), dy = y - int(y); return (1 - dx) \* (1 - dy) \* v00 + (1 - dx) \* dy \* v01 + dx \* (1 - dy) \* v10 + dx \* dy \* v11;

}

It is more convenient to work with coordinates that are with respect to the array’s dimensions and so this method starts by scaling the provided coordinates accordingly. Note that an offset of is not included in this remapping, as is done when indexing pixel values (recall the discussion of this topic in Section [8.1.4](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Theory.html#sec:pixel-concepts)); this is due to the fact that sum defines function values at the upper corners of the discretized regions rather than at their center.

<<Rescale to table resolution and compute integer coordinates>>=

x \*= sum.[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) (); y \*= sum.[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) (); int x0 = (int)x, y0 = (int)y;

Bilinear interpolation of the four values surrounding the lookup point proceeds as usual, using LookupInt() to look up values of the sum at provided integer coordinates.

<<Bilinearly interpolate between surrounding table values>>=

Float v00 = [LookupInt](#SummedAreaTable::LookupInt) (x0, y0), v10 = [LookupInt](#SummedAreaTable::LookupInt) (x0 + 1, y0); Float v01 = [LookupInt](#SummedAreaTable::LookupInt) (x0, y0 + 1), v11 = [LookupInt](#SummedAreaTable::LookupInt) (x0 + 1, y0 + 1); Float dx = x - int(x), dy = y - int(y); return (1 - dx) \* (1 - dy) \* v00 + (1 - dx) \* dy \* v01 + dx \* (1 - dy) \* v10 + dx \* dy \* v11;

LookupInt() returns the value of the sum for provided integer coordinates. In particular, it is responsible for handling the details related to the sum array storing the sum at the upper corners of the domain strata.

<<SummedAreaTable Private Methods>>+=

Float LookupInt(int x, int y) const { << [Return zero at lower boundaries](#fragment-Returnzeroatlowerboundaries-0) >>

if (x == 0 || y == 0) return 0;

<< [Reindex and return actual stored value](#fragment-Reindexxyandreturnactualstoredvalue-0) >>

[x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) = std::min([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) - 1, [sum](#SummedAreaTable::sum).[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) () - 1); y = std::min(y - 1, [sum](#SummedAreaTable::sum).[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) () - 1); return [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator), y);

}

If either coordinate is zero-valued, the lookup point is along one of the lower edges of the domain (or is at the origin). In this case, a sum value of 0 is returned.

<<Return zero at lower boundaries>>=

if (x == 0 || y == 0) return 0;

Otherwise, one is subtracted from each coordinate so that indexing into the sum array accounts for the zero sums at the lower edges not being stored in sum.

<<Reindex and return actual stored value>>=

[x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) = std::min([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator) - 1, [sum](#SummedAreaTable::sum).[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) () - 1); y = std::min(y - 1, [sum](#SummedAreaTable::sum).[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) () - 1); return [sum](#SummedAreaTable::sum) ([x](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::operator), y);

Summed-area tables compute sums and integrals over arbitrary rectangular regions in a similar way to how the interior sum values were originally initialized. Here it is also possible to verify this computation algebraically, but the geometric interpretation may be more intuitive; see Figure [A.14](#fig:sat-arbitrary-rect).

![](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf14.png)

Figure A.14: Computing the Sum of an Arbitrary Rectangular Region. Given two points and representing the corners of a rectangular region, the sum of values inside the rectangular region can be found in terms of sums of subregions. (a) The sum at gives the desired result and then much more. (b) Subtracting the sum eliminates some of the excess, leaving the region underneath the region to be removed. (c) Subtracting the sum takes care of the excess and then some; the shaded region has now been removed twice. (d) Adding the shaded region’s sum, which is the sum at, rectifies the excess subtraction and leaves us with the desired result.

The SummedAreaTable class provides this capability through its Integral() method, which returns the integral of the piecewise-constant function over a 2D bounding box. Here, the sum of function values over the region is converted to an integral by dividing by the size of the function strata over the domain. We have used double precision here to compute the final sum in order to improve its accuracy: especially if there are thousands of values in each dimension, the sums may have large magnitudes and thus taking their differences can lead to catastrophic cancellation.

<<SummedAreaTable Public Methods>>+=

Given [SummedAreaTable](#SummedAreaTable) ’s capability of efficiently evaluating integrals over rectangular regions of a piecewise-constant function’s domain, the WindowedPiecewiseConstant2D class is able to provide sampling and PDF evaluation functions that operate over arbitrary caller-specified regions.

<<WindowedPiecewiseConstant2D Definition>>=

class WindowedPiecewiseConstant2D { public: << [WindowedPiecewiseConstant2D Public Methods](#fragment-WindowedPiecewiseConstant2DPublicMethods-0) >>

WindowedPiecewiseConstant2D(Array2D<Float> f, Allocator alloc = {}): [sat](#WindowedPiecewiseConstant2D::sat) (f, alloc), [func](#WindowedPiecewiseConstant2D::func) (f, alloc) {} pstd::optional< [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) > Sample([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) u, [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) b, Float \*pdf) const { << [Handle zero-valued function for windowed sampling](#fragment-Handlezero-valuedfunctionforwindowedsampling-0) >>

if ([sat](#WindowedPiecewiseConstant2D::sat).[Integral](#SummedAreaTable::Integral) (b) == 0) return {};

<< [Define lambda function Px for marginal cumulative distribution](#fragment-DefinelambdafunctionmonoPxformarginalcumulativedistribution-0) >>

Float bInt = sat.[Integral](#SummedAreaTable::Integral) (b); auto Px = \[&, this\](Float x) -> Float { [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) bx = b; bx.pMax.x = x; return sat.[Integral](#SummedAreaTable::Integral) (bx) / bInt; };

<< [Sample marginal windowed function in](#fragment-Samplemarginalwindowedfunctioninx-0) >> << [Sample conditional windowed function in](#fragment-Sampleconditionalwindowedfunctioniny-0) >>

<< [Compute 2D bounds bCond for conditional sampling](#fragment-Compute2DboundsmonobCondforconditionalsampling-0) >>

int nx = [func](#WindowedPiecewiseConstant2D::func).[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) (); [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) bCond([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (pstd::floor(p.x \* nx) / nx, b.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y), [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (pstd::ceil(p.x \* nx) / nx, b.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y)); if (bCond.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x == bCond.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x) bCond.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x += 1.f / nx; if (sat.[Integral](#SummedAreaTable::Integral) (bCond) == 0) return {};

<< [Define lambda function for conditional distribution and sample](#fragment-Definelambdafunctionforconditionaldistributionandsampley-0) >>

Float condIntegral = sat.[Integral](#SummedAreaTable::Integral) (bCond); auto Py = \[&, this\](Float y) -> Float { [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) by = bCond; by.pMax.y = y; return sat.[Integral](#SummedAreaTable::Integral) (by) / condIntegral; }; p.y = [SampleBisection](#WindowedPiecewiseConstant2D::SampleBisection) (Py, u\[1\], b.pMin.y, b.pMax.y, func.[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) ());

<< [Compute PDF and return point sampled from windowed function](#fragment-ComputePDFandreturnpointsampledfromwindowedfunction-0) >>

\*pdf = [Eval](#WindowedPiecewiseConstant2D::Eval) (p) / bInt; return p;

} Float PDF([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p, const [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) &b) const { Float funcInt = [sat](#WindowedPiecewiseConstant2D::sat).[Integral](#SummedAreaTable::Integral) (b); if (funcInt == 0) return 0; return [Eval](#WindowedPiecewiseConstant2D::Eval) (p) / funcInt; }

private: << [WindowedPiecewiseConstant2D Private Methods](#fragment-WindowedPiecewiseConstant2DPrivateMethods-0) >>

template <typename CDF> static Float SampleBisection(CDF P, Float u, Float min, Float max, int n) { << [Apply bisection to bracket u](#fragment-Applybisectiontobracketmonou-0) >>

while (pstd::ceil(n \* max) - pstd::floor(n \* min) > 1) { Float mid = (min + max) / 2; if (P(mid) > u) max = mid; else min = mid; }

<< [Find sample by interpolating between min and max](#fragment-Findsamplebyinterpolatingbetweenmonominandmonomax-0) >>

Float t = (u - P(min)) / (P(max) - P(min)); return [Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp) ([Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (t, min, max), min, max);

} Float Eval([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p) const { [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) pi(std::min<int>(p\[0\] \* [func](#WindowedPiecewiseConstant2D::func).XSize(), [func](#WindowedPiecewiseConstant2D::func).XSize() - 1), std::min<int>(p\[1\] \* [func](#WindowedPiecewiseConstant2D::func).YSize(), [func](#WindowedPiecewiseConstant2D::func).YSize() - 1)); return [func](#WindowedPiecewiseConstant2D::func) \[pi\]; }

<< [WindowedPiecewiseConstant2D Private Members](#fragment-WindowedPiecewiseConstant2DPrivateMembers-0) >>

SummedAreaTable sat; Array2D<Float> func;

};

The constructor both copies the provided function values and initializes a summed-area table with them.

<<WindowedPiecewiseConstant2D Public Methods>>=

WindowedPiecewiseConstant2D(Array2D<Float> f, Allocator alloc = {}): [sat](#WindowedPiecewiseConstant2D::sat) (f, alloc), [func](#WindowedPiecewiseConstant2D::func) (f, alloc) {}

<<WindowedPiecewiseConstant2D Private Members>>=

SummedAreaTable sat; Array2D<Float> func;

With the [SummedAreaTable](#SummedAreaTable) in hand, it is now possible to bring the pieces together to implement the Sample() method. Because it is possible that there is no valid sample inside the specified bounds (e.g., if the function’s value is zero), an optional return value is used in order to be able to indicate such cases.

pstd::optional< [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) > Sample([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) u, [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) b, Float \*pdf) const { << [Handle zero-valued function for windowed sampling](#fragment-Handlezero-valuedfunctionforwindowedsampling-0) >>

if ([sat](#WindowedPiecewiseConstant2D::sat).[Integral](#SummedAreaTable::Integral) (b) == 0) return {};

<< [Define lambda function Px for marginal cumulative distribution](#fragment-DefinelambdafunctionmonoPxformarginalcumulativedistribution-0) >>

Float bInt = sat.[Integral](#SummedAreaTable::Integral) (b); auto Px = \[&, this\](Float x) -> Float { [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) bx = b; bx.pMax.x = x; return sat.[Integral](#SummedAreaTable::Integral) (bx) / bInt; };

<< [Sample marginal windowed function in](#fragment-Samplemarginalwindowedfunctioninx-0) >> << [Sample conditional windowed function in](#fragment-Sampleconditionalwindowedfunctioniny-0) >>

<< [Compute 2D bounds bCond for conditional sampling](#fragment-Compute2DboundsmonobCondforconditionalsampling-0) >>

int nx = [func](#WindowedPiecewiseConstant2D::func).[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) (); [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) bCond([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (pstd::floor(p.x \* nx) / nx, b.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y), [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (pstd::ceil(p.x \* nx) / nx, b.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y)); if (bCond.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x == bCond.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x) bCond.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x += 1.f / nx; if (sat.[Integral](#SummedAreaTable::Integral) (bCond) == 0) return {};

<< [Define lambda function for conditional distribution and sample](#fragment-Definelambdafunctionforconditionaldistributionandsampley-0) >>

Float condIntegral = sat.[Integral](#SummedAreaTable::Integral) (bCond); auto Py = \[&, this\](Float y) -> Float { [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) by = bCond; by.pMax.y = y; return sat.[Integral](#SummedAreaTable::Integral) (by) / condIntegral; }; p.y = [SampleBisection](#WindowedPiecewiseConstant2D::SampleBisection) (Py, u\[1\], b.pMin.y, b.pMax.y, func.[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) ());

<< [Compute PDF and return point sampled from windowed function](#fragment-ComputePDFandreturnpointsampledfromwindowedfunction-0) >>

\*pdf = [Eval](#WindowedPiecewiseConstant2D::Eval) (p) / bInt; return p;

}

The first step is to check whether the function’s integral is zero over the specified bounds. This may happen due to a degenerate Bounds2f or due to a plain old zero-valued function over the corresponding part of its domain. In this case, it is not possible to return a valid sample.

<<Handle zero-valued function for windowed sampling>>=

if ([sat](#WindowedPiecewiseConstant2D::sat).[Integral](#SummedAreaTable::Integral) (b) == 0) return {};

As discussed in Section [2.4.2](https://pbr-book.org/4ed/Monte_Carlo_Integration/Transforming_between_Distributions.html#sec:mc-multidimensional-sampling), multidimensional distributions can be sampled by first integrating out all of the dimensions but one, sampling the resulting function, and then using that sample value in sampling the corresponding conditional distribution. WindowedPiecewiseConstant2D applies that very same idea, taking advantage of the fact that the summed-area table can efficiently evaluate the necessary integrals as needed.

For a 2D continuous function defined over a rectangular domain from to, the marginal distribution in is defined by

and the marginal’s cumulative distribution is

The integrals in both the numerator and denominator of can be evaluated using a summed-area table. The following lambda function evaluates, using a cached normalization factor for the denominator in bInt to improve performance, as it will be necessary to repeatedly evaluate Px in order to sample from the distribution.

<<Define lambda function Px for marginal cumulative distribution>>=

Float bInt = sat.[Integral](#SummedAreaTable::Integral) (b); auto Px = \[&, this\](Float x) -> Float { [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) bx = b; bx.pMax.x = x; return sat.[Integral](#SummedAreaTable::Integral) (bx) / bInt; };

Sampling is performed using a separate utility method, SampleBisection(), that will also be useful for sampling the conditional density in.

<<Sample marginal windowed function in >>=

SampleBisection() draws a sample from the density described by the provided CDF P by applying the bisection method to solve for over a specified range. (It expects to have the value 0 at min and 1 at max.) This function has the built-in assumption that the CDF is piecewise-linear over equal-sized segments over. This fits [SummedAreaTable](#SummedAreaTable) perfectly, though it means that SampleBisection() would need modification to be used in other contexts.

<<WindowedPiecewiseConstant2D Private Methods>>=

template <typename CDF> static Float SampleBisection(CDF P, Float u, Float min, Float max, int n) { << [Apply bisection to bracket u](#fragment-Applybisectiontobracketmonou-0) >>

while (pstd::ceil(n \* max) - pstd::floor(n \* min) > 1) { Float mid = (min + max) / 2; if (P(mid) > u) max = mid; else min = mid; }

<< [Find sample by interpolating between min and max](#fragment-Findsamplebyinterpolatingbetweenmonominandmonomax-0) >>

Float t = (u - P(min)) / (P(max) - P(min)); return [Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp) ([Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (t, min, max), min, max);

}

The initial min and max values bracket the solution. Therefore, bisection can proceed by successively evaluating at their midpoint and then updating one or the other of them to maintain the bracket. This process continues until both endpoints lie inside one of the function discretization strata of width.

<<Apply bisection to bracket u >>=

while (pstd::ceil(n \* max) - pstd::floor(n \* min) > 1) { Float mid = (min + max) / 2; if (P(mid) > u) max = mid; else min = mid; }

Once both endpoints are in the same stratum, it is possible to take advantage of the fact that is known to be piecewise-linear and to find the value of in closed form.

<<Find sample by interpolating between min and max >>=

Float t = (u - P(min)) / (P(max) - P(min)); return [Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp) ([Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (t, min, max), min, max);

Given the sample, we now need to draw a sample from the conditional distribution

which has CDF

Although the SummedAreaTable class does not provide the capability to evaluate 1D integrals directly, because the function is piecewise-constant we can equivalently evaluate a 2D integral where the range spans only the stratum of the sampled value.

<<Sample conditional windowed function in >>=

<< [Compute 2D bounds bCond for conditional sampling](#fragment-Compute2DboundsmonobCondforconditionalsampling-0) >>

int nx = [func](#WindowedPiecewiseConstant2D::func).[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) (); [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) bCond([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (pstd::floor(p.x \* nx) / nx, b.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y), [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (pstd::ceil(p.x \* nx) / nx, b.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y)); if (bCond.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x == bCond.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x) bCond.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x += 1.f / nx; if (sat.[Integral](#SummedAreaTable::Integral) (bCond) == 0) return {};

<< [Define lambda function for conditional distribution and sample](#fragment-Definelambdafunctionforconditionaldistributionandsampley-0) >>

Float condIntegral = sat.[Integral](#SummedAreaTable::Integral) (bCond); auto Py = \[&, this\](Float y) -> Float { [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) by = bCond; by.pMax.y = y; return sat.[Integral](#SummedAreaTable::Integral) (by) / condIntegral; }; p.y = [SampleBisection](#WindowedPiecewiseConstant2D::SampleBisection) (Py, u\[1\], b.pMin.y, b.pMax.y, func.[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) ());

bCond stores the bounding box that spans the range of potential values and the stratum of the sample. It is necessary to check for a zero function integral over these bounds: this should not be possible mathematically, but may be the case due to floating-point round-off error. In that rare case, conditional sampling is not possible and an invalid sample must be returned.

<<Compute 2D bounds bCond for conditional sampling>>=

int nx = [func](#WindowedPiecewiseConstant2D::func).[XSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::XSize) (); [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) bCond([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (pstd::floor(p.x \* nx) / nx, b.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y), [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (pstd::ceil(p.x \* nx) / nx, b.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y)); if (bCond.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x == bCond.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x) bCond.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x += 1.f / nx; if (sat.[Integral](#SummedAreaTable::Integral) (bCond) == 0) return {};

Similar to the marginal CDF, we can define a lambda function to evaluate the conditional CDF. Again precomputing the normalization factor is worthwhile, as Py will be evaluated multiple times in the course of the sampling operation.

<<Define lambda function for conditional distribution and sample >>=

Float condIntegral = sat.[Integral](#SummedAreaTable::Integral) (bCond); auto Py = \[&, this\](Float y) -> Float { [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) by = bCond; by.pMax.y = y; return sat.[Integral](#SummedAreaTable::Integral) (by) / condIntegral; }; p.y = [SampleBisection](#WindowedPiecewiseConstant2D::SampleBisection) (Py, u\[1\], b.pMin.y, b.pMax.y, func.[YSize](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D::YSize) ());

The PDF value is computed by evaluating the function at the sampled point p and normalizing with its integral over b, which is already available in bInt.

<<Compute PDF and return point sampled from windowed function>>=

\*pdf = [Eval](#WindowedPiecewiseConstant2D::Eval) (p) / bInt; return p;

The Eval() method wraps up the details of looking up the function value corresponding to the provided 2D point.

<<WindowedPiecewiseConstant2D Private Methods>>+=

Float Eval([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p) const { [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) pi(std::min<int>(p\[0\] \* [func](#WindowedPiecewiseConstant2D::func).XSize(), [func](#WindowedPiecewiseConstant2D::func).XSize() - 1), std::min<int>(p\[1\] \* [func](#WindowedPiecewiseConstant2D::func).YSize(), [func](#WindowedPiecewiseConstant2D::func).YSize() - 1)); return [func](#WindowedPiecewiseConstant2D::func) \[pi\]; }

The PDF method implements the same computation that is used to compute the PDF in the Sample() method.

<<WindowedPiecewiseConstant2D Public Methods>>+=

Float PDF([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p, const [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) &b) const { Float funcInt = [sat](#WindowedPiecewiseConstant2D::sat).[Integral](#SummedAreaTable::Integral) (b); if (funcInt == 0) return 0; return [Eval](#WindowedPiecewiseConstant2D::Eval) (p) / funcInt; }

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
- [[A.2 Reservoir Sampling]]
- [[A.3 The Rejection Method]]
- [[A.4 Sampling 1D Functions]]
