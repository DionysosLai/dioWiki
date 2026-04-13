---
title: "7 Viewing"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 7 Viewing

7
Viewing
In the previous chapter, we saw how to use matrix transformations as a tool for
arranging geometric obj ects in 2D or 3D space. A second important use of geo-
metric transformations is in moving objects between their 3D locations and their
positions in a 2D view of the 3D world. This 3D to 2D mapping is called a viewing
transformation, and it plays an important role in object-order rendering, in which
we need to rapidly ﬁnd the image-space location of each object in the scene.
When we studied ray tracing in Chapter 4, we covered the different types of
perspective and orthographic views and h ow to generate viewing rays according
to any given view. This chapter is about the inverse of that process. Here we
explain how to use matrix transformations to express any parallel or perspective
view. The transformations in this chapter project 3D points in the scene (world
space) to 2D points in the image (image space), and they will project any point on
a given pixel’s viewing ray back to that pixel’s position in image space.
If you have not looked at it recently, it is advisable to review the discussion of
perspective and ray generation in Chapter 4 before reading this chapter.
By itself, the ability to project points from the world to the image is only
good for producing wireframe renderings—renderings in which only the edges
of objects are drawn, and closer surfaces do not occlude more distant surfaces
(Figure 7.1). Just as a ray tracer needs to ﬁnd the closest surface intersection
along each viewing ray, an object-order re nderer displaying solid-looking objects
has to work out which of the (possibly many) surfaces drawn at any given point
on the screen is closest and display only that one. In this chapter, we assume we
139

140 7. Viewing
Figure 7.1. Left: wireframe cube in orthographic p rojection. Middle: wireframe cube in
perspective projection. Right: perspect ive projection with hidden lines removed.
are drawing a model consisting only of 3D line segments that are speci ﬁed by
the (x,y,z ) coordinates of their two endpoints. Later chapters will discuss the
machinery needed to produce renderings of solid surfaces.
7.1 Viewing T ransformations
The viewing transformation has the job of mapping 3D locations, represented
as (x,y,z ) coordinates in the canonical coordinate system, to coordinates in the
image, expressed in units of pixels. I t is a complicated beast that depends on
Some APIs use “viewing
transformation” for just the
piece of our viewing trans-
formation that we call the
camera transformation.
many different things, including the camera position and orientation, the type
of projection, the ﬁeld of view, and the resolution of the image. As with all
complicated transformations it is best approached by breaking it up into a product
of several simpler transformations. Most graphics systems do this by using a
sequence of three transformations:
 A camera transformation or eye transformation, which is a rigid body trans-
formation that places the camera at the origin in a convenient orientation.
It depends only on the position and orientation, or pose, of the camera.
 A projection transformation, which projects points from camera space so
that all visible points fall in the range −1 to 1 in x and y. It depends only
on the type of projection desired.
 A viewport transformation or windowing transformation, which maps this
unit image rectangle to the desired rectangle in pixel coordinates. It de-
pends only on the size and position of the output image.
To make it easy to describe the stages of the process (Figure 7.2), we give names
to the coordinate systems that are the inputs and output of these transformations.

7.1. Viewing T ransformations 141
Object space
World space
Camera space
Screen space
Modeling
transformation
Canonical
view volume
Camera
transformation
Viewport
transformation
Projection
transformation
Figure 7.2. The sequence of spaces and transformations that gets objects from their original
coordinates into screen space.
The camera transformation converts points in canonical coordinates (or world
space) to camera coordinates or places them in camera space . The projection
transformation moves point s from camera space to the canonical view volume .
Other names: camera
space is also “eye space”
and the camera transfor-
mation is sometimes the
“viewing transformation;”
the canonical view volume
is also “clip space” or
“normalized device coordi-
nates;” screen space is also
“pixel coordinates.”
Finally, the viewport transformation maps the canonical view volume to screen
space.
Each of these transformations is individually quite simple. We’ll discuss them
in detail for the orthographic case beginning with the viewport transformation,
then cover the changes required to support perspective projection.
7.1.1 The Viewport T ransformation
We begin with a problem whose solution will be reused for any viewing condition.
We assume that the geometry we want to view is in the canonical view volume ,
The word “canonical”
crops up again—it means
something arbitrarily
chosen for convenience.
For instance, the unit
circle could be called the
“canonical circle.”
and we wish to view it with an orthographic camera looking in the −z direction.
The canonical view volume is the cube containing all 3D points whose Cartesian
coordinates are between −1 and +1—that is, (x,y,z ) ∈[−1,1]3 (Figure 7.3).
We project x = −1 to the left side of the screen, x =+ 1 to the right side of the
screen, y = −1 to the bottom of the screen, and y =+ 1to the top of the screen.
Recall the conventions for pixel coordinates from Chapter 3: each pixel “owns”
a unit square centered at integer coordinates; the image boundaries have a half-

