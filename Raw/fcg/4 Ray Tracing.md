---
title: "4 Ray Tracing"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 4 Ray Tracing

4
Ray T racing
One of the basic tasks of computer graphics is rendering three-dimensional ob-
jects: taking a scene, or model, composed of many geometric objects arranged
in 3D space and producing a 2D image that shows the objects as viewed from
a particular viewpoint. It is the same operation that has been done for centuries
by architects and engineers creating drawings to communicate their designs to
others.
Fundamentally, rendering is a process that takes as its input a set of objects and
produces as its output an array of pixels. One way or another, rendering involves
If the output is a vector
image rather than a raster
image, rendering doesn’t
have to involve pixels, but
we’ll assume raster images
in this book.
considering how each object contributes to each pixel; it can be organized in two
general ways. In object-order rendering, each object is considered in turn, and
for each object all the pixels that it in ﬂuences are found and updated. In image-
order rendering, each pixel is considered in turn, and for each pixel all the objects
that in ﬂuence it are found and the pixel value is computed. Y ou can think of
the difference in terms of the nesting of loops: in image-order rendering the “for
each pixel” loop is on the outside, wher eas in object-order rendering the “for each
object” loop is on the outside.
Image-order and object-order rendering approaches can compute exactly the
same images, but they lend themselves to computing different kinds of effects
and have quite different performance ch aracteristics. We’ll explore the compara-
In a ray tracer, it is easy
to compute accurate shad-
ows and re ﬂections, which
are awkward in the object-
order framework.
tive strengths of the approaches in Chapter 8 after we have discussed them both,
but, broadly speaking, image-order rendering is simpler to get working and more
ﬂexible in the effects that can be produced, and usually (though not always) takes
much more execution time to produce a comparable image.
69

70 4. Ray T racing
Ray
T1
T2
T3
Figure 4.1. The ray is “traced” into the scene and the ﬁrst object hit is the one seen through the
pixel. In this case, the triangle T2 is returned.
Ray tracing is an image-order algorithm for making renderings of 3D scenes,
and we’ll consider it ﬁrst because it’s possible to get a ray tracer working with-
out developing any of the mathematical machinery that’s used for object-order
rendering.
4.1 The Basic Ray-T racing Algorithm
A ray tracer works by computing one pixel at a time, and for each pixel the basic
task is to ﬁnd the object that is seen at that pixel’s position in the image. Each
pixel “looks” in a different direction, and any object that is seen by a pixel must
intersect the viewing ray, a line that emanates from the viewpoint in the direction
that pixel is looking. The particular object we want is the one that intersects
the viewing ray nearest the camera, since it blocks the view of any other objects
behind it. Once that object is found, a shading computation uses the intersection
point, surface normal, and other information (depending on the desired type of
rendering) to determine the color of the pixel. This is shown in Figure 4.1, where
the ray intersects two triangles, but only the ﬁrst triangle hit, T
2, is shaded.
A basic ray tracer therefore has three parts:
1. ray generation , which computes the origin a nd direction of each pixel’s
viewing ray based on the camera geometry;
2. ray intersection,w h i c hﬁnds the closest object intersecting the viewing ray;
3. shading, which computes the pixel color b ased on the results of ray inter-
section.
The structure of the basic ray tracing program is:

4.2. Perspective 71
for each pixel do
compute viewing ray
ﬁnd ﬁrst object hit by ray and its surface normal n
set pixel color to value computed from hit point, light, and n
This chapter covers basic methods for ray generation, ray intersection, and shad-
ing, that are suf ﬁcient for implementing a simple demonstration ray tracer. For a
really useful system, more ef ﬁcient ray intersection techniques from Chapter 12
need to be added, and the real potential of a ray tracer will be seen with the more
advanced shading methods from Chapter 10 and the additional rendering tech-
niques from Chapter 13.
4.2 Perspective
The problem of representing a 3D object or scene with a 2D drawing or paint-
ing was studied by artists hundreds of years before computers. Photographs also
represent 3D scenes with 2D images. Wh ile there are many unconventional ways
to make images, from cubist painting to ﬁsheye lenses (Figure 4.2) to peripheral
cameras, the standard approach for both art and photography, as well as computer
graphics, is linear perspective, in which 3D objects are projected onto an image
plane in such a way that straight lines in the scene become straight lines in the
image.
Figure 4.2. An image taken
with a ﬁs h e y el e n si sn o tal i n -
ear perspective image. Photo
courtesy Philip Greenspun.
The simplest type of projection is parallel projection, in which 3D points are
mapped to 2D by moving them along a projection direction until they hit the
image plane (Figures 4.3–4.4). The view that is produced is determined by the
choice of projection direction and image plane. If the image plane is perpendicular
Axis-aligned
orthographic
Orthographic
Figure 4.3. When projection lines are parallel and perpendicular to the image plane, the resulting
views are called orthographic.

