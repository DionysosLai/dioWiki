---
title: "5 Linear Algebra"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 5 Linear Algebra

5
Linear Algebra
Perhaps the most universal tools of graphics programs are the matrices that
change or transform points and vectors. In the next chapter, we will see
how a vector can be represented as a matrix with a single column, and how
the vector can be represented in a different basis via multiplication with a
square matrix. We will also describe how we can use such multiplications to
accomplish changes in the vector such as sca ling, rotation, and translation. In this
a
b
Figure 5.1. The signed area
of the parallelogram is |ab|,
and in this case the area is pos-
itive.
chapter, we review basic linear algebra from a geometric perspective, focusing on
intuition and algorithms that work well in the two- and three-dimensional case.
This chapter can be skipped by readers c omfortable with linear algebra. How-
ever, there may be some enlightening ti dbits even for such readers, such as the
development of determinants and the discussion of singular and eigenvalue de-
composition.
5.1 Determinants
We usually think of determinants as arising in the solution of linear equations.
However, for our purposes, we will think of determinants as another way to mul-
tiply vectors. For 2D vectors a and b, the determinant |ab| is the area of the
b
c
a
Figure 5.2. The signed
volume of the parallelepiped
shown is denoted by the de-
terminant |abc|, and in this
case the volume is positive be-
cause the vectors form a right-
handed basis.
parallelogram formed by a and b (Figure 5.1). This is a signed area, and the
sign is positive if a and b are right-handed and negative if they are left-handed.
This means |ab| = −|ba|. In 2D we can interpret “right-handed” as meaning we
rotate the ﬁrst vector counterclockwise to close the smallest angle to the second
vector. In 3D, the determinant must be taken with three vectors at a time. For
89

90 5. Linear Algebra
three 3D vectors, a, b,a n d c, the determinant |abc| is the signed volume of the
parallelepiped (3D parallelogram; a sheared 3D box) formed by the three vectors
(Figure 5.2). To compute a 2D determinant, we ﬁrst need to establish a few of its
properties. We note that scaling one side of a parallelogram scales its area by the
same fraction (Figure 5.3):
|(ka)b| = |a(kb)| = k|ab|.
Also, we note that “shearing” a parallelogram does not change its area (Fig-
ure 5.4):
4A
3A
2A
Area = A
Figure 5.3. Scaling a par-
allelogram along one direction
changes the area in the same
proportion.
|(a+kb)b| = |a(b+ka)| = |ab|.
Finally, we see that the determinant has the following property:
|a(b+ c)| = |ab|+|ac|, (5.1)
because as shown in Figure 5.5 we can “slide” the edge between the two parallel-
ograms over to form a single parallelogram without changing the area of either of
the two original parallelograms.
Now let’s assume a Cartesian representation for a and b:
Figure 5.4. Shearing a
parallelogram does not change
its area. These four parallel-
ograms have the same length
base and thus the same area.
|ab| = |(xax+yay)(xbx+yby)|
= xaxb|xx|+xayb|xy|+yaxb|yx|+yayb|yy|
= xaxb(0)+ xayb(+1)+ yaxb(−1)+ yayb(0)
= xayb −yaxb.
This simpli ﬁcation uses the fact that |vv| =0 for any vector v, because the
parallelograms would all be collinear with v and thus without area.
In three dimensions, the determinant of three 3D vectors a, b,a n dc is denoted
|abc|. With Cartesian representations for the vectors, there are analogous rules
for parallelepipeds as there are for parallelograms, and we can do an analogous
expansion as we did for 2D:
|abc| = |(xax+yay+zaz)(xb x+yby+zbz)(xcx+ycy+zcz)|
= xaybzc −xazbyc −yaxbzc +yazbxc +zaxbyc −zaybxc.
c
a ab
b + c
Figure 5.5. The geome-
try behind Equation 5.1. Both
of the parallelograms on the
left can be sheared to cover
the single parallelogram on the
right.
As you can see, the computation of determinants in this fashion gets uglier as the
dimension increases. We will discuss less error-prone ways to compute determi-
nants in Section 5.3.
Example. Determinants arise naturally when computing the expression for one
vector as a linear combination of two othe rs—for example, if we wish to express
a vector c as a combination of vectors a and b:
c = aca+bcb.

5.2. Matrices 91
a
b
c
a
bcb
bcb
aca
bcb
c
c
a
Figure 5.6. On the left, the vector c can be represented using two basis vectors as aca + bcb.O n
the right, we see that the parallelogram formed by a and c is a sheared version of the parallelogram
formed by bcb and a.
We can see from Figure 5.6 that
|(bcb)a| = |ca|,
because these parallelograms are just sheared versions of each other. Solving for
bc yields
bc = |ca|
|ba|.
An analogous argument yields
ac = |bc|
|ba|.
This is the two-dimensional version of Cramer’s rule which we will revisit in
Section 5.3.2.
5.2 Matrices
A matrix is an array of numeric elements that follow certain arithmetic rules. An
example of a matrix with two rows and three columns is
[1.7 −1.24 .2
3.04 .5 −7.2
]
.

