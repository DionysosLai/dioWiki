---
title: "6 Transformation Matrices"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 6 Transformation Matrices

6
T ransformation Matrices
The machinery of linear algebra can be used to express many of the operations
required to arrange objects in a 3D scene, view them with cameras, and get them
onto the screen. Geometric transformations like rotation, translation, scaling, and
projection can be accomplished with matrix multiplication, and the transforma-
tion matrices used to do this are the subject of this chapter.
We will show how a set of points transforms if the points are represented as
offset vectors from the origin, and we will use the clock shown in Figure 6.1 as
an example of a point set. So think of the clock as a bunch of points that are the
ends of vectors whose tails are at the origin. We also discuss how these transforms
operate differently on locations (points), displacement vectors, and surface normal
vectors.
6.1 2D Linear T ransformations
We can use a 2 × 2 matrix to change, or transform, a 2D vector:
[
a11 a12
a21 a22
][
x
y
]
=
[
a11x+a12y
a21x+a22y
]
.
This kind of operation, which takes in a 2-vector and produces another 2-vector
by a simple matrix multiplication, is a linear transformation.
109

110 6. T ransformation Matrices
By this simple formula we can achieve a variety of useful transformations,
depending on what we put in the entries of the matrix, as will be discussed in
the following sections. For our purposes, consider moving along the x-axis a
horizontal move and along the y-axis, a vertical move.
6.1.1 Scaling
The most basic transform is a scale along the coordinate axes. This transform can
change length and possibly direction:
scale(sx,sy) =
[
sx 0
0 sy
]
.
Note what this matrix does to a vector with Cartesian components (x,y):
[sx 0
0 sy
][ x
y
]
=
[sxx
syy
]
.
So, just by looking at the matrix of an axis-aligned scale, we can read off the two
scale factors.
Example. The matrix that shrinks x and y uniformly by a factor of two is (Fig-
ure 6.1)
scale(0.5,0.5)=
[0.50
00 .5
]
.
A matrix which halves in the horizontal an d increases by three-halves in the ver-
tical is (see Figure 6.2)
scale(0.5,1.5)=
[
0.50
01 .5
]
.
y
x
0.5
0
0
0.5
y
x
Figure 6.1. Scaling uniformly by half for each axis: The axis-aligned scale matrix has the proportion
of change in each of the diagonal elements a nd zeroes in the off-diagonal elements.

6.1. 2D Linear T ransformations 111
y
x
0.5
0
0
1.5
y
x
Figure 6.2. Scaling nonuniformly in x and y: The scaling matrix is diagonal with non-equal
elements. Note that the square outline of the clock b ecomes a rectangle and the circular face becomes
an ellipse.
6.1.2 Shearing
A shear is something that pushes things sideways, producing something like a
deck of cards across which you push your hand; the bottom card stays put and
cards move more the closer they are to the top of the deck. The horizontal and
vertical shear matrices are
shear-x(s) =
[
1 s
01
]
, shear-y(s) =
[
10
s 1
]
.
Example. The transform that shears horizontally so that vertical lines become45◦
lines leaning toward the right is (see Figure 6.3)
shear-x(1)=
[11
01
]
.
1
y y
xx
0
1
1
Figure 6.3. An x-shear matrix moves points to the right in proportion to their y-coordinate. Now
the square outline of the clock becomes a parallelogr am and, as with scaling, the circular face of the
clock becomes an ellipse.

112 6. T ransformation Matrices
1
y y
xx
1
0
1
Figure 6.4. A y-shear matrix moves points up in proportion to their x-coordinate.
An analogous transform vertically is (see Figure 6.4)
shear-y(1)=
[10
11
]
.
In both cases, the square outline of the sheared clock becomes a parallelogram,
In fact, the image of a cir-
cle under any matrix trans-
formation is an ellipse.
and the circular face of the sheared clock becomes an ellipse.
Another way to think of a shear is in terms of rotation of only the vertical
(or horizontal) axis. The shear transform that takes a vertical axis and tilts it
clockwise by an angle φis [
1t a nφ
01
]
.
Similarly, the shear matrix which rotates the horizontal axis counterclockwise by
angle φis [
10
tanφ 1
]
.
6.1.3 Rotation
Suppose we want to rotate a vector a by an angle φcounterclockwise to get
vector b (Figure 6.5). If a makes an angle αwith the x-axis, and its length is
r =
√
x2a +y2a, then we know that
xa = rcosα,
ya = rsinα.

6.1. 2D Linear T ransformations 113
Because b is a rotation of a, it also has length r. Because it is rotated an angle
φfrom a, b makes an angle (α+ φ) with the x-axis. Using the trigonometric
addition identities (Section 2.3.3):
b y
a
α
φ
x
Figure 6.5. The geometry
for Equation (6.1).
xb = rcos(α+φ)= rcosαcosφ−rsinαsinφ,
yb = rsin(α+φ)= rsinαcosφ+rcosαsinφ. (6.1)
Substitutingxa = rcosαand ya = rsinαgives
xb = xa cosφ−ya sinφ,
yb = ya cosφ+xa sinφ.
In matrix form, the transformation that takes a to b is then
rotate(φ) =
[
cosφ −sinφ
sinφ cosφ
]
.
Example. A matrix that rotates vectors by π/4 radians (45 degrees) is (see Fig-
ure 6.6) [
cos π
4 −sin π
4
sin π
4 cos π
4
]
=
[
0.707 −0.707
0.707 0.707
]
.
(0,1)
(0,1)
(–.707,.707)
(.707,.707)
.707
.707
xx
y
–.707
.707
Figure 6.6. A rotation by 45 ◦. Note that the rotation is counterclockwise and that cos(45 ◦)=
sin(45◦) ≈.707.
A matrix that rotates by π/6 radians (30 degrees) in the clockwise direction is
a rotation by −π/6 radians in our framework (see Figure 6.7):
[cos −π
6 −sin −π
6
sin −π
6 cos −π
6
]
=
[ 0.866 0.5
−0.50 .866
]
.