72 4. Ray T racing
Perspective Oblique
Figure 4.4. A parallel projection that has the image pl ane at an angle to the projection direction is
called oblique (right). In perspectiv e projection, the projection lin es all pass through the viewpoint,
rather than being parallel (left). The illustrated p erspective view is non-oblique because a projection
line drawn through the center of the image would be perpendicular to the image plane.
to the view direction, the projection is called orthographic; otherwise it is called
oblique.
Some books reserve “or-
thographic” for projection
directions that are parallel
to the coordinate axes.
Parallel projections are often used for m echanical and architectural drawings
because they keep parallel lines parallel a nd they preserve the size and shape of
planar objects that are parallel to the image plane.
The advantages of parallel projection are also its limitations. In our everyday
experience (and even more so in photographs) objects look smaller as they get
farther away, and as a result parallel lines receding into the distance do not ap-
pear parallel. This is because eyes and cameras don’t collect light from a single
viewing direction; they collect light that passes through a particular viewpoint.
As has been recognized by artists since the Renaissance, we can produce natural-
looking views using perspective projection : we simply project along lines that
pass through a single point, the viewpoint, rather than along parallel lines (Fig-
HorizonVanishing point
Figure 4.5. In three-point perspective, an artist picks “vanishing points” where parallel lines meet.
Parallel horizontal lines will meet at a point on th e horizon. Every set of parallel lines has its own
vanishing points. These rules are followed automatically if we implement perspective based on the
correct geometric principles.

4.3. Computing Viewing Rays 73
ure 4.4). In this way, objects farther from the viewpoint naturally become smaller
when they are projected. A perspective v iew is determined by the choice of view-
point (rather than projection direction) and image plane. As with parallel views,
there are oblique and non-oblique perspective views; the distinction is made based
on the projection direction at the center of the image.
Y ou may have learned about the artistic conventions of three-point perspec-
tive, a system for manually constructing perspective views (Figure 4.5). A sur-
prising fact about perspective is that all the rules of perspective drawing will be
followed automatically if we follow the simple mathematical rule underlying per-
spective: objects are projected directly to ward the eye, and they are drawn where
they meet a view plane in front of the eye.
4.3 Computing Viewing Rays
From the previous section, the basic tools of ray generation are the viewpoint (or
view direction, for parallel views) and the image plane. There are many ways to
work out the details of camera geometry; in this section we explain one based
on orthonormal bases that supports normal and oblique parallel and orthographic
views.
In order to generate rays, we ﬁrst need a mathematical representation for a ray.
A ray is really just an origin point and a propagation direction; a 3D parametric
line is ideal for this. As discussed in Section 2.5.7, the 3D parametric line from
the eye e to a point s on the image plane (Figure 4.6) is given by
e
s – e s
Figure 4.6. The ray from
the eye to a point on the image
plane.
p(t)= e+t(s−e).
This should be interpreted as, “we advance from e along the vector (s−e) a
fractional distance t to ﬁnd the point p.” So given t, we can determine a point p.
The point e is the ray’s origin,a n ds−e is the ray’s direction.
Caution: we are overload-
ing the variable t,w h i c hi s
the ray parameter and also
the v-coordinate of the top
edge of the image.
Note that p(0) = e,a n d p(1) = s, and more generally, if 0 <t 1 <t 2,t h e n
p(t1) is closer to the eye than p(t2). Also, if t< 0,t h e np(t) is “behind” the eye.
These facts will be useful when we search for the closest object hit by the ray that
is not behind the eye.
To compute a viewing ray, we need to know e (which is given) and s. Finding
s may seem dif ﬁcult, but it is actually straightforward if we look at the problem
in the right coordinate system.
All of our ray-generation methods start from an orthonormal coordinate frame
known as the camera frame, which we’ll denote by e, for the eye point, or view-
point, and u, v,a n dw for the three basis vectors, organized with u pointing right-
ward (from the camera’s view), v pointing upward, and w pointing backward, so

