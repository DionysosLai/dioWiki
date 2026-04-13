---
title: "22 Implicit Modeling"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 22 Implicit Modeling

22
Brian Wyvill
Implicit Modeling
Implicit modeling (also known as implic it surfaces) in computer graphics covers
many different methods for deﬁning models. These include skeletal implicit mod-
eling, offset surfaces, level sets, variational surfaces,a n d algebraic surfaces.I n
this chapter, we brieﬂy touch on these methods and describe how to build skeletal
implicit models in more detail. Curves can be de ﬁned by implicit equations of the
form
f(x,y)=0 .
If we consider a closed curve, such as a circle, with radius r, then the implicit
equation can be written as
f(x,y)= x2 +y2 −r2 =0 . (22.1)
The value of f(x,y) can be positive (outside the circle), negative (inside the
circle), or zero for points precisely on the circle. The equivalent in three dimen-
sions is a closed surface around a set of points that occupy a given volume or
region of space. The volume forms a scalar ﬁeld, i.e., we can compute a value for
every point and as can be seen for the circle, the negative values are bounded by
the implicit curve or surface. The surface can be visualized as a contour in the
ﬁeld, connecting points with a particular value such as zero (see Equation (22.1)).
To compute such a surface implies searching through space to ﬁnd the points that
satisfy the implicit equation; this method is unlikely to lead to an ef ﬁcient al-
gorithm for circle drawing (and even less likely in three dimensions). This was
perhaps the reason that algorithmic methods for modeling with parametric curves
585

586 22. Implicit Modeling
and surfaces were investigated before implicit methods; however, there are some
good reasons to develop algorithms to visualize implicit surfaces. In this chapter
we explore the implications of deriving the data from a modeling process rather
than from a scanner.
Despite the computational overhead of ﬁnding the implicit surface, designing
with implicit modeling techniques offers some advantages over other modeling
methods. Many geometric operations are simpli ﬁed using implicit methods in-
cluding:
 the deﬁnition of blends;
 the standard set operations (union, intersection, difference, etc.) of con-
structive solid geometry (CSG);
 functional composition with other implicit functions (e.g., R-functions,
Barthe blends, Ricci blends, and warping);
 inside/outside tests, (e.g., for collision detection).
Visualizing the surfaces can be done either by direct ray tracing using an algo-
rithm as described in (Kalra & Barr, 1989; Mitchell, 1990; Hart & Baker, 1996;
deGroot & Wyvill, 2005) or by ﬁrst converting to polygons (Wyvill, McPheeters,
& Wyvill, 1986).
One of the ﬁrst methods was proposed by Ricci as far back as 1973 (Ricci,
1973), who also introduced CSG in the same paper. Jim Blinn’s algorithm for
Figure 22.1. Blinn’s
Blobby Man 1980. Image
courtesy Jim Blinn.
ﬁnding contours in electron density ﬁelds, known as Blobby molecules (J. Blinn,
1982), Nishimura’s Metaballs (Nishimura et al., 1985) and Wyvills’ Soft Ob-
jects (Wyvill et al., 1986) were all early examples of implicit modeling meth-
ods. Jim Blinn’s Blobby Man (see Figure 22.1) was the ﬁrst rendering of a non-
algebraic implicit model.
22.1 Implicit Functions, Skeletal Primitives,
and Summation Blending
In the context of modeling an implicit function is de ﬁned as a function f applied
to a point p ∈E3 yielding a scalar value ∈R.
The implicit function fi(x,y,z ) may be split into a distance function
di(x,y,z ) and a fall-off ﬁlter function 1 gi(r),w h e r er stands for the distance
from the skeleton and the subscript refers to the ith skeletal element.
1These functions have been given many names by researchers in the past, e.g., ﬁlter , potential,
radial-basis, kernel, but we use fall-off ﬁlter as a simple term to describe their appearance.

22.1. Implicit Functions, Skeletal Primitives, and Summation Blending 587
1
0.8
0.6
0.4
0.2
Distance d
(a) Blinn (b) Metaballs
(c) Soft Objects (d) Wyvill
g(d) = e–rd2
Field Value
0.2 0.4 0.6 0.8 1 1.2 1.4
1
0.8
0.6
0.4
0.2
11
0.8
0.6
0.4
0.2 Field Value
0.8
0.6
0.4
0.2
0.2 0.4 0.6 0.8 1
0.2 0.4 0.6 0.8 10.2 0.4 0.6 0.8 1
g(d) =
1 – 3(–) 2d
r
0 d > r
0 ≤ d ≤ –r
3
– ≤ d ≤ r3– (1 – –) 2d
r r3
2
1 – d2 3
r2g(d) =1 – +4d6 3
9r6
17d4
9r4 –22d2
9r2g(d) =
Figure 22.2. Fall-off ﬁlter functions (0 ≤ r ≤ 1). (a) Blinn’s Gaussian or “Blobby” function; (b)
Nishimura’s “Metaball” function; (c) Wyvill et al.’s “soft objects” function; (d) the Wyvill function.
We will use the following notation:
fi(x,y,z )= gi ◦di(x,y,z ) (22.2)
A simple example is a point primitive, and we take the analogy of a star ra-
diating heat into space. The ﬁeld value (temperature in this example) may be
measured at any point p and can be found by taking the distance from p to the
center of the star and supplying the value to a fall-off ﬁlter function similar to
one of those given in Figure 22.2. In these sample functions, the ﬁeld is given a
value of 1 at the center of the star; the va lue falls off with distance. The surface
of a model may be derived from the implicit function f(x,y,z ) as the points of
space whose values are equal to some desired iso-value (iso); in the star example,
a spherical shell for values of iso ∈(0,1).
In general, ﬁlter functions ( gi) are chosen so that the ﬁeld values are max-
imized on the skeleton and fall off to zero at some chosen distance from the

588 22. Implicit Modeling
Figure 22.3. Each column shows two point primitives approaching each other. From left to right:
the fall-off ﬁlter functions used are Blobby, Metaball, soft objects, and Wyvill. Image courtesy Erwin
DeGroot.
skeleton. In the simple case where the resulting surfaces are blended together,
the global ﬁeldf(x,y,z ) of an object, the implicit function, may be de ﬁned as
f(x,y,z )=
i=n∑
i=1
fi(x,y,z ), (22.3)
where n skeletal elements contribute to the resulting ﬁeld value. An example is
shown in Figure 22.3 in which the ﬁeld at any point (x,y,z ) is calculated as in
Equation (22.3).
In this case, two point primitives are placed in close proximity. As the two
points are brought together, the surfaces bu lge and then blend together. The term
ﬁlter function is used because the function causes the primitives to be blurred
together somewhat akin to a ﬁlter function for images. The summation blend is
the most compact and ef ﬁcient blending operation that can be applied to implicit
surfaces (see Equation (22.3)).
One advantage of using ﬁlter functions with ﬁnite support is that primitives
that are far from p will have zero contribution and thus need not be consid-
ered (Wyvill et al., 1986).