92 5. Linear Algebra
Matrices are frequently used in computer graphics for a variety of purposes in-
cluding representation of spatial transforms. For our discussion, we assume the
elements of a matrix are all real numbers. This chapter describes both the mechan-
ics of matrix arithmetic and the determinant of “square” matrices, i.e., matrices
with the same number of rows as columns.
5.2.1 Matrix Arithmetic
A matrix times a constant results in a matrix where each element has been multi-
plied by that constant, e.g.,
2
[
1 −4
32
]
=
[
2 −8
64
]
.
Matrices also add element by element, e.g.,
[1 −4
32
]
+
[22
22
]
=
[3 −2
54
]
.
For matrix multiplication, we “multiply” rows of the ﬁrst matrix with columns of
the second matrix:
⎡
⎢⎢
⎢
⎢⎢⎢
⎢
⎢⎣
a
11 ... a 1m
...
...
ai1 ... a im
..
. .
.
.
a
r1 ... a rm
⎤
⎥⎥
⎥
⎥⎥⎥
⎥
⎥⎦
⎡
⎢⎢⎢⎣
b
11 ...
...
bm1 ...
b1j
...
bmj
... b 1c
...
... b mc
⎤
⎥⎥⎥⎦=
⎡
⎢⎢
⎢
⎢⎢⎢
⎢⎣
p
11 ... p 1j ... p 1c
...
...
...
pi1 ...
 pij
 ... p ic
..
. .
.
. .
.
.
p
r1 ... p rj ... p rc
⎤
⎥⎥
⎥
⎥⎥⎥
⎥⎦
.
So the element p
ij of the resulting product is
pij = ai1b1j +ai2b2j +··· +aimbmj. (5.2)
Taking a product of two matrices is only possible if the number of columns of the
left matrix is the same as the number of rows of the right matrix. For example,
⎡
⎣
01
23
45
⎤
⎦
[
6789
0123
]
=
⎡
⎣
0123
12 17 22 27
24 33 42 51
⎤
⎦.
Matrix multiplication is not commutative in most instances:
AB ̸= BA. (5.3)

5.2. Matrices 93
Also, if AB = AC, it does not necessarily follow that B = C. Fortunately,
matrix multiplication is associative and distributive:
(AB)C = A(BC),
A(B+ C)= AB+ AC,
(A+ B)C = AC+ BC.
5.2.2 Operations on Matrices
We would like a matrix analog of the inverse of a real number. We know the
inverse of a real number x is 1/x and that the product of x and its inverse is 1.
We need a matrix I that we can think of as a “matrix one.” This exists only for
square matrices and is known as the identity matrix; it consists of ones down the
diagonal and zeroes elsewhere. For example, the four by four identity matrix is
I =
⎡
⎢⎢⎣
1000
0100
0010
0001
⎤
⎥⎥⎦.
The inverse matrix A
−1 of a matrix A is the matrix that ensures AA−1 = I.
For example,
[12
34
]−1
=
[−2.01 .0
1.5 −0.5
]
because
[12
34
][ −2.01 .0
1.5 −0.5
]
=
[10
01
]
.
Note that the inverse of A
−1 is A.S o AA−1 = A−1A = I.T h e i n v e r s e o f a
product of two matrices is the product of the inverses, but with the order reversed:
(AB)−1 = B−1A−1. (5.4)
We will return to the question of computing inverses later in the chapter.
The transpose AT of a matrix A has the same numbers but the rows are
switched with the columns. If we label the entries of AT as a′
ij then
aij = a′
ji.
For example, ⎡
⎣
12
34
56
⎤
⎦
T
=
[135
246
]
.