74 4. Ray T racing
u
e
w
v
Ray
Screen
Figure 4.7. The sample points on the screen are mapped to a similar array on the 3D window. A
viewing ray is sent to each of these locations.
that {u, v, w} forms a right-handed coordinate system. The most common way
Up
Viewu
v
w
Figure 4.8. The vectors
of the camera frame, together
with the view direction and up
direction. The w vector is op-
posite the view direction, and
the v vector is coplanar with w
and the up vector.
to construct the camera frame is from the viewpoint, which becomes e,t h e view
direction,w h i c hi s−w,a n dt h eup vector, which is used to construct a basis that
has v and w in the plane deﬁned by the view direction and the up direction, using
Since v and w have to be
perpendicular, the up vec-
tor and v are not generally
the same. But setting the up
vector to point straight up-
ward in the scene will ori-
ent the camera in the way
we would think of as “up-
right.”
the process for constructing an orthonormal basis from two vectors described in
Section 2.4.7.
4.3.1 Orthographic Views
For an orthographic view, all the rays will have the direction −w. Even though
a parallel view doesn’t have a viewpoint per se, we can still use the origin of the
It might seem logical that
orthographic viewing rays
should start from in ﬁnitely
far away, but then it would
not be possible to make or-
thographic views of an ob-
ject inside a room, for in-
stance.
camera frame to de ﬁne the plane where the rays start, so that it’s possible for
objects to be behind the camera.
The viewing rays should start on the plane de ﬁned by the point e and the
vectors u and v; the only remaining information required is where on the plane the
image is supposed to be. We’ll de ﬁne the image dimensions with four numbers,
for the four sides of the image: l and r are the positions of the left and right
edges of the image, as measured from e along the u direction; and b andt are the
Many systems assume that
l =– r and b =– t so that a
width and a height suf ﬁce.
positions of the bottom and top edges of the image, as measured from e along the
v direction. Usually l< 0 <r and b< 0 <t . (See Figure 4.9.)
In Section 3.2 we discussed pixel coordinates in an image. To ﬁta ni m a g e
with nx × ny pixels into a rectangle of size (r −l) × (t −b), the pixels are
spaced a distance (r −l)/nx apart horizontally and (t −b)/ny apart vertically,
with a half-pixel space around the edge to center the pixel grid within the image
rectangle. This means that the pixel at position (i,j) in the raster image has the
position
u = l+(r−l)(i+0.5)/nx,
v = b+(t−b)(j +0.5)/ny, (4.1)

4.3. Computing Viewing Rays 75
u
e
v
w
uew
v
Parallel projection
same direction, diﬀerent origins
Perspective projection
same origin, diﬀerent directions
u = r u = r
u = l u = l
d
v = b v = b
v = t v = t
Figure 4.9. Ray generation using the camera frame. Lef t: In an orthographic view, the rays start
at the pixels’ locations on the image plane, and all share the same direction, which is equal to the
view direction. Right: In a perspective view, the rays start at the viewpoint, and each ray’s direction is
deﬁned by the line through the viewpoint, e, and the pixel’s location on the image plane.
where (u,v) are the coordinates of the pixel’s position on the image plane, mea-
sured with respect to the origin e and the basis {u, v}.
With l and r both speciﬁed,
there is redundancy: mov-
ing the viewpoint a bit to
the right and correspond-
ingly decreasing l and r
will not change the view
(and similarly on the v-
axis).
In an orthographic view, we can simply use the pixel’s image-plane position
as the ray’s starting point, and we already know the ray’s direction is the view
direction. The procedure for generatin g orthographic viewing rays is then:
computeu andv using (4.1)
ray.direction←−w
ray.origin← e+u u+v v
It’s very simple to make an oblique parallel view: just allow the image plane
normal w to be speci ﬁed separately from the view direction d. The procedure is
then exactly the same, but with d substituted for −w. Of course w is still used to
construct u and v.
4.3.2 Perspective Views
For a perspective view, all the rays have the same origin, at the viewpoint; it
is the directions that are different for each pixel. The image plane is no longer
positioned at e, but rather some distance d in front of e; this distance is the image
plane distance, often loosely called the focal length, because choosingd plays the
same role as choosing focal length in a real camera. The direction of each ray is
deﬁned by the viewpoint and the position of the pixel on the image plane. This
situation is illustrated in Figure 4.9, and the resulting procedure is similar to the

76 4. Ray T racing
orthographic one:
computeu andv using (4.1)
ray.direction←−d w+u u+v v
ray.origin← e
As with parallel projection, oblique perspective views can be achieved by spec-
ifying the image plane normal separately from the projection direction, then re-
placing−d w withdd in the expression for the ray direction.
4.4 Ray-Object Intersection
Once we’ve generated a ray e+td, we next need to ﬁnd the ﬁrst intersection with
any object where t> 0. In practice, it turns out to be useful to solve a slightly
more general problem: ﬁnd the ﬁrst intersection between the ray and a surface that
occurs at a t in the interval [t0,t1]. The basic ray intersection is the case where
t0 =0 and t1 =+ ∞. We solve this problem for both spheres and triangles. In
the next section, multiple objects are discussed.
4.4.1 Ray-Sphere Intersection
Given a ray p(t)= e+td and an implicit surface f(p)=0 (see Section 2.5.3),
we’d like to know where they intersect. Intersection points occur when points on
the ray satisfy the implicit equation, so the values of t we seek are those that solve
the equation
f(p(t)) = 0 or f(e+td)=0 .
A sphere with center c =( xc,yc,zc) and radius R can be represented by the
implicit equation
(x−xc)2 +(y −yc)2 +(z −zc)2 −R2 =0 .
We can write this same equation in vector form:
(p−c)·(p−c)−R2 =0 .
Any point p that satisﬁes this equation is on the sphere. If we plug points on the
ray p(t)= e + td into this equation, we get an equation in terms of t that is
satisﬁed by the values of t that yield points on the sphere:
(e+td−c)·(e+td−c)−R2 =0 .