22.1. Implicit Functions, Skeletal Primitives, and Summation Blending 589
22.1.1 C1 Continuity and the Gradient
The most basic form of continuity isC0 continuity, which ensures that there are no
“jumps” in a function. Higher-order continuity is de ﬁned in terms of derivatives
of functions (see Chapter 15).
In the case of a 3D scalar ﬁeldf ,t h eﬁrst derivative is a vector function known
as the gradient, written ∇f and deﬁned as
∇f(p)=
{∂f(p)
∂x , ∂f(p)
∂y , ∂f(p)
∂z
}
.
If∇f is deﬁned at all points, and the three one-dimensional partial derivatives
are each C0,t h e nf is C1. Informally, C1 surface continuity means that the
surface normal varies smoothly over the surface. The surface normal is the unit
vector perpendicular to the surf ace. If no unique surface normal can be de ﬁned
on the edge of a cube, for example, then the surface is not C1. For points on an
implicit surface, the surface normal can be computed by normalizing the gradient
vector ∇f . In the example of the circle, points inside have a negative value and
those on the outside have a positive one. F or many types of implicit surfaces, the
sense of inside and outside is inverted, and since the normal vector must always
point outward, it can be opposite to the gradient direction.
Skeletal implicit primitives are created by applying a fall-off ﬁlter function to
an unsigned distance ﬁeld as in Equation (22.2). Although the distance ﬁeld is
neverC1 at the skeleton, these discontinuities can be removed by using a suitable
fall-off function (Akleman & Chen, 1999). If an operator, g, combines implicit
functions, f1 and f2, where all points are C1,t h e ng(f1,f2) is not necessarily
C1. For example, it is possible to make a sharp CSG junction using the min and
max operators. The combination is not C1 continuous because the min and max
operators don’t have that property (see Section 22.5).
The analysis of operators is complicated by the fact that it is sometimes de-
s i r a b l et oc r e a t eaC1 discontinuity. This case occurs whenever a crease in the
surface is desired. For example, a cube is not C1 because tangent discontinu-
ities occur at each edge. To create creases using C1 primitives, the operator must
introduceC1 discontinuities, and hence cannot be C1 itself.
22.1.2 Distance Fields, R-Functions, and F-Reps
The distance ﬁeld is deﬁned with respect to some geometric object T:
F(T, p)=m i nq∈T
|q−p|.
Visually, F(T, p) is the shortest distance from p to T.Hence, when p lies on T,

590 22. Implicit Modeling
F(T, p)=0 and the surface created by the implicit function is the object T.
Outside of T, a nonzero distance is returned. The function T can be any geometric
entity embedded in 3D—a point, curve, surface, or solid. Procedural modeling
with distance ﬁelds started with Ricci (Ricci, 1973); R-functions (Rvachev, 1963)
were ﬁrst applied to shape modeling more than 20 years later (see (Shapiro, 1994)
and (A. Pasko, Adzhiev, Sourin, & Savchenko, 1995)).
An R-function or Rvachev function is a function whose sign can change if
and only if the sign of one of its arguments changes; that is, its sign is determined
solely by its arguments. R-functions provide a robust theoretical framework for
boolean composition of real functions, permitting the construction of C
n CSG
operators (Shapiro, 1988). These CSG operators can be used to create blending
operators simply by adding a ﬁxed offset to the result (A. Pasko et al., 1995).
Although these blending functions are no longer technically R-functions, they
have most of the desirable properties and can be mixed freely with R-functions
to create complex hierarchical models (Shapiro, 1988). These R-function-based
blending and CSG operators are referred to as R-operators (see Section 22.4).
The Hyperfun system (Adzhiev et al., 1999) is based on F-reps (function repre-
sentation), another name for an implicit surface. The system uses a procedural
C-like language to describe many types of implicit surfaces.
22.1.3 Level Sets
It is useful to represent an implicit ﬁeld discretely via a regular grid (Barthe, Mora,
Dodgson, & Sabin, 2002) or an adaptive grid (Frisken, Perry, Rockwood, & Jones,
2000). This is exactly what the polygonization algorithm does in the case of level
sets; moreover, the grid can be used for various other purposes besides build-
ing polygons. Discrete representations of f are commonly obtained by sampling
a continuous function at regular intervals. For example, the sampled function
may be de ﬁned by other volume model representations (V. V . Savchenko, Pasko,
Sourin, & Kunii, 1998). The data may also be a physical object sampled using
three-dimensional imaging techniques. Discrete volume data has most often been
used in conjunction with the level sets method (Osher & Sethian, 1988), which
deﬁnes a means for dynamically modifying the data structure using curvature-
dependent speed functions. Interactive modeling environments based on level
sets have been de ﬁned (Museth, Breen, Whitaker, & Barr, 2002), although level
sets are only one method employing a discrete representation of the implicit ﬁeld.
Methods for interactively deﬁning discrete representations using standard implicit
surfaces techniques have also been explored (Baerentzen & Christensen, 2002).
A key advantage to employing a discrete data structure is its ability to act as
a unifying approach for all of the various volume models de ﬁned by potential