114 6. T ransformation Matrices
y
x
.866
–.5
.5
.866
y
(.5,.866)
(1,0)
(.866,.5)
(0,1)
Figure 6.7. A rotation by –30 degrees. Note that the rotation is clockwise and that cos(–30 ◦) ≈
.866 and sin(–30◦) = –.5.
Because the norm of each row of a rotation matrix is one (sin2 φ+cos2 φ=1 ),
and the rows are orthogonal ( cosφ(−sinφ)+s i nφcosφ=0 ), we see that ro-
tation matrices are orthogonal matrices (Section 5.2.4). By looking at the matrix
we can read off two pairs of orthonormal vectors: the two columns, which are the
vectors to which the transformation sends the canonical basis vectors (1,0) and
(0,1); and the rows, which are the vectors that the transformations sends to the
canonical basis vectors.
Said brie ﬂy, Rei = ui and
Rvi = ui, for a rotation with
columns ui and rows vi.
6.1.4 Reﬂection
We can reﬂect a vector across either of the coordinate axes by using a scale with
one negative scale factor (see Figures 6.8 and 6.9):
reﬂect-y =
[
−10
01
]
, reﬂect-x =
[
10
0 −1
]
.
–1
y y
xx
0
0
1
Figure 6.8. Ar eﬂection about the y-axis is achieved by multiplying all x-coordinates by –1.

6.1. 2D Linear T ransformations 115
1
y y
xx
0
0
–1
Figure 6.9. Ar eﬂection about the x-axis is achieved by multiplying all y-coordinates by –1.
While one might expect that the matrix with −1 in both elements of the diagonal
is also a re ﬂection, in fact it is just a rotation by πradians.
This rotation can also be
called a “re ﬂection through
the origin.”
6.1.5 Composition and Decomposition of T ransformations
It is common for graphics programs to apply more than one transformation to an
object. For example, we might want to ﬁrst apply a scale S, and then a rotation
R. This would be done in two steps on a 2D vector v1:
ﬁrst,v2 = Sv1, then,v3 = Rv2.
Another way to write this is
v3 = R(Sv1).
Because matrix multiplication is associative, we can also write
v3 =( RS) v1.
In other words, we can represent the effects of transforming a vector by two ma-
trices in sequence using a single matrix of the same size, which we can compute
by multiplying the two matrices: M = RS (Figure 6.10).
It is very important to remember that these transforms are applied from the
right side ﬁrst. So the matrix M = RS ﬁrst applies S and then R.

116 6. T ransformation Matrices
x
1.0   0
 0   0.5
y
x
y
x
y
.707  –707
.707  .707.707 –.353
.707  .353
Figure 6.10. Applying the two transform matrices in se quence is the same as applying the product
of those matrices once. This is a key concept that underlies most graphics hardware and software.
Example. Suppose we want to scale by one-half in the vertical direction and then
rotate by π/4 radians (45 degrees). The resulting matrix is
[
0.707 −0.707
0.707 0.707
][
10
00 .5
]
=
[
0.707 −0.353
0.707 0.353
]
.
It is important to always remember that matrix multiplication is not commutative.
So the order of transforms does matter. In this example, rotating ﬁrst, and then
scaling, results in a different matrix (see Figure 6.11):
[10
00 .5
][ 0.707 −0.707
0.707 0.707
]
=
[0.707 −0.707
0.353 0.353
]
.
Example. Using the scale matrices we have presented, nonuniform scaling can
only be done along the coordinate axes. If we wanted to stretch our clock by
50% along one of its diagonals, so that 8:00 through 1:00 move to the northwest
and 2:00 through 7:00 move to the southeast, we can use rotation matrices in
combination with an axis-aligned scaling matrix to get the result we want. The
idea is to use a rotation to align the scaling axis with a coordinate axis, then
scale along that axis, then rotate back . In our example, the scaling axis is the
“backslash” diagonal of the square, and we can make it parallel to the x-axis with

6.1. 2D Linear T ransformations 117
y
x
y
x
x
y
x
y
x
y
Scale
Scale
Rotate
Rotate
x
Figure 6.11. The order in which two transforms are app lied is usually important. In this exam-
ple, we do a scale by one-half in y and then rotate by 45 ◦. Reversing the order in which these two
transforms are applied yie lds a different result.
a rotation by +45◦. Putting these operations together, the full transformation is
rotate(−45◦) scale(1.5,1) rotate(45◦).
Remember to read the
transformations from right
to left.
In mathematical notation, this can be written RSRT. The result of multiply-
ing the three matrices together is
It is no coincidence that
this matrix is symmetric—
try applying the transpose-
of-product rule to the for-
mula RSR
T.
[ 1.25 −0.25
−0.25 1.25
]

118 6. T ransformation Matrices
Building up a transformation from rotation and scaling transformations actu-
ally works for any linear transformati on, and this fact leads to a powerful way of
thinking about these transformations, as explored in the next section.
6.1.6 Decomposition of T ransformations
Sometimes it’s necessary to “undo” a composition of transformations, taking a
transformation apart into simpler pieces. F or instance, it’s often useful to present
a transformation to the user for manipulation in terms of separate rotations and
scale factors, but a transformation might be represented internally simply as a
Rotate (–58.3°)
Scale (1.618, 0.618)
Rotate (31.7°)
y
x
y
x
y
y
Figure 6.12. Singular V alue Decomposition (SVD) for a shear matrix. Any 2D matrix can be
decomposed into a product of rotation, scale, rota tion. Note that the circular face of the clock must
become an ellipse because it is ju st a rotated and scaled circle.

6.1. 2D Linear T ransformations 119
matrix, with the rotations and scales already mixed together. This kind of manip-
ulation can be achieved if the matrix can be computationally disassembled into the
desired pieces, the pieces adjusted, and the matrix reassembled by multiplying the
pieces together again.
It turns out that this decomposition, or factorization, is possible, regardless of
the entries in the matrix—and this fact provides a fruitful way of thinking about
transformations and what they do to geometry that is transformed by them.
Symmetric Eigenvalue Decomposition
Let’s start with symmetric matrices. Recall from Section 5.4 that a symmetric ma-
trix can always be taken apart using the eigenvalue decomposition into a product
of the form
A = RSR
T
where R is an orthogonal matrix and S is a diagonal matrix; we will call the
columns of R (the eigenvectors) by the names v1 and v2, and we’ll call the diag-
onal entries of S (the eigenvalues) by the names λ1 andλ2.
In geometric terms we can now recognize R as a rotation and S as a scale, so
this is just a multi-step geometric transformation (Figure 6.13):
1. Rotate v1 and v2 to the x-a n dy-axes (the transform by RT).
2. Scale in x andy by (λ1,λ2) (the transform by S).
3. Rotate the x-a n dy-axes back to v1 and v2 (the transform by R).
If you like to count di-
mensions: a symmetric 2
× 2 matrix has 3 de-
grees of freedom, and the
eigenvalue decomposition
rewrites them as a rotation
angle and two scale factors.
Looking at the effect of these three transforms together, we can see that they have
the effect of a nonuniform scale along a pair of axes. As with an axis-aligned
scale, the axes are perpendicular, but they aren’t the coordinate axes; instead they
RT
v2
v1
σ2v2
σ1v1
SR
Figure 6.13. What happens when the unit circle is transformed by an arbitrary symmetric matrix
A, also known as a non–axis-aligned, nonuniform scale. The two perpendicular vectors v1 and v2,
which are the eigenvectors of A, remain ﬁxed in direction but get scaled. In terms of elementary
transformations, this can be seen as ﬁrst rotating the eigenvectors to the canonical basis, doing an
axis-aligned scale, and then rotating the canonical basis back to the eigenvectors.