4.4. Ray-Object Intersection 77
Rearranging terms yields
(d· d)t2 +2 d·(e−c)t+( e−c)·(e−c)−R2 =0 .
Here, everything is known except the parameter t, so this is a classic quadratic
equation in t, meaning it has the form
At2 +Bt +C =0 .
The solution to this equation is discussed in Section 2.2. The term under the
square root sign in the quadratic solution, B2 −4AC, is called the discriminant
and tells us how many real solutions there are. If the discriminant is negative,
its square root is imaginary and the line and sphere do not intersect. If the dis-
criminant is positive, there are two solutions: one solution where the ray enters
the sphere and one where it leaves. If th e discriminant is zero, the ray grazes
the sphere, touching it at exactly one point. Plugging in the actual terms for the
sphere and canceling a factor of two, we get
t =
−d·(e−c)±
√
(d·(e−c))2 −(d· d)(( e−c)·(e−c)−R2)
(d· d) .
In an actual implementation, you should ﬁrst check the value of the discriminant
before computing other terms. If the sphere is used only as a bounding object for
more complex objects, then we need only d etermine whether we hit it; checking
the discriminant sufﬁces.
As discussed in Section 2.5.4, the normal vector at point p is given by the
gradient n =2 (p−c). The unit normal is (p−c)/R.
4.4.2 Ray-T riangle Intersection
There are many algorithms for computing ray-triangle intersections. We will
present the form that uses barycentric coordinates for the parametric plane con-
taining the triangle, because it requires n o long-term storage other than the ver-
tices of the triangle (Snyder & Barr, 1987).
To intersect a ray with a parametric surface, we set up a system of equations
where the Cartesian coordinates all match:
x
e +txd = f(u,v)
ye +tyd = g(u,v)
ze +tzd = h(u,v)
⎫
⎪⎬
⎪⎭
or, e+td = f(u,v).

78 4. Ray T racing
Here, we have three equations and three unknowns ( t, u,a n dv), so we can solve
numerically for the unknowns. If we are lucky, we can solve for them analytically.
In the case where the parametric surface is a parametric plane, the parametric
equation can be written in vector form as discussed in Section 2.7.2. If the vertices
of the triangle are a, b,a n dc, then the intersection will occur when
e+td = a+β(b−a)+ γ(c−a), (4.2)
for some t,β,a n dγ. The intersection p will be at e+td as shown in Figure 4.10.
Again, from Section 2.7.2, we know the intersection is inside the triangle if and
only if β>0,γ>0,a n dβ+γ<1. Otherwise, the ray has hit the plane outside
e
d
p
c
b
a
Figure 4.10. The ray hits
the plane containing the trian-
gle at point p.
the triangle, so it misses the triangle. If there are no solutions, either the triangle
is degenerate or the ray is parallel to the plane containing the triangle.
To solve for t, β,a n dγin Equation (4.2), we expand it from its vector form
into the three equations for the three coordinates:
xe +txd = xa +β(xb −xa)+ γ(xc −xa),
ye +tyd = ya +β(yb −ya)+ γ(yc −ya),
ze +tzd = za +β(zb −za)+ γ(zc −za).
This can be rewritten as a standard linear system:
⎡
⎣
xa −xb xa −xc xd
ya −yb ya −yc yd
za −zb za −zc zd
⎤
⎦
⎡
⎣
β
γ
t
⎤
⎦=
⎡
⎣
x
a −xe
ya −ye
za −ze
⎤
⎦.
The fastest classic method to solve this 3×3 linear system is Cramer’s rule.T h i s
gives us the solutions
β=
⏐⏐⏐⏐
⏐
⏐
x
a −xe xa −xc xd
ya −ye ya −yc yd
za −ze za −zc zd
⏐
⏐⏐⏐
⏐
⏐
|A| ,
γ=
⏐
⏐
⏐
⏐⏐⏐
x
a −xb xa −xe xd
ya −yb ya −ye yd
za −zb za −ze zd
⏐
⏐
⏐
⏐⏐⏐
|A| ,
t =
⏐⏐⏐
⏐
⏐
⏐
x
a −xb xa −xc xa −xe
ya −yb ya −yc ya −ye
za −zb za −zc za −ze
⏐⏐⏐
⏐
⏐
⏐
|A| ,