22.1. Implicit Functions, Skeletal Primitives, and Summation Blending 591
ﬁelds (discrete or not) (V. V . Savchenko et al., 1998). The conversion of any
continuous function to a discrete representation introduces the problem of how
to reconstruct a continuous function, needed for the combined purposes of addi-
tional modeling operations and visualization of the resulting potential ﬁeld. A
well-known solution to this problem is to apply a ﬁlter g using the convolution
operator (see Chapter 9). The choice of a ﬁlter is guided by the desired properties
of the reconstruction, and many ﬁlters have been explored (Marschner & Lobb,
1994). The salient point is that there is typically a tradeoff between the ef ﬁciency
of the chosen ﬁlter and the smoothness of the resulting reconstruction; see also
Section 22.9.
To be interactive, a discrete system must restrict the size of the grid relative
to the available computing power. This, in turn, limits the ability of the mod-
eler to include high-frequency details. A dditionally, the smoothing triquadratic
ﬁlter makes it impossible to include sharp edges, should they be desired. A par-
tial solution to this problem is the use of adaptive grids, although with any dis-
crete representation there will be limitations. A discrete grid is used in (Schmidt,
Wyvill, & Galin, 2005) to act as a cache representing a BlobTree node. The grid in
this work is used for fast prototyping and uses trilinear interpolation for position
and the slower, more accurate triquadratic interpolation to calculate gradient val-
ues, because the eye is more discerning in observing gradient errors than position
errors.
22.1.4 Variational Implicit Surfaces
It is often required to convert sampled data to an implicit representation. V aria-
tional implicit surfaces interpolate or approximate a set of points using a weighted
sum of globally supported basis functions (V . Savchenko, Pasko, Okunev, & Ku-
nii, 1995; Turk & O’Brien, 1999; Carr et al., 2001; Turk & O’Brien, 2002). These
radially symmetric basis functions are applied at each sample point. The continu-
ity of such a surface depends on the choice of basis function. The C
2 thin-plate
spline is most commonly used (Turk & O’Brien, 2002; Carr et al., 2001). Like
Blinn’s exponential function (see Figure 22.2), this function is unbounded as is
the resulting variational implicit surface.
If the ﬁe l di si sg l o b a l l yC
2, creases cannot be de ﬁned;2 however, anisotropic
basis functions can be used to produce ﬁelds which change more rapidly and may
appear to have creases (Dinh, Slabaugh, & Turk, 2001). At the appropriate scale,
the surface is still smooth. The smooth ﬁeld implies that self-intersections do not
2 Except see Section 15.2.

592 22. Implicit Modeling
occur, and hence volumes are always well-de ﬁned. The thin-plate spline guaran-
tees that global curvature is minimized (Duchon, 1977). V ariational interpolation
has many properties which are desirable for 3D modeling; however, controlling
the resulting surfaces can be dif ﬁcult.
V ariational implicit surfaces can also be based on compactly supported radial
basis functions (CS-RBFs) to reduce the computational cost of variational inter-
polation techniques (Morse, Y oo, Rheingans, Chen, & Subramanian, 2001). Each
CS-RBF only in ﬂuences a local region, so computing f(p) requires only evalua-
tion of basis functions within some small neighborhood of p. As with the globally
supported counterpart, the resulting ﬁeld is C
k, creases are not supported, and
self-intersections cannot occur. 3 The local support of each basis function results
in a bounded global ﬁeld. This also guarantees that additional iso-contours will be
present, as noted by various researchers (Ohtake, Belyaev, & Pasko, 2003; Reuter,
2003).
22.1.5 Convolution Surfaces
Convolution surfaces, introduced by Bl oomenthal and Shoemake (Bloomenthal
& Shoemake, 1991) are produced by convolving a geometric skeleton S with a
kernel function h. Hence, the value at any position in space is de ﬁned by an
integral over the skeleton:
f(p)=
∫
S
g(r)h(p−r)dr.
Any ﬁnitely supported function can be used as h; see (Sherstyuk, 1999) for a
detailed analysis of different kernels.
Like skeletal primitives, convolution surfaces have bounded ﬁelds. Blinn’s
“Blobby molecules” is the simplest form of a convolution surface (J. Blinn, 1982);
in this case, the skeleton consists of points only. This idea was extended by
Bloomenthal to include line, arc, triangle, and polygon skeletons (Bloomenthal
& Shoemake, 1991). These represent 1D and2D primitives;3D primitives were
later described by Bloomenthal (Bloomenthal, 1995).
Figure 22.4. Two
blended cylinders. Left: sum-
mation blend; right: convolu-
tion surface with barely dis-
cernible bulge (Bloomenthal,
1997). Image courtesy Erwin
DeGroot.
Combination of convolution surfaces is de ﬁned by composition of the un-
derlying geometric skeletons and has the advantage of eliminating the bulges that
tend to occur when composing multiple skeletal primitives with additive blending.
The surface resulting from convolution of the combined skeleton does not have
bulges, as in Figure 22.4, and the ﬁeld is continuous even if the combined skele-
3Note,k> 0 depending on the RBF (see Section 15.2).

22.1. Implicit Functions, Skeletal Primitives, and Summation Blending 593
Figure 22.5. Skeletal elements convolved to build a hand model. Image courtesy Jules Bloomenthal.
ton is nonconvex. Convolution surfaces are offset a ﬁxed distance from convex
portions of a skeleton, but produce a ﬁllet along concave portions of a skeleton.
An example of skeletal elements convolved to build a complex model is shown
in Figure 22.5. The hand model contains fourteen primitives.
22.1.6 Deﬁning Skeletal Primitives
As we will see in the following sections rendering the implicit models requires
ﬁnding the ﬁeld value and gradient for a large number of points. We need the
distance to supply to Equation (22.2) and the gradient is useful for root ﬁnding as
well as lighting calculations. Supplying the distance to the fall-off ﬁlter functions
of Figure 22.2 is a matter of calculating the nearest distance to the skeletal primi-
tive, simple for point primitives but a little trickier for more complex geometrical
shapes. A line segment primitive ( AB) can be de ﬁned as a cylinder around a line
with hemispherical end caps (see Figure 22.6). Point P0 lies on the surface where
y
xx = a
y = b
f(x,y) = y – mx – b
f(x,y) = 0
m = –b/a
Figure 22.6. Line primi-
tive ab and example points p0,
p1, p2 showing distance calcu-
lation.
f(P0) = isoandf(P1)=0 since it lies outside of the in ﬂuence of the line primi-
tive. The distance from some Pi to the line is found by simply projecting onto the
lineAB and calculating the perpendicular distance, e.g., |CP0|; this can be found