120 6. T ransformation Matrices
2
y y
xx
1
1
1
Figure 6.14. A symmetric matrix is always a scale along some axis. In this case it is along the φ=
31.7◦direction which means the real eigenvector for this matrix is in that direction.
are the eigenvectors of A. This tells us something about what it means to be a
symmetric matrix: symmetric matrices are just scaling operations—albeit poten-
tially nonuniform and non–axis-aligned ones.
Example. Recall the example from Section 5.4:
[21
11
]
= R
[λ1 0
0 λ2
]
RT
=
[
0.8507 −0.5257
0.5257 0.8507
][
2.618 0
00 .382
][
0.8507 0.5257
−0.5257 0.8507
]
= rotate (31.7◦) scale (2.618,0.382) rotate(−31.7◦).
The matrix above, then, according to its eigenvalue decomposition, scales in a
direction 31.7◦counterclockwise from three o’clock (the x-axis). This is a touch
before 2 p.m. on the clockface as is con ﬁrmed by Figure 6.14.
We can also reverse the diagonalization process; to scale by (λ1,λ2) with the
ﬁrst scaling direction an angle φclockwise from the x-axis, we have
[ cosφ sinφ
−sinφ cosφ
][ λ1 0
0 λ2
][ cosφ −sinφ
sinφ cosφ
]
=
[
λ1 cos2 φ+λ2 sin2 φ (λ2 −λ1)cos φsinφ
(λ2 −λ1)cos φsinφλ2 cos2 φ+λ1 sin2 φ
]
.
We should take heart that this is a symmetric matrix as we know must be true
since we constructed it from a symmetric eigenvalue decomposition.

6.1. 2D Linear T ransformations 121
VT
v2
v1
σ2u2
σ1u1
SU
Figure 6.15. What happens when the unit circle is transformed by an arbitrary matrix A.T h et w o
perpendicular vectors v1 and v2, which are the right singular vectors of A, get scaled and changed in
direction to match the left singular vectors, u1 and u2. In terms of elementary transformations, this
can be seen as ﬁrst rotating the right singular vectors to t he canonical basis, doing an axis-aligned
scale, and then rotating the canonical basis to the left singular vectors.
Singular Value Decomposition
A very similar kind of decomposition can be done with nonsymmetric matrices
as well: it’s the Singular V alue Decomposition (SVD), also discussed in Sec-
tion 5.4.1. The difference is that the matrices on either side of the diagonal matrix
are no longer the same:
A = USV
T
The two orthogonal matrices that replace the single rotation R are called U and
V, and their columns are called ui (the left singular vectors )a n d vi (the right
singular vectors), respectively. In this context, the diagonal entries of S are called
singular values rather than eigenvalues. The geometric interpretation is very sim-
ilar to that of the symmetric eigenvalue decomposition (Figure 6.15):
1. Rotate v1 and v2 to the x-a n dy-axes (the transform by VT).
2. Scale in x andy by (σ1,σ2) (the transform by S).
3. Rotate the x-a n dy-axes to u1 and u2 (the transform by U).
For dimension counters: a
general 2 × 2 matrix has
4 degrees of freedom, and
the SVD rewrites them as
two rotation angles and two
scale factors. One more bit
is needed to keep track of
reﬂections, but that doesn’t
add a dimension.
The principal difference is between a single rotation and two different orthogonal
matrices. This difference causes another, less important, difference. Because the
SVD has different singular vectors on the two sides, there is no need for neg-
ative singular values: we can always ﬂip the sign of a singular value, reverse
the direction of one of the associated singular vectors, and end up with the same
transformation again. For this reason, the SVD always produces a diagonal ma-
trix with all positive entries, but the matrices U and V are not guaranteed to be
rotations—they could include re ﬂection as well. In geometric applications like
graphics this is an inconvenience, but a minor one: it is easy to differentiate ro-
tations from re ﬂections by checking the determinant, which is +1 for rotations

122 6. T ransformation Matrices
and −1 for reﬂections, and if rotations are desired, one of the singular values can
be negated, resulting in a rotation–scale–rotation sequence where the re ﬂection is
rolled in with the scale, rather than with one of the rotations.
Example. The example used in Section 5.4.1 is in fact a shear matrix (Figure 6.12):
[
11
01
]
= R2
[
σ1 0
0 σ2
]
R1
=
[
0.8507 −0.5257
0.5257 0.8507
][
1.618 0
00 .618
][
0.5257 0.8507
−0.8507 0.5257
]
= rotate (31.7◦) scale (1.618,0.618) rotate(−58.3◦).
An immediate consequence of the existence of SVD is that all the 2D transforma-
tion matrices we have seen can be made from rotation matrices and scale matrices.
Shear matrices are a convenience, but they are not required for expressing trans-
formations.
In summary, every matrix can be decomposed via SVD into a rotation times
a scale times another rotation. Only sy mmetric matrices can be decomposed via
eigenvalue diagonalization into a rotation times a scale times the inverse-rotation,
and such matrices are a simple scale in an arbitrary direction. The SVD of a
symmetric matrix will yield the same tr iple product as eigenvalue decomposition
via a slightly more complex algebraic manipulation.
Paeth Decomposition of Rotations
Another decomposition uses shears to rep resent nonzero rotations (Paeth, 1990).
The following identity allows this:
[cosφ −sinφ
sinφ cosφ
]
=
[1 cosφ−1
sinφ
01
][ 10
sinφ 1
][ 1 cosφ−1
sinφ
01
]
.
For example, a rotation by π/4 (45 degrees) is (see Figure 6.16)
rotate(π
4)=
[
11 −
√
2
01
][ 10√
2
2 1
][
11 −
√
2
01
]
. (6.2)
This particular transform is useful for raster rotation because shearing is a
very efﬁcient raster operation for images; it introduces some jagginess, but will

