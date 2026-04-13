---
title: "8 The Graphics Pipeline"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 8 The Graphics Pipeline

8
The Graphics Pipeline
The previous several chapters have established the mathematical scaffolding we
need to look at the second major approach to rendering: drawing objects one by
one onto the screen, or object-order rendering. Unlike in ray tracing, where we
consider each pixel in turn and ﬁnd the objects that in ﬂuence its color, we’ll now
instead consider each geometric object in turn and ﬁnd the pixels that it could have
an effect on. The process of ﬁnding all the pixels in an image that are occupied by
Any graphics system has
one or more types of “prim-
itive object” that it can han-
dle directly, and more com-
plex objects are converted
into these “primitives.” Tri-
angles are the most often
used primitive.
a geometric primitive is called rasterization, so object-order rendering can also
be called rendering by rasterization. The sequence of operations that is required,
Rasterization-based sys-
tems are also called
scanline renderers.
starting with objects and ending by updating pixels in the image, is known as the
graphics pipeline.
Object-order rendering has enjoyed great success because of its ef ﬁciency.
For large scenes, management of data access patterns is crucial to performance,
and making a single pass over the scene visiting each bit of geometry once has
signiﬁcant advantages over repeatedly search ing the scene to retrieve the objects
required to shade each pixel.
The title of this chapter suggests that there is only one way to do object-
order rendering. Of course this isn’t true—two quite different examples of graph-
ics pipelines with very different goals are the hardware pipelines used to sup-
port interactive rendering via APIs like OpenGL and Direct3D and the software
pipelines used in ﬁlm production, supporting APIs like RenderMan. Hardware
pipelines must run fast enough to react in real time for games, visualizations,
and user interfaces. Production pipelin es must render the highest quality anima-
tion and visual effects possible and scale to enormous scenes, but may take much
159

160 8. The Graphics Pipeline
more time to do so. Despite the different design decisions resulting from these
divergent goals, a remarkable amount is shared among most, if not all, pipelines,
and this chapter attempts to focus on these common fundamentals, erring on the
side of following the hardware pipelines more closely.
The work that needs to be done in object-order rendering can be organized
into the task of rasterization itself, the operations that are done to geometry be-
fore rasterization, and the operations that are done to pixels after rasterization.
The most common geometric operation is applying matrix transformations, as
discussed in the previous two chapters, to map the points that de ﬁne the geometry
from object space to screen space, so that the input to the rasterizer is expressed
in pixel coordinates, or screen space. The most common pixelwise operation is
hidden surface removal which arranges for surfaces closer to the viewer to appear
in front of surfaces farther from the view er. Many other operations also can be in-
cluded at each stage, thereby achieving a wid e range of different rendering effects
using the same general process.
For the purposes of this chapter, we’ll discuss the graphics pipeline in terms of
four stages (Figure 8.1). Geometric objects are fed into the pipeline from an inter-
Application
Command Stream
Vertex Processing
Transformed Geometry
Rasterization
Fragments
Fragment Processing
Blending
Framebuffer Image
Display
Figure 8.1. The stages of a
graphics pipeline.
active application or from a scene description ﬁle, and they are always described
by sets of vertices. The vertices are operated on in the vertex-processing stage,
then the primitives using those vertices are sent to the rasterization stage .T h e
rasterizer breaks each primitive into a number of fragments, one for each pixel
covered by the primitive. The fragments are processed in the fragment processing
stage, and then the various fragments corresponding to each pixel are combined
in the fragment blending stage.
We’ll begin by discussing rasterization, then illustrate the purpose of the geo-
metric and pixel-wise stages by a series of examples.
8.1 Rasterization
Rasterization is the central operation in object-order graphics, and the rasterizer
is central to any graphics pipeline. For each primitive that comes in, the rasterizer
has two jobs: it enumerates the pixels that are covered by the primitive and it
interpolates values, called attributes, across the primitive—the purpose for these
attributes will be clear with later examples. The output of the rasterizer is a set of
fragments, one for each pixel covered by the primitive. Each fragment “lives” at
a particular pixel and carries its own set of attribute values.
In this chapter, we will present rasterization with a view toward using it to
render three-dimensional scenes. The same rasterization methods are used to draw

8.1. Rasterization 161
lines and shapes in 2D as well—although it is becoming more and more common
to use the 3D graphics system “under the covers” to do all 2D drawing.
8.1.1 Line Drawing
Most graphics packages contain a line drawing command that takes two endpoints
in screen coodinates (see Figure 3.10) and draws a line between them. For exam-
ple, the call for endpoints (1,1) and (3,2) would turn on pixels (1,1) and (3,2) and
ﬁll in one pixel between them. For general screen coordinate endpoints (x
0,y0)
and(x1,y1), the routine should draw some “reasonable” set of pixels that approx-
imates a line between them. Drawing such lines is based on line equations, and
Even though we often use
integer-valued endpoints
for examples, it’s impor-
tant to properly support
arbitrary endpoints.
we have two types of equations to choose from: implicit and parametric. This
section describes the approach using implicit lines.
Line Drawing Using Implicit Line Equations
The most common way to draw lines using implicit equations is the midpoint al-
gorithm (Pitteway (1967); van Aken and Novak (1985)). The midpoint algorithm
ends up drawing the same lines as the Bresenham algorithm (Bresenham, 1965)
but it is somewhat more straightforward.
The ﬁrst thing to do is ﬁnd the implicit equation for the line as discussed in
Section 2.5.2:
f(x,y) ≡(y
0 −y1)x+(x1 −x0)y +x0y1 −x1y0 =0 . (8.1)
We assume that x0 ≤x1. If that is not true, we swap the points so that it is true.
The slope m of the line is given by
m = y1 −y0
x1 −x0
.
The following discussion assumes m ∈(0,1]. Analogous discussions can be
derived for m ∈(−∞,−1],m ∈(−1,0],a n dm ∈(1,∞). The four cases cover
all possibilities.
For the casem ∈(0,1], there is more “run” than “rise,” i.e., the line is moving
faster in x than in y. I fw eh a v ea nA P Iw h e r et h ey-axis points downward, we
might have a concern about whether this makes the process harder, but, in fact, we
can ignore that detail. We can ignore the geometric notions of “up” and “down,”
because the algebra is exactly the same for the two cases. Cautious readers can
conﬁrm that the resulting algorithm works for the y-axis downward case. The key
assumption of the midpoint algorithm is that we draw the thinnest line possible