594 22. Implicit Modeling
fromAC,s i n c eA,P0, and B, are all known:
⃗AC = ⃗AB
⃗AP0 · ⃗AB
∥AB∥2 .
In Figure 22.6, the ﬁeld value ofP2 > 0,s i n c eP2 is in the hemispherical end-
cap, which can be checked separately. V ariations of this idea can deﬁne primitives
with endcaps of different radii producing interesting cone shapes. An example is
shown in Figure 22.7.
Figure 22.7. Cylinder prim-
itive blended with a sphere.
Image courtesy Erwin DeG-
root.
A great variety of geometrical skeletons have been described, and, in princi-
ple, it is simply a matter of deﬁning the distance to the skeleton from some point p
and also the gradient at p. For example, an offset surface of a triangle can be de-
ﬁned from the vertices of the triangle and a radius r. A simple way to implement
this is to use line segment primitives to describe bounding cylinders connecting
the vertices (radius r). The distance from a point q within the triangle that does
not fall within the bounding ﬁelds of one of the line segment primitives is returned
as the perpendicular distance to the plane of the triangle. Other examples include
an implicit disk, deﬁned by a circle and a thickness parameter, a torus also de ﬁned
by a circle and the radius of the cross section (or inner and outer circle radii), a
circular cone from a disk and a height, a cube with rounded corners, etc. (see
Figure 22.8).
22.2 Rendering
Modeling methods, such as parametric surf aces, lend themselves to visualization,
since it is easy to iterate over points on the surface that can be found directly from
the deﬁning equations; for example (x,y)=( c o sθ,sinθ),θ ∈[0,2π) produces
a circle.
Figure 22.8. Implicit mod-
els from various skeletal prim-
itives. Image courtesy Erwin
DeGroot. There are two techniques that are commonly used to render implicit surfaces:
ray tracing and surface tiling. In practice, a designer wants to visualize an implicit
surface model quickly, sacriﬁcing quality for speed for interaction purposes. Pro-
totyping algorithms have been concerned with producing a polygon mesh that can
be rendered in real time on modern workstations. Finding the polygonal mesh
which best approximates the desired surface is referred to as polygonization or
surface tiling . For animation or for a ﬁnal visualization, where quality is pre-
ferred over speed, ray tracing implicit surfaces directly without ﬁrst polygonizing
produces excellent results.
Figure 22.9. A ray-traced
dinosaur model showing the
underlying skeletal primitives.
Image courtesy Erwin DeG-
root.
As previously mentioned, ﬁnding an implicit surface requires searching
through space to ﬁnd the points that satisfy, f(p)=0 . There are two main ap-
proaches to executing such a search: space partitioning—partitioning space into

22.3. Space Partitioning 595
manageable units such as cubes, and non-sp ace partitioning, e.g., marching trian-
gles (Hartmann, 1998; Akkouche & Galin, 2001) and the shrinkwrap algorithm
(van Overveld & Wyvill, 2004).
In this chapter, we describe the original space partitioning algorithm and leave
it to the reader to explore the more advanced methods. This algorithm together
with postprocessing for mesh re ﬁnement (see Chapter 12) and caching provide a
method for interactive viewing of implicit models on modern workstations.
22.3 Space Partitioning
22.3.1 Exhaustive Search
The basic cubic space partitioning algorithm for tiling implicit surfaces was ﬁrst
published in (Wyvill et al., 1986) and a similar algorithm oriented toward volume
visualization, called marching cubes in (Lorensen & Cline, 1987). Since then
there have been many re ﬁnements and extensions.
A ﬁrst approach to ﬁnding the implicit surface might be to subdivide space
uniformly into a regular lattice of cubic cells and calculate a value for every ver-
tex. Each cell is replaced with a set of polygons that best approximates the part
of the surface contained within that cell. The problem with this method is that
many of the cells will be completely outside or completely inside the volume;
thus, many cells that contain no part of the surface are processed. For large grids
of data this can be very time consuming and memory intensive.
To avoid storing the whole grid, a hash table is used to store only the cubes
that contain a piece of the surface, based on the data structures used in (Wyvill et
al., 1986). Working software was published in Graphics Gems IV (Bloomenthal,
1990). The algorithm is based on numerical continuation ; it starts with a seed
cube that intersects part of the surface and builds neighboring cubes as necessary
to follow the surface.
The algorithm has two parts. In the ﬁrst part, cubic cells are found that contain
the surface and in the second part, each cube is replaced by triangles. The ﬁrst
part of the algorithm is driven by a queue of cubes, each of which contains part of
the surface; the second part of the algorithm is table-driven.
22.3.2 Algorithm Description
A fast overview of the algorithm is as follows:
 divide space into cubic voxels;
 search for surface, startin g from a skeletal element;

596 22. Implicit Modeling
 add voxel to queue, mark it visited;
 search neighbors;
 when done, replace voxel with polygons.
First, space is subdivided into a cubic lattice, and the next task is to ﬁnd a seed
cube containing part of the surface. A cube vertex vi inside the surface will have
a ﬁeld value vi >=i s oand a vertex outside the surface will have a ﬁeld value
vi < iso; thus, an edge with one of each type of vertex will intersect the surface.
We call this an intersecting edge. The ﬁeld value at the nearest cube vertex to the
ﬁrst primitive can be evaluated by summing the contributions of the primitives
as per Equation (22.3), although other operators can also be used as will be seen
later. We will assume that f(v0) > iso, which indicates that v0 lies within the
solid. The value of iso is chosen by the user; an example is iso = 0.5 when using
the soft fall-off function, which has some symmetry properties that lead to nice
blending (see Figure 22.3). The vertices along one axis are evaluated in turn until
av a l u evi < iso is found. The cube containing the intersecting edge is the seed
cube.
The neighbors of the seed cube are examined, and those that contain at least
one intersecting edge are added to the queue ready for processing. To process a
cube, we examine each face. If any of t he bounding edges have oppositely signed
vertices, the surface will pass through that face and the face neighbor must be
processed. When this process has been completed for all the faces, the second
phase of the algorithm is applied to the cube. If the surface is closed, eventually
a cube will be revisited and no more unmarked neighbors found, and the search
algorithm will terminate. Processing a cube involves marking it as processed
and processing its unmarked neighbors. Those that contain intersecting edges are
processed until the entire surface has been covered (see Figure 22.10).
Each cube is indexed by an identifying vertex which we deﬁne to be the lower-
left far corner (i.e., the vertex with the lowest (x,y,z )-coordinate values (see Fig-
ure 22.11)). For each vertex that is inside the surface, the corresponding bit will
be set to form the address in an 8-bit table (see Figure 22.11 and Section 22.3.3).
The identifying vertex is addressed by integers i,j,k , computed from the
(x,y,z )-coordinate location of the cube such that x = side∗i,e t c . ,w h e r eside is
the size of the cube. The identifying vertex of each cube may appear in as many
as eight other cubes, and it would be inef ﬁcient to store these vertices more than
once. Thus, the vertices are stored uniquely in a chained hash table. Since most
of the space does not contain any part of the surface, only those cubes that are
visited will be stored. The implicit func tion value is found for each vertex as it is
stored in the hash table.