6.2. 3D Linear T ransformations 123
y
x
y
x
y
x
x
Shear
Shear
Shear
Figure 6.16. Any 2D rotation can be accomplished by three shears in sequence. In this case a
rotation by 45◦is decomposed as shown in Equation 6.2.
leave no holes. The key observation is that if we take a raster position (i,j) and
apply a horizontal shear to it, we get
[1 s
01
][ i
j
]
=
[i+sj
j
]
.
If we round sj to the nearest integer, this amounts to taking each row in the
image and moving it sideways by some amount—a different amount for each
row. Because it is the same displacemen t within a row, this allows us to rotate
with no gaps in the resulting image. A similar action works for a vertical shear.
Thus, we can implement a simp le raster rotation easily.
6.2 3D Linear T ransformations
The linear 3D transforms are an extension of the 2D transforms. For example, a
scale along Cartesian axes is
scale(s
x,sy,sz)=
⎡
⎣
sx 00
0 sy 0
00 sz
⎤
⎦. (6.3)

124 6. T ransformation Matrices
Rotation is considerably more compli cated in 3D than in 2D, because there are
more possible axes of rotation. However, if we simply want to rotate about the
z-axis, which will only change x-a n dy-coordinates, we can use the 2D rotation
matrix with no operation on z:
rotate-z(φ)=
⎡
⎣
cosφ −sinφ 0
sinφ cosφ 0
00 1
⎤
⎦.
Similarly we can construct matrices to rotate about the x-axis and the y-axis:
rotate-x(φ)=
⎡
⎣
10 0
0c o sφ −sinφ
0s i nφ cosφ
⎤
⎦,
To understand why the mi-
nus sign is in the lower
left for the y-axis rotation,
think of the three axes in a
circular sequence: y after x;
z after y; x after z.
rotate-y(φ)=
⎡
⎣
cosφ 0s i nφ
01 0
−sinφ 0c o sφ
⎤
⎦.
We will discuss rotations about arbitrary axes in the next section.
As in two dimensions, we can shear along a particular axis, for example,
shear-x(d
y,dz)=
⎡
⎣
1 dy dz
010
001
⎤
⎦.
As with 2D transforms, any 3D transformation matrix can be decomposed using
SVD into a rotation, scale, and another rotation. Any symmetric 3D matrix has
an eigenvalue decomposition into rotation, scale, and inverse-rotation. Finally, a
3D rotation can be decomposed into a product of 3D shear matrices.
6.2.1 Arbitrary 3D Rotations
As in 2D, 3D rotations are orthogonal matrices. Geometrically, this means that the
three rows of the matrix are the Cartesian coordinates of three mutually orthogo-
nal unit vectors as discussed in Section 2.4.5. The columns are three, potentially
different, mutually orthogonal unit vectors. There are an in ﬁnite number of such
rotation matrices. Let’s write down such a matrix:
R
uvw =
⎡
⎣
xu yu zu
xv yv zv
xw yw zw
⎤
⎦.

6.2. 3D Linear T ransformations 125
Here, u = xux+yuy +zuz a n ds oo nf o rv and w. Since the three vectors are
orthonormal we know that
u· u = v· v = w· w =1 ,
u· v = v· w = w· u =0 .
We can infer some of the behavior of the rotation matrix by applying it to the
vectors u, v and w. For example,
Ruvw u =
⎡
⎣
xu yu zu
xv yv zv
xw yw zw
⎤
⎦
⎡
⎣
x
u
yu
zu
⎤
⎦=
⎡
⎣
x
uxu +yuyu +zuzu
xvxu +yvyu +zvzu
xwxu +ywyu +zwzu
⎤
⎦.
Note that those three rows of R
uvw u are all dot products:
Ruvw u =
⎡
⎣
u· u
v· u
w· u
⎤
⎦=
⎡
⎣
1
0
0
⎤
⎦= x.
Similarly, R
uvw v = y,a n d Ruvw w = z.S o Ruvw takes the basis uvw to the
corresponding Cartesian axes via rotation.
If Ruvw is a rotation matrix with orthonormal rows, then RT
uvw is also a ro-
tation matrix with orthonormal columns, and in fact is the inverse of Ruvw (the
inverse of an orthogonal matrix is always its transpose). An important point is that
for transformation matrices, the algebraic inverse is also the geometric inverse. So
if Ruvw takes u to x,t h e nRT
uvw takes x to u. The same should be true of v and
y as we can con ﬁrm:
RT
uvw
y =
⎡
⎣
xu xv xw
yu yv yw
zu zv zw
⎤
⎦
⎡
⎣
0
1
0
⎤
⎦=
⎡
⎣
x
v
yv
zv
⎤
⎦= v.
So we can always create rotation matrices from orthonormal bases.
If we wish to rotate about an arbitrary vector a, we can form an orthonormal
basis with w = a, rotate that basis to the canonical basis xyz, rotate about the
z-axis, and then rotate the canonical basis back to the uvw basis. In matrix form,
to rotate about the w-axis by an angle φ:
⎡
⎣
x
u xv xw
yu yv yw
zu zv zw
⎤
⎦
⎡
⎣
cosφ −sinφ 0
sinφ cosφ 0
00 1
⎤
⎦
⎡
⎣
x
u yu zu
xv yv zv
xw yw zw
⎤
⎦.
Here we have w a unit vector in the direction of a (i.e., a divided by its own
length). But what are u and v? A method to ﬁnd reasonable u and v is given in
Section 2.4.6.