162 8. The Graphics Pipeline
that has no gaps. A diagonal connection between two pixels is not considered a
gap.
As the line progresses from the left endpoint to the right, there are only two
possibilities: draw a pixel at the same height as the pixel drawn to its left, or draw
a pixel one higher. There will always be exactly one pixel in each column of pixels
between the endpoints. Zero would imply a gap, and two would be too thick a line.
There may be two pixels in the same row for the case we are considering; the line
is more horizontal than vertical so sometimes it will go right, and sometimes up.
This concept is shown in Figure 8.2, where three “reasonable” lines are shown,
each advancing more in the horizontal dir ection than in the vertical direction.
Figure 8.2. Three “reason-
able” lines that go seven pix-
els horizontally and three pix-
els vertically.
The midpoint algorithm for m ∈(0,1] ﬁrst establishes the leftmost pixel and
the column number (x-value) of the rightmost pixel and then loops horizontally
establishing the row (y-value) of each pixel. The basic form of the algorithm is:
y = y0
forx = x0 tox1 do
draw(x,y)
if(some condition) then
y = y +1
Note that x and y are integers. In words this says, “keep drawing pixels from left
to right and sometimes move upward in the y-direction while doing so.” The key
is to establish ef ﬁcient ways to make the decision in the if statement.
An effective way to make the choice is to look at the midpoint of the line
between the two potential pixel centers. More speci ﬁcally, the pixel just drawn
is pixel (x,y) whose center in real screen coordinates is at (x,y). The candidate
pixels to be drawn to the right are pixels (x +1 ,y) and (x +1 ,y +1 ).T h e
midpoint between the centers of the two candidate pixels is (x +1 ,y +0 .5).I f
the line passes below this midpoint we draw the bottom pixel, and otherwise we
draw the top pixel (Figure 8.3).
To decide whether the line passes above or below(x+1,y +0.5),w ee v a l u a t e
f(x,y +0 .5) in Equation (8.1). Recall from Section 2.5.1 that f(x,y)=0 for
points (x,y) on the line, f(x,y) > 0 for points on one side of the line, and
f(x,y) < 0 for points on the other side of the line. Because −f(x,
 y)=0 and
Figure 8.3. Top: the line
goes above the midpoint so the
top pixel is drawn. Bottom:
the line goes below the mid-
point so the bottom pixel is
drawn.
f(x,y)=0 are both perfectly good equations for the line, it is not immediately
clear whether f(x,y) being positive indicates that (x,y) is above the line, or
whether it is below. However, we can ﬁgure it out; the key term in Equation (8.1)
is the y term (x1 −x0)y. Note that (x1 −x0) is de ﬁnitely positive because
x1 >x 0. This means that as y increases, the term(x1−x0)y gets larger (i.e., more
positive or less negative). Thus, the case f(x,+∞) is de ﬁnitely positive, and
deﬁnitely above the line, implying points above the line are all positive. Another

8.1. Rasterization 163
way to look at it is that the y component of the gradient vector is positive. So
above the line, where y can increase arbitrarily, f(x,y) must be positive. This
means we can make our code more speci ﬁcb y ﬁlling in the if statement:
iff(x+1,y +0.5) < 0 then
y = y +1
The above code will work nicely for lines of the appropriate slope (i.e., between
zero and one). The reader can work out the other three cases which differ only in
small details.
If greater ef ﬁciency is desired, using an incremental method can help. An
incremental method tries to make a loop more ef ﬁcient by reusing computation
from the previous step. In the midpoint algorithm as presented, the main compu-
tation is the evaluation of f(x +1 ,y +0 .5). Note that inside the loop, after the
ﬁrst iteration, either we already evaluated f(x−1,y +0.5) or f(x−1,y −0.5)
(Figure 8.4). Note also this relationship:
Figure 8.4. When using
the decision point shown be-
tween the two orange pixels,
we just drew the blue pixel, so
we evaluated f at one of the
two left points shown.
f(x+1,y)= f(x,y)+( y0 −y1)
f(x+1,y +1)= f(x,y)+( y0 −y1)+( x1 −x0).
This allows us to write an incremental version of the code:
y = y0
d = f(x0 +1,y0 +0.5)
forx = x0 tox1 do
draw(x,y)
ifd< 0 then
y = y +1
d = d+(x1 −x0)+( y0 −y1)
else
d = d+(y0 −y1)
This code should run faster since it has little extra setup cost compared to the
non-incremental version (that is not always true for incremental algorithms), but
it may accumulate more numeric error because the evaluation of f(x,y +0 .5)
may be composed of many adds for long lines. However, given that lines are
rarely longer than a few thousand pixels, such an error is unlikely to be critical.
Slightly longer setup cost, but faster loop execution, can be achieved by storing
(x
1 −x0)+( y0 −y1) and(y0 −y1) as variables. We might hope a good compiler
would do that for us, but if the code is critical, it would be wise to examine the
results of compilation to make sure.

