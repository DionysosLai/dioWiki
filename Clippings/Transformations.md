---
title: "Transformations"
source: "https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations"
author:
published:
created: 2026-05-18
description:
tags:
  - "clippings"
---
## 3.9 Transformations

In general, a *transformation* is a mapping from points to points and from vectors to vectors:

The transformation may be an arbitrary procedure. However, we will consider a subset of all possible transformations in this chapter. In particular, they will be

- *Linear:* If is an arbitrary linear transformation and is an arbitrary scalar, then and. These two properties can greatly simplify reasoning about transformations.
- *Continuous:* Roughly speaking, maps the neighborhoods around and to neighborhoods around and.
- *One-to-one and invertible:* For each, maps to a single unique. Furthermore, there exists an inverse transform that maps back to.

We will often want to take a point, vector, or normal defined with respect to one coordinate frame and find its coordinate values with respect to another frame. Using basic properties of linear algebra, a matrix can be shown to express the linear transformation of a point or vector from one frame to another. Furthermore, such a matrix suffices to express all linear transformations of points and vectors within a fixed frame, such as translation in space or rotation around a point. Therefore, there are two different (and incompatible!) ways that a matrix can be interpreted:

- *Transformation within the frame:* Given a point, the matrix could express how to compute a *new* point in the same frame that represents the transformation of the original point (e.g., by translating it in some direction).
- *Transformation from one frame to another:* A matrix can express the coordinates of a point or vector in a new frame in terms of the coordinates in the original frame.

Most uses of transformations in pbrt are for transforming points from one frame to another.