126 6. T ransformation Matrices
If we have a rotation matrix and we wish to have the rotation in axis-angle
form, we can compute the one real eigenvalue (which will be λ=1 ), and the
corresponding eigenvector is the axis of rotation. This is the one axis that is not
changed by the rotation.
See Chapter 16 for a comparison of the few most-used ways to represent ro-
tations, besides rotation matrices.
6.2.2 T ransforming Normal Vectors
While most 3D vectors we use represent positions (offset vectors from the origin)
or directions, such as where light comes from, some vectors represent surface
normals. Surface normal vectors are perpendicular to the tangent plane of a sur-
face. These normals do not transform the way we would like when the underlying
surface is transformed. For example, if the points of a surface are transformed by
am a t r i xM, a vector t that is tangent to the surface and is multiplied by M will
be tangent to the transformed surface. However, a surface normal vector n that is
transformed by M may not be normal to the transformed surface (Figure 6.17).
We can derive a transform matrix N which does take n to a vector perpen-
dicular to the transformed surface. One way to attack this issue is to note that a
surface normal vector and a tangent vector are perpendicular, so their dot product
is zero, which is expressed in matrix form as
n
Tt = 0. (6.4)
If we denote the desired transformed vectors as tM = Mt and nN = Nn,
our goal is to ﬁnd N such that nT
N tM = 0. We can ﬁnd N by some algebraic
t
n
Mt
Mn
Nn
Figure 6.17. When a normal vector is transformed using the same matrix that transforms the points
on an object, the resulting vector may not be perpendicular to the surface as is shown here for the
sheared rectangle. The tangent vector, however, does transform to a vector tangent to the transformed
surface.

6.2. 3D Linear T ransformations 127
tricks. First, we can sneak an identity ma trix into the dot product, and then take
advantage of M−1M = I:
nTt = nTIt = nTM−1Mt = 0.
Although the manipulations above don’t obviously get us anywhere, note that we
can add parentheses that make the above expression more obviously a dot product:
(
nTM−1)
(Mt)=
(
nTM−1)
tM = 0.
This means that the row vector that is perpendicular to tM is the left part of the
expression above. This expression holds for any of the tangent vectors in the
tangent plane. Since there is only one direction in 3D (and its opposite) that
is perpendicular to all such tangent vectors, we know that the left part of the
expression above must be the row vector expression for n
N , i.e., it is nT
N ,s ot h i s
allows us to infer N:
nT
N = nTM−1,
so we can take the transpose of that to get
nN =
(
nTM−1)T
=
(
M−1)T
n. (6.5)
Therefore, we can see that the matrix whic h correctly transforms normal vectors
so they remain normal is N =( M−1)T, i.e., the transpose of the inverse matrix.
Since this matrix may change the length of n, we can multiply it by an arbitrary
scalar and it will still produce nN with the right direction. Recall from Section 5.3
that the inverse of a matrix is the transpose of the cofactor matrix divided by the
determinant. Because we don’t care about the length of a normal vector, we can
skip the division and ﬁnd that for a 3×3 matrix,
N =
⎡
⎣
m
c
11 mc
12
mc
13
mc
21 mc
22
mc
23
mc
31
mc
32
mc
33
⎤
⎦.
This assumes the element of M in row i and column j ismij . So the full expres-
sion for N is
N =
⎡
⎣
m22m33 −m23m32 m23m31 −m21m33 m21m32 −m22m31
m13m32 −m12m33 m11m33 −m13m31 m12m31 −m11m32
m12m23 −m13m22 m13m21 −m11m23 m11m22 −m12m21
⎤
⎦.

128 6. T ransformation Matrices
6.3 T ranslation and Afﬁne T ransformations
We have been looking at methods to change vectors using a matrix M.I n t w o
dimensions, these transforms have the form,
x′ = m11x + m12y,
y′ = m21x + m22y.
We cannot use such transforms to move objects, only to scale and rotate them. In
particular, the origin(0,0) always remains ﬁxed under a linear transformation. To
move, or translate, an object by shifting all its points the same amount, we need
a transform of the form,
x′ = x + xt,
y′ = y + yt.
There is just no way to do that by multiplying (x,y) by a 2 × 2 matrix. One
possibility for adding translation to our system of linear transformations is to
simply associate a separate translatio n vector with each transformation matrix,
letting the matrix take care of scaling and rotation and the vector take care of
translation. This is perfectly feasible, but the bookkeeping is awkward and the
rule for composing two transformations is not as simple and clean as with linear
transformations.
Instead, we can use a clever trick to get a single matrix multiplication to do
both operations together. The idea is simple: represent the point (x,y) by a 3D
vector[xy 1]
T, and use 3×3 matrices of the form
⎡
⎣
m11 m12 xt
m21 m22 yt
00 1
⎤
⎦.
The ﬁxed third row serves to copy the 1 into the transformed vector, so that all
vectors have a 1 in the last place, and the ﬁrst two rows compute x
′ and y′ as
linear combinations of x, y,a n d1:
⎡
⎣
x
′
y′
1
⎤
⎦=
⎡
⎣
m
11 m12 xt
m21 m22 yt
00 1
⎤
⎦
⎡
⎣
x
y
1
⎤
⎦=
⎡
⎣
m
11x+m12y +xt
m21x+m22y +yt
1
⎤
⎦.
The single matrix implements a linear transformation followed by a translation!
This kind of transformation is called an afﬁne transformation, and this way of
implementing afﬁne transformations by adding an extra dimension is called ho-
mogeneous coordinates (Roberts, 1965; Riesenfeld, 1981; Penna & Patterson,
1986). Homogeneous coordinates not only cl ean up the code for transformations,