164 8. The Graphics Pipeline
8.1.2 T riangle Rasterization
We often want to draw a 2D triangle with 2D points p0 =( x0,y0), p1 =( x1,y1),
and p2 =( x2,y2) in screen coordinates. This is similar to the line drawing
problem, but it has some of its own subtleties. As with line drawing, we may
wish to interpolate color or other properties from values at the vertices. This is
straightforward if we have the barycentric coordinates (Section 2.7). For example,
if the vertices have colors c0, c1,a n d c2, the color at a point in the triangle with
barycentric coordinates (α,β,γ) is
c = αc0 +βc1 +γc2.
This type of interpolation of color is known in graphics as Gouraud interpolation
after its inventor (Gouraud, 1971).
Another subtlety of rasterizing triangles is that we are usually rasterizing tri-
angles that share vertices and edges. Th is means we would like to rasterize ad-
jacent triangles so there are no holes . We could do this by using the midpoint
algorithm to draw the outline of each triangle and then ﬁll in the interior pixels.
This would mean adjacent triangles bot h draw the same pixels along each edge.
If the adjacent triangles have different c olors, the image will depend on the order
in which the two triangles are drawn. T he most common way to rasterize trian-
gles that avoids the order problem and e liminates holes is to use the convention
that pixels are drawn if and only if their centers are inside the triangle, i.e., the
barycentric coordinates of the pixel center are all in the interval (0,1).T h i sr a i s e s
the issue of what to do if the center is exactly on the edge of the triangle. There
are several ways to handle this as will be discussed later in this section. The key
observation is that barycentric coordinates allow us to decide whether to draw a
pixel and what color that pixel should be if we are interpolating colors from the
vertices. So our problem of rasterizing the triangle boils down to ef ﬁciently ﬁnd-
ing the barycentric coordinates of pixel centers (Pineda, 1988). The brute-force
rasterization algorithm is:
for all x do
for all y do
compute(α,β,γ) for(x,y)
if(α∈[0,1] and β∈[0,1] and γ∈[0,1]) then
c = αc
0 +βc1 +γc2
drawpixel(x,y) with color c
The rest of the algorithm limits the outer loops to a smaller set of candidate pixels
and makes the barycen tric computation efﬁcient.

8.1. Rasterization 165
We can add a simple ef ﬁciency by ﬁnding the bounding rectangle of the
three vertices and only looping over this rectangle for candidate pixels to draw.
We can compute barycentric coordinates using Equation (2.32). This yields the
algorithm:
x
min = ﬂoor(xi)
xmax = ceiling(xi)
ymin = ﬂoor(yi)
ymax = ceiling(yi)
fory = ymin toymax do
forx = xmin to xmax do
α= f12(x,y)/f12(x0,y0)
β= f20(x,y)/f20(x1,y1)
γ= f01(x,y)/f01(x2,y2)
if(α>0 andβ>0 andγ>0) then
c = αc0 +βc1 +γc2
drawpixel(x,y) with color c
Herefij is the line given by Equation (8.1) with the appropriate vertices:
f01(x,y)=( y0 −y1)x+(x1 −x0)y +x0y1 −x1y0,
f12(x,y)=( y1 −y2)x+(x2 −x1)y +x1y2 −x2y1,
f20(x,y)=( y2 −y0)x+(x0 −x2)y +x2y0 −x0y2.
Note that we have exchanged the test α∈(0,1) with α> 0 etc., because if
all of α, β, γare positive, then we know they are all less than one because α+
β+ γ=1 . We could also compute only two of the three barycentric variables
Figure 8.5. A colored triangle with barycentric inter polation. Note that the changes in color
components are linear in each row and column as well as along each edge. In fact it is constant along
every line, such as the diagonals, as well.

166 8. The Graphics Pipeline
and get the third from that relation, but it is not clear that this saves computation
once the algorithm is made incremental, which is possible as in the line drawing
algorithms; each of the computations of α, β,a n d γdoes an evaluation of the
form f(x,y)= Ax+By +C. In the inner loop, only x changes, and it changes
by one. Note that f(x+1,y)= f(x,y)+ A. This is the basis of the incremental
algorithm. In the outer loop, the evaluation changes for f(x,y) to f(x,y +1 ),
so a similar ef ﬁciency can be achieved. Because α, β,a n dγchange by constant
increments in the loop, so does the color c. So this can be made incremental as
well. For example, the red value for pixel (x +1 ,y) differs from the red value
for pixel (x,y) by a constant amount that can be precomputed. An example of a
triangle with color interpolation is shown in Figure 8.5.
Dealing with Pixels on T riangle Edges
We have still not discussed what to do for pixels whose centers are exactly on
the edge of a triangle. If a pixel is exactly on the edge of a triangle, then it is
also on the edge of the adjacent triangle i f there is one. There is no obvious way
to award the pixel to one triangle or the other. The worst decision would be to
not draw the pixel because a hole would res ult between the two triangles. Better,
but still not good, would be to have both tria ngles draw the pixel. If the triangles
are transparent, this will result in a double-coloring. We would really like to
award the pixel to exactly one of the triangles, and we would like this process
to be simple; which triangle is chosen does not matter as long as the choice is
well deﬁned.
off-screen point
a
b
Figure 8.6. The off-screen
point will be on one side of
the triangle edge or the other.
Exactly one of the non-shared
vertices a and b will be on the
same side.
One approach is to note that any off-screen point is de ﬁnitely on exactly one
side of the shared edge and that is the edge we will draw. For two non-overlapping
triangles, the vertices not on the edge are on opposite sides of the edge from each
other. Exactly one of these vertices will be on the same side of the edge as the
off-screen point (Figure 8.6). This is the basis of the test. The test if numbers p
and q have the same sign can be implemented as the test pq > 0,w h i c hi sv e r y
efﬁcient in most environments.
Note that the test is not perfect becau se the line through the edge may also
go through the off-screen point, but we have at least greatly reduced the number
of problematic cases. Which off-screen point is used is arbitrary, and (x,y)=
(−1,−1) is as good a choice as any. We will need to add a check for the case of a
point exactly on an edge. We would like this check not to be reached for common
cases, which are the completely inside or outside tests. This suggests:
x
min = ﬂoor(xi)
xmax = ceiling(xi)
ymin = ﬂoor(yi)