22.3. Space Partitioning 597
Surface Skeleton Voxel
–
+
Figure 22.10. A section through the cubic lattice. The + sign indicates a vertex inside the surface
(f (vi ≥iso) and - is outside f (vi < iso).
Nothing is known about the topology of the surface so a search must be started
from every primitive to avoid any disconnected parts of the surface being missed.
A scalar can be used to scale the in ﬂuence of a primitive. If the scalar can be less
than zero, then it is possible to search along an axis without ﬁnding an intersect-
ing edge. In this case, a more sophisticated search must be done to ﬁnd a seed
cube (Galin & Akkouche, 1999).
0 1
2 3
4 5
6 7
Top
Front
Right
0  0  00000001
1  01  00000010
2  010  00000100
3  011  00001000
4  100  00010000
5  101  00100000
6  110  01000000
7  111  10000000
Vertex             If (+)
Figure 22.11. Ve r t ex n u m -
bering.
Data Structures
The hash table entry holds ﬁve values:
 thei,j,k lattice indices of the identifying vertex (see Figure 22.11);
 f , the implicit function value of the identifying vertex;
 Boolean to indicate whether this cube has been visited.
The hash function computes an address in the hash table by selecting a few bits out
of each of i,j,k and combining them arithmetically. For example, the ﬁve least
signiﬁcant bits produces a 15-bit address for a table, which must have a length

598 22. Implicit Modeling
of 215. Such a hash function can be neatly implemented in the C-preprocessor as
follows:
#define NBITS 5
#define BMASK 037
#define HASH(a,b,c) (((a&BMASK)<<NBITS|b&BMASK)
<<NBITS|c&BMASK)
#define HSIZE 1<<NBITS *3
The queue (FIFO list) is used as temporary storage to identify the neighbors
for processing. The algorithm begins with a seed cube that is marked as visited
and placed on the queue. The ﬁrst cube on the queue is dequeued and all its
unvisited neighbors are added to the queue. Each cube is processed and passed to
the second phase of the algorithm if it cont ains part of the surface. The queue is
then processed until empty.
22.3.3 Polygonization Algorithm
The second phase of the algorithm treat s each cube independently. The cell is
replaced by a set of triangles that best matches the shape of the part of the surface
that passes through the cell. The algorithm must decide how to polygonize the cell
given the implicit function values at each vertex. These values will be positive or
negative (i.e., less than or greater than the iso-value), giving 256 combinations
of positive or negative vertices for the eight vertices of the cube. A table of 256
entries provides the right vertices to us e in each triangle (Figure 22.12). For ex-
ample, entry 4(00000100) points to a second table that records the vertices that
bound the intersecting edges. In this example, vertex number 2 is inside the sur-
face (f(V2) >=i s o) and, therefore, we wish to draw a triangle that connects the
points on the surface that intersect with edges bounded by (V 2,V 0), (V 2,V 3),
and (V 2,V 6) as shown in Figure 22.13.
Finding Cube-Surface Intersections
Figure 22.13 shows a cube where vertex V2 is inside the surface and all other
vertices are outside. Intersections with the surface occur on three edges as shown.
The surface intersects edge V
2 −V6 at the point A. The fastest, but inaccurate,
way to calculate A is to use linear interpolation:
f(A)−f(V2)
f(V6)−f(V2) = |A−V2|
side .

22.3. Space Partitioning 599
Table 1
00000000
V1 set
V0 & V1 set
V0 set
V2 set
all unset
00000001
00000010
00000011
00000100
V0..V7 set
V1..V7 set
all set except
V1 unset11111101
11111110
11111111
Table 2
Table 2
0
1
2
3
V2-V0
1
3
# polys
# edges
V2-V3
V2-V6
edges to
intersect
Figure 22.12. Table 2 contains the edges intersected by the surface. Table 1 points to the appropriate
entry in Table 2.
If the cube side is 1 and the iso-value sought for f(A) is0.5,t h e n
A = V3 + 0.5−f(V2)
f(V6)−f(V2).
This works well for a static image, but in animation error differences between
frames will be very noticeable. A root- ﬁnding method such as regula falsi should
be employed. This becomes more computationally costly as the gradient is needed
to evaluate the point of intersection. The gradient is also needed at surface points
for rendering. For many types of primitives it is simpler to ﬁnd a numerical ap-
proximation using sample points around p,a si n
∇f(p)=
(f(p+Δx)−f(p)
Δx , f(p+Δy)−f(p)
Δy , f(p+Δz)−f(p)
Δz
)
.
A reasonable value for Δhas been found empirically to be 0.01∗side where side
is the length of a cube edge.
+
–
–
–
––
–V3
V0
V6
V2
A
Figure 22.13. Finding the
intersection of the surface with
a cube edge.
For manufacturing a mesh, as opposed to a set of independent triangles, a
second hash table can maintain a list of all the intersecting edges. Since each cube
edge is shared by up to four neighbors, th e edge hash table prevents repetition of
the surface-cube edge intersection calculation. The hash address can be derived
from the same hash function as for vertices (applied to the edge endpoints).

600 22. Implicit Modeling
+ +
+
– –
–
–
–
+
++
–
–
–
–
–
+
–
–
+–
–
Figure 22.14. Examples of vertices inside (+) and outside (-) the surface. Note the extra sample
gives a clue to avoid ambiguous cases.
22.3.4 Sampling Problems
Ambiguities occur when opposite corners of a face (or the cube) have the same
sign and the other pair of vertices on the face have the opposite sign (see Figure
22.14). A sample taken in the center of the face will give a clue as to whether the
cube represents the meeting of two surfaces or a saddle. It should be made clear
that a spatial grid stores a sample of the implicit function at every vertex. If the
function happens to vary considerably within a cell, the polygonal representation
will not show such variations (see Figure 22.15). The surface cannot be resolved
by sampling alone unless something is known about the curvature of the surface.
A good discussion of this topic appears in (Kalra & Barr, 1989).
–
–
+
+
Figure 22.15. Cube too
large to capture small varia-
tion in implicit function.
This ambiguity problem (not the undersampling problem) is avoided by sub-
dividing the cubic cell into tetrahedra. The tetrahedra can then be polygonized
unambiguously. Since there are four vertices in each tetrahedron, a table of 16
entries will provide the correct triangle information. The disadvantage is that
approximately twice the number of polygons will be generated.
Subdividing a Cube
Without requiring additional cell vertices, a cube may be decomposed into ﬁve or
six tetrahedra as shown in Figure 22.16. These decompositions introduce diago-
nals on the cube faces, and to maintain a consistent diagonal direction between