4.4. Ray-Object Intersection 79
where the matrix A is
A =
⎡
⎣
xa −xb xa −xc xd
ya −yb ya −yc yd
za −zb za −zc zd
⎤
⎦,
and|A| denotes the determinant of A.T h e3×3 determinants have common sub-
terms that can be exploited. Looking at the linear systems with dummy variables
⎡
⎣
adg
beh
cfi
⎤
⎦
⎡
⎣
β
γ
t
⎤
⎦=
⎡
⎣
j
k
l
⎤
⎦,
Cramer’s rule gives us
β= j(ei−hf)+ k(gf −di)+ l(dh−eg)
M ,
γ= i(ak −jb)+ h(jc −al)+ g(bl−kc)
M ,
t = −f(ak −jb)+ e(jc −al)+ d(bl−kc)
M ,
where
M = a(ei−hf)+ b(gf −di)+ c(dh−eg).
We can reduce the number of operations by reusing numbers such as
“ei-minus-hf.”
The algorithm for the ray-triangle intersection for which we need the linear so-
lution can have some conditions for early termination. Thus, the function should
look something like:
boolean raytri (ray r, vector3 a, vector3 b, vector3 c,
interval[t
0,t1])
computet
if(t<t 0) or (t>t 1) then
return false
computeγ
if(γ<0) or (γ>1) then
return false
computeβ
if(β<0) or (β>1−γ) then
return false
return true

80 4. Ray T racing
4.4.3 Ray-Polygon Intersection
Given a planar polygon with m vertices p1 through pm and surface normal n,
we ﬁrst compute the intersection points between the ray e + td and the plane
containing the polygon with implicit equation
(p−p1)· n =0 .
We do this by setting p = e+td and solving for t to get
t = (p1 −e)· n
d· n .
This allows us to compute p.I f p is inside the polygon, then the ray hits it;
otherwise, it does not.
We can answer the question of whether p is inside the polygon by projecting
the point and polygon vertices to the xy plane and answering it there. The easiest
w a yt od ot h i si st os e n da n y2 Dr a yo u tf r o mp and to count the number of inter-
sections between that ray and the boundary of the polygon (Sutherland, Sproull,
& Schumacker, 1974; Glassner, 1989). If the number of intersections is odd, then
the point is inside the polygon; otherwis e it is not. This is true because a ray that
goes in must go out, thus creating a pair of intersections. Only a ray that starts
inside will not create such a pair. To make computation simple, the 2D ray may
as well propagate along the x-axis:
[
x
y
]
=
[
x
p
yp
]
+s
[
1
0
]
.
It is straightforward to compute the intersection of that ray with the edges such as
(x1,y1,x2,y2) for s ∈(0,∞).
A problem arises, however, for polygons whose projection into the xy plane
is a line. To get around this, we can choose among the xy, yz,o r zx planes for
whichever is best. If we implement our points to allow an indexing operation,
e.g., p(0) =x
p then this can be accomplished as follows:
if(abs(zn)> abs(xn)) and (abs(zn)> abs(yn)) then
index0 = 0
index1 = 1
else if (abs(y
n) > abs(xn)) then
index0 = 0
index1 = 2
else
index0 = 1
index1 = 2
Now, all computations can use p(index0) rather than x
p, and so on.

4.5. Shading 81
Another approach to polygons, one that is often used in practice, is to replace
them by several triangles.
4.4.4 Intersecting a Group of Objects
Of course, most interesting scenes consist of more than one object, and when we
intersect a ray with the scene we must ﬁnd only the closest intersection to the
camera along the ray. A simple way to implement this is to think of a group of
objects as itself being another type of object. To intersect a ray with a group, you
simply intersect the ray with the objects in the group and return the intersection
with the smallest t value. The following code tests for hits in the interval t ∈
[t
0,t 1]:
Figure 4.11. As i m p l e
scene rendered with only ray
generation and surface inter-
section, but no shading; each
pixel is just set to a ﬁxed color
depending on which object it
hit.
hit = false
for each object o in the group do
if (o is hit at ray parameter t and t ∈[t0,t 1]) then
hit = true
hitobject = o
t
1 = t
return hit
4.5 Shading
Once the visible surface for a pixel is known, the pixel value is computed by eval-
uating a shading model . How this is done depends entirely on the application—
methods range from very simple heuristics to elaborate numerical computations.
In this chapter we describe the two mos t basic shading models; more advanced
models are discussed in Chapter 10.
Most shading models, one way or another, are designed to capture the process
of light re ﬂection, whereby surfaces are illuminated by light sources and re ﬂect
part of the light to the camera. Simple shading models are de ﬁned in terms of
illumination from a point light source. The important variables in light re ﬂection
are the light direction l, which is a unit vector pointing toward the light source;
the view direction v, which is a unit vector pointing toward the eye or camera; the
surface normal n, which is a unit vector perpendicular to the surface at the point
where re ﬂection is taking place; and the char acteristics of the surface—color,
shininess, or other properties depending on the particular model.