In general, transformations make it possible to work in the most convenient coordinate space. For example, we can write routines that define a virtual camera, assuming that the camera is located at the origin, looks down the axis, and has the axis pointing up and the axis pointing right. These assumptions greatly simplify the camera implementation. To place the camera at any point in the scene looking in any direction, we construct a transformation that maps points in the scene’s coordinate system to the camera’s coordinate system. (See Section [5.1.1](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#sec:camera-coordinate-spaces) for more information about camera coordinate spaces in pbrt.)

### 3.9.1 Homogeneous Coordinates

Given a frame defined by, there is ambiguity between the representation of a point and a vector with the same coordinates. Using the representations of points and vectors introduced at the start of the chapter, we can write the point as the inner product and the vector as the inner product These four-vectors of three values and a zero or one are called the *homogeneous* representations of the point and the vector. The fourth coordinate of the homogeneous representation is sometimes called the *weight*. For a point, its value can be any scalar other than zero: the homogeneous points and describe the same Cartesian point. Converting homogeneous points into ordinary points entails dividing the first three components by the weight:

We will use these facts to see how a transformation matrix can describe how points and vectors in one frame can be mapped to another frame. Consider a matrix that describes the transformation from one coordinate system to another:

(In this book, we define matrix element indices starting from zero, so that equations and source code correspond more directly.) Then if the transformation represented by is applied to the axis vector, we have

Thus, directly reading the columns of the matrix shows how the basis vectors and the origin of the current coordinate system are transformed by the matrix:

In general, by characterizing how the basis is transformed, we know how any point or vector specified in terms of that basis is transformed. Because points and vectors in a coordinate system are expressed in terms of the coordinate system’s frame, applying the transformation to them directly is equivalent to applying the transformation to the coordinate system’s basis and finding their coordinates in terms of the transformed basis.

We will not use homogeneous coordinates explicitly in our code; there is no HomogeneousPoint class in pbrt. However, the various transformation routines in the next section will implicitly convert points, vectors, and normals to homogeneous form, transform the homogeneous points, and then convert them back before returning the result. This isolates the details of homogeneous coordinates in one place (namely, the implementation of transformations).

### 3.9.2 Transform Class Definition

The Transform class represents a transformation. Its implementation is in the files [util/transform.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/transform.h) and [util/transform.cpp](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/transform.cpp).

<<Transform Definition>>=

class Transform { public: << [Transform Public Methods](#fragment-TransformPublicMethods-0) >>

PBRT\_CPU\_GPU inline [Ray](https://pbr-book.org/4ed/Geometry_and_Transformations/Rays.html#Ray) ApplyInverse(const [Ray](https://pbr-book.org/4ed/Geometry_and_Transformations/Rays.html#Ray) &r, Float \*tMax = nullptr) const; PBRT\_CPU\_GPU inline [RayDifferential](https://pbr-book.org/4ed/Geometry_and_Transformations/Rays.html#RayDifferential) ApplyInverse(const [RayDifferential](https://pbr-book.org/4ed/Geometry_and_Transformations/Rays.html#RayDifferential) &r, Float \*tMax = nullptr) const; template <typename T> PBRT\_CPU\_GPU inline Vector3<T> ApplyInverse(Vector3<T> v) const; template <typename T> PBRT\_CPU\_GPU inline Normal3<T> ApplyInverse(Normal3<T> ) const; std::string ToString() const; [Transform](#Transform) () = default; [Transform](#Transform) (const [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> & [m](#Transform::m)): [m](#Transform::m) ([m](#Transform::m)) { pstd::optional< [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4>> inv = [Inverse](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix::Inverse) ([m](#Transform::m)); if (inv) [mInv](#Transform::mInv) = \*inv; else { << [Initialize mInv with not-a-number values](#fragment-InitializemonomInvwithnot-a-numbervalues-0) >>

Float NaN = std::numeric\_limits<Float>::has\_signaling\_NaN? std::numeric\_limits<Float>::signaling\_NaN(): std::numeric\_limits<Float>::quiet\_NaN(); for (int i = 0; i < 4; ++i) for (int j = 0; j < 4; ++j) [mInv](#Transform::mInv) \[i\]\[j\] = NaN;

} } [Transform](#Transform) (const Float mat\[4\]\[4\]): [Transform](#Transform) ([SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4>(mat)) {} [Transform](#Transform) (const [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> & [m](#Transform::m), const [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> & [mInv](#Transform::mInv)): [m](#Transform::m) ([m](#Transform::m)), [mInv](#Transform::mInv) ([mInv](#Transform::mInv)) {} const [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> &GetMatrix() const { return [m](#Transform::m); } const [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> &GetInverseMatrix() const { return [mInv](#Transform::mInv); } bool operator==(const [Transform](#Transform) &t) const { return t.[m](#Transform::m) == [m](#Transform::m); } bool operator!=(const [Transform](#Transform) &t) const { return t.[m](#Transform::m)!= [m](#Transform::m); } bool [IsIdentity](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix::IsIdentity) () const { return [m](#Transform::m).[IsIdentity](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix::IsIdentity) (); } bool HasScale(Float tolerance = 1e-3f) const { Float la2 = [LengthSquared](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#LengthSquared) ((\*this)([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) (1, 0, 0))); Float lb2 = [LengthSquared](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#LengthSquared) ((\*this)([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) (0, 1, 0))); Float lc2 = [LengthSquared](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#LengthSquared) ((\*this)([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) (0, 0, 1))); return (std::abs(la2 - 1) > tolerance || std::abs(lb2 - 1) > tolerance || std::abs(lc2 - 1) > tolerance); } template <typename T> PBRT\_CPU\_GPU Point3<T> operator()(Point3<T> p) const; template <typename T> Point3<T> ApplyInverse(Point3<T> p) const; template <typename T> PBRT\_CPU\_GPU Vector3<T> operator()(Vector3<T> v) const; template <typename T> PBRT\_CPU\_GPU Normal3<T> operator()(Normal3<T> ) const; PBRT\_CPU\_GPU [Ray](https://pbr-book.org/4ed/Geometry_and_Transformations/Rays.html#Ray) operator()(const [Ray](https://pbr-book.org/4ed/Geometry_and_Transformations/Rays.html#Ray) &r, Float \*tMax = nullptr) const; PBRT\_CPU\_GPU [RayDifferential](https://pbr-book.org/4ed/Geometry_and_Transformations/Rays.html#RayDifferential) operator()(const [RayDifferential](https://pbr-book.org/4ed/Geometry_and_Transformations/Rays.html#RayDifferential) &r, Float \*tMax = nullptr) const; PBRT\_CPU\_GPU [Bounds3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds3f) operator()(const [Bounds3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds3f) &b) const; PBRT\_CPU\_GPU [Transform](#Transform) operator\*(const [Transform](#Transform) &t2) const; PBRT\_CPU\_GPU bool SwapsHandedness() const; explicit [Transform](#Transform) (const [Frame](https://pbr-book.org/4ed/Geometry_and_Transformations/Applying_Transformations.html#Frame) &frame); explicit [Transform](#Transform) (Quaternion q); explicit operator Quaternion() const; void Decompose([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) \*T, [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> \*R, [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> \*S) const; PBRT\_CPU\_GPU [Interaction](https://pbr-book.org/4ed/Geometry_and_Transformations/Interactions.html#Interaction) operator()(const [Interaction](https://pbr-book.org/4ed/Geometry_and_Transformations/Interactions.html#Interaction) &in) const; PBRT\_CPU\_GPU [Interaction](https://pbr-book.org/4ed/Geometry_and_Transformations/Interactions.html#Interaction) ApplyInverse(const [Interaction](https://pbr-book.org/4ed/Geometry_and_Transformations/Interactions.html#Interaction) &in) const; PBRT\_CPU\_GPU [SurfaceInteraction](https://pbr-book.org/4ed/Geometry_and_Transformations/Interactions.html#SurfaceInteraction) operator()(const [SurfaceInteraction](https://pbr-book.org/4ed/Geometry_and_Transformations/Interactions.html#SurfaceInteraction) &si) const; PBRT\_CPU\_GPU [SurfaceInteraction](https://pbr-book.org/4ed/Geometry_and_Transformations/Interactions.html#SurfaceInteraction) ApplyInverse(const [SurfaceInteraction](https://pbr-book.org/4ed/Geometry_and_Transformations/Interactions.html#SurfaceInteraction) &in) const; Point3fi operator()(const Point3fi &p) const { Float x = Float(p.x), y = Float(p.y), z = Float(p.z); <<Compute transformed coordinates from point (x, y, z) >>

Float xp = (m\[0\]\[0\] \* x + m\[0\]\[1\] \* y) + (m\[0\]\[2\] \* z + m\[0\]\[3\]); Float yp = (m\[1\]\[0\] \* x + m\[1\]\[1\] \* y) + (m\[1\]\[2\] \* z + m\[1\]\[3\]); Float zp = (m\[2\]\[0\] \* x + m\[2\]\[1\] \* y) + (m\[2\]\[2\] \* z + m\[2\]\[3\]); Float wp = (m\[3\]\[0\] \* x + m\[3\]\[1\] \* y) + (m\[3\]\[2\] \* z + m\[3\]\[3\]);

<< [Compute absolute error for transformed point, pError](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#fragment-ComputeabsoluteerrorfortransformedpointmonopError-0) >>

[Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) pError; if (p.[IsExact](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Point3fi::IsExact) ()) { << [Compute error for transformed exact p](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#fragment-Computeerrorfortransformedexactmonop-0) >>

pError.x = [gamma](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#gamma) (3) \* (std::abs(m\[0\]\[0\] \* x) + std::abs(m\[0\]\[1\] \* y) + std::abs(m\[0\]\[2\] \* z) + std::abs(m\[0\]\[3\])); pError.y = [gamma](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#gamma) (3) \* (std::abs(m\[1\]\[0\] \* x) + std::abs(m\[1\]\[1\] \* y) + std::abs(m\[1\]\[2\] \* z) + std::abs(m\[1\]\[3\])); pError.z = [gamma](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#gamma) (3) \* (std::abs(m\[2\]\[0\] \* x) + std::abs(m\[2\]\[1\] \* y) + std::abs(m\[2\]\[2\] \* z) + std::abs(m\[2\]\[3\]));

} else { <<Compute error for transformed approximate p >>

[Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) pInError = p.Error(); pError.x = (gamma(3) + 1) \* (std::abs(m\[0\]\[0\]) \* pInError.x + std::abs(m\[0\]\[1\]) \* pInError.y + std::abs(m\[0\]\[2\]) \* pInError.z) + gamma(3) \* (std::abs(m\[0\]\[0\] \* x) + std::abs(m\[0\]\[1\] \* y) + std::abs(m\[0\]\[2\] \* z) + std::abs(m\[0\]\[3\])); pError.y = (gamma(3) + 1) \* (std::abs(m\[1\]\[0\]) \* pInError.x + std::abs(m\[1\]\[1\]) \* pInError.y + std::abs(m\[1\]\[2\]) \* pInError.z) + gamma(3) \* (std::abs(m\[1\]\[0\] \* x) + std::abs(m\[1\]\[1\] \* y) + std::abs(m\[1\]\[2\] \* z) + std::abs(m\[1\]\[3\])); pError.z = (gamma(3) + 1) \* (std::abs(m\[2\]\[0\]) \* pInError.x + std::abs(m\[2\]\[1\]) \* pInError.y + std::abs(m\[2\]\[2\]) \* pInError.z) + gamma(3) \* (std::abs(m\[2\]\[0\] \* x) + std::abs(m\[2\]\[1\] \* y) + std::abs(m\[2\]\[2\] \* z) + std::abs(m\[2\]\[3\]));

}

if (wp == 1) return Point3fi([Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) (xp, yp, zp), pError); else return Point3fi([Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) (xp, yp, zp), pError) / wp; } Vector3fi operator()(const Vector3fi &v) const; PBRT\_CPU\_GPU Point3fi ApplyInverse(const Point3fi &p) const;

private: << [Transform Private Members](#fragment-TransformPrivateMembers-0) >>

[SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> m, mInv;

};

The transformation matrix is represented by the elements of the matrix m, which is represented by a SquareMatrix<4> object. (The [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) class is defined in Section [B.2.12](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#sec:square-matrix).) The matrix m is stored in *row-major* form, so element m\[i\]\[j\] corresponds to, where is the row number and is the column number. For convenience, the [Transform](#Transform) also stores the inverse of m in its [Transform::mInv](#Transform::mInv) member variable; for pbrt ’s needs, it is better to have the inverse easily available than to repeatedly compute it as needed.

<<Transform Private Members>>=

[SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> m, mInv;

This representation of transformations is relatively memory hungry: assuming 4 bytes of storage for a Float value, a [Transform](#Transform) requires 128 bytes of storage. Used naïvely, this approach can be wasteful; if a scene has millions of shapes but only a few thousand unique transformations, there is no reason to redundantly store the same matrices many times. Therefore, [Shape](https://pbr-book.org/4ed/Shapes/Basic_Shape_Interface.html#Shape) s in pbrt store a pointer to a Transform and the scene specification code defined in Section [C.2.3](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#sec:managing-xforms) uses an [InternCache](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#InternCache) of [Transform](#Transform) s to ensure that all shapes that share the same transformation point to a single instance of that transformation in memory.

### 3.9.3 Basic Operations

When a new [Transform](#Transform) is created, it defaults to the *identity transformation* —the transformation that maps each point and each vector to itself. This transformation is represented by the *identity matrix*:

The implementation here relies on the default [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) constructor to fill in the identity matrix for m and mInv.

<<Transform Public Methods>>=

[Transform](#Transform) () = default;

A [Transform](#Transform) can also be created from a given matrix. In this case, the matrix must be explicitly inverted.

[Transform](#Transform) (const [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> &m): m(m) { pstd::optional< [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4>> inv = [Inverse](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix::Inverse) (m); if (inv) mInv = \*inv; else { << [Initialize mInv with not-a-number values](#fragment-InitializemonomInvwithnot-a-numbervalues-0) >>

Float NaN = std::numeric\_limits<Float>::has\_signaling\_NaN? std::numeric\_limits<Float>::signaling\_NaN(): std::numeric\_limits<Float>::quiet\_NaN(); for (int i = 0; i < 4; ++i) for (int j = 0; j < 4; ++j) [mInv](#Transform::mInv) \[i\]\[j\] = NaN;

} }

If the matrix provided by the caller is degenerate and cannot be inverted, mInv is initialized with floating-point not-a-number values, which poison computations that involve them: arithmetic performed using a not-a-number value always gives a not-a-number value. In this way, a caller who provides a degenerate matrix m can still use the [Transform](#Transform) as long as no methods that access mInv are called.

<<Initialize mInv with not-a-number values>>=

Float NaN = std::numeric\_limits<Float>::has\_signaling\_NaN? std::numeric\_limits<Float>::signaling\_NaN(): std::numeric\_limits<Float>::quiet\_NaN(); for (int i = 0; i < 4; ++i) for (int j = 0; j < 4; ++j) [mInv](#Transform::mInv) \[i\]\[j\] = NaN;

Another constructor allows specifying the elements of the matrix using a regular 2D array.

[Transform](#Transform) (const Float mat\[4\]\[4\]): [Transform](#Transform) ([SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4>(mat)) {}

The most commonly used constructor takes a reference to the transformation matrix along with an explicitly provided inverse. This is a superior approach to computing the inverse in the constructor because many geometric transformations have simple inverses and we can avoid the expense and potential loss of numeric accuracy from computing a general matrix inverse. Of course, this places the burden on the caller to make sure that the supplied inverse is correct.

[Transform](#Transform) (const [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> &m, const [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> &mInv): m(m), mInv(mInv) {}

Both the matrix and its inverse are made available for callers that need to access them directly.

const [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> &GetMatrix() const { return [m](#Transform::m); } const [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> &GetInverseMatrix() const { return [mInv](#Transform::mInv); }

The [Transform](#Transform) representing the inverse of a Transform can be returned by just swapping the roles of mInv and m.

<<Transform Inline Functions>>=

Transposing the two matrices in the transform to compute a new transform can also be useful.

The Transform class also provides equality and inequality testing methods as well as an IsIdentity() method that checks to see if the transformation is the identity.

bool operator==(const [Transform](#Transform) &t) const { return t.[m](#Transform::m) == [m](#Transform::m); } bool operator!=(const [Transform](#Transform) &t) const { return t.[m](#Transform::m)!= [m](#Transform::m); } bool [IsIdentity](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix::IsIdentity) () const { return [m](#Transform::m).[IsIdentity](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix::IsIdentity) (); }

### 3.9.4 Translations

One of the simplest transformations is the *translation transformation*,. When applied to a point, it translates ’s coordinates by,, and, as shown in Figure [3.25](#fig:translateexample). As an example,.

[![](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f25.svg)](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f25.svg)

Figure 3.25: Translation in 2D. Adding offsets and to a point’s coordinates correspondingly changes its position in space.

Translation has some basic properties:

Translation only affects points, leaving vectors unchanged. In matrix form, the translation transformation is

When we consider the operation of a translation matrix on a point, we see the value of homogeneous coordinates. Consider the product of the matrix for with a point in homogeneous coordinates:

As expected, we have computed a new point with its coordinates offset by. However, if we apply to a vector, we have

The result is the same vector. This makes sense because vectors represent directions, so translation leaves them unchanged.

The Translate() function returns a [Transform](#Transform) that represents a given translation—it is a straightforward application of the translation matrix equation. The inverse of the translation is easily computed, so it is provided to the Transform constructor as well.

<<Transform Function Definitions>>=

[Transform](#Transform) Translate([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) delta) { [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> m(1, 0, 0, delta.x, 0, 1, 0, delta.y, 0, 0, 1, delta.z, 0, 0, 0, 1); [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> minv(1, 0, 0, -delta.x, 0, 1, 0, -delta.y, 0, 0, 1, -delta.z, 0, 0, 0, 1); return [Transform](#Transform) (m, minv); }

### 3.9.5 Scaling

Another basic transformation is the *scale transformation*,. It has the effect of taking a point or vector and multiplying its components by scale factors in,, and:. It has the following basic properties:

We can differentiate between *uniform scaling*, where all three scale factors have the same value, and *nonuniform scaling*, where they may have different values. The general scale matrix is

[Transform](#Transform) Scale(Float x, Float y, Float z) { [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> m(x, 0, 0, 0, 0, y, 0, 0, 0, 0, z, 0, 0, 0, 0, 1); [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> minv(1 / x, 0, 0, 0, 0, 1 / y, 0, 0, 0, 0, 1 / z, 0, 0, 0, 0, 1); return [Transform](#Transform) (m, minv); }

It is useful to be able to test if a transformation has a scaling term in it; an easy way to do this is to transform the three coordinate axes and see if any of their lengths are appreciably different from one.

bool HasScale(Float tolerance = 1e-3f) const { Float la2 = [LengthSquared](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#LengthSquared) ((\*this)([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) (1, 0, 0))); Float lb2 = [LengthSquared](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#LengthSquared) ((\*this)([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) (0, 1, 0))); Float lc2 = [LengthSquared](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#LengthSquared) ((\*this)([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) (0, 0, 1))); return (std::abs(la2 - 1) > tolerance || std::abs(lb2 - 1) > tolerance || std::abs(lc2 - 1) > tolerance); }

### 3.9.6,, and Axis Rotations

Another useful type of transformation is the *rotation transformation*,. In general, we can define an arbitrary axis from the origin in any direction and then rotate around that axis by a given angle. The most common rotations of this type are around the,, and coordinate axes. We will write these rotations as,, and so on. The rotation around an arbitrary axis is denoted by.

Rotations also have some basic properties:

where is the matrix transpose of. This last property, that the inverse of is equal to its transpose, stems from the fact that is an *orthogonal matrix*; its first three columns (or rows) are all normalized and orthogonal to each other. Fortunately, the transpose is much easier to compute than a full matrix inverse.

For a left-handed coordinate system, the matrix for clockwise rotation around the axis is

Figure [3.26](#fig:rotate-x) gives an intuition for how this matrix works.

[![](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f26.svg)](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f26.svg)

Figure 3.26: Clockwise rotation by an angle about the axis leaves the coordinate unchanged. The and axes are mapped to the vectors given by the dashed lines; and coordinates move accordingly.

It is easy to see that the matrix leaves the axis unchanged:

It maps the axis to and the axis to. The and axes remain in the same plane, perpendicular to the axis, but are rotated by the given angle. An arbitrary point in space is similarly rotated about the axis by this transformation while staying in the same plane as it was originally.

The implementation of the [RotateX()](#RotateX) function is straightforward.

[Transform](#Transform) RotateX(Float theta) { Float sinTheta = std::sin([Radians](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Radians) (theta)); Float cosTheta = std::cos([Radians](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Radians) (theta)); [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> m(1, 0, 0, 0, 0, cosTheta, -sinTheta, 0, 0, sinTheta, cosTheta, 0, 0, 0, 0, 1); return [Transform](#Transform) (m, Transpose(m)); }

Similarly, for clockwise rotation around and, we have

The implementations of RotateY() and RotateZ() follow directly and are not included here.

### 3.9.7 Rotation around an Arbitrary Axis

We also provide a routine to compute the transformation that represents rotation around an arbitrary axis. A common derivation of this matrix is based on computing rotations that map the given axis to a fixed axis (e.g., ), performing the rotation there, and then rotating the fixed axis back to the original axis. A more elegant derivation can be constructed with vector algebra.

Consider a normalized direction vector that gives the axis to rotate around by angle, and a vector to be rotated (Figure [3.27](#fig:rot-arb-axis)).

[![](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f27.svg)](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f27.svg)

Figure 3.27: A vector can be rotated around an arbitrary axis by constructing a coordinate system in the plane perpendicular to the axis that passes through ’s end point and rotating the vectors and about. Applying this rotation to the axes of the coordinate system,, and gives the general rotation matrix for this rotation.

First, we can compute the vector along the axis that is in the plane through the end point of and is parallel to. Assuming and form an angle, we have

We now compute a pair of basis vectors and in this plane. Trivially, one of them is

and the other can be computed with a cross product

Because is normalized, and have the same length, equal to the length of the vector between and. To now compute the rotation by an angle about in the plane of rotation, the rotation formulae earlier give us

To convert this to a rotation matrix, we apply this formula to the basis vectors,, and to get the values of the rows of the matrix. The result of all this is encapsulated in the following function. As with the other rotation matrices, the inverse is equal to the transpose.

Because some callers of the Rotate() function already have and at hand, pbrt provides a variant of the function that takes those values directly.

[Transform](#Transform) Rotate(Float sinTheta, Float cosTheta, [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) axis) { [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) a = [Normalize](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Normalize) (axis); [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> m; << [Compute rotation of first basis vector](#fragment-Computerotationoffirstbasisvector-0) >>

m\[0\]\[0\] = a.x \* a.x + (1 - a.x \* a.x) \* cosTheta; m\[0\]\[1\] = a.x \* a.y \* (1 - cosTheta) - a.z \* sinTheta; m\[0\]\[2\] = a.x \* a.z \* (1 - cosTheta) + a.y \* sinTheta; m\[0\]\[3\] = 0;

<<Compute rotations of second and third basis vectors>>

m\[1\]\[0\] = a.x \* a.y \* (1 - cosTheta) + a.z \* sinTheta; m\[1\]\[1\] = a.y \* a.y + (1 - a.y \* a.y) \* cosTheta; m\[1\]\[2\] = a.y \* a.z \* (1 - cosTheta) - a.x \* sinTheta; m\[1\]\[3\] = 0; m\[2\]\[0\] = a.x \* a.z \* (1 - cosTheta) - a.y \* sinTheta; m\[2\]\[1\] = a.y \* a.z \* (1 - cosTheta) + a.x \* sinTheta; m\[2\]\[2\] = a.z \* a.z + (1 - a.z \* a.z) \* cosTheta; m\[2\]\[3\] = 0;

return [Transform](#Transform) (m, [Transpose](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix::Transpose) (m)); }

<<Compute rotation of first basis vector>>=

m\[0\]\[0\] = a.x \* a.x + (1 - a.x \* a.x) \* cosTheta; m\[0\]\[1\] = a.x \* a.y \* (1 - cosTheta) - a.z \* sinTheta; m\[0\]\[2\] = a.x \* a.z \* (1 - cosTheta) + a.y \* sinTheta; m\[0\]\[3\] = 0;

The code for the other two basis vectors follows similarly and is not included here.

A second variant of Rotate() takes the angle in degrees, computes its sine and cosine, and calls the first.

[Transform](#Transform) [Rotate](#Rotate) (Float theta, [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) axis) { Float sinTheta = std::sin([Radians](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Radians) (theta)); Float cosTheta = std::cos([Radians](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Radians) (theta)); return [Rotate](#Rotate) (sinTheta, cosTheta, axis); }

### 3.9.8 Rotating One Vector to Another

It is sometimes useful to find the transformation that performs a rotation that aligns one unit vector with another (where denotes “from” and denotes “to”). One way to do so is to define a rotation axis by the cross product of the two vectors, compute the rotation angle as the arccosine of their dot product, and then use the [Rotate()](#Rotate) function. However, this approach not only becomes unstable when the two vectors are nearly parallel but also requires a number of expensive trigonometric function calls.

A different approach to deriving this rotation matrix is based on finding a pair of reflection transformations that reflect to an intermediate vector and then reflect to. The product of such a pair of reflections gives the desired rotation. The *Householder matrix* provides a way to find these reflections: it reflects the given vector to its negation while leaving all vectors orthogonal to unchanged and is defined as

where is the identity matrix.

With the product of the two reflections

([3.10](#eq:householder-rot-from-to))

the second matrix reflects to and the first then reflects to, which together give the desired rotation.

<<Transform Inline Functions>>+=

[Transform](#Transform) RotateFromTo([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) from, [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) to) { << [Compute intermediate vector for vector reflection](#fragment-Computeintermediatevectorforvectorreflection-0) >>

[Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) refl; if (std::abs(from.x) < 0.72f && std::abs(to.x) < 0.72f) refl = [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) (1, 0, 0); else if (std::abs(from.y) < 0.72f && std::abs(to.y) < 0.72f) refl = [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) (0, 1, 0); else refl = [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) (0, 0, 1);

<< [Initialize matrix r for rotation](#fragment-Initializematrixmonorforrotation-0) >>

[Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) u = refl - from, v = refl - to; [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> r; for (int i = 0; i < 3; ++i) for (int j = 0; j < 3; ++j) << [Initialize matrix element r\[i\]\[j\]](#fragment-Initializematrixelementmonorij-0) >>

r\[i\]\[j\] = ((i == j)? 1: 0) - 2 / [Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (u, u) \* u\[i\] \* u\[j\] - 2 / [Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (v, v) \* v\[i\] \* v\[j\] + 4 \* [Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (u, v) / ([Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (u, u) \* [Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (v, v)) \* v\[i\] \* u\[j\];

return [Transform](#Transform) (r, [Transpose](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix::Transpose) (r)); }

The intermediate reflection direction refl is determined by choosing a basis vector that is not too closely aligned to either of the from and to vectors. In the computation here, because is just slightly greater than, the absolute value of at least one pair of matching coordinates must then both be less than, assuming the vectors are normalized. In this way, a loss of accuracy is avoided when the reflection direction is nearly parallel to either from or to.

<<Compute intermediate vector for vector reflection>>=

[Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) refl; if (std::abs(from.x) < 0.72f && std::abs(to.x) < 0.72f) refl = [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) (1, 0, 0); else if (std::abs(from.y) < 0.72f && std::abs(to.y) < 0.72f) refl = [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) (0, 1, 0); else refl = [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) (0, 0, 1);

Given the reflection axis, the matrix elements can be initialized directly.

<<Initialize matrix r for rotation>>=

[Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) u = refl - from, v = refl - to; [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> r; for (int i = 0; i < 3; ++i) for (int j = 0; j < 3; ++j) << [Initialize matrix element r\[i\]\[j\]](#fragment-Initializematrixelementmonorij-0) >>

r\[i\]\[j\] = ((i == j)? 1: 0) - 2 / [Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (u, u) \* u\[i\] \* u\[j\] - 2 / [Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (v, v) \* v\[i\] \* v\[j\] + 4 \* [Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (u, v) / ([Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (u, u) \* [Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (v, v)) \* v\[i\] \* u\[j\];

Expanding the product of the Householder matrices in Equation ([3.10](#eq:householder-rot-from-to)), we can find that the matrix element is given by

where is the Kronecker delta function that is 1 if and are equal and 0 otherwise. The implementation follows directly.

<<Initialize matrix element r\[i\]\[j\] >>=

r\[i\]\[j\] = ((i == j)? 1: 0) - 2 / [Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (u, u) \* u\[i\] \* u\[j\] - 2 / [Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (v, v) \* v\[i\] \* v\[j\] + 4 \* [Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (u, v) / ([Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (u, u) \* [Dot](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Dot) (v, v)) \* v\[i\] \* u\[j\];

### 3.9.9 The Look-at Transformation

The *look-at transformation* is particularly useful for placing a camera in the scene. The caller specifies the desired position of the camera, a point the camera is looking at, and an “up” vector that orients the camera along the viewing direction implied by the first two parameters. All of these values are typically given in world-space coordinates; this gives a transformation from world space to camera space (Figure [3.28](#fig:lookat)). We will assume that use in the discussion below, though note that this way of specifying transformations can also be useful for placing light sources in the scene.

[![](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f28.svg)](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f28.svg)

Figure 3.28: Given a camera position, the position being looked at from the camera, and an “up” direction, the look-at transformation describes a transformation from a left-handed viewing coordinate system where the camera is at the origin looking down the axis, and the axis is along the up direction.

In order to find the entries of the look-at transformation matrix, we use principles described earlier in this section: the columns of a transformation matrix give the effect of the transformation on the basis of a coordinate system.

[Transform](#Transform) LookAt([Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) pos, [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) look, [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) up) { [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> worldFromCamera; << [Initialize fourth column of viewing matrix](#fragment-Initializefourthcolumnofviewingmatrix-0) >>

worldFromCamera\[0\]\[3\] = pos.x; worldFromCamera\[1\]\[3\] = pos.y; worldFromCamera\[2\]\[3\] = pos.z; worldFromCamera\[3\]\[3\] = 1;

<< [Initialize first three columns of viewing matrix](#fragment-Initializefirstthreecolumnsofviewingmatrix-0) >>

[Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) dir = [Normalize](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Normalize) (look - pos); [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) right = [Normalize](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Normalize) ([Cross](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Cross) ([Normalize](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Normalize) (up), dir)); [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) newUp = [Cross](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Cross) (dir, right); worldFromCamera\[0\]\[0\] = right.x; worldFromCamera\[1\]\[0\] = right.y; worldFromCamera\[2\]\[0\] = right.z; worldFromCamera\[3\]\[0\] = 0.; worldFromCamera\[0\]\[1\] = newUp.x; worldFromCamera\[1\]\[1\] = newUp.y; worldFromCamera\[2\]\[1\] = newUp.z; worldFromCamera\[3\]\[1\] = 0.; worldFromCamera\[0\]\[2\] = dir.x; worldFromCamera\[1\]\[2\] = dir.y; worldFromCamera\[2\]\[2\] = dir.z; worldFromCamera\[3\]\[2\] = 0.;

[SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4> cameraFromWorld = [InvertOrExit](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix::InvertOrExit) (worldFromCamera); return [Transform](#Transform) (cameraFromWorld, worldFromCamera); }

The easiest column is the fourth one, which gives the point that the camera-space origin, maps to in world space. This is clearly just the camera position, supplied by the user.

<<Initialize fourth column of viewing matrix>>=

worldFromCamera\[0\]\[3\] = pos.x; worldFromCamera\[1\]\[3\] = pos.y; worldFromCamera\[2\]\[3\] = pos.z; worldFromCamera\[3\]\[3\] = 1;

The other three columns are not much more difficult. First, [LookAt()](#LookAt) computes the normalized direction vector from the camera location to the look-at point; this gives the vector coordinates that the axis should map to and, thus, the third column of the matrix. (In a left-handed coordinate system, camera space is defined with the viewing direction down the axis.) The first column, giving the world-space direction that the axis in camera space maps to, is found by taking the cross product of the user-supplied “up” vector with the recently computed viewing direction vector. Finally, the “up” vector is recomputed by taking the cross product of the viewing direction vector with the transformed axis vector, thus ensuring that the and axes are perpendicular and we have an orthonormal viewing coordinate system.

<<Initialize first three columns of viewing matrix>>=

[Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) dir = [Normalize](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Normalize) (look - pos); [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) right = [Normalize](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Normalize) ([Cross](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Cross) ([Normalize](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Normalize) (up), dir)); [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) newUp = [Cross](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Cross) (dir, right); worldFromCamera\[0\]\[0\] = right.x; worldFromCamera\[1\]\[0\] = right.y; worldFromCamera\[2\]\[0\] = right.z; worldFromCamera\[3\]\[0\] = 0.; worldFromCamera\[0\]\[1\] = newUp.x; worldFromCamera\[1\]\[1\] = newUp.y; worldFromCamera\[2\]\[1\] = newUp.z; worldFromCamera\[3\]\[1\] = 0.; worldFromCamera\[0\]\[2\] = dir.x; worldFromCamera\[1\]\[2\] = dir.y; worldFromCamera\[2\]\[2\] = dir.z; worldFromCamera\[3\]\[2\] = 0.;