22.4. More on Blending 601
Figure 22.16. Decomposing a cube into six tetrahedra. Image courtesy Erwin DeGroot.
neighbors, the six decomposition is pre ferable. The introduction of diagonal
edges produces a higher-resolution surf ace than replacing each cube directly with
triangles. The decomposition into tetrahedra and the replacement of the tetrahe-
dra with triangles are fast, table-driven algorithms, which produce topologically
consistent meshes.
22.3.5 Cell Polygonization
Two obvious problems emerge from the use of uniform space subdivision. The
size of triangles output by this algorithm do not adapt to the curvature of the sur-
face and a further sample is required to solve the ambiguities, in which cubic cells
are replaced by polygons. A space subdivision algorithm based on an octree was
developed by Bloomenthal (Bloomenthal, 1988), which does adapt to the curva-
ture of the surface. Cells are subdivided int o eight octants and cracks are avoided
by using a restricted octree scheme, i.e., neighboring cells cannot differ by more
than one level of subdivision. This indeed reduces the number of polygons gen-
erated, but full advantage of large cells can only be taken if the ﬂat regions of
the surface happen to fall entirely within the appropriate octants. The algorithm
proves in practice to be considerably slower than the uniform voxel algorithm and
is more complicat ed to implement.
22.4 More on Blending
Section 22.1 showed that blending can be made to occur when ﬁeld values are
summed. Ricci, in his landmark paper (Ricci, 1973), describes super-elliptic

602 22. Implicit Modeling
blending. Given two functions FA and FB , previously we simply found the im-
plicit value as Ftotal = FA + FB . We can denote this more general blending
operator as A⋄B. The Ricci blend is de ﬁned as:
fA⋄B =( fA
n +fB
n)
1
n . (22.4)
It is interesting to point out the following properties:
lim
n→+∞
(fA
n +fB
n)
1
n =m a x(fA,fB),
lim
n→−∞
(fA
n +fB
n)
1
n = min(fA,fB).
Figure 22.17. By vary-
ing n, the Ricci blend may
be made to change smoothly
from blend to union. Image
courtesy Erwin DeGroot.
Moreover, this generalized blending is associative, i.e.,f(A⋄B)⋄C = fA⋄(B⋄C).
The standard blending operator + proves to be a special case of the super-elliptic
blend with n =1 .W h e n n varies from 1 to in ﬁnity, it creates a set of blends
interpolating between blending A+B and union A∪B (see Figure 22.17). Fig-
ure 22.27 shows the nodes to be binary or unary; in fact the binary nodes can
easily be extended using the above formulation to n-ary nodes.
The power of Ricci’s operators is that they are closed under the operations
on the space of all possible implicit volumes, meaning that an application of an
operator simply produces another scalar ﬁeld deﬁ ning another implicit volume.
This new ﬁeld can be composed with other ﬁelds, again using Ricci’s operators.
Equation (22.4) will always produce the exact union of two implicit volumes,
regardless of how complex they are. Compared with the dif ﬁculties involved in
applying boolean CSG operations to B-rep surfaces, solid modeling with implicit
volumes is incredibly simple.
Following Pasko’s functional representation (A. Pasko et al., 1995), another
generalized blending function may be de ﬁned:
f
A⋄B =
(
fA +fB +α
√
fA
2 +fB
2
)(
fA
2 +fB
2)n
2
.
Whenα∈[−1,1] varies from −1 to1, it creates a set of blends interpolating
the union and the intersection operators. However, this operator is no longer
associative which is incompatible with the de ﬁnition of n-ary operators.
22.5 Constructive Solid Geometry
Implicit models are frequently termed implicit surfaces; however, they are inher-
ently volume models and useful for solid modeling operations. Ricci introduced a

22.5. Constructive Solid Geometry 603
A
B
P0 P2
P1
Figure 22.18. Ricci operators for CSG. Image courtesy Erwin DeGroot.
constructive geometry for deﬁning complex shapes from operations such as union,
intersection, difference, and blend upon primitives (Ricci, 1973). The surface was
considered as the boundary between the half spaces f(p) < 1,d e ﬁning the in-
side, and f(p) > 1 deﬁning the outside. This initial approach to solid modeling
evolved into constructive solid geometry or CSG (Ricci, 1973; Requicha, 1980).
CSG is typically evaluated bottom-up according to a binary tree, with low-degree
polynomial primitives as the leaf nodes and internal nodes representing Boolean
set operations. These methods are readily adapted for use in implicit modeling,
and in the case of skeletal implicit surfaces, the Boolean set operations union
∪
max, intersection ∩min and difference \minmax are de ﬁned as follows (Wyvill,
Galin, & Guy, 1999):
∪max f =
k−1
max
i=0
(fi), (22.5)
∩min f =
k−1
min
i=0
(fi),
\minmax f =m i n
(
f0,2∗iso−
k−1
max
j=1
(fj)
)
.
The Ricci operators are illustrated in Figure 22.18 for point primitives A
and B. For union (bottom left) the ﬁeld at all points inside the union will be the
greater of fA() and fB(). For intersection (center), points in the region marked
asP1 will have valuemin(fA(P1),fB(P1)) = 0, since the contribution of B will
be zero outside of its range of in ﬂuence. Similarly, for the region marked as P2,
(inﬂuence of A is zero, i.e., the minimum) leaving only the intersection region
with positive values. Difference works similarly using the iso-value in the three

604 22. Implicit Modeling
marked regions (Pi) as follows:
f(P0)=m i n ( fB(P0),2∗iso−fA(P0))
= min([iso ,1],[2∗iso−1,iso])
=[ 2 ∗iso−1,iso] < iso
f(P1)=m i n ( fB(P1),2∗iso−fA(P1))
= min([0 ,iso],[2∗iso−1,iso]) < iso
f(P2)=m i n ( fB(P2),2∗iso−fA(P2))
= min([iso ,1],[iso,2∗iso]) >=i s o
CSG operators create creases, i.e., C1 discontinuities. For example, the min()
operator (Equation (22.5)) creates C1 discontinuities at all points where f1(p)=
f2(p). When applied to two spheres, the discontinuities produced by this union
operator result in a crease on the surface, as shown in Figure 22.18, which is
the desired result. Discontinuities unfortunately extend into the ﬁeld outside of
the surface, which is not visible in thi s image. If a blend is then applied to the
result of the union, the C1-discontinuous plane in the ﬁeld produces a shading
discontinuity (Figure 22.19).
Crease
Figure 22.19. Two point
primitives on the left are con-
nected by the Ricci union.
A third primitive is blended
to the result, creating an un-
wanted crease in the ﬁeld. Im-
age courtesy Erwin DeGroot.
The problem can be avoided to an extent (G. Pasko, Pasko, Ikeda, & Kunii,
2002), and CSG operators have been developed that are C1 at all points except
those where f1(p)= f2(p) = iso(Barthe, Dodgson, Sabin, Wyvill, & Gaildrat,
2003).
22.6 Warping
The ability to distort the shape of a surface by warping the space in its neigh-
borhood is a useful modeling tool. A warp is a continuous function w(x,y,z )
that maps R3 onto R3. Sederberg provides a good analogy for warping when de-
scribing free form deformations (Sederberg & Parry, 1986). He suggests that the
warped space can be likened to a clear, ﬂexible, plastic parallelepiped in which
the objects to be warped are embedded. A warped element may be de ﬁned by
simply applying some warp function w(p) to the implicit equation:
fi(x,y,z )= gi ◦di ◦wi(x,y,z ). (22.6)
A warped element may be fully characterized by the distance to its skele-
ton di(x,y,z ), its fall-off ﬁlter function gi(r), and eventually its warp function
wi(x,y,z ). To render or perform operations on an implicit surface, the implicit
P
Q
Figure 22.20. Point Q re-
turns the ﬁeld value for point
P.