142 7. Viewing
unit overshoot from the pixel centers; and the smallest pixel center coordinates
are (0,0). If we are drawing into an image (or window on the screen) that has
nx by ny pixels, we need to map the square [−1,1]2 to the rectangle [−0.5,nx −
0.5]×[−0.5,ny −0.5].
Mapping a square to a po-
tentially non-square rectan-
gle is not a problem; x and
y just end up with differ-
ent scale factors going from
canonical to pixel coordi-
nates.
For now, we will assume that all line segments to be drawn are completely
inside the canonical view volume. Later we will relax that assumption when we
discuss clipping.
Since the viewport transformation maps one axis-aligned rectangle to another,
it is a case of the windowing transform given by Equation (6.6):
⎡
⎣
xscreen
yscreen
1
⎤
⎦=
⎡
⎣
nx
2 0 nx−1
2
0 ny
2
ny−1
2
00 1
⎤
⎦
⎡
⎣
x
canonical
ycanonical
1
⎤
⎦. (7.1)
Note that this matrix ignores the z-coordinate of the points in the canonical view
z
y
(1,1,1)
(–1,–1,–1) x
Figure 7.3. The canoni-
cal view volume is a cube with
side of length two centered at
the origin.
volume, because a point’s distance along th e projection direction doesn’t affect
where that point projects in the image. But before we of ﬁcially call this the view-
port matrix , we add a row and column to carry along the z-coordinate without
changing it. We don’t need it in this chapter, but eventually we will need the z
values because they can be used to make closer surfaces hide more distant surfaces
(see Section 8.2.3).
Mvp =
⎡
⎢⎢⎣
nx
2 00 nx−1
2
0 ny
2 0 ny−1
2
00 10
00 01
⎤
⎥⎥⎦. (7.2)
7.1.2 The Orthographic Projection T ransformation
Of course, we usually want to render geometry in some region of space other than
the canonical view volume. Our ﬁrst step in generalizing the view will keep the
view direction and orientation ﬁxed looking along −z with +y up, but will allow
arbitrary rectangles to be viewed. Rather than replacing the viewport matrix, we’ll
augment it by multiplying it with another matrix on the right.
Under these constraints, the view volume is an axis-aligned box, and we’ll
name the coordinates of its sides so that the view volume is [l,r]×[b,t]×[f,n]
shown in Figure 7.4. We call this box the orthographic view volume and refer to

7.1. Viewing T ransformations 143
y
z x
(l,b,n)
(r,t,f )
Figure 7.5. The orthographic view volume is along the negative z-axis, so f i sam o r en e g a t i v e
number than n, thus n > f.
the bounding planes as follows:
x = l ≡left plane,
x = r ≡right plane,
y = b ≡bottom plane,
y = t ≡top plane,
z = n ≡near plane,
z = f ≡far plane.
That vocabulary assumes a viewer who is looking along the minus z-axis with
(l,b,n)
(r,t,f )
Figure 7.4. The ortho-
graphic view volume.
his head pointing in the y-direction.1 This implies that n>f ,w h i c hm a yb e
unintuitive, but if you assume the entire orthographic view volume has negative z
values then the z = n “near” plane is closer to the viewer if and only if n>f ;
heref is a smaller number than n, i.e., a negative number of larger absolute value
than n.
This concept is shown in Figure 7.5. The transform from orthographic view
volume to the canonical view volume is another windowing transform, so we can
simply substitute the bounds of the orthographic and canonical vi ew volumes into
Equation (6.7) to obtain the matrix for this transformation:
This matrix is very close to
the one used traditionally in
OpenGL, except that n, f ,
and z
canonical all have the
opposite sign.
Morth =
⎡
⎢⎢⎣
2
r−l 00 −r+l
r−l
0 2
t−b 0 −t+b
t−b
00 2
n−f −n+f
n−f
000 1
⎤
⎥⎥⎦. (7.3)
1 Most programmers ﬁnd it intuitive to have the x-axis pointing right and the y-axis pointing up. In
a right-handed coordinate system, this implies that we are looking in the −z direction. Some systems
use a left-handed coordinate system for v iewing so that the gaze direction is along +z. Which is best
is a matter of taste, and this text assumes a right-h anded coordinate system. A reference that argues
for the left-handed system instead is given in the notes at the end of the chapter.