6.3. T ranslation and Afﬁne T ransformations 129
but this scheme also makes it obvious how to compose two afﬁne transformations:
simply multiply the matrices.
A problem with this new formalism arises when we need to transform vec-
tors that are not supposed to be positions—they represent directions, or offsets
between positions. V ectors that represent directions or offsets should not change
when we translate an object. Fortunately, we can arrange for this by setting the
third coordinate to zero:
⎡
⎣
10 xt
01 yt
001
⎤
⎦
⎡
⎣
x
y
0
⎤
⎦=
⎡
⎣
x
y
0
⎤
⎦.
If there is a scaling/rotation transformation in the upper-left 2 × 2 entries of the
matrix, it will apply to the vector, but the translation still multiplies with the zero
and is ignored. Furthermore, the zero is copied into the transformed vector, so
direction vectors remain direction v ectors after they are transformed.
This is exactly the behavior we want for vectors, so they ﬁt smoothly into the
system: the extra (third) coordinate will be either 1 or0 depending on whether we
are encoding a position or a direction. We actually do need to store the homoge-
This gives an explanation
for the name “homoge-
neous:” translation, rota-
tion, and scaling of posi-
tions and directions all ﬁt
into a single system.
neous coordinate so we can distinguish between locations and other vectors. For
example,
⎡
⎣
3
2
1
⎤
⎦is a location and
⎡
⎣
3
2
0
⎤
⎦is a displacement or direction.
Later, when we do perspective viewing, we will see that it is useful to allow the
homogeneous coordinate to take on values other than one or zero.
Homogeneous coordinates are used nearly universally to represent transfor-
mations in graphics systems. In particul ar, homogeneous coordinates underlie the
Homogeneous coordinates
are also ubiquitous in com-
puter vision.
design and operation of renderers implem ented in graphics hardware. We will see
in Chapter 7 that homogeneous coordinates also make it easy to draw scenes in
perspective, another reason for their popularity.
Homogeneous coordinates can be considered just a clever way to handle the
bookkeeping for translation, but there is also a different, geometric interpretation.
The key observation is that when we do a 3D shear based on the z-coordinate we
get this transform: ⎡
⎣
10 x
t
01 yt
001
⎤
⎦
⎡
⎣
x
y
z
⎤
⎦=
⎡
⎣
x+x
tz
y +ytz
z
⎤
⎦.
Note that this almost has the form we want in x and y for a 2D translation, but
has a z hanging around that doesn’t have a meaning in 2D. Now comes the key

130 6. T ransformation Matrices
decision: we will add a coordinate z =1 to all 2D locations. This gives us
⎡
⎣
10 xt
01 yt
001
⎤
⎦
⎡
⎣
x
y
1
⎤
⎦=
⎡
⎣
x+x
t
y +yt
1
⎤
⎦.
By associating a (z =1 )-coordinate with all 2D points, we now can encode trans-
lations into matrix form. For example, to ﬁrst translate in 2D by (x
t,yt) and then
rotate by angle φwe would use the matrix
M =
⎡
⎣
cosφ −sinφ 0
sinφ cosφ 0
00 1
⎤
⎦
⎡
⎣
10 x
t
01 yt
001
⎤
⎦.
Note that the 2D rotation matrix is now 3×3 with zeros in the “translation slots.”
With this type of formalism, which uses shears alongz =1 to encode translations,
we can represent any number of 2D shears, 2D rotations, and 2D translations as
one composite 3D matrix. The bottom row of that matrix will always be (0,0,1),
so we don’t really have to store it. We just need to remember it is there when we
multiply two matrices together.
In 3D, the same technique works: we can add a fourth coordinate, a homoge-
neous coordinate, and then we have translations:
⎡
⎢⎢⎣
100x
t
010 yt
001 zt
0001
⎤
⎥⎥⎦
⎡
⎢⎢⎣
x
y
z
1
⎤
⎥⎥⎦=
⎡
⎢⎢⎣
x+xt
y +yt
z +zt
1
⎤
⎥⎥⎦.
Again, for a direction vector, the fourth coordinate is zero and the vector is thus
unaffected by translations.
Example (Windowing transformations). Often in graphics we need to create a trans-
form matrix that takes points in the rectangle [xl,xh] × [yl,yh] to the rectangle
[x′
l,x′
h
] ×[y′
l,y′
h]. This can be accomplished with a single scale and translate in
sequence. However, it is more intuitive to create the transform from a sequence
of three operations (Figure 6.18):
1. Move the point (xl,yl) to the origin.
2. Scale the rectangle to be the sa me size as the target rectangle.
3. Move the origin to point (x′
l,y′
l).

6.3. T ranslation and Afﬁne T ransformations 131
Translate
Scale
Translate
(xh – xl, yh – yl)
(xh, yh)
y
x
y
x
y
x
y
x
(xl, yl)
(x´h, y´h)
(x´l, y´l)
(x´h – x´l, y´h – y´l)
Figure 6.18. To take one rectangle (window) to the other, we ﬁrst shift the lower-left corner to
the origin, then scale it to the new size, and then move the origin to the lower-left corner of the target
rectangle.
Remembering that the right-hand matrix is applied ﬁrst, we can write
window = translate(x′
l,y′
l) scale
(
x′
h−x′
l
xh−xl
, y′
h−y′
l
yh−yl
)
translate (−xl,−yl)
=
⎡
⎢⎣
10 x′
l
01 y′
l
001
⎤
⎥⎦
⎡
⎢⎢⎣
x′
h−x′
l
xh−xl
00
0 y′
h−y′
l
yh−yl
0
00 1
⎤
⎥⎥⎦
⎡
⎢⎣
10 −xl
01 −yl
00 1
⎤
⎥⎦
=
⎡
⎢⎢⎣
x′
h−x′
l
xh−xl
0 x′
l
xh−x′
h
xl
xh−xl
0 y′
h−y′
l
yh−yl
y′
lyh−y′
hyl
yh−yl
00 1
⎤
⎥⎥⎦. (6.6)
It is perhaps not surprising to some readers that the resulting matrix has the form
it does, but the constructive process with the three matrices leaves no doubt as to
the correctness of the result.
An exactly analogous construction can be used to de ﬁne a 3D windowing
transformation, which maps the box [x
l,xh] × [yl,yh] × [zl,zh] to the box