22.6. Warping 605
value of many points f(P) must be found. First, P is transformed by the warp
function to some new point Q,a n d f(Q) is returned in place of f(P).I n F i g -
ure 22.20, instead of returning the implicit value of some point f(Q),t h ev a l u e
forf(P) is returned. In this case, the iso-value is returned and the implicit surface
(curve in 2D) passes through Q instead of P . Thus, the circle is warped into an
ellipse.
Barr introduced the notion of global and local deformations using the opera-
tions of twist, taper,a n d bend applied to parametric surfaces (Barr, 1984). The
deformations can be nested to produce models such as the one shown in Fig-
ure 22.27. Conceptually, these are easy to apply to an implicit surface, as indi-
cated in Equation (22.6).
Note that the normal cannot be calculated in a similar manner to warping a
point. This problem is similar to the problem outlined in Section 13.2 on in-
stancing. In this case, the normal can most easily be approximated using Equa-
tion (22.3.3) although the use of the Jacobian, as suggested in (Barr, 1984), yields
precise results. The Barr warps are described in the following sections.
22.6.1 T wist
In this example, the twist is around the z-axis by θ(see Figure 22.21) for three
blended implicit cylinders with a twist warp applied to them.
The twist around z is expressed as
w(x,y,z )=
⎧
⎨
⎩
x∗cos(θ(z))−y ∗sin(θ(z))
x∗sin(θ(z)) +y ∗cos(θ(z))
z
⎫
⎬
⎭.
Figure 22.21. Three
blended implicit cylinders
twisted together. Image
courtesy Erwin DeGroot.
Figure 22.22. Three
blended implicit cylinders,
twisted then tapered. Image
courtesy Erwin DeGroot.
22.6.2 T aper
Taper is applied along one major axis. A linear taper has proved to be the most
useful although quadratic and cubic tapers are easily implemented. For example,
a linear taper along the y-axis involves changing both x-a n dz-coordinates. (See
Figure 22.22.) A linear scale is applied to y betweenymax and ymin:
s(y)= ymax −y
ymax −ymin
w(x,y,z )=
⎧
⎨
⎩
s(y)x
y
s(y)z
⎫
⎬
⎭

606 22. Implicit Modeling
22.6.3 Bend
Bend is also applied along one major axis. (See Figure 22.23.) For the bend
example below, the bending rate is k measured in radians per unit length, the axis
of the bend is (x0,1/k), and the angle θis de ﬁned as (x −x0) ∗k. The bend
aroundz is
Figure 22.23. Three
blended implicit cylinders,
twisted together, tapered and
bent. Image courtesy Erwin
DeGroot.
w(x,y,z )=
⎧
⎨
⎩
−sin(θ)∗(y −1/k)+ x0
cos(θ)∗(y −1/k)+1 /k
z
⎫
⎬
⎭
22.7 Precise Contact Modeling
Precise contact modeling (PCM) is a method of deforming implicit surface prim-
itives in contact situations while maintaining a precise contact surface with C1
continuity (Gascuel, 1993). PCM is important in that it is a simple and automatic
way of showing how a model can react to its environment. This cannot be so
easily done with non-implicit methods (see Figure 22.24).
PCM is implemented by the inclusion of a deforming function s(p) that mod-
iﬁes the ﬁeld value returned for each point. For each pair of objects, collision is
Figure 22.24. Sea anemone
deforms to implicit rock. Im-
age courtesy Mai Nur and X.
Liang.
ﬁrst detected using a bounding-box test. Once it is established that a collision is
likely, PCM is applied. A local, geometric deformation term si is computed and
added to the implicit function fi. The volume of the colliding objects is divided
into an interpenetration region and a deformation region. The result of applying
si is that the interpenetration region is compressed so that contact is maintained
without interpenetration occurring (see Figure 22.25). The effect of si is attenu-
ated to zero within the propagation region so that the volume outside of the two
regions is not deformed.
Given two skeletal elements generating ﬁelds f
1(p) and f2(p), the surface
around each one is calculated as
f1(p)+ s1(p)=0 ,
f2(p)+ s2(p)=0 .
We need to generate a surface common to both elements (dotted line in Fig-
ure 22.25), i.e., where they share a solution in the interpenetration region for some
p in that region:
s
1(p)−f1(p) = iso , (22.7)
s2(p)−f2(p) = iso .

22.7. Precise Contact Modeling 607
Interpenetration
region
Propagation
region
f0 = f1f0 = 0
f1 = 0
p
p0
Figure 22.25. A 2D slice through objects in collision showing the various regions and PCM
deformation. Image courtesy Erwin DeGroot.
Intuitively, the deeper within object 1 that object 2 penetrates, the higher the im-
plicit value of object 1 and thus the more that object 2 will be compressed.
The function, si is de ﬁned to produce a smooth junction at the boundary of
the interpenetration region, in other words where si =0 but its derivative is
greater than zero. From here to the boundary of the propagation region, si is used
to attenuate the propagation to zero. The nearest point on the interpenetration
region boundary p0 is found by following the gradient.
Within the propagation region si(p)= hi(r),w h e r ep =( x,y,z ) is the point
whose implicit value is being calculated and r = ∥p−p0∥ (see Figure 22.26). The
value of ri, set by the user, de ﬁnes the size of the propagation region; no defor-
mation occurs beyond this region. To control how much the objects in ﬂate in the
propagation region, the user provides a value for the parameter α. The maximum
r
WiWi/2
k
hi
Max = Mi
Figure 22.26. The function, hi(r) is the value of the deformation function wi in the propagation
region.

