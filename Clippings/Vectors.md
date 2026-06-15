---
title: "Vectors"
source: "https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors"
author:
published:
created: 2026-05-06
description:
tags:
  - "clippings"
---
## 3.3 Vectors



pbrt provides both 2D and 3D vector classes that are based on the corresponding two- and three-dimensional tuple classes. Both vector types are themselves parameterized by the type of the underlying vector element, thus making it easy to instantiate vectors of both integer and floating-point types.

<<Vector2 Definition>>=

template <typename T> class Vector2: public [Tuple2](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple2) <Vector2, T> { public: <<Vector2 Public Methods>>

using Tuple2<Vector2, T>::x; using Tuple2<Vector2, T>::y; Vector2() = default; Vector2(T x, T y): Tuple2<pbrt::Vector2, T>(x, y) {} template <typename U> explicit Vector2(Point2<U> p); template <typename U> explicit Vector2(Vector2<U> v): Tuple2<pbrt::Vector2, T>(T(v.x), T(v.y)) {}

};

Two-dimensional vectors of Float s and integers are widely used, so we will define aliases for those two types.

<<Vector2\* Definitions>>=

using Vector2f = Vector2<Float>; using Vector2i = Vector2<int>;

As with [Tuple2](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple2), we will not include any further details of [Vector2](#Vector2) since it is very similar to [Vector3](#Vector3), which we will discuss in more detail.

A Vector3 ’s tuple of component values gives its representation in terms of the,, and (in 3D) axes of the space it is defined in. The individual components of a 3D vector will be written,, and.

<<Vector3 Definition>>=

template <typename T> class Vector3: public [Tuple3](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple3) <Vector3, T> { public: << [Vector3 Public Methods](#fragment-Vector3PublicMethods-0) >>

using [Tuple3](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple3) <Vector3, T>::x; using [Tuple3](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple3) <Vector3, T>::y; using [Tuple3](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple3) <Vector3, T>::z; Vector3(T x, T y, T z): [Tuple3](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple3) <pbrt::Vector3, T>(x, y, z) {} template <typename U> explicit Vector3(Vector3<U> v): [Tuple3](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple3) <pbrt::Vector3, T>(T(v.x), T(v.y), T(v.z)) {} template <typename U> explicit Vector3(Point3<U> p); template <typename U> explicit Vector3(Normal3<U> n);

};

We also define type aliases for two commonly used three-dimensional vector types.

<<Vector3\* Definitions>>=

using Vector3f = Vector3<Float>; using Vector3i = Vector3<int>;

Vector3 provides a few constructors, including a default constructor (not shown here) and one that allows specifying each component value directly.

<<Vector3 Public Methods>>=

Vector3(T x, T y, T z): Tuple3<pbrt::Vector3, T>(x, y, z) {}

There is also a constructor that takes a Vector3 with a different element type. It is qualified with explicit so that it is not unintentionally used in automatic type conversions; a cast must be used to signify the intent of the type conversion.

template <typename U> explicit Vector3(Vector3<U> v): [Tuple3](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple3) <pbrt::Vector3, T>(T(v.x), T(v.y), T(v.z)) {}

Finally, constructors are provided to convert from the forthcoming [Point3](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3) and [Normal3](https://pbr-book.org/4ed/Geometry_and_Transformations/Normals.html#Normal3) types. Their straightforward implementations are not included here. These, too, are explicit to help ensure that they are only used in situations where the conversion is meaningful.

<<Vector3 Public Methods>>+=

template <typename U> explicit Vector3(Point3<U> p); template <typename U> explicit Vector3(Normal3<U> n);

Addition and subtraction of vectors is performed component-wise, via methods from [Tuple3](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple3). The usual geometric interpretation of vector addition and subtraction is shown in Figures [3.3](#fig:vectoradd) and [3.4](#fig:vectorsubtract). A vector’s length can be changed via component-wise multiplication or division by a scalar. These capabilities, too, are provided by [Tuple3](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple3) and so do not require any additional implementation in the [Vector3](#Vector3) class.

[![](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f03.svg)](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f03.svg)
![[Pasted image 20260506225715.png]]
Figure 3.3: (a) Vector addition:. (b) Notice that the sum forms the diagonal of the parallelogram formed by and, which shows the commutativity of vector addition:.

[![](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f04.svg)](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f04.svg)

Figure 3.4: (a) Vector subtraction. (b) If we consider the parallelogram formed by two vectors, the diagonals are given by (dashed line) and (not shown).

### 3.3.1 Normalization and Vector Length

It is often necessary to *normalize* a vector—that is, to compute a new vector pointing in the same direction but with unit length. A normalized vector is often called a *unit vector*. The notation used in this book for normalized vectors is that is the normalized version of. Before getting to normalization, we will start with computing vectors’ lengths.

The squared length of a vector is given by the sum of the squares of its component values.

<<Vector3 Inline Functions>>=

template <typename T> T LengthSquared(Vector3<T> v) { return [Sqr](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (v.x) + [Sqr](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (v.y) + [Sqr](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (v.z); }

Moving on to computing the length of a vector leads us to a quandary: what type should the Length() function return? For example, if the [Vector3](#Vector3) stores an integer type, that type is probably not an appropriate return type since the vector’s length will not necessarily be integer-valued. In that case, [Float](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#Float) would be a better choice, though we should not standardize on [Float](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#Float) for everything, because given a [Vector3](#Vector3) of double-precision values, we should return the length as a double as well. Continuing our journey through advanced C++, we turn to a technique known as *type traits* to solve this dilemma.

First, we define a general TupleLength template class that holds a type definition, type. The default is set here to be [Float](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#Float).

<<TupleLength Definition>>=

template <typename T> struct TupleLength { using type = Float; };

For Vector3 s of double s, we also provide a template specialization that defines double as the type for length given double for the element type.

<<TupleLength Definition>>+=

template <> struct TupleLength<double> { using type = double; };

Now we can implement Length(), using [TupleLength](#TupleLength) to determine which type to return. Note that the return type cannot be specified before the function declaration is complete since the type T is not known until the function parameters have been parsed. Therefore, the function is declared as auto with the return type specified after its parameter list.

template <typename T> auto Length(Vector3<T> v) -> typename [TupleLength](#TupleLength) <T>::type { using std::sqrt; return sqrt([LengthSquared](#LengthSquared) (v)); }

There is one more C++ subtlety in these few lines of code: the reader may wonder, why have a using std::sqrt declaration in the implementation of Length() and then call sqrt(), rather than just calling std::sqrt() directly? That construction is used because we would like to be able to use component types T that do not have overloaded versions of std::sqrt() available to them. For example, we will later make use of [Vector3](#Vector3) s that store intervals of values for each component using a forthcoming [Interval](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Interval) class. With the way the code is written here, if std::sqrt() supports the type T, the std variant of the function is called. If not, then so long as we have defined a function named sqrt() that takes our custom type, that version will be used.

With all of this in hand, the implementation of Normalize() is thankfully now trivial. The use of auto for the return type ensures that if for example Normalize() is called with a vector with integer components, then the returned vector type has [Float](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#Float) components according to type conversion from the division operator.

template <typename T> auto Normalize(Vector3<T> v) { return v / [Length](#Length) (v); }

### 3.3.2 Dot and Cross Product

Two useful operations on vectors are the dot product (also known as the scalar or inner product) and the cross product. For two 3D vectors and, their *dot product* is defined as

and the implementation follows directly.

template <typename T> T [Dot](#Dot) (Vector3<T> v, Vector3<T> w) { return v.x \* w.x + v.y \* w.y + v.z \* w.z; }

A few basic properties directly follow from the definition of the dot product. For example, if,, and are vectors and is a scalar value, then:

The dot product has a simple relationship to the angle between the two vectors:

([3.1](#eq:dot-cos))

where is the angle between and, and denotes the length of the vector. It follows from this that is zero if and only if and are perpendicular, provided that neither nor is *degenerate* —equal to. A set of two or more mutually perpendicular vectors is said to be *orthogonal*. An orthogonal set of unit vectors is called *orthonormal*.

It follows from Equation ([3.1](#eq:dot-cos)) that if and are unit vectors, their dot product is the cosine of the angle between them. As the cosine of the angle between two vectors often needs to be computed for rendering, we will frequently make use of this property.

If we would like to find the angle between two normalized vectors, we could use the standard library’s inverse cosine function, passing it the value of the dot product between the two vectors. However, that approach can suffer from a loss of accuracy when the two vectors are nearly parallel or facing in nearly opposite directions. The following reformulation does more of its computation with values close to the origin where there is more floating-point precision, giving a more accurate result.

template <typename T> Float AngleBetween(Vector3<T> v1, Vector3<T> v2) { if ([Dot](#Dot) (v1, v2) < 0) return Pi - 2 \* [SafeASin](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SafeASin) ([Length](#Length) (v1 + v2) / 2); else return 2 \* [SafeASin](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SafeASin) ([Length](#Length) (v2 - v1) / 2); }

We will frequently need to compute the absolute value of the dot product as well. The [AbsDot()](#AbsDot) function does this for us so that a separate call to std::abs() is not necessary in that case.

template <typename T> T [AbsDot](#AbsDot) (Vector3<T> v1, Vector3<T> v2) { return std::abs([Dot](#Dot) (v1, v2)); }

[![](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f05.svg)](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f05.svg)

Figure 3.5: The orthogonal projection of a vector onto a normalized vector gives a vector that is parallel to. The difference vector,, shown here as a dashed line, is perpendicular to.

A useful operation on vectors that is based on the dot product is the *Gram–Schmidt* process, which transforms a set of non-orthogonal vectors that form a basis into orthogonal vectors that span the same basis. It is based on successive application of the *orthogonal projection* of a vector onto a normalized vector, which is given by (see Figure [3.5](#fig:gs-orthogonal-projection)). The orthogonal projection can be used to compute a new vector

([3.2](#eq:gs-orthogonal-projection))

that is orthogonal to. An advantage of computing in this way is that and span the same subspace as and did.

The GramSchmidt() function implements Equation ([3.2](#eq:gs-orthogonal-projection)); it expects the vector w to already be normalized.

template <typename T> Vector3<T> GramSchmidt(Vector3<T> v, Vector3<T> w) { return v - [Dot](#Dot) (v, w) \* w; }

The *cross product* is another useful operation for 3D vectors. Given two vectors in 3D, the cross product is a vector that is perpendicular to both of them. Given orthogonal vectors and, then is defined to be a vector such that form an orthogonal coordinate system.

The cross product is defined as:

A way to remember this is to compute the determinant of the matrix:

where,, and represent the axes,, and, respectively. Note that this equation is merely a memory aid and not a rigorous mathematical construction, since the matrix entries are a mix of scalars and vectors.

The cross product implementation here uses the [DifferenceOfProducts()](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#DifferenceOfProducts) function that is introduced in Section [B.2.9](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#sec:fp-error-free-xforms). Given values a, b, c, and d, it computes a\*b-c\*d in a way that maintains more floating-point accuracy than a direct implementation of that expression would. This concern is not a theoretical one: previous versions of pbrt have resorted to using double precision for the implementation of Cross() so that numerical error would not lead to artifacts in rendered images. Using [DifferenceOfProducts()](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#DifferenceOfProducts) is a better solution since it can operate entirely in single precision while still computing a result with low error.

template <typename T> Vector3<T> Cross(Vector3<T> v, Vector3<T> w) { return { [DifferenceOfProducts](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#DifferenceOfProducts) (v.y, w.z, v.z, w.y), [DifferenceOfProducts](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#DifferenceOfProducts) (v.z, w.x, v.x, w.z), [DifferenceOfProducts](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#DifferenceOfProducts) (v.x, w.y, v.y, w.x)}; }

From the definition of the cross product, we can derive

([3.3](#eq:cross-sin-theta))

where is the angle between and. An important implication of this is that the cross product of two perpendicular unit vectors is itself a unit vector. Note also that the result of the cross product is a degenerate vector if and are parallel.

This definition also shows a convenient way to compute the area of a parallelogram (Figure [3.6](#fig:parallelogram-area)). If the two edges of the parallelogram are given by vectors and, and it has height, the area is given by. Since, we can use Equation ([3.3](#eq:cross-sin-theta)) to see that the area is.

[![](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f06.svg)](https://pbr-book.org/4ed/Geometry_and_Transformations/pha03f06.svg)

Figure 3.6: The area of a parallelogram with edges given by vectors and is equal to. From Equation ([3.3](#eq:cross-sin-theta)), the length of the cross product of and is equal to the product of the two vector lengths times the sine of the angle between them—the parallelogram area.

### 3.3.3 Coordinate System from a Vector

We will sometimes find it useful to construct a local coordinate system given only a single normalized 3D vector. To do so, we must find two additional normalized vectors such that all three vectors are mutually perpendicular.

Given a vector, it can be shown that the two vectors

fulfill these conditions. However, computing those properties directly has high error when due to a loss of accuracy when is calculated. A reformulation of that computation, used in the following implementation, addresses that issue.

<<Vector3 Inline Functions>>+=

template <typename T> void CoordinateSystem(Vector3<T> v1, Vector3<T> \*v2, Vector3<T> \*v3) { Float sign = pstd::copysign(Float(1), v1.z); Float a = -1 / (sign + v1.z); Float b = v1.x \* v1.y \* a; \*v2 = Vector3<T>(1 + sign \* [Sqr](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (v1.x) \* a, sign \* b, -sign \* v1.x); \*v3 = Vector3<T>(b, sign + [Sqr](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (v1.y) \* a, -v1.y); }