132 6. T ransformation Matrices
[x′
l,x′
h
]×[y′
l,y′
h]×[z′
l,z′
h]:
⎡
⎢⎢⎢
⎢
⎢⎣
x′
h−x′
l
xh−xl
00 x′
l
xh−x′
h
xl
xh−xl
0 y′
h−y′
l
yh−yl
0 y′
lyh−y′
hyl
yh−yl
00 z′
h−z′
l
zh−zl
z′
lzh−z′
hzl
zh−zl
000 1
⎤
⎥⎥⎥
⎥
⎥⎦
. (6.7)
It is interesting to note that if we multiply an arbitrary matrix composed of
scales, shears, and rotations with a simple translation (translation comes second),
we get
⎡
⎢⎢⎣
100x t
010 yt
001 zt
0001
⎤
⎥⎥⎦
⎡
⎢⎢⎣
a11 a12 a13 0
a21 a22 a23 0
a31 a32 a33 0
000 1
⎤
⎥⎥⎦=
⎡
⎢⎢⎣
a11 a12 a13 xt
a21 a22 a23 yt
a31 a32 a33 zt
000 1
⎤
⎥⎥⎦.
Thus, we can look at any matrix and think of it as a scaling/rotation part and a
translation part because the component s are nicely separated from each other.
An important class of transforms are rigid-body transforms. These are com-
posed only of translations and rotations, so they have no stretching or shrinking
of the objects. Such transforms will have a pure rotation for the a
ij above.
6.4 Inverses of T ransformation Matrices
While we can always invert a matrix algebraically, we can use geometry if we
know what the transform does. For example, the inverse of scale (s
x,sy,sz) is
scale(1/sx,1/sy,1/sz). The inverse of a rotation is the same rotation with the
opposite sign on the angle. The inverse of a translation is a translation in the
opposite direction. If we have a series of matrices M = M
1M2 ···M n then
M−1 = M−1
n ···M −1
2 M−1
1 .
Also, certain types of transformation matrices are easy to invert. We’ve al-
ready mentioned scales, which are diagonal matrices; the second important ex-
ample is rotations, which are orthogonal matrices. Recall (Section 5.2.4) that the
inverse of an orthogonal matrix is its transpose. This makes it easy to invert ro-
tations and rigid body transformations (see Exercise 6). Also, it’s useful to know
that a matrix with [0 0 0 1]in the bottom row has an inverse that also has [0 0 0 1]
in the bottom row (see Exercise 7).
Interestingly, we can use SVD to invert a matrix as well. Since we know
that any matrix can be decomposed into a rotation times a scale times a rotation,

6.5. Coordinate T ransformations 133
inversion is straightforward. For example, in 3D we have
M = R1scale(σ1,σ2,σ3)R2,
and from the rules above it follows easily that
M−1 = RT
2 scale(1/σ1,1/σ2,1/σ3)RT
1
.
6.5 Coordinate T ransformations
All of the previous discussion has been in terms of using transformation matrices
to move points around. We can also think of them as simply changing the coor-
dinate system in which the point is represented. For example, in Figure 6.19, we
see two ways to visualize a movement. In different contexts, either interpretation
may be more suitable.
For example, a driving game may have a model of a city and a model of
a car. If the player is presented with a view out the windshield, objects inside
the car are always drawn in the same pl ace on the screen, while the streets and
buildings appear to move backward as the player drives. On each frame, we
apply a transformation to these objects that moves them farther back than on the
previous frame. One way to think of this operation is simply that it moves the
buildings backward; another way to think of it is that the buildings are staying put
but the coordinate system in which we want to draw them—which is attached to
the car—is moving. In the second interpretation, the transformation is changing
(2, 1)
(1, 1)
(1, 1)
Figure 6.19. The point (2,1) has a transform “translate by (-1,0)” applied to it. On the top right is
our mental image if we view this transformation as a physical movement, and on the bottom right is
our mental image if we view it as a change of coordinates (a movement of the origin in this case). The
artiﬁcial boundary is just an arti ﬁce, and the relative position of the axes and the point are the same in
either case.

134 6. T ransformation Matrices
the coordinates of the city geometry, expressing them as coordinates in the car’s
coordinate system. Both ways will lead to exactly the same matrix that is applied
to the geometry outside the car.
If the game also supports an overhead view to show where the car is in the
city, the buildings and streets need to be drawn in ﬁxed positions while the car
needs to move from frame to frame. The same two interpretations apply: we
can think of the changing transformation as moving the car from its canonical
position to its current location in the world; or we can think of the transformation
as simply changing the coordinates of the car’s geometry, which is originally
expressed in terms of a coordinate system attached to the car, to express them
instead in a coordinate system ﬁxed relative to the city. The change-of-coordinates
interpretation makes it clear that the matrices used in these two modes (city-to-car
coordinate change vs. car-to-city coordinate change) are inverses of one another.
The idea of changing coordinate systems is much like the idea of type conver-
sions in programming. Before we can add a ﬂoating-point number to an integer,
we need to convert the integer to ﬂoating point or the ﬂoating-point number to an
integer, depending on our needs, so that the types match. And before we can draw
the city and the car together, we need to convert the city to car coordinates or the
car to city coordinates, depending on our needs, so that the coordinates match.
When managing multiple coordinate systems, it’s easy to get confused and
wind up with objects in the wrong coordinates, causing them to show up in un-
expected places. But with systematic th inking about transf ormations between
coordinate systems, you can reliably get the transformations right.
Geometrically, a coordinate system, or coordinate frame, consists of an origin
and a basis—a set of three vectors. Orthonormal bases are so convenient that
In 2D, of course, there are
two basis vectors.
we’ll normally assume frames are orthonormal unless otherwise speci ﬁed. In a
frame with origin p and basis {u, v, w}, the coordinates (u,v,w ) describe the
point
p+uu+vv+ww.
When we store these vectors in the computer, they need to be represented in
terms of some coordinate system. To get things started, we have to designate
some canonical coordinate system, often called “global” or “world” coordinates,
which is used to describe all other systems. In the city example, we might adopt
the street grid and use the convention that the x-axis points along Main Street,
the y-axis points up, and the z-axis points along Central Avenue. Then when we
write the origin and basis of the car frame in terms of these coordinates it is clear
what we mean.
In 2D our convention is is to use the point o for the origin, and x and y for
In 2D, right-handed means
y is counterclockwise from
x.
 the right-handed orthonormal basis vectors x and y (Figure 6.20).