608 22. Implicit Modeling
value of hi is Mi. The current minimum of si is negative in the interpenetra-
tion region and is given as simin,w h e r eMi = −αisimin. Thus an object will
be compressed in the interpenetration region and will in ﬂate in the propagation
region. The equation for hi is formed in two parts by two cubic polynomials that
are designed to join at r = ri/2, where the slope is zero:
c = 4(wik −4Mi)
w3
i
,
d = 4(3Mi −wik)
w2
i
,
hi(r)= cr3 +dr2 +kr if r ∈[0,wi/2],
hi(r)= 4Mi
w3
i
(r−wi)2(4r−wi)3 if r ∈[wi/2,wi].
It is desirable that we have C1-continuity as we move from the interpenetra-
tion to the propagation region. Thus, h′
i(0) =k in Figure 22.26, is the directional
derivative of si at the junction (marked as p0 in Figure 22.25). As indicated in
Equation (22.7), si = −fi in the interpenetration region, thus:
k = ∥∇(fi,p0)∥
PCM is only an approximation to a properly deformed surface, but it is an
attractive algorithm due to its simplicity.
22.8 The BlobT ree
The BlobTree is a method that employs a tree structure that extended the CSG
tree to include various blending operations using skeletal primitives (Wyvill et
al., 1999). A system with similar capabilities, the Hyperfun project, used a spe-
cialized language to describe F-rep objects (Adzhiev et al., 1999).
In the BlobTree system, models are de ﬁned by expressions that combine im-
plicit primitives and the operators ∪(union), ∩(intersection), − (difference),
+ (blend), ⋄ (super-elliptic blend), and w (warp). The BlobTree is not only the
data structure built from these expressions but also a way of visualizing the struc-
ture of the models. The operators listed above are binary with the exception of
warp, which is a unary operator. In general it is more ef ﬁcient to use n-ary rather
than binary operators. The BlobTree incorporates af ﬁne transformations as nodes
so that it is also a scene graph and primitives (e.g., skeletons) form the leaf nodes.

22.8. The BlobT ree 609
Figure 22.27. BlobTree. The spiral staircase is built fro m a central textured cylinder to which the
stairs and the railing are blended. The railing is com prised of a series of cylinders blended with two
circle (torus) primitives, blended together and further blended with a vertical cylinder. The BlobTree is
also a scene graph and instancing nodes repeat the various parts transformed by the appropriate matri-
ces. Each stair is made from a tapered polygon primitive (that becomes an offset surface); intersection
and union nodes combine the in ﬂated disk with the stair.
22.8.1 T raversing the BlobT ree
An example of a BlobTree including the Barr warps and CSG operations is shown
in Figure 22.27. Other nodes can include 2D texturing (Schmidt, Grimm, &
Wyvill, 2006), precise contact modeling, as well as animation and other attributes.
The traversal of the BlobTree is in essence very simple. All that is required to ren-
der the object either by polygonizing or ray tracing is to ﬁnd the implicit value of
any point (and the corresponding gradient). This can be done by traversing the
tree. Polygonization and ray-tracing algorithms need to evaluate the implicit ﬁeld
function at a large number of points in space. The function f(N,M ) returns the
ﬁeld value for the node N at the point M , which depends on the type of the node.
The values L and R indicate that the left or right branch of the tree is explored.
The algorithm below is written (for simplicity) as if the tree were binary:
functionf(N,M ):
 primitive: f(M);
 warp: f(L(N),w(M));
 blend: f(L(N),M )+ f(R(N),M ));
 union: max(f(L(N),M ),f (R(N),M ));

610 22. Implicit Modeling
Figure 22.28. “Spiral Stairs.” A complex BlobTree implicit model created in Erwin DeGroot’s
BlobTree.net system.
 intersection: min(f(L(N),M ),f (R(N),M ));
 difference: min(f(L(N),M ),−f(R(N),M )).
A complex BlobTree model showing many of the features that have been in-
tegrated is shown in Figure 22.28.
22.9 Interactive Implicit Modeling Systems
Early sketch-based modeling systems , such as Teddy (Igarashi, Matsuoka, &
Tanaka, 1999), used a few drawn strokes from the user to infer a polygonal model
in 3-space. With better hardware and improved algorithms, sketch-based implicit
modeling systems are now possible. Sh apeshop uses implicit sweep surfaces to
Figure 22.29. Outlines are
inﬂated. Image courtesy Er-
win DeGroot.
manufacture 3D strokes from 2D user strokes and also preserves the hierarchy
of the BlobTree unlike the early systems that produced homogeneous meshes
(Schmidt, Wyvill, Sousa, & Jorge, 2005). This enables a user to produce com-
plex models of arbitrary topology from a few simple strokes. The margin ﬁgures
show a closed drawn stroke (Figure 22.29) inﬂ ated into a an implicit sweep and
a second sweep (Figure 22.30) that has a smaller sweep object subtracted using
CSG.

22.9. Interactive Implicit Modeling Systems 611
One of the improvements that made this possible is a caching system that uses
a ﬁxed 3D grid of implicit values at each node of the BlobTree representing the
values found by traversing the tree bel ow the node (Schmidt, Wyvill, & Galin,
2005). If the value of some point p is required at node N , a value may be returned
without traversing the tree below N , provided that part of the tree is unaltered.
Instead, an interpolation scheme (see Chapter 9) is used to ﬁnd a value for p.T h i s
scheme speeds up traversal for complex BlobTrees and is one factor in enabling a
system to run at interactive rates.
Figure 22.30. BlobTree op-
erations can be applied, e.g.,
CSG difference. Image cour-
tesy Erwin DeGroot.
The next generation of implicit modeling systems will exploit hardware
and software advances to be able to handle more and more complex hierarchical
models interactively. A more complex Shapeshop example is shown in Fig-
ure 22.31.
Figure 22.31. “The Next Step.” A complex BlobTree implic it model created interactively in Ryan
Schmidt’s Shapeshop by artist Corien Clapwijk (Andusan).

612 22. Implicit Modeling
Exercises
1. In an implicit surface modeling system the fall-off ﬁlter function is de ﬁned
as
f(r)=
{
0,r > R ,
1−r/R, otherwise,
where R is a constant. A point primitive placed at (−1,0) and another
at (1,0) are rendered to show the f =0 .5 iso-surface. The value R,t h e
distance where the potential due to the point falls to zero in both cases, is
1.5.
Calculate the potential at the point (0,0) and at +0.5 intervals until the
point (2.5,0).S k e t c h t h e0.5 contour and the contour at which the ﬁeld
falls to zero.
2. Why are the ambiguous cases in the polygonization algorithm considered
to be a sampling problem?
3. Calculate the error involved in using linear interpolation to estimate the
intersection of an implicit surface and a cubic voxel.
4. Design an implicit primitive function using the skeleton of your choice. The
function must take as input a point and return an implicit value and also the
gradient at that point.

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