8.1. Rasterization 167
ymax = ceiling(yi)
fα= f12(x0,y0)
fβ= f20(x1,y1)
fγ= f01(x2,y2)
fory = ymin toymax do
forx = xmin to xmax do
α= f12(x,y)/fα
β= f20(x,y)/fβ
γ= f01(x,y)/fγ
if(α≥0 andβ≥0 andγ≥0) then
if(α>0 or fαf12(−1,−1) > 0) and
(β>0 or fβf20(−1,−1)> 0) and
(γ>0 orfγf01(−1,−1) > 0) then
c = αc0 +βc1 +γc2
drawpixel(x,y) with color c
We might expect that the above code would work to eliminate holes and double-
draws only if we use exactly the same line equation for both triangles. In fact,
the line equation is the same only if the two shared vertices have the same order
in the draw call for each triangle. Otherwise the equation might ﬂip in sign. This
could be a problem depending on whethe r the compiler changes the order of op-
erations. So if a robust implementation is needed, the details of the compiler and
arithmetic unit may need to be examined. The ﬁrst four lines in the pseudocode
above must be coded carefully to handle cases where the edge exactly hits the
pixel center.
In addition to being amenable to an incremental implementation, there are
several potential early exit points. For example, if αis negative, there is no need
to computeβorγ. While this may well result in a speed improvement, pro ﬁling is
always a good idea; the extra branches could reduce pipelining or concurrency and
might slow down the code. So as always, test any attractive-looking optimizations
if the code is a critical section.
Another detail of the above code is that the divisions could be divisions by
zero for degenerate triangles, i.e., if f
γ=0 . Either the ﬂoating point error condi-
tions should be accounted for properl y, or another test will be needed.
8.1.3 Clipping
Simply transforming primitives into s creen space and rasterizing them does not
quite work by itself. This is because prim itives that are outside the view volume—
particularly, primitives that are behind the eye—can end up being rasterized, lead-
ing to incorrect results. For instance, consider the triangle shown in Figure 8.7.

168 8. The Graphics Pipeline
z = fz = n
z'
z
n
f
n + f
eye
eye
gaze
direction
gaze
direction
a
b
c
a'
b'
c'
Figure 8.7. The depth z is transformed to the depth z′ by the perspective transform. Note that when
z moves from positive to negative, z′ switches from negative to positive. Thus vertices behind the eye
are moved in front of the eye beyond z′ = n + f. This will lead to wrong results, which is why the
triangle is ﬁrst clipped to ensure all vertices are in front of the eye.
Two vertices are in the view volume, but the third is behind the eye. The projec-
tion transformation maps this vertex to a nonsensical location behind the far plane,
and if this is allowed to happen the triangle will be rasterized incorrectly. For this
reason, rasterization has to be preceded by a clipping operation that removes parts
of primitives that could extend behind the eye.

8.1. Rasterization 169
Clipping is a common operation in graphics, needed whenever one geometric
entity “cuts” another. For example, if you clip a triangle against the plane x =0 ,
the plane cuts the triangle into two parts if the signs of the x-coordinates of the
vertices are not all the same. In most applications of clipping, the portion of the
triangle on the “wrong” side of the plane is discarded. This operation for a single
plane is shown in Figure 8.8.
Clip
Clipping
plane
Figure 8.8. A polygon
is clipped against a clipping
plane. The portion “inside”
the plane is retained.
In clipping to prepare for rasterization, the “wrong” side is the side outside
the view volume. It is always safe to clip away all geometry outside the view
volume—that is, clipping against all six faces of the volume—but many systems
manage to get away with only clipping against the near plane.
This section discusses the basic implementation of a clipping module. Those
interested in implementing an industrial-speed clipper should see the book by
Blinn mentioned in the notes at the end of this chapter.
The two most common approaches for implementing clipping are
1. in world coordinates using the six planes that bound the truncated viewing
pyramid,
2. in the 4D transformed space before the homogeneous divide.
Either possibility can be effectively implemented (J. Blinn, 1996) using the fol-
lowing approach for each triangle:
for each of six planes do
if(triangle entirely outside of plane) then
break(triangle is not visible)
else if triangle spans plane then
clip triangle
if(quadrilateral is left) then
break into two triangles
8.1.4 Clipping Before the T ransform (Option 1)
Option 1 has a straightforward implementation. The only question is, “What are
the six plane equations?” Because these equations are the same for all triangles
rendered in the single image, we do not need to compute them very ef ﬁciently.
For this reason, we can just invert the transform shown in Figure 5.11 and apply

170 8. The Graphics Pipeline
it to the eight vertices of the transformed view volume:
(x,y,z )= (l,b,n )
(r,b,n )
(l,t,n )
(r,t,n )
(l,b,f )
(r,b,f )
(l,t,f )
(r,t,f ).
The plane equations can be inferred from h ere. Alternatively, we can use vector
geometry to get the planes directly from the viewing parameters.
8.1.5 Clipping in Homogeneous Coordinates (Option 2)
Surprisingly, the option usually implemented is that of clipping in homogeneous
coordinates before the divide. Here the view volume is 4D, and it is bounded by
3D volumes (hyperplanes). These are
−x+lw =0 ,
x−rw =0 ,
−y +bw =0 ,
y −tw =0 ,
−z +nw =0 ,
z −fw =0 .
These planes are quite simple, so the ef ﬁciency is better than for Option 1. They
still can be improved by transforming the view volume [l,r] × [b,t] × [f,n] to
[0,1]
3. It turns out that the clipping of the triangles is not much more complicated
than in 3D.
8.1.6 Clipping against a Plane
No matter which option we choose, we must clip against a plane. Recall from
Section 2.5.5 that the implicit equation for a plane through point q with normal
n is
f(p)= n·(p−q)=0 .