94 5. Linear Algebra
The transpose of a product of two matrices obeys a rule similar to Equation (5.4):
(AB)T = BTAT.
The determinant of a square matrix is simply the determinant of the columns
of the matrix, considered as a set of vect ors. The determinant has several nice
relationships to the matrix operations just discussed, which we list here for refer-
ence:
|AB| = |A|| B|, (5.5)
⏐⏐A−1⏐
⏐ = 1
|A|, (5.6)
⏐⏐⏐AT
⏐⏐⏐ = |A|. (5.7)
5.2.3 Vector Operations in Matrix Form
In graphics, we use a square matrix to transform a vector represented as a matrix.
For example, if you have a 2D vector a =( xa,ya) and want to rotate it by 90
degrees about the origin to form vector a′ =( −ya,xa), you can use a product of
a2×2 matrix and a 2×1 matrix, called a column vector. The operation in matrix
form is [0 −1
10
][ xa
ya
]
=
[−ya
xa
]
.
We can get the same result by using the transpose of this matrix and multiplying
on the left (“premultiplying”) with a row vector:
[x
a ya
][ 01
−10
]
=
[−ya xa
]
.
These days, postmultiplication using column vectors is fairly standard, but in
many older books and systems you will run across row vectors and premulti-
plication. The only difference is that t he transform matrix must be replaced with
its transpose.
We also can use matrix formalism to encode operations on just vectors. If we
consider the result of the dot product as a 1 × 1 matrix, it can be written
a· b = a
Tb.
For example, if we take two 3D vectors we get
[xa ya za
]
⎡
⎣
xb
yb
zb
⎤
⎦=
[x
axb +yayb +zazb
]
.

5.2. Matrices 95
A related vector product is the outer product between two vectors, which can
be expressed as a matrix multiplication with a column vector on the left and a row
vector on the right: abT. The result is a matrix consisting of products of all pairs
of an entry of a with an entry of b. For 3D vectors, we have
⎡
⎣
xa
ya
za
⎤
⎦[
x
b yb zb
]
=
⎡
⎣
x
axb xayb xazb
yaxb yayb yazb
zaxb zayb zazb
⎤
⎦.
It is often useful to think of matrix multiplication in terms of vector operations.
To illustrate using the three-dimensional case, we can think of a 3 × 3m a t r i xa s
a collection of three 3D vectors in two ways: either it is made up of three column
vectors side-by-side, or it is made up of three row vectors stacked up. For instance,
the result of a matrix-vector multiplication y = Ax can be interpreted as a vector
whose entries are the dot products of x with the rows of A.N a m i n g t h e s e r o w
vectors r
i,w eh a v e
⎡
⎣
|
y
|
⎤
⎦=
⎡
⎣
— r
1 —
— r2 —
— r3 —
⎤
⎦
⎡
⎣
|
x
|
⎤
⎦;
y
i = ri · x.
Alternatively, we can think of the same product as a sum of the three columns ci
of A, weighted by the entries of x:
⎡
⎣
|
y
|
⎤
⎦=
⎡
⎣
|||
c
1 c2 c3
|||
⎤
⎦
⎡
⎣
x
1
x2
x3
⎤
⎦;
y = x
1c1 +x2c2 +x3c3.
Using the same ideas, one can understand a matrix-matrix product AB as an
array containing the pairwise dot products of all rows of A with all columns of B
(cf. (5.2)); as a collection of products of the matrix A with all the column vectors
of B, arranged left to right; as a collection of products of all the row vectors of
A with the matrix B, stacked top to bottom; or as the sum of the pairwise outer
products of all columns of A with all rows of B.( S e eE x e r c i s e8 . )
These interpretations of matrix multiplication can often lead to valuable geo-
metric interpretations of operations that may otherwise seem very abstract.
5.2.4 Special T ypes of Matrices
The identity matrix is an example of a diagonal matrix , where all nonzero ele-
ments occur along the diagonal. The diagonal consists of those elements whose
column index equals the row index counting from the upper left.

96 5. Linear Algebra
The identity matrix also has the property that it is the same as its transpose.
Such matrices are called symmetric.
The identity matrix is also an orthogonal matrix, because each of its columns
The idea of an orthogonal
matrix corresponds to the
idea of an orthonormal ba-
sis, not just a set of orthog-
onal vectors—an unfortu-
nate glitch in terminology.
considered as a vector has length1 and the columns are orthogonal to one another.
The same is true of the rows (see Exercise 2). The determinant of any orthogonal
matrix is either +1 or −1.
A very useful property of orthogonal matrices is that they are nearly their own
inverses. Multiplying an orthogonal matrix b y its transpose results in the identity,
RTR = I = RRT for orthogonal R.
This is easy to see because the entries of RTR are dot products between the
columns of R. Off-diagonal entries are dot products between orthogonal vec-
tors, and the diagonal entries are dot products of the (unit-length) columns with
themselves.
Example. The matrix ⎡
⎣
800
020
009
⎤
⎦
is diagonal, and therefore symmetric, but not orthogonal (the columns are orthog-
onal but they are not unit length).
The matrix ⎡
⎣
112
197
271
⎤
⎦
is symmetric, but not diagonal or orthogonal.
The matrix ⎡
⎣
010
001
100
⎤
⎦
is orthogonal, but neither diagonal nor symmetric.
5.3 Computing with Matrices and Determinants
Recall from Section 5.1 that the determinant takes nn -dimensional vectors and
combines them to get a signed n-dimensional volume of the n-dimensional par-
allelepiped deﬁned by the vectors. For example, the determinant in 2D is the area