144 7. Viewing
To draw 3D line segments in the orthographic view volume, we project them
into screen x-a n dy-coordinates and ignore z-coordinates. We do this by com-
bining Equations (7.2) and (7.3). Note that in a program we multiply the matrices
together to form one matrix and then manipulate points as follows:
⎡
⎢⎢⎣
xpixel
ypixel
zcanonical
1
⎤
⎥⎥⎦=( MvpMorth)
⎡
⎢⎢⎣
x
y
z
1
⎤
⎥⎥⎦.
Thez-coordinate will now be in [−1,1]. We don’t take advantage of this now, but
it will be useful when we examine z-buffer algorithms.
The code to draw many 3D lines with endpoints ai and bi thus becomes both
simple and efﬁcient:
This is a ﬁrst example of
how matrix transformation
machinery makes graphics
programs clean and ef ﬁ-
cient.
construct Mvp
construct Morth
M = MvpMorth
for each line segment (ai, bi) do
p = Mai
q = Mbi
drawline(xp,yp,xq,yq)
7.1.3 The Camera T ransformation
We’d like to be able to change the viewpoint in 3D and look in any direction.
There are a multitude of conventions for specifying viewer position and orienta-
tion. We will use the following one (see Figure 7.6):
w
u
e
g
t
v
Figure 7.6. The user spec-
iﬁes viewing as an eye posi-
tion e, a gaze direction g,a n d
an up vector t. We construct
a right-handed basis with w
pointing opposite to the gaze
and v being in the same plane
as g and t.
 the eye position e,
 the gaze direction g,
 the view-up vector t.
The eye position is a location that the eye “sees from.” If you think of graphics
as a photographic process, it is the center of the lens. The gaze direction is any
vector in the direction that the viewer is looking. The view-up vector is any vector
in the plane that both bisects the viewer’s head into right and left halves and points
“to the sky” for a person standing on the ground. These vectors provide us with
enough information to set up a coordinate system with origin e and a uvw basis,

7.1. Viewing T ransformations 145
v
e
u
w
z
(u=l, v=b, w=n)
(u=r, v=t, w=f )
x o
y
Figure 7.7. For arbitrary viewing, we need to change th e points to be stored in the “appropriate”
coordinate system. In this case it has origin e and offset coordinates in terms of uvw.
using the construction of Section 2.4.7:
w = − g
∥g∥,
u = t× w
∥t× w∥,
v = w× u.
Our job would be done if all points we wished to transform were stored in co-
ordinates with origin e and basis vectors u, v,a n dw. But as shown in Figure 7.7,
the coordinates of the model are stored in terms of the canonical (or world) ori-
gin o and the x-, y-, and z-axes. To use the machinery we have already developed,
we just need to convert the coordinates of the line segment endpoints we wish to
draw from xyz-coordinates into uvw-coordinates. This kind of transformation
was discussed in Section 6.5, and the matrix that enacts this transformation is the
canonical-to-basis matrix of the camera’s coordinate frame:
M
cam =
[uvwe
0001
]−1
=
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
⎥⎥⎦. (7.4)
Alternatively, we can think of t his same transformation as ﬁrst moving e to the
origin, then aligning u, v, w to x, y, z.
To make our previouslyz-axis-only viewing algorithm work for cameras with
any location and orientation, we just n eed to add this camera transformation to