82 4. Ray T racing
4.5.1 Lambertian Shading
Illumination from real
point sources falls off as
distance squared, but that is
often more trouble than it’s
worth in a simple renderer.
The simplest shading model is based on an observation made by Lambert in the
18th century: the amount of energy from a light source that falls on an area of
surface depends on the angle of the surface to the light. A surface facing directly
toward the light receives maximum illumination; a surface tangent to the light
direction (or facing away from the light) receives no illumination; and in between
the illumination is proportional to the cosine of the angle θbetween the surface
normal and the light source (Figure 4.12). This leads to the Lambertian shading
model:
L = k
d I max(0, n· l)
where L is the pixel color; kd is the diffuse coefﬁcient, or the surface color; and
I is the intensity of the light source. Because n and l are unit vectors, we can
l n
θ v
Figure 4.12. Geometry for
Lambertian shading.
use n · l as a convenient shorthand (both on paper and in code) for cosθ.T h i s
equation (as with the other shading equations in this section) applies separately to
the three color channels, so the red component of the pixel value is the product of
the red diffuse component, the red light source intensity, and the dot product; the
same holds for green and blue.
When in doubt, make light
sources neutral in color,
with equal red, green, and
blue intensities.
The vector l is computed by subtracting the intersection point of the ray and
surface from the light source position. Don’t forget that v, l,a n d n all must be
unit vectors; failing to normalize these vectors is a very common error in shading
computations.
4.5.2 Blinn-Phong Shading
Lambertian shading is view independent : the color of a surface does not depend
on the direction from which you look. Many real surfaces show some degree
of shininess, producing highlights, or specular reﬂections , that appear to move
around as the viewpoint changes. Lambertian shading doesn’t produce any high-
lights and leads to a very matte, chalky appearance, and many shading models
add a specular component to Lambertian shading; the Lambertian part is then the
diffuse component.
A very simple and widely used model for specular highlights was proposed
by Phong (Phong, 1975) and later updated by Blinn (J. F. Blinn, 1976) to the form
most commonly used today. The idea is to produce reﬂection that is at its brightest
when v and l are symmetrically positioned across the surface normal, which is
when mirror reﬂection would occur; the re ﬂection then decreases smoothly as the
vectors move away from a mirror conﬁ guration.

4.5. Shading 83
Figure 4.13. As i m p l e
scene rendered with diffuse
shading from a single light
source.
Figure 4.14. As i m p l e
scene rendered with diffuse
shading and shadows (Sec-
tion 4.7) from three light
sources.
Figure 4.15. As i m p l e
scene rendered with diffuse
shading (blue sphere), Blinn-
Phong shading (green sphere),
and shadows from three light
sources.
We can tell how close we are to a mirror con ﬁguration by comparing the
half vector h (the bisector of the angle between v and l) to the surface normal
(Figure 4.16). If the half vector is near the surface normal, the specular component
l n h
vα
Figure 4.16. Geometry for
Blinn-Phong shading.
should be bright; if it is far away it should be dim. This result is achieved by
computing the dot product between h and n (remember they are unit vectors, so
Typical values of p:
10—“eggshell”;
100—mildly shiny;
1000—really glossy;
10,000—nearlymirror-like.
n· h reaches its maximum of 1 when the vector s are equal), then taking the result
to a power p> 1 to make it decrease faster. The power, or Phong exponent ,
controls the apparent shininess of the surface. The half vector itself is easy to
compute: since v and l are the same length, their sum is a vector that bisects the
angle between them, which only needs to be normalized to produce h.
Putting this all together, the Blinn- Phong shading model is as follows:
When in doubt, make the
specular color gray, with
equal red, green, and blue
values.
h = v + l
∥v + l∥,
L = kd I max(0, n· l)+ ks I max(0, n· h)p,
where ks is the specular coefﬁcient, or the specular color, of the surface.
4.5.3 Ambient Shading
Surfaces that receive no illumination at all will be rendered as completely black,
which is often not desirable. A crude but useful heuristic to avoid black shadows
 In the real world, surfaces
that are not illuminated by
light sources are illumi-
nated by indirect re ﬂec-
tions from other surfaces.
is to add a constant component to the shading model, one whose contribution
to the pixel color depends only on the object hit, with no dependence on the
surface geometry at all. This is known as ambient shading—it is as if surfaces
were illuminated by “ambient” light that comes equally from everywhere. For