8.2. Operations Before and After Rasterization 171
This is often written
f(p)= n· p+D =0 . (8.2)
Interestingly, this equation not only describes a 3D plane, but it also describes a
line in 2D and the volume analog of a plane in 4D. All of these entities are usually
called planes in their appropriate dimension.
If we have a line segment between points a and b, we can “clip” it against
a plane using the techniques for cutting the edges of 3D triangles in BSP tree
programs described in Section 12.4.3. Here, the points a and b are tested to
determine whether they are on opposite sides of the plane f(p)=0 by checking
whether f(a) and f(b) have different signs. Typically f(p) < 0 is deﬁned to be
“inside” the plane, and f(p) > 0 is “outside” the plane. If the plane does split the
line, then we can solve for the intersection point by substituting the equation for
the parametric line,
p = a+t(b−a),
into the f(p)=0 plane of Equation (8.2). This yields
n·(a+t(b−a))+ D =0 .
Solving for t gives
t = n· a+D
n·(a−b).
We can then ﬁnd the intersection point and “shorten” the line.
To clip a triangle, we again can follow Section 12.4.3 to produce one or two
triangles.
8.2 Operations Before and After Rasterization
Before a primitive can be rasterized, the vertices that de ﬁne it must be in screen
coordinates, and the colors or other attributes that are supposed to be interpolated
across the primitive must be known. Preparing this data is the job of the vertex-
processing stage of the pipeline. In this stage, incoming vertices are transformed
by the modeling, viewing, and projection transformations, mapping them from
their original coordinates into screen space (where, recall, position is measured
in terms of pixels). At the same time, other information, such as colors, surface
normals, or texture coordinates, is transformed as needed; we’ll discuss these
additional attributes in the examples below.
After rasterization, further processing is done to compute a color and depth
for each fragment. This processing can be as simple as just passing through an in-
terpolated color and using the depth computed by the rasterizer; or it can involve

172 8. The Graphics Pipeline
complex shading operations. Finally, the blending phase combines the fragments
generated by the (possibly several) prim itives that overlapped each pixel to com-
pute the ﬁnal color. The most common blending approach is to choose the color
of the fragment with the smallest depth (closest to the eye).
The purposes of the different stages are best illustrated by examples.
8.2.1 Simple 2D Drawing
The simplest possible pipeline does nothing in the vertex or fragment stages, and
in the blending stage the color of each fragment simply overwrites the value of the
previous one. The application supplies primitives directly in pixel coordinates,
and the rasterizer does all the work. This basic arrangement is the essence of
many simple, older APIs for drawing user interfaces, plots, graphs, and other 2D
content. Solid color shapes can be drawn by specifying the same color for all
vertices of each primitive, and our model pipeline also supports smoothly varying
color using interpolation.
8.2.2 A Minimal 3D Pipeline
To draw objects in 3D, the only change needed to the 2D drawing pipeline is a
single matrix transformation: the vertex-processing stage multiplies the incoming
vertex positions by the product of the modeling, camera, projection, and viewport
matrices, resulting in screen-space triangles that are then drawn in the same way
as if they’d been speci ﬁed directly in 2D.
Figure 8.9. Two occlusion
cycles, which cannot be drawn
in back-to-front order.
One problem with the minimal 3D pipeline is that in order to get occlusion
relationships correct—to get nearer objects in front of farther away objects—
primitives must be drawn in back-to-front order. This is known as the painter’s
algorithm for hidden surface removal, by analogy to painting the background of
a painting ﬁrst, then painting the foreground over it. The painter’s algorithm is
a perfectly valid way to remove hidden surfaces, but it has several drawbacks.
It cannot handle triangles that intersect one another, because there is no correct
order in which to draw them. Similarly, several triangles, even if they don’t inter-
sect, can still be arranged in an occlusion cycle , as shown in Figure 8.9, another
case in which the back-to-front order does not exist. And most importantly, sort-
ing the primitives by depth is slow, especially for large scenes, and disturbs the
efﬁcient ﬂow of data that makes object-order rendering so fast. Figure 8.10 shows
the result of this process when the objects are not sorted by depth.
Figure 8.10. The result of
drawing two spheres of iden-
tical size using the minimal
pipeline. The sphere that ap-
pears smaller is farther away
b u ti sd r a w nl a s t ,s oi ti n -
correctly overwrites the nearer
one.