146 7. Viewing
the product of the viewport and projection transformations, so that it converts the
incoming points from world to camera coordinates before they are projected:
construct Mvp
construct Morth
construct Mcam
M = MvpMorthMcam
for each line segment (ai, bi) do
p = Mai
q = Mbi
drawline(xp,yp,xq,yq)
Again, almost no code is needed once t he matrix infrastructure is in place.
7.2 Projective T ransformations
We have left perspective for last because it takes a little bit of cleverness to make
it ﬁt into the system of vectors and matrix transformations that has served us so
well up to now. To see what we need to do, let’s look at what the perspective
projection transformation needs to do w ith points in camera space. Recall that the
For the moment we will ig-
nore the sign of z to keep
the equations simpler, but it
will return on page 150.
viewpoint is positioned at the origin and the camera is looking along the z-axis.
The key property of perspective is that the size of an object on the screen is
proportional to 1/z for an eye at the origin looking up the negative z-axis. This
can be expressed more precisely in an equation for the geometry in Figure 7.8:
ys = d
zy, (7.5)
eg ys
View plane
d
z
y
Figure 7.8. The geometry for Equation (7.5). The viewer’s eye is at e and the gaze direction is g
(the minus z-axis). The view plane is a distance d from the eye. A point is projected toward e and
where it intersects the view plane is where it is drawn.

7.2. Projective T ransformations 147
where y is the distance of the point along the y-axis, and ys is where the point
should be drawn on the screen.
We would really like to use the matrix machinery we developed for ortho-
graphic projection to draw perspective images; we could then just multiply an-
other matrix into our composite matrix and use the algorithm we already have.
However, this type of transformation, in which one of the coordinates of the input
vector appears in the denominator, can’t be achieved using afﬁne transformations.
We can allow for division with a simple g eneralization of the mechanism of
homogeneous coordinates that we have been using for af ﬁne transformations.
We have agreed to represent the point (x,y,z ) using the homogeneous vector
[xyz 1]
T; the extra coordinate, w,i sa l w a y se q u a lt o1, and this is ensured by
always using [ 0001 ]T as the fourth row of an af ﬁne transformation matrix.
Rather than just thinking of the 1 as an extra piece bolted on to coerce matrix
multiplication to implement translation, we now de ﬁne it to be the denominator
of the x-, y-, and z-coordinates: the homogeneous vector [xyzw ]T represents
the point(x/w,y/w,z/w) . This makes no difference when w =1 , but it allows a
broader range of transformations to be implemented if we allow any values in the
bottom row of a transformation matrix, causing w to take on values other than 1.
Concretely, linear transformations allow us to compute expressions like
x
′ = ax+by+cz
and afﬁne transformations extend this to
x′ = ax+by+cz +d.
Treating w as the denominator further expands the possibilities, allowing us to
compute functions like
x′ = ax+by+cz +d
ex+fy +gz +h;
this could be called a “linear rational function” of x,y,a n dz. But there is an extra
constraint—the denominators are the same for all coordinates of the transformed
point:
x
′ = a1x+b1y +c1z +d1
ex+fy +gz +h ,
y′ = a2x+b2y +c2z +d2
ex+fy +gz +h ,
z′ = a3x+b3y +c3z +d3
ex+fy +gz +h .