84 4. Ray T racing
convenience in tuning the parameters, ambient shading is usually expressed as
the product of a surface color with an ambient light color, so that ambient shading
can be tuned for surfaces individually or fo r all surfaces together. Together with
the rest of the Blinn-Phong model, ambient shading completes the full version of
a simple and useful shading model:
L = k
a Ia +kd Imax(0, n· l)+ ks Imax(0, n· h)n, (4.3)
where ka is the surface’s ambient coef ﬁcient, or “ambient color,” and Ia is the
When in doubt set the am-
bient color to be the same
as the diffuse color.
ambient light intensity.
4.5.4 Multiple Point Lights
A very useful property of light is superposition—the effect caused by more than
one light source is simply the sum of the effects of the light sources individually.
For this reason, our simple shading model can easily be extended to handle N
light sources:
L = ka Ia +
N∑
i=1
[kd Ii max(0, n· li)+ ks Ii max(0, n· hi)p], (4.4)
where Ii, li,a n d hi are the intensity, direction, and half vector of the ith light
source.
4.6 A Ray-T racing Program
We now know how to generate a viewing ray for a given pixel, how to ﬁnd the
closest intersection with an object, and how to shade the resulting intersection.
These are all the parts required for a program that produces shaded images with
hidden surfaces removed.
for each pixel do
compute viewing ray
if(ray hits an object with t ∈[0,∞)) then
Compute n
Evaluate shading model and set pixel to that color
else
set pixel color to background color

4.6. A Ray-T racing Program 85
H e r e t h e s t a t e m e n t“ i f r a y h i t s a n o b j e c t...” c a n b e i m p l e m e n t e du s i n g t h e a l g o -
rithm of Section 4.4.4.
In an actual implementation, the surface intersection routine needs to some-
how return either a reference to the obj ect that is hit, or at least its normal vec-
tor and shading-relevant material properties. This is often done by passing a
record/structure with such information. In an object-oriented implementation, it
is a good idea to have a class called something like surface with derived classes
triangle, sphere, group, etc. Anything that a ray can intersect would be under that
class. The ray-tracing program would then have one reference to a “surface” for
the whole model, and new types of objects and ef ﬁciency structures can be added
transparently.
4.6.1 Object-Oriented Design for a Ray-T racing Program
As mentioned earlier, the key class hierarchy in a ray tracer are the geometric
objects that make up the model. These should be subclasses of some geometric
object class, and they should support a hit function (Kirk & Arvo, 1988). To
avoid confusion from use of the word “object,” surface is the class name often
used. With such a class, you can create a ray tracer that has a general interface
that assumes little about modeling primiti ves and debug it using only spheres. An
important point is that anything that can be “hit” by a ray should be part of this
class hierarchy, e.g., even a collection of surfaces should be considered a subclass
of the surface class. This includes ef ﬁciency structures, such as bounding volume
hierarchies; they can be hit by a ray, so they are in the class.
For example, the “abstract” or “base” class would specify the hit function as
well as a bounding box function that will prove useful later:
class surface
virtual bool hit(ray e+td, real t
0, real t1, hit-record rec)
virtual box bounding-box()
Here (t0,t1) is the interval on the ray where hits will be returned, and rec is a
record that is passed by reference; it contains data such as the t at the intersection
when hit returns true. The type box is a 3D “bounding box,” that is two points that
deﬁne an axis-aligned box that encloses the surface. For example, for a sphere,
the function would be implemented by
box sphere::bounding-box()
vector3 min = center −vector3(radius,radius,radius)
vector3 max = center + vector3(radius,radius,radius)
return box(min, max)

86 4. Ray T racing
Another class that is useful is material. This allows you to abstract the material
behavior and later add materials transparently. A simple way to link objects and
materials is to add a pointer to a material in the surface class, although more
programmable behavior might be desirable. A big question is what to do with
textures; are they part of the material class or do they live outside of the material
class? This will be discussed more in Chapter 11.
4.7 Shadows
Once you have a basic ray tracing program, shadows can be added very easily.
Recall from Section 4.5 that li ght comes from some direction l. If we imagine
ourselves at a point p on a surface being shaded, the point is in shadow if we
“look” in direction l and see an object. If there are no objects, then the light is not
blocked.
This is shown in Figure 4.17, where the ray p + tl does not hit any objects
and is thus not in shadow. The point q is in shadow because the ray q+tl does
hit an object. The vector l is the same for both points because the light is “far”
away. This assumption will later be relaxed. The rays that determine in or out of
shadow are called shadow rays to distinguish them from viewing rays.
II
p q
Figure 4.17. The point p is
not in shadow, while the point
q is in shadow.
To get the algorithm for shading, we add an if statement to determine whether
the point is in shadow. In a naive implementation, the shadow ray will check
for t ∈[0,∞), but because of numerical impreci sion, this can result in an inter-
section with the surface on which p lies. Instead, the usual adjustment to avoid
that problem is to test for t ∈[ϵ,∞) where ϵ is some small positive constant
(Figure 4.18).
I
p p + εl
Figure 4.18. By test-
ing in the interval starting at
ϵ, we avoid numerical impre-
cision causing the ray to hit the
surface p is on.
If we implement shadow rays for Phong lighting with Equation 4.3 then we
have the following:
function raycolor( ray e+td, real t0, real t1 )
hit-record rec, srec
if(scene→hit(e+td, t0,t1,r e c)) then
p = e+( rec.t) d
colorc = rec.ka Ia
if(not scene→hit(p+sl,ϵ,∞,s r e c)) then
vector3 h = normalized(normalized(l)+ normalized(−d))
c = c+ rec.kd I max(0, rec.n· l)+( rec.ks)I(rec.n· h)rec.p
returnc
else
return background-color