5.3. Computing with Matrices and Determinants 97
of the parallelogram formed by the v ectors. We can use matrices to handle the
mechanics of computing determinants.
If we have 2D vectors r and s, we denote the determinant |rs|; this value is
the signed area of the parallelogram formed by the vectors. Suppose we have
two 2D vectors with Cartesian coordinates (a,b) and (A,B) (Figure 5.7). The
determinant can be written in terms of column vectors or as a shorthand:
(A,B)
(a,b)
Figure 5.7. The 2D de-
terminant in Equation 5.8 is
the area of the parallelogram
formed by the 2D vectors.
⏐⏐
⏐
⏐
[a
b
][ A
B
]⏐
⏐
⏐
⏐ ≡
⏐
⏐
⏐
⏐
aA
bB
⏐
⏐
⏐
⏐ = aB −Ab. (5.8)
Note that the determinant of a matrix is t he same as the determinant of its trans-
pose: ⏐
⏐
⏐
⏐
aA
bB
⏐
⏐
⏐
⏐ =
⏐
⏐
⏐
⏐
ab
AB
⏐
⏐
⏐
⏐ = aB −Ab.
This means that for any parallelogram in 2D there is a “sibling” parallelogram that
has the same area but a different shape (Figure 5.8). For example, the parallelo-
gram de ﬁned by vectors (3,1) and (2,4) has area 10, as does the parallelogram
deﬁned by vectors (3,2) and (1,4).
(b,B)
(a,A)
Figure 5.8. The sibling par-
allelogram has the same area
as the parallelogram in Fig-
ure 5.7.
Example. The geometric meaning of the 3D determinant is helpful in seeing why
certain formulas make sense. For example, the equation of the plane through the
points (xi,yi,zi) fori =0 ,1,2 is
⏐⏐⏐
⏐
⏐
⏐
x−x
0 x−x1 x−x2
y −y0 y −y1 y −y2
z −z0 z −z1 z −z2
⏐⏐⏐
⏐
⏐
⏐
=0 .
Each column is a vector from point (x
i,yi,zi) to point (x,y,z ). The volume of
the parallelepiped with those vectors as sides is zero only if (x,y,z ) is coplanar
with the three other points. Almost all equations involving determinants have
similarly simple underlying geometry.
As we saw earlier, we can compute determinants by a brute force expansion
where most terms are zero, and there is a great deal of bookkeeping on plus and
minus signs. The standard way to manage the algebra of computing determinants
is to use a form of Laplace’s expansion. The key part of computing the determi-
nant this way is to ﬁnd cofactors of various matrix elements. Each element of a
square matrix has a cofactor which is the determinant of a matrix with one fewer
row and column possibly multiplied by minus one. The smaller matrix is obtained
by eliminating the row and column that the element in question is in. For exam-
ple, for a 10×10 matrix, the cofactor of a
82 is the determinant of the 9×9 matrix
with the 8th row and 2nd column eliminated. The sign of a cofactor is positive if

98 5. Linear Algebra
the sum of the row and column indices is even and negative otherwise. This can
be remembered by a checkerboard pattern:
⎡
⎢⎢⎢
⎢
⎢⎣
+ − + − ···
− + − + ···
+ − + − ···
− + − + ···
...
...
...
... . . .
⎤
⎥⎥⎥
⎥
⎥⎦
.
So, for a 4×4 matrix,
A =
⎡
⎢⎢⎣
a
11 a12 a13 a14
a21 a22 a23 a24
a31 a32 a33 a34
a41 a42 a43 a44
⎤
⎥⎥⎦.
The cofactors of the ﬁrst row are
ac
11 =
⏐⏐
⏐
⏐⏐⏐
a
22 a23 a24
a32 a33 a34
a42 a43 a44
⏐
⏐
⏐
⏐⏐⏐
,a
c
12 = −
⏐⏐
⏐
⏐⏐⏐
a
21 a23 a24
a31 a33 a34
a41 a43 a44
⏐
⏐
⏐
⏐⏐⏐
,
a
c
13 =
⏐⏐⏐
⏐
⏐
⏐
a
21 a22 a24
a31 a32 a34
a41 a42 a44
⏐⏐⏐
⏐
⏐
⏐
,a
c
14 = −
⏐⏐⏐
⏐
⏐
⏐
a
21 a22 a23
a31 a32 a33
a41 a42 a43
⏐⏐⏐
⏐
⏐
⏐
.
The determinant of a matrix is found by taking the sum of products of the elements
of any row or column with their cofactors. For example, the determinant of the
4×4 matrix above taken about its second column is
|A| = a
12ac
12 +a22ac
22
+a32ac
32
+a42ac
42
.
We could do a similar expansion about any row or column and they would all
yield the same result. Note the recursive nature of this expansion.
Example. A concrete example for the determinant of a particular 3×3 matrix by
expanding the cofactors of the ﬁrst row is
⏐⏐
⏐
⏐⏐⏐
012
345
678
⏐⏐
⏐
⏐⏐⏐
=0
⏐
⏐
⏐⏐
45
78
⏐⏐
⏐⏐−1
⏐⏐
⏐⏐
35
68
⏐⏐
⏐⏐+2
⏐⏐
⏐⏐
34
67
⏐⏐
⏐⏐
=0 ( 3 2−35)−1(24−30)+2(21 −24)
=0 .