8.2. Operations Before and After Rasterization 173
8.2.3 Using a z-Buffer for Hidden Surfaces
In practice, the painter’s algorithm is rar ely used; instead a simple and effective
hidden surface removal algorithm known as the z-buffer algorithm is used. The
method is very simple: at each pixel we keep track of the distance to the closest
surface that has been drawn so far, and we throw away fragments that are farther
away than that distance. The closest distan ce is stored by allocating an extra value
for each pixel, in addition to the red, green, and blue color values, which is known
as the depth, or z-value. The depth buffer, or z-buffer, is the name for the grid of
depth values.
The z-buffer algorithm is implemented in the fragment blending phase, by
comparing the depth of each fragment with the current value stored in the z-buffer.
If the fragment’s depth is closer, both its color and its depth value overwrite the
values currently in the color and depth buffers. If the fragment’s depth is farther
Of course there can be ties
in the depth test, in which
case the order may well
matter.
away, it is discarded. To ensure that theﬁrst fragment will pass the depth test, thez
buffer is initialized to the maximum depth (the depth of the far plane). Irrespective
of the order in which surfaces are drawn, the same fragment will win the depth
test, and the image will be the same.
The z-buffer algorithm requires each fragment to carry a depth. This is done
simply by interpolating thez-coordinate as a vertex attribute, in the same way that
color or other attribut es are interpolated.
The z-buffer is such a simple and practical way to deal with hidden surfaces in
object-order rendering that it is by far the dominant approach. It is much simpler
than geometric methods that cut surfaces into pieces that can be sorted by depth,
because it avoids solving any problems that don’t need to be solved. The depth
Figure 8.11. The result of
drawing the same two spheres
using the z-buffer.
order only needs to be determined at the locations of the pixels, and that is all
that the z-buffer does. It is universally supported by hardware graphics pipelines
and is also the most commonly used method for software pipelines. Figure 8.11
shows an example result.
Precision Issues
In practice, the z-values stored in the buffer are nonnegative integers. This is
preferable to true ﬂoats because the fast memory needed for the z-buffer is some-
what expensive and is worth keeping to a minimum.
The use of integers can cause some precision problems. If we use an integer
range havingB values{0,1,...,B −1}, we can map 0 to the near clipping plane
z = n andB−1 to the far clipping plane z = f . Note, that for this discussion, we
assumez,n,a n df are positive. This will result in the same results as the negative
case, but the details of the argument are easier to follow. We send each z-value to

174 8. The Graphics Pipeline
1
1
1
1
1
1
1
1
1
1
1
1
1
1
1
1
∞
∞
∞
7
∞
∞
∞
7
∞
∞
∞
∞
∞
∞
∞
∞
∞
∞
∞
∞
∞
∞
∞
∞
∞
∞
5
5
∞
∞
5
5
∞
3
3
3
∞
3
3
3
∞
3
3
3
1
1
1
1
∞
3
3
3
1
1
1
1
∞
3
3
3
∞
3
3
3
Figure 8.12. A z-buffer rasterizing two triangles in each of two possible orders. The ﬁrst triangle
is fully rasterized. The second triangle has every pix el computed, but for three of the pixels the depth-
contest is lost, and those pixels are not drawn. The ﬁnal image is the same regardless.
a “bucket” with depth Δz =( f −n)/B. We would not use the integer z-buffer if
memory were not a premium, so it is useful to make B as small as possible.
If we allocate b bits to store the z-value, then B =2 b. We need enough bits
to make sure any triangle in front of another triangle will have its depth mapped
to distinct depth bins.
For example, if you are rendering a scene where triangles have a separation of
at least one meter, then Δz< 1 should yield images without artifacts. There are
t w ow a y st om a k eΔz smaller: move n andf closer together or increase b.I f b is
ﬁx e d ,a si tm a yb ei nA P I so ro np a r t i c u lar hardware platforms, adjusting n andf
is the only option.
The precision of z-buffers must be handled with great care when perspective
images are created. The value Δz above is used after the perspective divide.
Recall from Section 7.3 that the result of the perspective divide is
z = n+f −fn
zw
.

8.2. Operations Before and After Rasterization 175
The actual bin depth is related to zw , the world depth, rather than z, the post-
perspective divide depth. We can approximate the bin size by differentiating both
sides:
Δz ≈fnΔzw
z2w
.
Bin sizes vary in depth. The bin size in world space is
Δzw ≈z2
wΔz
fn .
Note that the quantity Δz is as previously discussed. The biggest bin will be for
z′ = f ,w h e r e
Δzmax
w ≈fΔz
n .
Note that choosing n =0 , a natural choice if we don’t want to lose objects right
in front of the eye, will result in an in ﬁnitely large bin—a very bad condition. To
makeΔzmax
w as small as possible, we want to minimize f and maximize n. Thus,
it is always important to choose n andf carefully.
8.2.4 Per-vertex Shading
So far the application sending triangles in to the pipeline is res ponsible for setting
the color; the rasterizer just interpolates the colors and they are written directly
into the output image. For some applications this is suf ﬁcient, but in many cases
we want 3D objects to be drawn with shading, using the same illumination equa-
tions that we used for image-order renderi ng in Chapter 4. Recall that these equa-
tions require a light direction, an eye direction, and a surface normal to compute
the color of a surface.
One way to handle shading computations is to perform them in the vertex
stage. The application provides normal vectors at the vertices, and the positions
and colors of the lights are provided separately (they don’t vary across the surface,
so they don’t need to be speci ﬁed for each vertex). For each vertex, the direction
to the viewer and the direction to each light are computed based on the positions
of the camera, the lights, and the vertex. The desired shading equation is evaluated
t oc o m p u t eac o l o r ,w h i c hi st h e np a s s e dt othe rasterizer as the vertex color. Per-
vertex shading is sometimes called Gouraud shading.
One decision to be made is the coordinate system in which shading com-
putations are done. World space or eye space are good choices. It is impor-
tant to choose a coordinate system that is orthonormal when viewed in world
space, because shading equations depend on angles between vectors, which are