4.8. Ideal Specular Reﬂection 87
Note that the ambient color is added whether p is in shadow or not. If there are
multiple light sources, we can send a shadow ray before evaluating the shading
model for each light. The code above assumes that d and l are not necessarily unit
vectors. This is crucial for d, in particular, if we wish to cleanly add instancing
later (see Section 13.2).
4.8 Ideal Specular Reﬂection
r
n
θ θ
d
Figure 4.19. When look-
ing into a perfect mirror, the
viewer looking in direction d
will see whatever the viewer
“below” the surface would see
in direction r.
It is straightforward to add ideal specular reﬂection, or mirror reﬂection,t oar a y -
tracing program. The key observation is shown in Figure 4.19 where a viewer
looking from direction e sees what is in direction r as seen from the surface. The
vector r is found using a variant of the Phong lighting re ﬂection Equation (10.6).
There are sign changes because the vector d points toward the surface in this case,
so,
r = d−2(d· n)n. (4.5)
In the real world, some energy is lost when the light re ﬂects from the surface, and
this loss can be different for different colors. For example, gold re ﬂects yellow
more efﬁciently than blue, so it shifts the colors of the objects it re ﬂects. This can
be implemented by adding a recursive call in raycolor:
Figure 4.20. As i m -
ple scene rendered with dif-
fuse and Blinn-Phong shad-
ing, shadows from three light
sources, and specular re ﬂec-
tion from the ﬂoor.
colorc = c+kmraycolor(p+sr,ϵ, ∞)
where km (for “mirror re ﬂection”) is the specular RGB color. We need to make
sure we test for s ∈[ϵ,∞) for the same reason as we did with shadow rays; we
don’t want the re ﬂection ray to hit the object that generates it.
The problem with the recursive call above is that it may never terminate. For
example, if a ray starts inside a room, it will bounce forever. This can be ﬁxed by
adding a maximum recursion depth. The code will be more ef ﬁcient if a reﬂection
ray is generated only if km is not zero (black).
4.9 Historical Notes
Ray tracing was developed early in the history of computer graphics (Appel,
1968) but was not used much until suf ﬁcient compute power was available (Kay
& Greenberg, 1979; Whitted, 1980).
Ray tracing has a lower asymptotic time complexity than basic object-order
rendering (Snyder & Barr, 1987; Muuss, 1995; S. Parker et al., 1999; Wald,
Slusallek, Benthin, & W agner, 2001). Although it was traditionally thought of

88 4. Ray T racing
as an of ﬂine method, real-time ray tracing implementations are becoming more
and more common.
Frequently Asked Questions
•Why is there no perspective matrix in ray tracing?
The perspective matrix in a z-buffer exists so that we can turn the perspective pro-
jection into a parallel projection. This is not needed in ray tracing, because it is
easy to do the perspective projection implicitly by fanning the rays out from the
eye.
•Can ray tracing be made interactive?
For suf ﬁciently small models and images, any modern PC is suf ﬁciently pow-
erful for ray tracing to be interactive. In practice, multiple CPUs with a shared
frame buffer are required for a full-screen implementation. Computer power is in-
creasing much faster than screen resolution, and it is just a matter of time before
conventional PCs can ray trace comp lex scenes at screen resolution.
•Is ray tracing useful in a hardware graphics program?
Ray tracing is frequently used for picking. When the user clicks the mouse on a
pixel in a 3D graphics program, the program needs to determine which object is
visible within that pixel. Ray traci ng is an ideal way to determine that.
Exercises
1. What are the ray parameters of the intersection points between ray(1,1,1)+
t(−1,−1,−1) and the sphere centered at the origin with radius 1? Note:
this is a good debugging case.
2. What are the barycentric coordinates and ray parameter where the ray
(1,1,1)+ t(−1,−1,−1) hits the triangle with vertices (1,0,0), (0,1,0),
and(0,0,1)? Note: this is a good debugging case.
3. Do a back of the envelope computation of the approximate time complexity
of ray tracing on “nice” (non-adversarial) models. Split your analysis into
the cases of preprocessing and computing the image, so that you can predict
the behavior of ray tracing multiple frames for a static model.

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
**关联概念**: [[光线追踪]] · [[BVH加速结构]]
**pbrt 对应**: pbrt Ch1 Introduction (光线追踪核心概念)