5.3. Computing with Matrices and Determinants 99
We can deduce that the volume of the parallelepiped formed by the vectors
deﬁned by the columns (or rows since the determinant of the transpose is the
same) is zero. This is equivalent to saying that the columns (or rows) are not
linearly independent. Note that the sum of the ﬁrst and third rows is twice the
second row, which implies linear dependence.
5.3.1 Computing Inverses
Determinants give us a tool to compute the inverse of a matrix. It is a very inef-
ﬁcient method for large matrices, but of ten in graphics our matrices are small. A
key to developing this method is that the determinant of a matrix with two iden-
tical rows is zero. This should be clear because the volume of the n-dimensional
parallelepiped is zero if two of its sides are the same. Suppose we have a 4×4 A
and we wish to ﬁnd its inverse A
−1.T h ei n v e r s ei s
A−1 = 1
|A|
⎡
⎢⎢⎣
ac
11 ac
21
ac
31
ac
41
ac
12 ac
22
ac
32
ac
42
ac
13
ac
23
ac
33
ac
43
ac
14
ac
24
ac
34
ac
44
⎤
⎥⎥⎦.
Note that this is just the transpose of the matrix where elements of A are replaced
by their respective cofactors multiplied by the leading constant (1 or -1). This
matrix is called the adjoint of A. The adjoint is the transpose of the cofactor
matrix of A. We can see why this is an inverse. Look at the product AA−1
which we expect to be the identity. If we multiply the ﬁrst row of A by the ﬁrst
column of the adjoint matrix we need to get |A| (remember the leading constant
above divides by |A|:
⎡
⎢⎢⎣
a11 a12 a13 a14
····
····
····
⎤
⎥⎥⎦
⎡
⎢⎢⎣
ac
11 ···
ac
12 ···
ac
13 ···
ac
14
···
⎤
⎥⎥⎦=
⎡
⎢⎢⎣
|A|···
· ···
· ···
· ···
⎤
⎥⎥⎦.
This is true because the elements in the ﬁrst row of A are multiplied exactly
by their cofactors in the ﬁrst column of the adjoint matrix which is exactly the
determinant. The other values along the diagonal of the resulting matrix are |A|
for analogous reasons. The zeros follow a similar logic:
⎡
⎢⎢⎣
····
a21 a22 a23 a24
····
····
⎤
⎥⎥⎦
⎡
⎢⎢⎣
ac
11 ···
ac
12 ···
ac
13
···
ac
14 ···
⎤
⎥⎥⎦=
⎡
⎢⎢⎣
····
0 ···
····
····
⎤
⎥⎥⎦.

100 5. Linear Algebra
Note that this product is a determinant of some matrix:
a21ac
11 +a22ac
12
+a23ac
13
+a24ac
14
.
The matrix in fact is ⎡
⎢⎢⎣
a21 a22 a23 a24
a21 a22 a23 a24
a31 a32 a33 a34
a41 a42 a43 a44.
⎤
⎥⎥⎦.
Because the ﬁrst two rows are identical, the matrix is singular, and thus, its deter-
minant is zero.
The argument above does not apply just to four by four matrices; using that
size just simpliﬁ es typography. For any matrix, the inverse is the adjoint matrix
divided by the determinant of the matrix being inverted. The adjoint is the trans-
pose of the cofactor matrix, which is just the matrix whose elements have been
replaced by their cofactors.
Example. The inverse of one particular three by three matrix whose determinant
is 6 is
⎡
⎣
112
134
025
⎤
⎦
−1
= 1
6
⎡
⎢⎢
⎢
⎢⎢⎢
⎢
⎢
⎢⎢⎣
⏐⏐⏐
⏐
34
25
⏐⏐⏐
⏐ −
⏐⏐⏐
⏐
12
25
⏐⏐⏐
⏐
⏐⏐⏐
⏐
12
34
⏐⏐⏐
⏐
−
⏐⏐⏐
⏐
14
05
⏐⏐⏐
⏐
⏐⏐⏐
⏐
12
05
⏐⏐⏐
⏐ −
⏐⏐⏐
⏐
12
14
⏐⏐⏐
⏐
⏐
⏐
⏐
⏐
13
02
⏐⏐
⏐
⏐ −
⏐
⏐
⏐
⏐
11
02
⏐⏐
⏐
⏐
⏐
⏐
⏐
⏐
11
13
⏐⏐
⏐
⏐
⎤
⎥⎥
⎥
⎥⎥⎥
⎥
⎥
⎥⎥⎦
= 1
6
⎡
⎣
7 −1 −2
−55 −2
2 −22
⎤
⎦.
Y ou can check this yourself by multiplying the matrices and making sure you get
the identity.
5.3.2 Linear Systems
We often encounter linear systems in graphics with “n equations andn unknowns,”
usually for n =2 or n =3 . For example,
3x+7y +2z =4 ,
2x−4y −3z = −1,
5x+2y + z =1 .

5.4. Eigenvalues and Matrix Diagonalization 101
Here x, y,a n dz are the “unknowns” for which we wish to solve. We can write
this in matrix form:
⎡
⎣
372
2 −4 −3
521
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
4
−1
1
⎤
⎦.
A common shorthand for such systems is Ax = b where it is assumed that A is
a square matrix with known constants, x is an unknown column vector (with ele-
mentsx, y,a n dz in our example), and b is a column matrix of known constants.
There are many ways to solve such systems, and the appropriate method de-
pends on the properties and dimensions of the matrix A. Because in graphics
we so frequently work with systems of size n ≤4, we’ll discuss here a method
appropriate for these systems, known as Cramer’s rule , which we saw earlier,
from a 2D geometric viewpoint, in the example on page 90. Here, we show this
algebraically. The solution to the above equation is
x =
⏐⏐
⏐⏐⏐
⏐
472
−1 −4 −3
121
⏐
⏐
⏐⏐⏐
⏐
⏐
⏐
⏐
⏐⏐⏐
372
2 −4 −3
521
⏐⏐
⏐
⏐⏐⏐
; y =
⏐
⏐
⏐⏐⏐
⏐
342
2 −1 −3
511
⏐⏐
⏐⏐⏐
⏐
⏐
⏐
⏐
⏐⏐⏐
372
2 −4 −3
521
⏐⏐
⏐
⏐⏐⏐
; z =
⏐
⏐
⏐⏐⏐
⏐
374
2 −4 −1
521
⏐⏐
⏐⏐⏐
⏐
⏐
⏐
⏐
⏐⏐⏐
372
2 −4 −3
521
⏐⏐
⏐
⏐⏐⏐
.
The rule here is to take a ratio of determinants, where the denominator is |A| and
the numerator is the determinant of a matrix created by replacing a column of A
with the column vector b. The column replaced corresponds to the position of
the unknown in vector x. For example, y is the second unknown and the second
column is replaced. Note that if |A| =0 , the division is unde ﬁned and there is
no solution. This is just another version of the rule that if A is singular (zero
determinant) then there is no unique solution to the equations.
5.4 Eigenvalues and Matrix Diagonalization
Square matrices have eigenvalues and eigenvectors associated with them. The
eigenvectors are those nonzero vectors whose directions do not change when mul-
tiplied by the matrix. For example, suppose for a matrix A and vector a,w eh a v e
Aa = λa. (5.9)
This means we have stretched or compressed a, but its direction has not changed.
The scale factorλis called the eigenvalue associated with eigenvectora. Knowing

102 5. Linear Algebra
the eigenvalues and eigenvectors of mat rices is helpful in a variety of practical
applications. We will describe them to gain insight into geometric transformation
matrices and as a step toward singular values and vectors described in the next
section.
If we assume a matrix has at least one eigenvector, then we can do a standard
manipulation to ﬁnd it. First, we write both sides as the product of a square matrix
with the vector a:
Aa = λIa, (5.10)
where I is an identity matrix. This can be rewritten
Aa−λIa =0 . (5.11)
Because matrix multiplication is distributive, we can group the matrices:
(A−λI) a =0 . (5.12)
This equation can only be true if the matrix (A −λI) is singular, and thus its
determinant is zero. The elements in this matrix are the numbers in A except
along the diagonal. For example, for a 2×2 matrix the eigenvalues obey
⏐⏐
⏐
⏐
a
11 −λa 12
a21 a22 −λ
⏐
⏐
⏐
⏐ = λ
2 −(a11 +a22)λ+(a11a22 −a12a21)=0 . (5.13)
Because this is a quadratic equation, we know there are exactly two solutions for
λ. These solutions may or may not be unique or real. A similar manipulation
for an n × n matrix will yield an nth-degree polynomial in λ. Because it is not
possible, in general, to ﬁnd exact explicit solutions of polynomial equations of
degree greater than four, we can only compute eigenvalues of matrices 4 × 4 or
smaller by analytic methods. For larger matrices, numerical methods are the only
option.
An important special case where eigenvalues and eigenvectors are particu-
larly simple is symmetric matrices (where A = A
T). The eigenvalues of real
symmetric matrices are always real numbers, and if they are also distinct, their
eigenvectors are mutually orthogonal. Such matrices can be put into diagonal
form:
A = QDQ
T, (5.14)
where Q is an orthogonal matrix and D is a diagonal matrix. The columns of Q
Recall that an orthogo-
nal matrix has orthonor-
mal rows and orthonormal
columns.
are the eigenvectors of A and the diagonal elements of D are the eigenvalues of
A. Putting A in this form is also called the eigenvalue decomposition, because it
decomposes A into a product of simpler matrices that reveal its eigenvectors and
eigenvalues.

5.4. Eigenvalues and Matrix Diagonalization 103
Example. Given the matrix
A =
[21
11
]
,
the eigenvalues of A are the solutions to
λ2 −3λ+1=0 .
We approximate the exact values for compactness of notation:
λ= 3±
√
5
2 , ≈
[2.618
0.382
]
.
Now we can ﬁnd the associated eigenvector. The ﬁrst is the nontrivial (not x =
y =0 ) solution to the homogeneous equation,
[2−2.618 1
11 −2.618
][ x
y
]
=
[0
0
]
.
This is approximately (x,y)=( 0 .8507,0.5257). Note that there are in ﬁnitely
many solutions parallel to that 2D vector, and we just picked the one of unit length.
Similarly the eigenvector associated with λ
2 is(x,y)=( −0.5257,0.8507).T h i s
means the diagonal form of A is (within some precision due to our numeric ap-
proximation):
[21
11
]
=
[0.8507 −0.5257
0.5257 0.8507
][ 2.618 0
00 .382
][ 0.8507 0.5257
−0.5257 0.8507
]
.
We will revisit the geometry of this matrix as a transform in the next chapter.
5.4.1 Singular Value Decomposition
We saw in the last section that any symmetric matrix can be diagonalized, or de-
composed into a convenient product of orthogonal and diagonal matrices. How-
ever, most matrices we encounter in graphics are not symmetric, and the eigen-
value decomposition for nonsymmetric ma trices is not nearly so convenient or
illuminating, and in general involves complex-valued eigenvalues and eigenvec-
tors even for real-valued inputs.
We would recommend
learning in this order: sym-
metric eigenvalues/vectors,
singular values/vectors,
and then nonsymmetric
eigenvalues, which are
much trickier.
There is another generalization of the symmetric eigenvalue decomposition
to nonsymmetric (and even non-square) matrices; it is the singular value decom-
position (SVD). The main difference between the eigenvalue decomposition of a
symmetric matrix and the SVD of a nonsymmetric matrix is that the orthogonal
matrices on the left and right sides are not required to be the same in the SVD:
A = USVT.

104 5. Linear Algebra
Here U and V are two, potentially different, orthogonal matrices, whose columns
are known as the left and right singular vectors of A,a n d S is a diagonal matrix
whose entries are known as the singular values of A.W h e nA is symmetric and
has all nonnegative eigenvalues, the SVD and the eigenvalue decomposition are
the same.
There is another relationship between singular values and eigenvalues that
can be used to compute the SVD (though this is not the way an industrial-strength
SVD implementation works). First we de ﬁne M = AAT. We assume that we
can perform a SVD on M:
M = AAT =( USVT)(USVT)T = US(VTV)SUT = US2UT.
The substitution is based on the fact that (BC)T = CTBT, that the transpose
of an orthogonal matrix is its inverse, and the transpose of a diagonal matrix
is the matrix itself. The beauty of this new form is that M is symmetric and
US2UT is its eigenvalue decomposition, where S2 contains the (all nonnegative)
eigenvalues. Thus, we ﬁnd that the singular values of a matrix are the square
roots of the eigenvalues of the product of the matrix with its transpose, and the
left singular vectors are the eigenvectors of that product. A similar argument
allows V, the matrix of right singular vectors, to be computed from A
TA.
Example. We now make this concrete with an example:
A =
[
11
01
]
; M = AAT =
[
21
11
]
.
We saw the eigenvalue decomposition for this matrix in the previous section. We
observe immediately
[11
01
]
=
[0.8507 −0.5257
0.5257 0.8507
][ √
2.618 0
0
√
0.382
]
VT.
We can solve for V algebraically:
V =( S−1UTA)T.
The inverse of S is a diagonal matrix with the reciprocals of the diagonal elements
of S. This yields
[
11
01
]
= U
[
σ1 0
0 σ2
]
VT
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
.

5.4. Eigenvalues and Matrix Diagonalization 105
This form used the standard symbol σi for the ith singular value. Again, for a
symmetric matrix, the eigenvalues and the singular values are the same (σi = λi).
We will examine the geometry of SVD further in Section 6.1.6.
Frequently Asked Questions
•Why is matrix multiplication deﬁned the way it is rather than just element
by element?
Element by element multiplication is a perfectly good way to de ﬁne matrix mul-
tiplication, and indeed it has nice properties. However, in practice it is not very
useful. Ultimately, most matrices are used to transform column vectors, e.g., in
3D you might have
b = Ma,
where a and b are vectors and M is a3×3 matrix. To allow geometric operations
such as rotation, combinations of all three elements of a must go into each element
of b. That requires us to either go row-by-row or column-by-column through M.
That choice is made based on composition of matrices having the desired property,
M
2(M1a)=( M2M1)a
which allows us to use one composite matrixC = M2M1 to transform our vector.
This is valuable when many vectors will be transformed by the same composite
matrix. So, in summary, the somewhat weird rule for matrix multiplication is en-
gineered to have these desired properties.
•Sometimes I hear that eigenvalues and singular values are the same
thing and sometimes that one is the square of the other. Which is right?
If a real matrix A is symmetric, and its eigenvalues are nonnegative, then its
eigenvalues and singular values are the same. If A is not symmetric, the matrix
M = AA
T is symmetric and has nonnegative real eignenvalues. The singular
values of A and AT are the same and are the square roots of the singular/eigen-
values of M. Thus, when the square root statement is made, it is because two
different matrices (with a very particular relationship) are being talked about:
M = AAT.

106 5. Linear Algebra
Notes
The discussion of determinants as volumes is based on A V ector Space Approach
to Geometry (Hausner, 1998). Hausner has an excellent discussion of vector
analysis and the fundamentals of geometry as well. The geometric derivation
of Cramer’s rule in 2D is taken from Practical Linear Algebra: A Geometry T ool-
box (Farin & Hansford, 2004). That book also has geometric interpretations of
other linear algebra operations such as Gaussian elimination. The discussion of
eigenvalues and singular values is based primarily on Linear Algebra and Its Ap-
plications (Strang, 1988). The example of SVD of the shear matrix is based on a
discussion in Computer Graphics and Geometric Modeling (Salomon, 1999).
Exercises
1. Write an implicit equation for the 2D line through points (x0,y0) and
(x1,y1) using a 2D determinant.
2. Show that if the columns of a matrix are orthonormal, then so are the rows.
3. Prove the properties of matrix determinants stated in Equations (5.5)–(5.7).
4. Show that the eigenvalues of a diagonal matrix are its diagonal elements.
5. Show that for a square matrix A, AAT is a symmetric matrix.
6. Show that for three 3D vectorsa, b, c, the following identity holds:|abc| =
(a× b)· c.
7. Explain why the volume of the tetrahedron with side vectors a, b, c (see
Figure 5.2) is given by |abc|/6.
8. Demonstrate the four interpretations of matrix-matrix multiplication by tak-
ing the following matrix-matrix multiplication code, rearranging the nested
loops, and interpreting the resulting c ode in terms of matrix and vector op-
erations.
function mat-mult(in a[m][p], in b[p][n], out c[m][n]) {
// the array c is initialized to zero
f o ri=1t om
f o rj=1t on
for k = 1 to p
c[i][j] += a[i][k] * b[k][j]
}

5.4. Eigenvalues and Matrix Diagonalization 107
9. Prove that if A, Q,a n d D satisfy Equation (5.14), v is the ith row of Q,
and λis the ith entry on the diagonal of D,t h e nv is an eigenvector of A
with eigenvalueλ.
10. Prove that if A, Q,a n dD satisfy Equation (5.14), the eigenvalues of A are
all distinct, and v is an eigenvector of A with eigenvalue λ, then for some
i, v is the ith row of Q andλis the ith entry on the diagonal of D.
11. Given the (x,y) coordinates of the three vertices of a 2D triangle, explain
why the area is given by
1
2
⏐⏐
⏐
⏐
⏐⏐
x
0 x1 x2
y0 y1 y2
111
⏐⏐
⏐
⏐
⏐⏐
.

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
**关联概念**: [[仿射变换]]
**pbrt 对应**: pbrt Ch3.9-3.10 Transformations