176 8. The Graphics Pipeline
not preserved by operations like nonuniform scale that are often used in the mod-
eling transformation, or perspective projection, often used in the projection to the
canonical view volume. Shading in eye space has the advantage that we don’t
need to keep track of the camera positi on, because the camera is always at the
origin in eye space, in perspective proj ection, or the view direction is always +z
in orthographic projection.
Per-vertex shading has the disadvantage that it cannot produce any details in
the shading that are smaller than the primitives used to draw the surface, because
it only computes shading once for each vertex and never in between vertices.
Figure 8.13. Two spheres
drawn using per-vertex
(Gouraud) shading. Because
the triangles are large, inter-
polation artifacts are visible.
For instance, in a room with a ﬂoor that is drawn using two large triangles and
illuminated by a light source in the middle of the room, shading will be evaluated
only at the corners of the room, and the interpolated value will likely be much too
dark in the center. Also, curved surfaces that are shaded with specular highlights
must be drawn using primitives small enough that the highlights can be resolved.
Figure 8.13 shows our two spheres drawn with per-vertex shading.
8.2.5 Per-fragment Shading
Per-fragment shading is
sometimes called Phong
shading, which is confus-
ing because the same name
is attached to the Phong
illumination model.
To avoid the interpolation artifacts asso ciated with per-vertex shading, we can
avoid interpolating colors by performing the shading computations after the in-
terpolation, in the fragment stage. In per-fragment shading, the same shading
equations are evaluated, but they are eva luated for each fragment using interpo-
lated vectors, rather than for each vertex using the vectors from the application.
In per-fragment shading, the geometric information needed for shading is
passed through the rasterizer as attributes, so the vertex stage must coordinate
with the fragment stage to prepare the data appropriately. One approach is to in-
terpolate the eye-space surface normal and the eye-space vertex position, which
then can be used just as they would in per-vertex shading.
Figure 8.14 shows our two spheres drawn with per-fragment shading.
Figure 8.14. Two spheres
drawn using per-fragment
shading. Because the trian-
gles are large, interpolation
artifacts are visible.
8.2.6 T exture Mapping
T extures(discussed in Chapter 11) are images that are used to add extra detail to
the shading of surfaces that would oth erwise look too homogeneous and arti ﬁcial.
The idea is simple: each time shading is computed, we read one of the values
used in the shading computation—the diffuse color, for instance—from a texture
instead of using the attribute values that are attached to the geometry being ren-
dered. This operation is known as a texture lookup: the shading code speci ﬁes a
texture coordinate, a point in the domain of the texture, and the texture-mapping

8.3. Simple Antialiasing 177
system ﬁnds the value at that point in the texture image and returns it. The texture
value is then used in the shading computation.
The most common way to de ﬁne texture coordinates is simply to make the
texture coordinate another vertex attribute. Each primitive then knows where it
lives in the texture.
8.2.7 Shading Frequency
The decision about where to place shading computations depends on how fast the
color changes—the scale of the details being computed. Shading with large-scale
features, such as diffuse shading on curved surfaces, can be evaluated fairly infre-
quently and then interpolated: it can be computed with a low shading frequency.
Shading that produces small-scale features, such as sharp highlights or detailed
textures, needs to be evaluated at a high shading frequency. For details that need
to look sharp and crisp in the image, the shading frequency needs to be at least
one shading sample per pixel.
So large-scale effects can safely be computed in the vertex stage, even when
the vertices de ﬁning the primitives are many pixels apart. Effects that require a
high shading frequency can also be computed at the vertex stage, as long as the
vertices are close together in the image; a lternatively, they can be computed at the
fragment stage when primitives are larger than a pixel.
For example, a hardware pipeline as used in a computer game, generally us-
ing primitives that cover several pixels to ensure high ef ﬁciency, normally does
most shading computations per fragment. On the other hand, the PhotoRealistic
RenderMan system does all shading computations per vertex, after ﬁrst subdivid-
ing, or dicing, all surfaces into small quadrilaterals called micropolygons that are
about the size of pixels. Since the primitives are small, per-vertex shading in this
system achieves a high shading frequency that is suitable for detailed shading.
8.3 Simple Antialiasing
Just as with ray tracing, rasterization will produce jagged lines and triangle edges
if we make an all-or-nothing determination of whether each pixel is inside the
primitive or not. In fact, the set of fragments generated by the simple triangle
rasterization algorithms described in this chapter, sometimes called standard or
aliased rasterization, is exactly the same as the set of pixels that would be mapped
to that triangle by a ray tracer that sends one ray through the center of each pixel.

178 8. The Graphics Pipeline
Figure 8.15. An antialiased and a jaggy line viewed at close range so individual pixels are visible.
Also as in ray tracing, the solution is to allow pixels to be partly covered by a
primitive (Crow, 1978). In practice, this form of blurring helps visual quality,
especially in animations. This is shown as the top line of Figure 8.15.
There are a number of different approaches to antialiasing in rasterization
applications. Just as with a ray tracer, we can produce an antialiased image by
setting each pixel value to the average color of the image over the square area
belonging to the pixel, an approach known as box ﬁltering. T h i sm e a n sw eh a v e
There are better ﬁlters than
the box, but a box ﬁlter will
sufﬁce for all but the most
demanding applications.
to think of all drawable entities as having well-deﬁned areas. For example, the line
in Figure 8.15 can be thought of as approximating a one-pixel-wide rectangle.
The easiest way to implement box- ﬁlter antialiasing is by supersampling:c r e -
ate images at very high resolutions and then downsample. For example, if our
goal is a 256 × 256 pixel image of a line with width 1.2 pixels, we could rasterize
a rectangle version of the line with width 4.8 pixels on a 1024 × 1024 screen,
a n dt h e na v e r a g e4× 4 groups of pixels to get the colors for each of the 256 ×
256 pixels in the “shrunken” image. This is an approximation of the actual box-
ﬁltered image, but works well when objects are not extremely small relative to the
distance between pixels.
Supersampling is quite expensive, however. Because the very sharp edges
that cause aliasing are normally caused b y the edges of primitives, rather than
sudden variations in shading within a primitive, a widely used optimization is
to sample visibility at a higher rate tha n shading. If information about coverage
and depth is stored for several point s within each pixel, very good antialiasing
can be achieved even if only one color is computed. In systems like RenderMan
that use per-vertex shading, this is achieved by rasterizing at high resolution: it is
inexpensive to do so because shading is s imply interpolated to produce colors for
the many fragments, or visibility samples. In systems with per-fragment shading,
such as hardware pipelines, multisample antialiasing is achieved by storing for
each fragment a single color plus a coverage mask and a set of depth values.