148 7. Viewing
Expressed as a matrix transformation,
⎡
⎢⎢⎣
˜x
˜y
˜z
˜w
⎤
⎥⎥⎦=
⎡
⎢⎢⎣
a1 b1 c1 d1
a2 b2 c2 d2
a3 b3 c3 d3
efgh
⎤
⎥⎥⎦
⎡
⎢⎢⎣
x
y
z
1
⎤
⎥⎥⎦
and
(x′,y′,z′)=(˜x/˜w, ˜y/˜w,˜z/˜w).
A transformation like this is known as a projective transformation or a
homography.
Example. The matrix
M =
⎡
⎣
20 −1
03 0
0 2
3
1
3
⎤
⎦
represents a 2D projective transformation that transforms the unit square ( [0,1]×
[0,1]) to the quadrilateral shown in Figure 7.9.
For instance, the lower-right corner of the square at (1,0) is represented by
the homogeneous vector [1 0 1]T and transforms as follows:
1
1
3
3
1
Unit
square
Figure 7.9. A projective
transformation maps a square
to a quadrilateral, preserving
straight lines but not parallel
lines.
⎡
⎣
20 −1
03 0
0 2
3
1
3
⎤
⎦
⎡
⎣
1
0
1
⎤
⎦=
⎡
⎣
1
0
1
3
⎤
⎦,
which represents the point (1/1
3,0/1
3),o r (3,0).N o t et h a ti fw eu s et h em a t r i x
3M =
⎡
⎣
60 −3
09 0
02 1
⎤
⎦
instead, the result is [ 301 ]T, which also represents (3,0). In fact, any scalar
multiple cM is equivalent: the numerator and denominator are both scaled by c,
which does not change the result.
There is a more elegant way of expressingthe same idea, which avoids treating
the w-coordinate specially. In this view a 3D projective transformation is simply
a 4D linear transformation, with the extra stipulation that all scalar multiples of a
vector refer to the same point:
x ∼αx for all α̸=0 .
The symbol ∼is read as “is equivalent to” and means that the two homogeneous
vectors both describe the same point in space.

7.3. Perspective Projection 149
x = 1.5 x = 3
h = 1
h = 2
x
h
Figure 7.10. The point x = 1.5 is represented by any point on the line x =1 . 5h, such as points at the
hollow circles. However, before we interpret x as a conventional Cartesian coordinate, we ﬁrst divide
by h to get (x,h ) = (1.5,1) as shown by the black point.
Example. In 1D homogeneous coordinates, in which we use 2-vectors to repre-
sent points on the real line, we could represent the point (1.5) using the homoge-
neous vector [1.51 ]T, or any other point on the line x =1 .5h in homogeneous
space. (See Figure 7.10.)
In 2D homogeneous coordinates, in which we use 3-vectors to represent points
in the plane, we could represent the point (−1,−0.5) using the homogeneous
vector [−2;−1;2]T, or any other point on the line x = α[−1 −0.51 ]T.A n y
homogeneous vector on the line can be mapped to the line’s intersection with the
plane w =1 to obtain its Cartesian coordinates. (See Figure 7.11.)
y
x
w
(–1, –.5, 1)
(–2, –1, 2)
αv
v
w = 1
Figure 7.11. A point
in homogeneous coordinates
is equivalent to any other point
on the line through it and the
origin, and normalizing the
point amounts to intersecting
this line with the plane w =1 .
It’s ﬁne to transform homogeneous vectors as many times as needed, with-
out worrying about the value of the w-coordinate—in fact, it is ﬁne if the w-
coordinate is zero at some intermediate phase. It is only when we want the ordi-
nary Cartesian coordinates of a point that we need to normalize to an equivalent
point that has w =1 , which amounts to dividing all the coordinates by w.O n c e
we’ve done this we are allowed to read off the (x,y,z )-coordinates from the ﬁrst
three components of the homogeneous vector.
7.3 Perspective Projection
The mechanism of projective transformations makes it simple to implement the
division byz required to implement perspective. In the 2D example shown in Fig-
ure 7.8, we can implement the perspective projection with a matrix transformation

