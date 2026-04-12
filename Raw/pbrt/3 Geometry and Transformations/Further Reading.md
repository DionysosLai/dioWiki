---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Geometry_and_Transformations/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
DeRose, Goldman, and their collaborators have argued for an elegant “coordinate-free” approach to describing vector geometry for graphics, where the fact that positions and directions happen to be represented by coordinates with respect to a particular coordinate system is deemphasized and where points and vectors themselves record which coordinate system they are expressed in terms of ([Goldman 1985](#cite:Goldman85); [DeRose 1989](#cite:DeRose89); [Mann et al. 1997](#cite:Mann97)). This makes it possible for a software layer to ensure that common errors like adding a vector in one coordinate system to a point in another coordinate system are transparently handled by transforming them to a common coordinate system first. A related approach was described by Geisler et al. ([^9]), who encoded coordinate systems using the programming language’s type system. We have not followed either of these approaches in pbrt, although the principles behind them are well worth understanding and keeping in mind when working with coordinate systems in computer graphics.

Schneider and Eberly’s *Geometric Tools for Computer Graphics* is influenced by the coordinate-free approach and covers the topics of this chapter in much greater depth (Schneider and Eberly [^19]). It is also full of useful geometric algorithms for graphics. A classic and more traditional introduction to the topics of this chapter is *Mathematical Elements for Computer Graphics* by Rogers and Adams ([^18]). Note that their book uses a row-vector representation of points and vectors, however, which means that our matrices would be transposed when expressed in their framework, and that they multiply points and vectors by matrices to transform them , rather than multiplying matrices by points as we do . Homogeneous coordinates were only briefly mentioned in this chapter, although they are the basis of projective geometry, where they are the foundation of many elegant algorithms. Stolfi’s book is an excellent introduction to this topic ([Stolfi 1991](#cite:Stolfi91)).

There are many good books on linear algebra and vector geometry. We have found Lang ([^13]) and Buck ([^3]) to be good references on these respective topics. See also Akenine-Möller et al.’s *Real-Time Rendering* book ([^1]) for a solid graphics-based introduction to linear algebra. Ström et al. have written an excellent online linear algebra book, [immersivemath.com](http://immersivemath.com/), that features interactive figures that illustrate the key concepts ([^21]).

Donnay’s book ([^6]) gives a concise but complete introduction to spherical trigonometry. The expression for the solid angle of a triangle in Equation ([3.6](https://pbr-book.org/4ed/Geometry_and_Transformations/Spherical_Geometry.html#eq:triangle-solid-angle-better)) is due to Van Oosterom and Strackee ([^22]).

An alternative approach for designing a vector math library is exemplified by the widely used *eigen* system by Guennebaud, Jacob, and others ([^10]). In addition to including support for CPU SIMD vector instruction sets, it makes extensive use of *expression templates*, a C++ programming technique that makes it possible to simplify and optimize the evaluation of vector and matrix expressions at compile time.

The subtleties of how normal vectors are transformed were first widely understood in the graphics community after articles by Wallis ([^23]) and Turkowski ([1990b](#cite:Turkowski90)).

Cigolle et al. ([^4]) compared a wide range of approaches for compactly encoding unit vectors. The approach implemented in [OctahedralVector](https://pbr-book.org/4ed/Geometry_and_Transformations/Spherical_Geometry.html#OctahedralVector) is due to Meyer et al. ([^16]), who also showed that if 52 bits are used with this representation, the precision is equal to that of normalized [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) s. (Our implementation also includes an improvement suggested by Cigolle et al. ([^4]).) The octahedral encoding it is based on was introduced by Praun and Hoppe ([^17]).

The equal-area sphere mapping algorithm in Section [3.8.3](https://pbr-book.org/4ed/Geometry_and_Transformations/Spherical_Geometry.html#sec:equi-area-sphere) is due to Clarberg ([^5]); our implementation of the mapping functions is derived from the high-performance CPU SIMD implementation that accompanies that paper. The square-to-hemisphere mapping that it is based on was developed by Shirley and Chiu ([^20]).

The algorithm used in [CoordinateSystem()](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#CoordinateSystem) is based on an approach first derived by Frisvad ([^8]). The reformulation to improve numerical accuracy that we have used in our implementation was derived concurrently by Duff et al. ([^7]) and by Max ([^15]). The algorithm implemented in [RotateFromTo()](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#RotateFromTo) was introduced by Möller and Hughes ([^14]), with an adjustment to the computation of the reflection vector due to Hughes ([^12]).

The numerically robust [AngleBetween()](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#AngleBetween) function defined in this chapter is due to Hatch ([^11]).

An algorithm to compute a tight bounding cone for multiple direction vectors was given by Barequet and Elber ([^2]).

### References

[^1]: Akenine-Möller, T., E. Haines, N. Hoffman, A. Peesce, M. Iwanicki, and S. Hillaire. 2018. *Real-Time Rendering* (4th ed.). Boca Raton, FL: CRC Press.

[^2]: Barequet, G., and G. Elber. 2005. Optimal bounding cones of vectors in three dimensions. *Information Processing Letters* *93*  (2), 83–89.

[^3]: Buck, R. C. 1978. *Advanced Calculus*. New York: McGraw-Hill.

[^4]: Cigolle, Z. H., S. Donow, D. Evangelakos, M. Mara, M. McGuire, and Q. Meyer. 2014. Survey of efficient representations for independent unit vectors. *Journal of Computer Graphics Techniques (JCGT)* *3*  (2), 1–30.

[^5]: Clarberg, P. 2008. Fast equal-area mapping of the (hemi)sphere using SIMD. *Journal of Graphics Tools* *13*  (3), 53–68.

[^6]: Donnay, J. D. H. 1945. *Spherical Trigonometry after the Cesàro Method*. New York, NY: Interscience Publishers.

[^7]: Duff, T., J. Burgess, P. Christensen, C. Hery, A. Kensler, M. Liani, and R. Villemin. 2017. Building an orthonormal basis, revisited. *Journal of Computer Graphics Techniques (JCGT)* *6*  (1), 1–8.

[^8]: Frisvad, J. R. 2012. Building an orthonormal basis from a 3d unit vector without normalization. *Journal of Graphics Tools* *16*  (3), 151–159.

[^9]: Geisler, D., I. Yoon, A. Kabra, H. He, Y. Sanders, and A. Sampson. 2020. Geometry types for graphics programming. *Proceedings of the ACM on Programming Languages (OOPSLA 2020)* *4*, 173:1–25.

[^10]: Guennebaud, G., B. Jacob, and others. 2010. Eigen v3. [http://eigen.tuxfamily.org](http://eigen.tuxfamily.org/)

[^11]: Hatch, D. 2003. The right way to calculate stuff. [http://www.plunk.org/ hatch/rightway.html](http://www.plunk.org/%C2%A0hatch/rightway.html)

[^12]: Hughes, J. F. 2021. Personal communication.

[^13]: Lang, S. 1986. *An Introduction to Linear Algebra*. New York: Springer-Verlag.

[^14]: Möller, T., and J. Hughes. 1999. Efficiently building a matrix to rotate one vector to another. *Journal of Graphics Tools* *4*  (4), 1–4.

[^15]: Max, N. 2017. Improved accuracy when building an orthonormal basis. *Journal of Computer Graphics Techniques (JCGT)* *6*  (1), 9–16.

[^16]: Meyer, Q., J. Süssmuth, G. Sussner, M. Stamminger, and G. Greiner. 2010. On floating-point normal vectors. *Proceedings of the 21st Eurographics Conference on Rendering*, 1405–9.

[^17]: Praun, E., and Hoppe, H. 2003. Spherical parameterization and remeshing. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2003)* *22*  (3), 340–49.

[^18]: Rogers, D. F., and J. A. Adams. 1990. *Mathematical Elements for Computer Graphics*. New York: McGraw-Hill.

[^19]: Schneider, P. J., and D. H. Eberly. 2003. *Geometric Tools for Computer Graphics*. San Francisco: Morgan Kaufmann.

[^20]: Shirley, P., and K. Chiu. 1997. A low distortion map between disk and square. *Journal of Graphics Tools* *2*  (3), 45–52.

[^21]: Ström, J., K. Åström, and T. Akenine-Möller. 2020. Immersive linear algebra. [immersivemath.com](http://immersivemath.com/)

[^22]: Van Oosterom, A., and J. Strackee. 1983. The solid angle of a plane triangle. *IEEE Transactions on Biomedical Engineering* *BME-30*  (2), 125–26.

[^23]: Wallis, B. 1990. Forms, vectors, and transforms. In A. S. Glassner (ed.), *Graphics Gems I*, 533–38. San Diego: Academic Press.