8.4. Culling Primitives for Efﬁciency 179
8.4 Culling Primitives for Efﬁciency
The strength of object-order rendering, that it requires a single pass over all the
geometry in the scene, is also a weakness for complex scenes. For instance, in a
model of an entire city, only a few buildings are likely to be visible at any given
time. A correct image can be obtained by drawing all the primitives in the scene,
but a great deal of effort will be wasted processing geometry that is behind the
visible buildings, or behind the viewer, and therefore doesn’t contribute to the
ﬁnal image.
Identifying and throwing away invisible geometry to save the time that would
be spent processing it is known as culling. Three commonly implemented culling
strategies (often used in tandem) are

view volume culling —the removal of geometry that is outside the view
volume;
 occlusion culling —the removal of geometry that may be within the view
volume but is obscured, or occluded, by other geometry closer to the
camera;
 backface culling—the removal of primitives facing away from the camera.
We will brieﬂy discuss view volume culling and backface culling, but culling
in high performance systems is a complex topic; see (Akenine-M¨oller et al., 2008)
for a complete discussion and for in formation about occlusion culling.
8.4.1 View Volume Culling
When an entire primitive lies outside the view volume, it can be culled, since it
will produce no fragments when rasterized. If we can cull many primitives with a
quick test, we may be able to speed up drawing signi ﬁcantly. On the other hand,
testing primitives individually to decide exactly which ones need to be drawn may
cost more than just letting the rasterizer eliminate them.
View volume culling, also known as view frustum culling , is especially help-
ful when many triangles are grouped into an object with an associated bounding
volume. If the bounding volume lies outside the view volume, then so do all the
triangles that make up the object. For example, if we have 1000 triangles bounded
by a single sphere with center c and radius r, we can check whether the sphere
lies outside the clipping plane,
(p−a)· n =0 ,

180 8. The Graphics Pipeline
where a is a point on the plane, and p is a variable. This is equivalent to checking
whether the signed distance from the center of the sphere c to the plane is greater
than +r. This amounts to the check that
(c−a)· n
∥n∥ >r .
Note that the sphere may overlap the plane even in a case where all the triangles
do lie outside the plane. Thus, this is a conservative test. How conservative the
test is depends on how well the sphere bounds the object.
The same idea can be applied hierarc hically if the scene is organized in one
of the spatial data structures described in Chapter 12.
8.4.2 Backface Culling
When polygonal models are closed, i.e., they bound a closed space with no holes,
then they are often assumed to have outward facing normal vectors as discussed
in Chapter 10. For such models, the polygons that face away from the eye are
certain to be overdrawn by polygons that face the eye. Thus, those polygons can
be culled before the pipeline even starts. The test for this condition is the same
one used for silhouette drawing given in Section 10.3.1.
Frequently Asked Questions
•I’ve often seen clipping discussed at length, and it is a much more in-
volved process than that described in this chapter. What is going on here?
The clipping described in this chapter works, but lacks optimizations that an
industrial-strength clipper would have. These optimizations are discussed in de-
tail in Blinn’s deﬁ nitive work listed in the chapter notes.
•How are polygons that are not triangles rasterized?
These can either be done directly scan-line by scan-line, or they can be broken
down into triangles. The latter appears to be the more popular technique.
•Is it always better to antialias?
No. Some images look crisper without antialiasing. Many programs use unan-
tialiased “screen fonts” becau se they are easier to read.

8.4. Culling Primitives for Efﬁciency 181
•The documentation for my API talks about “scene graphs” and “matrix
stacks. ” Are these part of the graphics pipeline?
The graphics pipeline is certainly designed with these in mind, and whether we
deﬁne them as part of the pipeline is a matter of taste. This book delays their
discussion until Chapter 12.
•Is a uniform distance z-buffer better than the standard one that includes
perspective matrix nonlinearities?
It depends. One “feature” of the nonlinearities is that the z-buffer has more res-
olution near the eye and less in the distan ce. If a level-of-detail system is used,
then geometry in the distance is coarser and the “unfairness” of the z-buffer can
be a good thing.
•Is a software z-buffer ever useful?
Y es. Most of the movies that use 3D computer graphics have used a variant of the
software z-buffer developed by Pixar (Cook, Carpenter, & Catmull, 1987).
Notes
A wonderful book about designing a graphics pipeline is Jim Blinn’s Corner:
A Trip Down the Graphics Pipeline (J. Blinn, 1996). Many nice details of the
pipeline and culling are in 3D Game Engine Design (Eberly, 2000) and Real-Time
Rendering (Akenine-M¨oller et al., 2008).
Exercises
1. Suppose that in the perspective transform we have n =1 andf =2 . Under
what circumstances will we have a “reversal” where a vertex before and
after the perspective transform ﬂips from in front of to behind the eye or
vice versa?
2. Is there any reason not to clip in x and y after the perspective divide (see
Figure 11.2, stage 3)?
3. Derive the incremental form of the midpoint line-drawing algorithm with
colors at endpoints for 0 <m ≤1.

182 8. The Graphics Pipeline
4. Modify the triangle-drawing algorithm so that it will draw exactly one pixel
for points on a triangle edge which goes through (x,y)=( −1,−1).
5. Suppose you are designing an integer z-buffer for ﬂight simulation where
all of the objects are at least one meter thick, are never closer to the viewer
than 4 meters, and may be as far away as 100 km. How many bits are
needed in the z-buffer to ensure there are no visibility errors? Suppose that
visibility errors only matter near the viewer, i.e., for distances less than 100
meters. How many bits are needed in that case?

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