150 7. Viewing
as follows:
[ys
1
]
∼
[d 00
010
]⎡
⎣
y
z
1
⎤
⎦.
This transforms the 2D homogeneous vector [y;z;1]T to the 1D homogeneous
vector[dy z]T, which represents the 1D point (dy/z) (because it is equivalent to
the 1D homogeneous vector [dy/z 1]T. This matches Equation (7.5).
For the “of ﬁcial” perspective projection matrix in 3D, we’ll adopt our usual
convention of a camera at t he origin facing in the −z direction, so the distance
of the point (x,y,z ) is −z. As with orthographic projection, we also adopt the
notion of near and far planes that limit the range of distances to be seen. In this
context, we will use the near plane as the projection plane, so the image plane
distance is −n.
Remember, n < 0.
 The desired mapping is then ys =( n/z)y, and similarly for x. This transfor-
mation can be implemented by the perspective matrix:
P =
⎡
⎢⎢⎣
n 00 0
0 n 00
00 n+f −fn
00 1 0
⎤
⎥⎥⎦.
The ﬁrst, second, and fourth rows simply implement the perspective equation.
The third row, as in the orthographic and viewport matrices, is designed to bring
thez-coordinate “along for the ride” so that we can use it later for hidden surface
removal. In the perspective projec tion, though, the addition of a non-constant
denominator prevents us from actually preserving the value of z—it’s actually
impossible to keep z from changing while getting x and y to do what we need
them to do. Instead we’ve opted to keep z unchanged for points on the near or far
More on this later.
 planes.
There are many matrices that could func tion as perspective matrices, and all
of them nonlinearly distort the z-coordinate. This speci ﬁc matrix has the nice
properties shown in Figures 7.12 and 7.13; it leaves points on the (z = n)-
plane entirely alone, and it leaves points on the (z = f)-plane while “squishing”
them in x and y by the appropriate amount. The effect of the matrix on a point
(x,y,z ) is
P
⎡
⎢⎢
⎢
⎢⎣
x
y
z
1
⎤
⎥⎥
⎥
⎥⎦
=
⎡
⎢⎢
⎢
⎢⎣
nx
ny
(n+f)z −fn
z
⎤
⎥⎥
⎥
⎥⎦
∼
⎡
⎢⎢
⎢
⎢⎣
nx
z
ny
z
n+f −fn
z
1
⎤
⎥⎥
⎥
⎥⎦
.

7.3. Perspective Projection 151
y
z x
y
z x
(l,b,n)
(r,t,f )
Figure 7.12. The perspective projection leaves points on the z=n plane unchanged and maps the
large z=f rectangle at the back of the perspective volume to the small z=f rectangle at the back of
the orthographic volume.
Figure 7.13. The perspective projection maps any line t hrough the origin/eye to a line parallel to
the z-axis and without moving the point on the line at z=n .
As you can see, x and y are scaled and, more importantly, divided by z. Because
both n and z (inside the view volume) are negative, there are no “ ﬂips” in x
and y. Although it is not obvious (see the exercise at the end of the chapter),
the transform also preserves the relative order of z values between z = n and
z = f , allowing us to do depth ordering after this matrix is applied. This will be
important later when we do hidden surface elimination.
Sometimes we will want to take the inverse of P, for example, to bring a
screen coordinate plus z back to the original space, as we might want to do for

152 7. Viewing
picking. The inverse is
P−1 =
⎡
⎢⎢⎣
1
n 00 0
0 1
n 00
00 0 1
00 −1
fn
n+f
fn
⎤
⎥⎥⎦.
Since multiplying a homogeneous vector by a scalar does not change its meaning,
the same is true of matrices that operate on homogeneous vectors. So we can
write the inverse matrix in a pre ttier form by multiplying through by nf :
P
−1 =
⎡
⎢⎢⎣
f 00 0
0 f 00
00 0 fn
00 −1 n+f
⎤
⎥⎥⎦.
This matrix is not literally
the inverse of the matrix
P, but the transformation
it describes is the inverse
of the transformation de-
scribed by P.
Taken in the context of the orthographic projection matrix Morth in Equa-
tion (7.3), the perspective matrix simply maps the perspective view volume (which
is shaped like a slice, or frustum, of a pyramid) to the orthographic view volume
(which is an axis-aligned box). The beauty of the perspective matrix is that once
we apply it, we can use an orthographic transform to get to the canonical view
volume. Thus, all of the orthographic machinery applies, and all that we have
added is one matrix and the division by w. It is also heartening that we are not
“wasting” the bottom row of our four by four matrices!
Concatenating P with M
orth results in the perspective projection matrix,
Mper = MorthP.
One issue, however, is: How are l,r ,b,tdetermined for perspective? They
identify the “window” through which we look. Since the perspective matrix does
not change the values of x and y on the (z = n)-plane, we can specify (l,r,b,t )
on that plane.
To integrate the perspective matrix into our orthographic infrastructure, we
simply replace M
orth with Mper, which inserts the perspective matrix P after the
camera matrix Mcam has been applied but before the orthographic projection. So
the full set of matrices for perspective viewing is
M = MvpMorthPMcam.
The resulting algorithm is:
compute Mvp
compute Mper
compute Mcam

7.4. Some Properties of the Perspective T ransform 153
M = MvpMperMcam
for each line segment (ai, bi) do
p = Mai
q = Mbi
drawline(xp/wp,yp/wp,xq/wq,yq/wq)
Note that the only change other than the additional matrix is the divide by the
homogeneous coordinate w.
Multiplied out, the matrix Mper looks like this:
Mper =
⎡
⎢⎢
⎢⎢⎢
⎢
⎢
⎢⎣
2n
r−l 0 l+r
l−r 0
0 2n
t−b
b+t
b−t 0
00 f+n
n−f
2fn
f−n
001 0
⎤
⎥⎥
⎥⎥⎥
⎥
⎥
⎥⎦
.
This or similar matrices often appear in documentation, and they are less mysteri-
ous when one realizes that they are usually the product of a few simple matrices.
Example. Many APIs such as OpenGL (Shreiner, Neider, Woo, & Davis, 2004)
use the same canonical view volume as presented here. They also usually have the
user specify the absolute values of n andf . The projection matrix for OpenGL is
M
OpenGL =
⎡
⎢⎢⎢⎢
⎢
⎢
⎢
⎢⎢⎣
2|n|
r−l 0 r+l
r−l 0
0 2|n|
t−b
t+b
t−b 0
00 |n|+|f|
|n|−|f|
2|f||n|
|n|−|f|
00 −10
⎤
⎥⎥⎥⎥
⎥
⎥
⎥
⎥⎥⎦
.
Other APIs send n and f to 0 and 1, respectively. Blinn (J. Blinn, 1996) recom-
mends making the canonical view volume[0,1]
3 for efﬁciency. All such decisions
will change the the projection matrix slightly.
7.4 Some Properties of the Perspective T ransform
An important property of the perspective transform is that it takes lines to lines
and planes to planes. In addition, it takes line segments in the view volume to line

154 7. Viewing
segments in the canonical volume. To see this, consider the line segment
q+t(Q−q).
When transformed by a 4×4 matrix M, it is a point with possibly varying homo-
geneous coordinate:
Mq+t(MQ−Mq) ≡r+t(R−r).
The homogenized 3D line segment is
r+t(R−r)
wr +t(wR −wr). (7.6)
If Equation (7.6) can be rewritten in a form
r
wr
+f(t)
( R
wR
− r
wr
)
, (7.7)
then all the homogenized points lie on a 3D line. Brute force manipulation of
Equation (7.6) yields such a form with
f(t)= wRt
wr +t(wR −wr). (7.8)
It also turns out that the line segments do map to line segments preserving the
ordering of the points (Exercise 8), i.e., they do not get reordered or “torn.”
A byproduct of the transform taking line segments to line segments is that
it takes the edges and vertices of a triangle to the edges and vertices of another
triangle. Thus, it takes triangles to triangles and planes to planes.
7.5 Field-of-View
While we can specify any window using the (l,r,b,t ) and n values, sometimes
we would like to have a simpler system where we look through the center of the
window. This implies the constraint that
l = −r,
b = −t.
If we also add the constraint that the pixels are square, i.e., there is no distortion
of shape in the image, then the ratio of r to t must be the same as the ratio of the
number of horizontal pixels to the number of vertical pixels:
n
x
ny
= r
t.

7.5. Field-of-View 155
v
e
u
w
(u = –r, v = –t, w = n)
(u = +r, v = +t, w = n)
θ/2
Figure 7.14. The ﬁeld-of-viewθis the angle from the bottom of the screen to the top of the screen
as measured from the eye.
Once nx and ny are speci ﬁed, this leaves only one degree of freedom. That is
often set using the ﬁeld-of-view shown as θin Figure 7.14. This is sometimes
called the vertical ﬁeld-of-view to distinguish it from the angle between left and
right sides or from the angle between diagonal corners. From the ﬁgure we can
see that
tan θ
2 = t
|n|.
If n and θare speci ﬁed, then we can derive t and use code for the more general
viewing system. In some systems, the value ofn is hard-coded to some reasonable
value, and thus we have one fewer degree of freedom.
Frequently Asked Questions
•Is orthographic projection ever useful in practice?
It is useful in applications where relative length judgments are important. It can
also yield simpli ﬁcations where perspective would be too expensive as occurs in
some medical visualization applications.
•The tessellated spheres I draw in perspective look like ovals. Is this a
bug?
No. It is correct behavior. If you place your eye in the same relative position to
the screen as the virtual viewer has with respect to the viewport, then these ovals
will look like circles because they themselves are viewed at an angle.

156 7. Viewing
•Does the perspective matrix take negative z values to positive z values
with a reversed ordering? Doesn’t that cause trouble?
Y es. The equation for transformedz is
z′ = n+f −fn
z .
So z =+ ϵ is transformed to z′ = −∞and z = −ϵ is transformed to z = ∞.
So any line segments that span z =0 will be “torn” although all points will be
projected to an appropriate screen locati on. This tearing is not relevant when all
objects are contained in the viewing volume. This is usually assured by clipping
to the view volume. However, clipping itself is made more complicated by the
tearing phenomenon as is discussed in Chapter 8.
•The perspective matrix changes the value of the homogeneous coordi-
nate. Doesn’t that make the move and scale transformations no longer
work properly?
Applying a translation to a homogeneous point we have
⎡
⎢⎢⎣
100t x
010 ty
001 tz
0001
⎤
⎥⎥⎦
⎡
⎢⎢⎣
hx
hy
hz
h
⎤
⎥⎥⎦=
⎡
⎢⎢⎣
hx+htx
hy +hty
hz +htz
h
⎤
⎥⎥⎦homogenize−−−−−−−→
⎡
⎢⎢⎣
x+tx
y +ty
z +tz
1
⎤
⎥⎥⎦.
Similar effects are true for other transforms (see Exercise 5).
Notes
Most of the discussion of viewing matrices is based on information in Real-Time
Rendering (Akenine-M¨oller et al., 2008), the OpenGL Programming Guide(Shreiner
et al., 2004), Computer Graphics (Hearn & Baker, 1986), and 3D Game Engine
Design (Eberly, 2000).
Exercises
1. Construct the viewport matrix required for a system in which pixel coordi-
nates count down from the top of the image, rather than up from the bottom.
2. Multiply the viewport and orthographi c projection matrices, and show that
the result can also be obtained by a single application of Equation (6.7).

7.5. Field-of-View 157
3. Derive the third row of Equation (7.3) from the constraint thatz is preserved
for points on the near and far planes.
4. Show algebraically that the perspective matrix preserves order of z values
within the view volume.
5. For a4×4 matrix whose top three rows are arbitrary and whose bottom row
is(0,0,0,1), show that the points (x,y,z, 1) and(hx,hy,hz,h) transform
to the same point after homogenization.
6. V erify that the form of M−1
p given in the text is correct.
7. V erify that the full perspective to canonical matrix Mprojection takes(r,t,n )
to(1,1,1).
8. Write down a perspective matrix for n =1 ,f =2 .
9. For the point p =( x,y,z, 1), what are the homogenized and unhomoge-
nized results for that point transformed by the perspective matrix in Exer-
cise 6?
10. For the eye position e =( 0,1,0), a gaze vector g =( 0,−1,0),a n dav i e w -
up vector t =( 1,1,0), what is the resulting orthonormal uvw basis used
for coordinate rotations?
11. Show, that for a perspective transform, line segments that start in the view
volume do map to line segments in the canonical volume after homogeniza-
tion. Further, show that the relative ordering of points on the two segments
is the same. Hint: Show that the f(t) in Equation (7.8) has the properties
f(0) = 0,f(1) = 1, the derivative of f is positive for all t ∈[0,1],a n dt h e
homogeneous coordinate does not change sign.

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
**关联概念**: [[仿射变换]]