6.5. Coordinate T ransformations 135
y
xo
v
p
e
u 0.5u
e
o 2.5x
0.9y
–0.7v
p
Figure 6.20. The point p can be represented in terms of either coordinate system.
Another coordinate system might have an origin e and right-handed orthonor-
mal basis vectors u and v. Note that typically the canonical data o, x,a n d y are
never stored explicitly. They are the frame-of-reference for all other coordinate
systems. In that coordinate system, we often write down the location of p as an
ordered pair, which is shorthand for a full vector expression:
p =( xp,yp) ≡o+xpx+ypy.
For example, in Figure 6.20, (xp,yp)=( 2 .5,0.9). Note that the pair (xp,yp)
implicitly assumes the origin o. Similarly, we can express p in terms of another
equation:
p =( up,vp) ≡e+upu+vpv.
In Figure 6.20, this has (up,vp)=( 0.5,−0.7). Again, the origin e is left as an
implicit part of the coordinate system associated with u and v.
We can express this same relationship using matrix machinery, like this:
⎡
⎣
xp
yp
1
⎤
⎦=
⎡
⎣
10 x
e
01 ye
00 1
⎤
⎦
⎡
⎣
x
u xv 0
yu yv 0
00 1
⎤
⎦
⎡
⎣
u
p
vp
1
⎤
⎦=
⎡
⎣
x
u xv xe
yu yv ye
001
⎤
⎦
⎡
⎣
u
p
vp
1
⎤
⎦.
Note that this assumes we have the point e and vectors u and v stored in canonical
coordinates; the (x,y )-coordinate system is the ﬁrst among equals. In terms of the
basic types of transformations we’ve discussed in this chapter, this is a rotation
(involving u and v) followed by a translation (involving e). Looking at the matrix
for the rotation and translation together, you can see it’s very easy to write down:
we just put u, v,a n d e into the columns of a matrix, with the usual [0 0 1]in the
third row. To make this even clearer we can write the matrix like this:
p
xy =
[uve
001
]
puv.
We call this matrix the frame-to-canonical matrix for the ( u, v) frame. It takes
points expressed in the (u,v) frame and converts them to the same points ex-
pressed in the canonical frame.
The name “frame-to-
canonical” is based on
thinking about changing
the coordinates of a
vector from one system to
another. Thinking in terms
of moving vectors around,
the frame-to-canonical
matrix maps the canonical
frame to the ( u,v) frame.


136 6. T ransformation Matrices
To go in the other direction we have
⎡
⎣
up
vp
1
⎤
⎦=
⎡
⎣
x
u yu 0
xv yv 0
00 1
⎤
⎦
⎡
⎣
10 −x
e
01 −ye
00 1
⎤
⎦
⎡
⎣
x
p
yp
1
⎤
⎦.
This is a translation followed by a rotation; they are the inverses of the rotation and
translation we used to build the frame-to-canonical matrix, and when multiplied
together they produce the inverse of the frame-to-canonical matrix, which is (not
surprisingly) called the canonical-to-frame matrix:
p
uv =
[uve
001
]−1
pxy.
The canonical-to-frame matrix takes points expressed in the canonical frame and
converts them to the same points expressed in the ( u,v) frame. We have written
this matrix as the inverse of the frame-to-canonical matrix because it can’t im-
mediately be written down using the canonical coordinates of e, u,a n d v.B u t
remember that all coordinate systems are equivalent; it’s only our convention of
storing vectors in terms of x-a n dy-coordinates that creates this seeming asym-
metry. The canonical-to-frame matrix can be expressed simply in terms of the (u,
v) coordinates of o, x,a n dy:
p
uv =
[
xuv yuv ouv
001
]
pxy.
All these ideas work strictly analogously in 3D, where we have
⎡
⎢⎢⎣
xp
yp
zp
1
⎤
⎥⎥⎦=
⎡
⎢⎢⎣
100x e
010 ye
001 ze
000 1
⎤
⎥⎥⎦
⎡
⎢⎢⎣
xu xv xw 0
yu yv yw 0
zu zv zw 0
0001
⎤
⎥⎥⎦
⎡
⎢⎢⎣
up
vp
wp
1
⎤
⎥⎥⎦
pxyz =
[
uvwe
0001
]
puvw,
(6.8)
and ⎡
⎢⎢⎣
up
vp
wp
1
⎤
⎥⎥⎦=
⎡
⎢⎢⎣
xu yu zu 0
xv yv zv 0
xw yw zw 0
000 1
⎤
⎥⎥⎦
⎡
⎢⎢⎣
100− xe
010 −ye
001 −ze
000 1
⎤
⎥⎥⎦
⎡
⎢⎢⎣
xp
yp
zp
1
⎤
⎥⎥⎦
puvw =
[
uvwe
0001
]−1
pxyz.
(6.9)

6.5. Coordinate T ransformations 137
Frequently Asked Questions
•Can’t I just hardcode transforms rather than use the matrix formalisms?
Y es, but in practice it is harder to derive, harder to debug, and not any more ef-
ﬁcient. Also, all current graphics APIs use this matrix formalism so it must be
understood even to use graphics libraries.
•The bottom row of the matrix is always (0,0,0,1). Do I have to store it?
Y ou do not have to store it unless you include perspective transforms (Chapter 7).
Notes
The derivation of the transformation properties of normals is based on Proper-
ties of Surface Normal Transformations (Turkowski, 1990). In many treatments
through the mid-1990s, vectors were represented as row vectors and premulti-
plied, e.g., b = aM. In our notation this would be bT = aTMT. If you want to
ﬁnd a rotation matrix R that takes one vector a to a vector b of the same length:
b = Ra you could use two rotations constructed from orthonormal bases. A more
efﬁcient method is given in Efﬁciently Building a Matrix to Rotate One V ector to
Another (Akenine-M¨ oller, Haines, & Hoffman, 2008).
Exercises
1. Write down the 4×4 3D matrix to move by (xm,ym,zm).
2. Write down the 4×4 3D matrix to rotate by an angle θabout the y-axis.
3. Write down the 4×4 3D matrix to scale an object by 50% in all directions.
4. Write the 2D rotation matrix that rotates by 90 degrees clockwise.
5. Write the matrix from Exercise 4 as a product of three shear matrices.
6. Find the inverse of the rigid body transformation:
[ Rt
000 1
]
where R is a 3×3 rotation matrix and t is a 3-vector.

138 6. T ransformation Matrices
7. Show that the inverse of the matrix for an af ﬁne transformation (one that
has all zeros in the bottom row except for a one in the lower right entry)
also has the same form.
8. Describe in words what this 2D transform matrix does:
⎡
⎣
0 −11
101
001
⎤
⎦.
9. Write down the3×3 matrix that rotates a 2D point by angle θabout a point
p =( xp,yp).
10. Write down the 4×4 rotation matrix that takes the orthonormal 3D vectors
u =( xu,yu,zu), v =( xv,yv,zv),a n d w =( xw,yw,zw), to orthonormal
3D vectors a =( xa,ya,za), b =( xb,yb,zb),a n d c =( xc,yc,zc),S o
M u = a, M v = b,a n dM w = c.
11. What is the inverse matrix for the answer to the previous problem?

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
**关联概念**: [[仿射变换]]
**pbrt 对应**: pbrt Ch3.9-3.10 Transformations
