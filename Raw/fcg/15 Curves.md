---
title: "15 Curves"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 15 Curves

15
Michael Gleicher
Curves
15.1 Curves
Intuitively, think of a curve as something you can draw with a pen. The curve is
the set of points that the pen traces over an interval of time. While we usually
think of a pen writing on paper (e.g., a curve that is in a 2D space), the pen could
move in 3D to generate a space curve , or you could imagine the pen moving in
some other kind of space.
Mathematically, deﬁnitions of curve can be seen in at least two ways:
1. the continuous image of some interval in an n-dimensional space;
2. a continuous map from a one-dimensional space to ann-dimensional space.
Both of these de ﬁnitions start with the idea of an interval range (the time over
which the pen traces the curve). However, there is a signi ﬁcant difference: in
the ﬁrst deﬁnition, the curve is the set of point s the pen traces (the image), while
in the second de ﬁnition, the curve is the mapping between time and that set of
points. For this chapter, we use the ﬁrst deﬁnition.
A curve is an in ﬁnitely large set of points. The points in a curve have the
property that any point has two neighbors, except for a small number of points
that have one neighbor (these are the endpoints). Some curves have no endpoints,
either because they are in ﬁnite (like a line) or they are closed (loop around and
connect to themselves).
359

360 15. Curves
Because the “pen” of the curve is thin (inﬁ nitesimally), it is dif ﬁcult to create
ﬁlled regions. While space-ﬁ lling curves are possible (by having them fold over
themselves inﬁnitely many times), we do not consider such mathematical oddities
here. Generally, we think of curves as the outlines of things, not the “insides.”
The problem that we need to address is how to specify a curve—to give a
name or representation to a curve so that we can represent it on a computer. For
some curves, the problem of naming them is easy since they have known shapes:
line segments, circles, elliptical arcs, etc. A general curve that does not have a
“named” shape is sometimes called a free-form curve. Because a free-form curve
can take on just about any shape, they are much harder to specify.
There are three main ways to sp ecify curves mathematically:
1.
Implicit curve representations deﬁne the set of points on a curve by giving a
procedure that can test to see if a point in on the curve. Usually, an implicit
curve representation is de ﬁned by an implicit function of the form
f(x,y)=0 ,
so that the curve is the set of points for which this equation is true. Note that
the implicit function f is a scalar function (it returns a single real number).
2. Parametric curve representations provide a mapping from a free parameter
to the set of points on the curve. That is, this free parameter provides an
index to the points on the curve. The parametric form of a curve is a func-
tion that assigns positions to values of the free parameter. Intuitively, if you
think of a curve as something you can draw with a pen on a piece of paper,
the free parameter is time, ranging over the interval from the time that we
began drawing the curve to the time that we ﬁnish. The parametric function
of this curve tells us where the pen is at any instant in time:
(x,y)= f(t).
Note that the parametric function is a v ector-valued function. This example
is a 2D curve, so the output of the function is a 2-vector; in 3D it would be
a 3-vector.
3.
Generative or procedural curve representations provide procedures that can
generate the points on the curve that do not fall into the ﬁrst two categories.
Examples of generative curve descriptions include subdivision schemes and
fractals.
Remember that a curve is a set of points. These representations give us ways
to specify those sets. Any curve has many possible representations. For this

15.1. Curves 361
reason, mathematicians typically are careful to distinguish between a curve and
its representations. In computer graphics we are often sloppy, since we usually
only refer to the representation, not the actual curve itself. So when someone says
“an implicit curve,” they are either referring to the curve that is represented by
some implicit function or to the implicit function that is one of the representations
of some curve. Such distinctions are not usually important, unless we need to
consider different representations of the same curve. We will consider different
curve representations in this chapter, so we will be more careful. When we use a
term like “polynomial curve,” we will mean the curve that can be represented by
the polynomial.
By the de ﬁnition given at the beginning of the chapter, for something to be a
curve it must have a parametric represent ation. However, many curves have other
representations. For example, a circle in 2D with its center at the origin and radius
equal to 1 can be written in implicit form as
f(x,y)= x
2 +y2 −1=0 ,
or in parametric form as
(x,y)= f(t)=( c o st,sint),t ∈[0,2π).
The parametric form need not be the most convenient representation for a given
curve. In fact, it is possible to have curves with simple implicit or generative
representations for which it is dif ﬁcult to ﬁnd a parametric representation.
Different representations of curves ha ve advantages and disadvantages. For
example, parametric curves are much easier to draw, because we can sample the
free parameter. Generally, parametric forms are the most commonly used in com-
puter graphics since they are easier to work with. Our focus will be on parametric
representations of curves.
15.1.1 Parameterizations and Reparameterizations
A parametric curve refers to the curve that is given by a speciﬁ c parametric func-
tion over some particular interval. To be more precise, a parametric curve has a
given function that is a mapping from an interval of the parameters. It is often
convenient to have the parameter run ove r the unit interval from 0 to 1. When the
free parameter varies over the unit interval, we often denote the parameter as u.
If we view the parametric curve to be a line drawn with a pen, we can consider
u =0 as the time when the pen is ﬁrst set down on the paper and the unit of time
to be the amount of time it takes to draw the curve ( u =1 is the end of the curve).

362 15. Curves
The curve can be speciﬁed by a function that maps time (in these unit coordinates)
to positions. Basically, the speci ﬁcation of the curve is a function that can answer
the question, “Where is the pen at time u?”
If we are given a function f(t) that speci ﬁes a curve over interval [a,b],w e
can easily de ﬁne a new function f2(u) that speciﬁes the same curve over the unit
interval. We can ﬁrst deﬁne
g(u)= a+(b−a)u,
and then
f2(u)= f(g(u)).
The two functions, f and f2 both represent the same curve; however, they provide
different parameterizations of the curve. The process of creating a new param-
eterization for an existing curve is called reparameterization, and the mapping
from old parameters to the new ones ( g, in this example) is called the reparame-
terization function.
If we have de ﬁned a curve by some parameterization, inﬁ nitely many others
exist (because we can always reparameterize). Being able to have multiple param-
eterizations of a curve is useful, because it allows us to create parameterizations
that are convenient. However, it can also be problematic, because it makes it
difﬁcult to compare two functions to see if they represent the same curve.
The essence of this problem is more general: the existence of the free param-
eter (or the element of time) adds an invisible, potentially unknown element to
our representation of the curves. When we look at the curve after it is drawn,
we don’t necessarily know the timing. The pen might have moved at a constant
speed over the entire time interval, or it might have started slowly and sped up.
For example, while u =0 .5 is halfway through the parameter space, it may not
be halfway along the curve if the motion of the pen starts slowly and speeds up at
the end. Consider the following representations of a very simple curve:
(x,y)= f(u)= ( u,u),
(x,y)= f(u)= ( u
2,u2),
(x,y)= f(u)= ( u5,u5).
All three functions represent the same curve on the unit interval; however when
u is not 0 or 1, f(u) refers to a different point depe nding on the representation of
the curve.
If we are given a parameterization of a curve, we can use it directly as our
speciﬁcation of the curve, or we can develop a more convenient parameterization.
Usually, the natural parameterization is created in a way that is convenient (or

15.1. Curves 363
natural) for specifying the curve, so we don’t have to know about how the speed
changes along the curve.
If we know that the pen moves at a constant velocity, then the values of the
free parameters have more meaning. Halfway through parameter space is halfway
along the curve. Rather than measuring time, the parameter can be thought to
measure length along the curve. Suc h parameterizations are called arc-length
parameterizations because they de ﬁne curves by functions that map from the dis-
tance along the curve (known as the arc length) to positions. We often use the
variables to denote an arc-length parameter.
Technically, a parameterization is an arc-length parameterization if the mag-
nitude of its tangent (that is, the derivative of the parameterization with respect to
the parameter) has constant magnitude. Expressed as an equation,
⏐⏐
⏐
⏐
df(s)
ds
⏐
⏐
⏐
⏐
2
= c.
Computing the length along a curve can be tricky. In general, it is de ﬁned by
the integral of the magnitude of the derivative (intuitively, the magnitude of the
derivative is the velocity of the pen as it moves along the curve). So, given a value
for the parameter v, you can compute s (the arc-length distance along the curve
from the point f(0) to the point f(v))a s
s =
∫
v
0
⏐⏐
⏐
⏐
df(t)
dt
⏐
⏐
⏐
⏐
2
dt, (15.1)
where f(t) is a function that de ﬁnes the curve with a natural parameterization.
Using the arc-length parameterization requires being able to solve Equation
(15.1) for t,g i v e ns. For many of the kinds of curves we examine, it cannot be
done in a closed-form (simple) manner and must be done numerically.
Generally, we use the variable u to denote free parameters that range over the
unit interval,s to denote arc-length free parameters, and t to represent parameters
that aren’t one of the other two.
15.1.2 Piecewise Parametric Representations
For some curves, deﬁning a parametric function that represents their shape is easy.
For example, lines, circles, and ellipses all have simple functions that de ﬁne the
points they contain in terms of a parameter. For many curves, ﬁnding a function
that speciﬁes their shape can be hard. The main strategy that we use to create com-
plex curves is divide-and-conquer: we break the curve into a number of simpler
smaller pieces, each of which has a simple description.

364 15. Curves
(a) (b) (c)
Figure 15.1. (a) A curve that can be easily represented as two lines; (b) a curve that can be easily
represented as a line and a circular arc ; (c) a curve approximating curve (b) with ﬁve line segments.
For example, consider the curves in Figure 15.1. The ﬁrst two curves are
easily speciﬁed in terms of two pieces. In the case of the curve in Figure 15.1(b),
we need two different kinds of pieces: a line segment and a circle.
To create a parametric representation of a compound curve (like the curve
in Figure 15.1(b)), we need to have our parametric function switch between the
functions that represent the pieces. If we de ﬁne our parametric functions over the
range0 ≤u ≤1, then the curve in Figures 15.1(a) or (b) might be deﬁ ned as
f(u)=
{
f1(2u)i f u ≤0.5,
f2(2u−1) if u> 0.5, (15.2)
where f1 is a parameterization of the ﬁrst piece, f2 is a parameterization of the
second piece, and both of these functions are de ﬁned over the unit interval.
We need to be careful in de ﬁning the functions f1 and f2 to make sure that the
pieces of the curve ﬁt together. If f1(1) ̸= f2(0), then our curve pieces will not
connect and will not form a single continuous curve.
To represent the curve in Figure 15.1(b), we needed to use two different types
of pieces: a line segment and a circular ar c. For simplicity’s sake, we may prefer
to use a single type of piece. If we try to represent the curve in Figure 15.1(b)
with only one type of piece (line segments), we cannot exactly re-create the curve
(unless we use an in ﬁnite number of pieces). While the new curve made of line
segments (as in Figure 15.1(c)) may not be exactly the same shape as in Fig-
ure 15.1(b), it might be close enough for our use. In such a case, we might prefer
the simplicity of using the simpler line segment pieces to having a curve that more
accurately represents the shape.
Also, notice that as we use an increasing number of pieces, we can get a better
approximation. In the limit (using an in ﬁnite number of pieces), we can exactly
represent the original shape.

15.2. Curve Properties 365
One advantage to using a piecewise representation is that it allows us to make
a tradeoff between
1. how well our represented curve approximates the real shape we are trying
to represent;
2. how complicated the pieces that we use are;
3. how many pieces we use.
So, if we are trying to represent a comp licated shape, we might decide that a
crude approximation is acceptable and us e a small number of simple pieces. To
improve the approximation, we can choose between using more pieces and using
more complicated pieces.
In computer graphics practice, we tend to prefer using relatively simple curve
pieces (either line segments, arcs, or polynomial segments).
15.1.3 Splines
Before computers, when draftsmen wanted to draw a smooth curve, one tool they
employed was a stiff piece of metal that they would bend into the desired shape
for tracing. Because the metal would bend, not fold, it would have a smooth
shape. The stiffness meant that the metal would bend as little as possible to make
the desired shape. This stiff piece of metal was called a spline.
Mathematicians found that they could represent the curves created by a draft-
man’s spline with piecewise polynomial functions. Initially, they used the term
spline to mean a smooth, piecewise polynom ial function. More recently, the term
spline has been used to describe any piecewise polynomial function. We prefer
this latter de ﬁnition.
For us, a spline is a piecewise polynomial function. Such functions are very
useful for representing curves.
15.2 Curve Properties
To describe a curve, we need to give some facts about its properties. For “named”
curves, the properties are usually speci ﬁc according to the type of curve. For
example, to describe a circle, we might provide its radius and the position of its
center. For an ellipse, we might also provide the orientation of its major axis and
the ratio of the lengths of the axes. For free-form curves however, we need to
have a more general set of properties to describe individual curves.

366 15. Curves
Some properties of curves are attributed to only a single location on the curve,
while other properties require knowledge of the whole curve. For an intuition of
the difference, imagine that the curve is a train track. If you are standing on the
track on a foggy day, you can tell that the track is straight or curved and whether
or not you are at an endpoint. These are local properties. Y ou cannot tell whether
or not the track is a closed curve, or crosses itself, or how long it is. We call this
type of property, a global property.
The study of local properties of geometric objects (curves and surfaces) is
known as differential geometry. Technically, to be a differential property, there
are some mathematical restrictions about the properties (roughly speaking, in the
train-track analogy, you would not be able to have a GPS or a compass). Rather
than worry about this distinction, we will use the term local property rather than
differential property.
Local properties are important tools for describing curves because they do not
require knowledge about the whole curve. Local properties include
 continuity,
 position at a speciﬁcp l a c eo nt h ec u r v e ,
 direction at a speci ﬁcp l a c eo nt h ec u r v e ,
 curvature (and other derivatives).
Often, we want to specify that a curve includes a particular point. A curve is
said to interpolate a point if that point is part of the curve. A function f interpo-
lates a value v if there is some value of the parameter u for which f(t)= v. We
call the place of interpolation, that is the value of t, the site.
15.2.1 Continuity
It will be very important to understand the local properties of a curve where two
parametric pieces come together. If a curve is de ﬁned using an equation like
Equation (15.2), then we need to be careful about how the pieces are de ﬁned. If
f
1(1) ̸= f2(0), then the curve will be “broken”—we would not be able to draw
the curve in a continuous stroke of a pen. We call the condition that the curve
pieces ﬁt together continuity conditions because if they hold, the curve can be
drawn as a continuous piece. Because our de ﬁnition of ”curve” at the beginning
of the chapter requires a curve to be continuous, technically a ”broken curve” is
not a curve.

15.2. Curve Properties 367
In addition to the positions, we can also check that the derivatives of the pieces
match correctly. If f′
1(1) ̸= f′
2(0), then the combined curve will have an abrupt
change in its ﬁrst derivative at the switching point; the ﬁrst derivative will not
be continuous. In general, we say that a curve is Cn continuous if all of its
derivatives up ton match across pieces. We denote the position itself as the zeroth
derivative, so that the C0 continuity condition means that the positions of the
curve are continuous, and C1 continuity means that positions and ﬁrst derivatives
are continuous. The de ﬁnition of curve requires the curve to be C0.
An illustration of some continuity cond itions is shown in Figure 15.2. A dis-
continuity in the ﬁrst derivative (the curve is C0 but not C1) is usually noticeable
because it displays a sharp corner. A di scontinuity in the second derivative is
sometimes visually noticeable. Discontinuities in higher derivatives might mat-
ter, depending on the application. For example, if the curve represents a motion,
an abrupt change in the second derivative is noticeable, so third derivative con-
tinuity is often useful. If the curve is going to have a ﬂuid ﬂowing over it (for
example, if it is the shape for an airplane wing or boat hull), a discontinuity in the
fourth or ﬁfth derivative might cause turbulence.
The type of continuity we have just introduced ( C
n) is commonly referred to
as parametric continuity as it depends on the parameterization of the two curve
pieces. If the “speed” of each piece is diff erent, then they will not be continuous.
For cases where we care about the shape of the curve, and not its parameteriza-
tion, we de ﬁne geometric continuity that requires that the derivatives of the curve
pieces match when the curves are parameterized equivalently (for example, us-
ing an arc-length parameterization). Intuitively, this means that the corresponding
derivatives must have the same direction, even if they have different magnitudes.
C 0
C 1
C 2
G 1
G 2
Figure 15.2. An illustration of various types of continuity between two curve segments .

368 15. Curves
So, if the C1 continuity condition is
f′
1(1) =f′
2(0),
theG1 continuity condition would be
f′
1(1) =k f′
2(0),
for some value of scalar k. Generally, geometric continuity is less restrictive
than parametric continuity. A Cn curve is also Gn except when the parametric
derivatives vanish.
15.3 Polynomial Pieces
The most widely used representations of curves in computer graphics is done
by piecing together basic elements that are de ﬁned by polynomials and called
polynomial pieces. For example, a line element is given by a linear polynomial.
In Section 15.3.1, we give a formal de ﬁnition and explain how to put pieces of
polynomial together.
15.3.1 Polynomial Notation
Polynomials are functions of the form
f(t)= a
0 +a1t+a2t2 +... +antn. (15.3)
The ai are called the coefﬁcients,a n dn is called the degree of the polynomial if
an ̸=0 . We also write Equation (15.3) in the form
f(t)=
n∑
i=0
aiti. (15.4)
We call this the canonical form of the polynomial.
We can generalize the canonical form to
f(t)=
n∑
i=0
cibi(t), (15.5)
where bi(t) is a polynomial. We can choose these polynomials in a convenient
form for different applications, and we call them basis functions or blending
functions (see Section 15.3.5). In Equation (15.4), the ti are the bi(t) of Equa-
tion (15.5). If the set of basis functions is chosen correctly, any polynomial of
degreen+1 can be represented by an appropriate choice of c.

15.3. Polynomial Pieces 369
The canonical form does not always have convenient coef ﬁcients. For prac-
tical purposes, throughout this chapter, we will ﬁnd sets of basis functions such
that the coef ﬁcients are convenient ways to control the curves represented by the
polynomial functions.
To specify a curve embedded in two dimensions, one can either specify two
polynomials in t: one for how x varies with t and one for how y varies with t;
or specify a single polynomial where each of the ai is a 2D point. An analogous
situation exists for any curve in an n-dimensional space.
15.3.2 A Line Segment
To introduce the concepts of piecewise polynomial curve representations, we will
discuss line segments. In practice, line segments are so simple that the mathemat-
ical derivations will seem excessive. However, by understanding this simple case,
things will be easier when we move on to more complicated polynomials.
Consider a line segment that connects point p
0 to p1. We could write the
parametric function over the unit domain for this line segment as
f(u)=( 1 −u)p0 +up1. (15.6)
By writing this in vector form, we have hidden the dimensionality of the points
and the fact that we are dealing with each di mension separately. For example,
were we working in 2D, we could have created separate equations:
fx(u)= ( 1 −u)x0 +ux1,
fy(u)= ( 1 −u)y0 +uy1.
The line that we specify is determined by the two endpoints, but from now
on we will stick to vector notation since it is cleaner. We will call the vector of
control parameters, p,t h econtrol points , and each element of p,a control point.
While describing a line segment by the positions of its endpoints is obvious
and usually convenient, there are other ways to describe a line segment. For
example,
1. the position of the center of the line segment, the orientation, and the length;
2. the position of one endpoint and the position of the second point relative to
the ﬁrst;
3. the position of the middle of the line segment and one endpoint.

370 15. Curves
It is obvious that given one kind of a description of a line segment, we can switch
to another one.
A different way to describe a line segment is using the canonical form of the
polynomial (as discussed in Section 15.3.1),
f(u)= a0 +ua1. (15.7)
Any line segment can be represented either by specifying a0 and a1 or the end-
points (p0 and p1). It is usually more convenient to specify the endpoints, because
we can compute the other parameters from the endpoints.
To write the canonical form as a vector expression, we de ﬁn eav e c t o ru that
is a vector of the powers of u:
u =
[
1uu 2 u3 ... u n]
,
so that Equation (15.4) can be written as
f(u)= u · a. (15.8)
This vector notation will make transforming between different forms of the curve
easier.
Equation (15.8) describes a curve segment by the set of polynomial coef ﬁ-
cients for the simple form of the polynomial. We call such a representation the
canonical form. We will denote the parameters of the canonical form by a.
While it is mathematically simple, the canonical form is not always the most
convenient way to specify curves. For example, we might prefer to specify a
line segment by the positions of its endpoints. If we want to de ﬁne p
0 to be the
beginning of the segment (where the segment is when u =0 )a n d p1 to be the
end of the line segment (where the line segment is at u =1 ), we can write
p0 = f(0) = [1 0] ·[a0 a1],
p1 = f(1) = [1 1] ·[a0 a1]. (15.9)
We can solve these equations for a0 and a1:
a0 = p0,
a1 = p1 −p0.
Matrix Form for Polynomials
While this ﬁrst example was easy enough to solve, for more complicated examples
it will be easier to write Equation (15.9) in the form
[ p0
p1
]
=
[ 10
11
][ a0
a1
]
.

15.3. Polynomial Pieces 371
Alternatively, we can write
p = Ca, (15.10)
where we call C, the constraint matrix.1 If having vectors of points bothers you,
you can consider each dimension independently (so that p is [x0 x1] or [y0 y1]
and a is handled correspondingly).
We can solve Equation (15.10) for a by ﬁnding the inverse of C. This inverse
matrix which we will denote by B is called the basis matrix. The basis matrix
is very handy since it tells us how to convert between the convenient parameters
p and the canonical form a, and, therefore, gives us an easy way to evaluate the
curve
f(u)= uBp .
We can ﬁnd a basis matrix for whatever form of the curve that we want, providing
that there are no nonlinearities in the de ﬁnition of the parameters. Examples of
nonlinearly deﬁned parameters include the length and angle of the line segment.
Now, suppose we want to parameterize the line segment so that p0 is the half-
way point ( u =0 .5), and p1 is the ending point ( u =1 ). To derive the basis
matrix for this parameterization, we set
p0 = f(0.5) = 1 a0 +0.5 a1,
p1 = f(1) = 1 a0 +1 a1.
So
C =
[
1 .5
11
]
,
and therefore
B = C−1 =
[
2 −1
−22
]
.
15.3.3 Beyond Line Segments
Line segments are so simple that ﬁnding a basis matrix is trivial. However, it was
good practice for curves of higher degree. First, let’s consider quadratics (curves
of degree two). The advantage of the canonical form (Equation (15.4)) is that it
works for these more complicated curves, just by letting n be a larger number.
1 We assume the form of a vector (row or column) is obvious from the context, and we will skip all
of the transpose symbols for vectors.

372 15. Curves
A quadratic (a degree-two polynomial) has three coef ﬁcients, a0, a1,a n d a2.
These coefﬁcients are not convenient for describing the shape of the curve. How-
ever, we can use the same basis matrix method to devise more convenient param-
eters. If we know the value of u, Equation (15.4) becomes a linear equation in the
parameters, and the linear algebra from the last section still works.
Suppose that we wanted to describe our curves by the position of the begin-
ning (u =0 ), middle 2 (u =0 .5), and end ( u =1 ). Entering the appropriate
values into Equation (15.4):
p0 = f(0) = a0 +0 1 a1 +0 2 a2,
p1 = f(0.5) = a0 +0 .51 a1 +0 .52 a2,
p2 = f(1) = a0 +1 1 a1 +1 2 a2.
So the constraint matrix is
C =
⎡
⎣
10 0
1 .5 .25
11 1
⎤
⎦,
and the basis matrix is
B = C−1 =
⎡
⎣
100
−34 −1
2 −42
⎤
⎦.
There is an additional type of constraint (or parameter) that is sometimes con-
venient to specify: the derivative of th e curve (with respect to its free parameter)
at a particular value. Intuitively, the derivatives tell us how the curve is changing,
so that the ﬁrst derivative tells us what direction the curve is going, the second
derivative tells us how quickly the curve is changing direction, etc. We will see
examples of why it is useful to specify derivatives later.
For the quadratic,
f(u)= a
0 + a1u+ a2u2,
the derivatives are simple:
f′(u)= df
du = a1 +2 a2u,
and
f′′(u)= d2f
du2 = df′
du =2 a2.
2Notice that this is the middle of the parameter s pace, which might not be the middle of the curve
itself.

15.3. Polynomial Pieces 373
Or, more generally,
f′(u)= ∑n
i=1 iui−1ai,
f′′(u)= ∑n
i=2
i(i−1)ui−2ai.
For example, consider a case where we want to specify a quadratic curve
segment by the position, ﬁrst, and second derivative at its middle ( u =0 .5).
p0 = f(0.5) = a0+0 .51 a1+0 .52 a2,
p1 = f′(0.5) = a1+20 .5 a2,
p2 = f′′(0.5) = 2 a2.
The constraint matrix is
C =
⎡
⎣
1 .5 .25
01 1
00 2
⎤
⎦,
and the basis matrix is
B = C−1 =
⎡
⎣
1 −.5 .125
01 −.5
00 .5
⎤
⎦.
15.3.4 Basis Matrices for Cubics
Cubic polynomials are popular in graphics (See Section 15.5). The derivations
for the various forms of cubics are just like the derivations we’ve seen already in
this section. We will work through one more example for practice.
A very useful form of a cubic polynomial is the Hermite form, where we
specify the position and ﬁrst derivative at the beginning and end, that is,
p
0 = f(0) = a0 +0 1 a1 +0 2 a2+0 3 a3,
p1 = f′(0) = a1 +2 0 1 a2+30 2 a3,
p2 = f(1) = a0 +1 1 a1 +1 2 a2+1 3 a3,
p3 = f′(1) = a1 +2 1 1 a2+31 2 a3.

374 15. Curves
Thus, the constraint matrix is
C =
⎡
⎢⎢⎣
1000
0100
1111
0123
⎤
⎥⎥⎦,
and the basis matrix is
B = C
−1 =
⎡
⎢⎢⎣
1000
0100
−3 −23 −1
21 −21
⎤
⎥⎥⎦.
We will discuss Hermite cubic splines in Section 15.5.2.
15.3.5 Blending Functions
If we know the basis matrix, B, we can multiply it by the parameter vector, u,t o
get a vector of functions
b(u)= uB.
Notice that we denote this vector by b(u) to emphasize the fact that its value
depends on the free parameter u. We call the elements of b(u) the blending func-
tions, because they specify how to blend the v alues of the control point vector
together:
f(u)=
n∑
i=0
bi(u)pi. (15.11)
It is important to note that for a chosen value of u, Equation (15.11) is a linear
equation specifying a linear blend (or weighted average) of the control points.
This is true no matter what degree polynomials are “hidden” inside of the bi
functions.
Blending functions provide a nice abstraction for describing curves. Any type
of curve can be represented as a linear comb ination of its control points, where
those weights are computed as some arb itrary functions of the free parameter.
15.3.6 Interpolating Polynomials
In general, a polynomial of degree n can interpolate a set of n +1 values. If
we are given a vector p =( p
0,...,p n) of points to interpolate and a vector
t =( t0,...,t n) of increasing parameter values, ti ̸= tj , we can use the methods

15.4. Putting Pieces T ogether 375
1
2
3
4
5
 1
23 45
6
 1
2
4
3
5
6
(a) Interpolating polynomial
through ﬁve points
(b) Interpolating polynomial
through six points
(c) Interpolating polynomial through ﬁve and six points
Figure 15.3. Interpolating polynomials through multiple points. Notice the extra wiggles and
over-shooting between points. In (c), when the sixth point is added, it completely changes the shape
of the curve due to the non-local nature of interpolating polynomials.
described in the previous sections to determine an n+1× n+1 basis matrix that
gives us a function f(t) such that f(ti)= pi. For any given vector t, we need to
set up and solve an n =1 ×n+1 linear system. This provides us with a set of
n+1 basis functions that perform interpolation:
f(t)=
n∑
i=0
pibi(t).
These interpolating basis functions can be derived in other ways. One partic-
ularly elegant way to deﬁ ne them is the Lagrange form:
bi =
n∏
j=0,j̸=i
x−tj
ti −tj
. (15.12)
There are more computationally ef ﬁcient ways to express the interpolating basis
functions than the Lagrange form (see De Boor (1978) for details).
Interpolating polynomials provide a mechanism for de ﬁning curves that in-
terpolate a set of points. Figure 15.3 shows some examples. While it is possible
to create a single polynomial to interpolate any number of points, we rarely use
high-order polynomials to represent curves in computer graphics. Instead, inter-
polating splines (piecewise polynomial functions) are preferred. Some reasons
for this are considered in Section 15.5.3.
15.4 Putting Pieces T ogether
Now that we’ve seen how to make individual pieces of polynomial curves, we can
consider how to put these pieces together.

376 15. Curves
0 .5 1
0
.5
1
b1(u)
b2(u)
b3(u)
p1
p2
p3
(b)(a)
Figure 15.4. (a) Two line segments connect three points; (b) the blending functions for each of the
points are graphed at right.
15.4.1 Knots
The basic idea of a piecewise parametric function is that each piece is only used
over some parameter range. For example, if we want to de ﬁne a function that
has two piecewise linear segments that c onnect three points (as shown in Fig-
ure 15.4(a)), we might de ﬁne
f(u)=
{
f1(2u)i f 0 ≤u< 1
2,
f2(2u−1) if 1
2 ≤u<1 ,
(15.13)
where f1 and f2 are functions for each of the two line segments. Notice that
we have rescaled the parameter for each of the pieces to facilitate writing their
equations as
f1(u)=( 1 −u)p1 +up2.
For each polynomial in our piecewise func tion, there is a site (or parameter
value) where it starts and ends. Sites where a piece function begins or ends are
called knots. For the example in Equation (15.13), the values of the knots are
0,0.5, and 1.
We may also write piecewise polynomial functions as the sum of basis func-
tions, each scaled by a coef ﬁcient. For example, we can rewrite the two line
segments of Equation (15.13) as
f(u)= p1b1(u)+ p2b2(u)+ p3b3(u), (15.14)
where the function b1(u) is deﬁned as
b1(u)=
{
1−2u if 0≤u< 1
2,
0o t h e r w i s e,
andb2 andb3 are deﬁned similarly. These functions are plotted in Figure 15.4(b).

15.4. Putting Pieces T ogether 377
The knots of a polynomial function are the combination of the knots of all of
the pieces that are used to create it. The knot vector is a vector that stores all of
the knot values in ascending order.
Notice that in this section we have used two different mechanisms for combin-
ing polynomial pieces: using independent polynomial pi eces for different ranges
of the parameter and blending together piecewise polynomial functions.
15.4.2 Using Independent Pieces
In Section 15.3, we de ﬁned pieces of polynomials over t he unit parameter range.
If we want to assemble these pieces, we need to convert from the parameter of the
overall function to the value of the parameter for the piece. The simplest way to
do this is to de ﬁne the overall curve over the parameter range [0,n] wheren is the
number of segments. Depending on the value of the parameter, we can shift it to
the required range.
15.4.3 Putting Segments T ogether
If we want to make a single curve from two line segments, we need to make sure
that the end of the ﬁrst line segment is at the same location as the beginning of the
next. There are three ways to connect the two segments (in order of simplicity):
1. Represent the line segment as its two endpoints, and then use the same point
for both. We call this a shared-point scheme.
2. Copy the value of the end of the ﬁrst segment to the beginning of the second
segment every time that the parameters of the ﬁrst segment change. We call
this a dependency scheme.
3. Write an explicit equation for the connection, and enforce it through nu-
merical methods as the other parameters are changed.
While the simpler schemes are preferable since they require less work, they also
place more restrictions on the way the line segments are parameterized. For ex-
ample, if we want to use the center of the line segment as a parameter (so that the
user can specify it directly), we will use the beginning of each line segment and
the center of the line segment as their parameters. This will force us to use the
dependency scheme.
Notice that if we use a shared-point or dependency scheme, the total number
of control points is less than n ∗m, where n is the number of segments and m

378 15. Curves
is the number of control points for each se gment; many of the control points of
the independent pieces will be computed as functions of other pieces. Notice
that if we use either the shared-point sche me for lines (each segment uses its two
endpoints as parameters and shares interior points with its neighbors), or if we
use the dependency scheme (such as the example one with the ﬁrst endpoint and
midpoint), we end up with n+1 controls for an n-segment curve.
Dependency schemes have a more serious problem. A change in one place in
the curve can propagate through the entire curve. This is called a lack of locality.
Locality means that if you move a point on a curve it will only affect a local
region. The local region might be big, but it will be ﬁnite. If a curve’s controls do
not have locality, changing a control point may affect points in ﬁnitely far away.
To see locality, and the lack thereof, in action, consider two chains of line
segments, as shown in Figure 15.5. One chain has its pieces parameterized by
their endpoints and uses point-sharing to maintain continuity. The other has its
pieces parameterized by an endpoint and m idpoint and uses dependency propa-
gation to keep the segments together. The two segment chains can represent the
same curves: they are both a set of n connected line segments. However, because
of locality issues, the endpoint-shared for m is likely to be more convenient for the
user. Consider changing the position of the ﬁrst control point in each chain. For
the endpoint-shared version, only the ﬁrst segment will change, while all of the
segments will be affected in the midpoint version, as in Figure 15.5. In fact, for
any point moved in the endpoint-shared version, at most two line segments will
change. In the midpoint version, all segments after the control point that is moved
will change, even if the chain is inﬁ nitely long.
In this example, the dependency propagation scheme was the one that did not
have local control. This is not always true . There are direct sharing schemes that
are not local and propagation schemes that are local.
We emphasize that locality is a convenience of control issue. While it is in-
convenient to have the entire curve change every time, the same changes can be
made to the curve. It simply requires moving several points in unison.
15.5 Cubics
In graphics, when we represent curves using piecewise polynomials, we usually
use either line segments or cubic polynom ials for the pieces. There are a number
of reasons why cubics are popular in computer graphics:
 Piecewise cubic polynomials allow for C2 continuity, which is generally
sufﬁcient for most visual tasks. The C1 smoothness that quadratics offer is

15.5. Cubics 379
Each line segment is parameterized by its endpoint and
its centerpoint.
Each line segment is parameterized by its endpoints.
The endpoint of segment two is equated
to the "free" end of segment one.
The endpoint of segment three is equated
 to the "free" end of segment two, etc.
Moving a control point causes a change only in a localized region.
The end of one segment is shared with the beginning endpoint of the next segment.
A change in any control point causes
ALL later line segments to be affected.
Figure 15.5. A chain of line segments with local control and one with non-local control.
often insufﬁcient. The greater smoothness offered by higher-order polyno-
mials is rarely important.
 Cubic curves provide the minimum-curvature interpolants to a set of points.
That is, if you have a set of n+3 points and de ﬁne the “smoothest” curve
that passes through them (that is the curve that has the minimum curvature
over its length), this curve can be represented as a piecewise cubic with n
segments.

380 15. Curves
 Cubic polynomials have a nice symmetry where position and derivative can
be speciﬁed at the beginning and end.
 Cubic polynomials have a nice tradeoff between the numerical issues in
computation and the smoothness.
Notice that we do not have to use cubics; they just tend to be a good tradeoff
between the amount of smoothness and complexity. Different applications may
have different tradeoffs. We focus on cubics since they are the most commonly
used.
The canonical form of a cubic polynomial is
f(u)= a
0 + a1 u+ a2 u2 + a3 u3.
As we discussed in Section 15.3, these canonical form coef ﬁcients are not a con-
venient way to describe a cubic segment.
We seek forms of cubic polynomials for which the coef ﬁcients are a conve-
nient way to control the resulting curve represented by the cubic. One of the main
conveniences will be to provide ways t o ensure the connectedness of the pieces
and the continuity between the segments.
Each cubic polynomial piece requires four coef ﬁcients or control points. That
means for a piecewise polynomial with n pieces, we may require up to 4n control
points if no sharing between segments is done or dependencies used. More often,
some part of each segment is either shared or depends on an adjacent segment, so
the total number of control points is much lower. Also, note that a control point
might be a position or a derivative of the curve.
Unfortunately, there is no single “best” representation for a piecewise cubic.
It is not possible to have a piecewise polynomial curve representation that has all
of the following desirable properties:
1. each piece of the curve is a cubic;
2. the curve interpolates the control points;
3. the curve has local control;
4. the curve has C
2 continuity.
We can have any three of these properties, but not all four; there are repre-
sentations that have any combination of three. In this book, we will discuss cubic
B-splines that do not interpolate their control points (but have local control and
are C
2); Cardinal splines and Catmull-Rom splines that interpolate their control

15.5. Cubics 381
points and offer local control, but are not C2; and natural cubics that interpolate
and are C2, but do not have local control.
The continuity properties of cubics refer to the continuity between the seg-
ments (at the knot points). The c ubic pieces themselves have in ﬁnite continuity
in their derivatives (the way we have been talking about continuity so far). Note
that if you have a lot of control points (or knots), the curve can be wiggly, which
might not seem “smooth.”
15.5.1 Natural Cubics
With a piecewise cubic curve, it is possible to create a C
2 curve. To do this, we
need to specify the position and ﬁrst and second derivative at the beginning of
each segment (so that we can make sure th at it is the same as at the end of the
previous segment). Notice that each curve segment receives three out of its four
parameters from the previous curve in the chain. These C2 continuous chains of
cubics are sometimes referred to as natural cubic splines.
For one segment of the natural cubic, we need to parameterize the cubic by
the positions of its endpoints and the ﬁrst and second derivative at the beginning
point. The control points are therefore
p0 = f(0) = a0 +0 1a1 +0 2 a2 +0 3 a3,
p1 = f′(0) = 1 1a1 +2 0 1 a2 +3 0 2 a3,
p2 = f′′(0) = 2 1 1a2 +6 0 1 a3,
p3 = f(1) = a0 +1 1 a1 +1 2 a2 +1 3 a3.
Therefore, the constraint matrix is
C =
⎡
⎢⎢⎣
1000
0100
0020
1111
⎤
⎥⎥⎦,
and the basis matrix is
B = C−1 =
⎡
⎢⎢⎣
10 0 0
01 0 0
00.50
−1 −1 −.51
⎤
⎥⎥⎦.
Given a set ofn control points, a natural cubic spline hasn−1 cubic segments.
The ﬁrst segment uses the control points to de ﬁne its beginning position, ending
position, and ﬁrst and second derivative at the beginning. A dependency scheme

382 15. Curves
copies the position, and ﬁrst and second derivative of the end of the ﬁrst segment
for use in the second segment.
A disadvantage of natural cubic splin es is that they are not local. Any change
in any segment may require the entire curve to change (at least the part after
the change was made). To make matters worse, natural cubic splines tend to be
ill-conditioned: a small change at the beginning of the curve can lead to large
changes later. Another issue is that we only have control over the derivatives of
the curve at its beginning. Segments after the beginning of the curve determine
their derivatives from their beginning point.
15.5.2 Hermite Cubics
Hermite cubic polynomials were introduced in Section 15.3.4. A segment of a
cubic Hermite spline allows the positions and ﬁrst derivatives of both of its end-
points to be speci ﬁed. A chain of segments can be linked into a C
1 spline by
using the same values for the position and derivative of the end of one segment
and for the beginning of the next.
Given a set of n control points, where every other control point is a derivative
value, a cubic Hermite spline contains(n−2)/2 cubic segments. The spline inter-
polates the points, as shown in Figure 15.6, but can guarantee only C1 continuity.
Hermite cubics are convenient because they provide local control over the
shape, and provide C1 continuity. However, since the user must specify both po-
sitions and derivatives, a special interface for the derivatives must be provided.
One possibility is to provide the user with points that represent where the deriva-
tive vectors would end if they were “placed” at the position point.
15.5.3 Cardinal Cubics
A cardinal cubic spline is a type of C
1 interpolating spline made up of cubic
polynomial segments. Given a set of n control points, a cardinal cubic spline uses
Figure 15.6. A Hermite cubic spline made up of three segments.

15.5. Cubics 383
n−2 cubic polynomial segments to interpolate all of its points except for the ﬁrst
and last.
Cardinal splines have a parameter called tension that controls how “tight” the
curve is between the points it interpolates. The tension is a number in the range
[0,1) that controls how the curve bends toward the next control point. For the
important special case of t =0 , the splines are called Catmull-Rom splines.
Each segment of the cardinal spline uses four control points. For segment i,
the points used are i, i +1 , i +2 ,a n di +3 as the segments share three points
with their neighbors. Each segment begins at its second control point and ends at
its third control point. The derivative at the beginning of the curve is determined
p4 – p
2
p1
p2 p3
p4
p3 – p1
Figure 15.7. As e g m e n t
of a cardinal cubic spline in-
terpolates its second and third
control points (p2 and p3), and
uses its other points to deter-
mine the derivatives at the be-
ginning and end.
by the vector between the ﬁrst and third control points, while the derivative at the
end of the curve is given by the vector between the second and fourth points, as
shown in Figure 15.7.
The tension parameter adjusts how much the derivatives are scaled. Specif-
ically, the derivatives are scaled by (1 −t)/2. The constraints on the cubic are
therefore
f(0) = p2,
f(1) = p3,
f′(0) = 1
2(1−t)(p3 −p1),
f′(1) = 1
2(1−t)(p4 −p2).
Solving these equations for the control points (de ﬁnings =( 1−t)/2)g i v e s
p0 = f(1)− 2
1−t f′(0) = a0 +(1−1
s) a1 + a2 + a3,
p1 = f(0) = a0,
p2 = f(1) = a0 + a1 + a2 + a3,
p3 = f(0)+ 1
s f′(1) = a0 +1
s a1 +21
s a2 +31
s a3.
This yields the cardinal matrix
B = C−1 =
⎡
⎢⎢⎣
01 0 0
−s 0 s 0
2ss −33 −2s −s
−s 2−ss −2 s
⎤
⎥⎥⎦.
Since the third point of segmenti is the second point of segmenti+1, adjacent
segments of the cardinal spline connect. Similarly, the same points are used to
specify the ﬁrst derivative of each segment, providing C1 continuity.
Cardinal splines are useful, because they provide an easy way to interpolate
a set of points with C1 continuity and local control. They are only C1,s ot h e y
sometimes get “kinks” in them. The tension parameter gives some control over
what happens between the interpolated points, as shown in Figure 15.8, where a

384 15. Curves
3 4 5
12 67
Figure 15.8. Cardinal splines through seven control points with varying values of tension parame-
ter t.
set of cardinal splines through a set of points is shown. The curves use the same
control points, but they use different values for the tension parameters. Note that
the ﬁrst and last control points are not interpolated.
Given a set of n points to interpolate, you might wonder why we might prefer
to use a cardinal cubic spline (that is a set of n−2 cubic pieces) rather than a sin-
gle, ordern polynomial as described in Section 15.3.6. Some of the disadvantages
of the interpolating polynomial are:
 The interpolating polynomial tends to overshoot the points, as seen in Fig-
ure 15.9. This overshooting gets worse as the number of points grows
larger. The cardinal splines tend to be well behaved in between the points.
 Control of the interpolating polynomial is not local. Changing a point at the
beginning of the spline affects the entire spline. Cardinal splines are local:
any place on the spline is affected by its four neighboring points at most.
 Evaluation of the interpolating polynomial is not local. Evaluating a point
on the polynomial requires access to a ll of its points. Evaluating a point
on the piecewise cubic requires a ﬁxed small number of computations, no
matter how large the total number of points is.
There are a variety of other numerical an d technical issues in using interpolat-
ing splines as the number of points grows larger. See De Boor (2001) for more
information.
A cardinal spline has the disadvantage that it does not interpolate the ﬁrst or
last point, which can be easily ﬁxed by adding an extra point at either end of
the sequence. The cardinal spline also is not as continuous—providing only C
1
continuity at the knots.

15.6. Approximating Curves 385
Figure 15.9. Splines interpolating nine control points (marked with small crosses). The thick
orange line shows an interpolating polynomial. The t hin line shows a Catmull-Rom spline. The latter
is made of seven cubic segments, which are each shown in alternating blue tones.
15.6 Approximating Curves
It might seem like the easiest way to control a curve is to specify a set of points
for it to interpolate. In practice, however, interpolation schemes often have unde-
sirable properties because they have less continuity and offer no control of what
happens between the points. Curve schemes that only approximate the points are
often preferred. With an approximating scheme, the control points in ﬂuence the
shape of the curve, but do not specify it exactly. Although we give up the ability
to directly specify points for the curve to pass through, we gain better behavior
of the curve and local control. Should we need to interpolate a set of points, the
positions of the control points can be computed such that the curve passes through
these interpolation points.
The two most important types of approximating curves in computer graphics
are B´ezier curves and B-spline curves.
15.6.1 B ´ezier Curves
B´ezier curves are one of the most common representations for free-form curves
in computer graphics. The curves are named for Pierre B´ ezier, one of the people
who was instrumental in their development. B´ ezier curves have an interesting
history where they were concurrently de veloped by several independent groups.
AB ´ezier curve is a polynomial curve that approximates its control points. The
curves can be a polynomial of any degree. A curve of degree d is controlled by
d +1 control points. The curve interpolates its ﬁrst and last control points, and
the shape is directly in ﬂuenced by the other points.
Often, complex shapes are made by connecting a number of B´ ezier curves of
low degree, and in computer graphics, cubic (d =3 )B ´ezier curves are commonly
used for this purpose. Many popular illustration programs, such as Adobe Illus-

386 15. Curves
trator, and font representation schemes, such as that used in Postscript, use cubic
B´ezier curves. B´ezier curves are extremely popular in computer graphics because
they are easy to control, have a number of useful properties, and there are very
efﬁcient algorithms for working with them.
B´ezier curves are constructed such that:
 The curve interpolates the ﬁrst and last control points, with u =0 and 1,
respectively.
 The ﬁrst derivative of the curve at its beginning (end) is determined by the
vector between the ﬁrst and second (next to last and last) control points.
The derivatives are given by the vector s between these points scaled by the
degree of the curve.
 Higher derivatives at the beginning (end) of the curve depend on the points
at the beginning (end) of the curve. The n
th derivative depends on the ﬁrst
(last)n+1 points.
For example, consider the B´ezier curve of degree 3 (cubic) as in Figure 15.10.
The curve has four ( d+1 ) control points. It begins at the ﬁrst control point ( p0)
and ends at the last ( p1). The ﬁrst derivative at the beginning is proportional to
the vector between the ﬁrst and second control points (p1 −p0). Speci ﬁcally,
f′(0) = 3(p1 −p0). Similarly, the ﬁrst derivative at the end of the curve is given
by f′(1) = 3(p3 −p2). The second derivative at the beginning of the curve can
be determined from control points p0, p1 and p2.
Using the facts about B´ezier cubics in the preceding paragraph, we can use the
methods of Section 15.5 to create a parametric function for them. The de ﬁnitions
p0
p1
p2
p3
p1 – p
0
p
3 – p
2
f’(0)=3(p1 – p0)
f’(1)=3(p3 – p2)
Figure 15.10. A cubic B´ezier curve is controlled by four points. It interpolates the ﬁrst and last,
and the beginning and ﬁnal derivatives are three times the vectors between the ﬁrst two (or last two)
points.

15.6. Approximating Curves 387
of the beginning and end interpolation and derivatives give
p0 = f(0) = a303 + a202 + a10+a 0,
p3 = f(1) = a313 + a212 + a11+a 0,
3(p1 −p0)= f′(0) = 3 a302 +2 a20+a 1,
3(p3 −p2)= f′(1) = 3 a312 +2 a21+a 1.
This can be solved for the basis matrix
B = C−1 =
⎡
⎢⎢⎣
100 0
−330 0
3 −63 0
−13 −31
⎤
⎥⎥⎦,
and then written as
f(u)=( 1−3u+3u2−u3)p0 +(3u−6u2+3u3)p1 +(3u2−3u3)p2+(u3)p3,
or
f(u)=
d∑
i=0
bi,3pi,
where the bi,3 are the B´ezier blending functions of degree 3:
b0,3 =( 1 −u)3,
b1,3 =3 u(1−u)2,
b2,3 =3 u2(1−u),
b3,3 = u3.
Fortunately, the blending functions for B´ezier curves have a special form that
works for all degrees. These functions are known as the Bernstein basis polyno-
mials and have the general form
bk,n(u)= C(n,k)uk (1−u)(n−k),
where n is the order of the B´ ezier curve, and k is the blending function number
between 0 and n (inclusive). C(n,k) are the binomial coef ﬁcients:
C(n,k)= n!
k!(n−k)!.
Given the positions of the control pointspk, the function to evaluate the B´ezier
curve of order n (with n+1 control points) is
p(u)=
n∑
k=0
pkC(n,k)uk (1−u)(n−k).
Some B´ezier segments are shown in Figure 15.11.

388 15. Curves
Figure 15.11. V arious B´ezier segments of degree 2–6. The control points are shown with crosses,
and the control polygons (line segments connec ting the control points) are also shown.
B´ezier segments have several useful properties:
 The curve is bounded by the convex hull of the control points.
 Any line intersects the curve no more times than it intersects the set of
line segments connecting the control points. This is called the variation
diminishing property. This property is illustrated in Figure 15.12.
 The curves are symmetric: reversing the order of the control points yields
the same curve, with a reversed parameterization.
 The curves are afﬁne invariant. This means that translating, scaling, rotat-
ing, or skewing the control points is the same as performing those opera-
tions on the curve itself.
 There are good simple algorithms for evaluating and subdividing B´ ezier
curves into pieces that are themselves B´ ezier curves. Because subdivision
Figure 15.12. The variation diminishing property of B´ezier curves means that the curve does not
cross a line more than its control polygon does. Therefore, if the control polygon has no “wiggles,”
the curve will not have them either. B-splines (Section 15.6.2) also have this property.

15.6. Approximating Curves 389
can be done effectively using the algorithm described later, a divide and
conquer approach can be used to create effective algorithms for important
tasks such as rendering B´ ezier curves, approximating them with line seg-
ments, and determining the int ersection between two curves.
When B´ezier segments are connected together to make a spline, connectivity be-
tween the segments is created by sharing the endpoints. However, continuity
of the derivatives must be created by positioning the other control points. This
provides the user of a B´ ezier spline with control over the smoothness. For G
1
continuity, the second-to-last point of the ﬁrst curve and the second point of the
 Figure 15.13. Two B ´ezier
segments connect to form a C1
spline, because the vector be-
tween the last two points of
the ﬁrst segment is equal to the
vector between the ﬁrst two
points of the second segment.
second curve must be collinear with the equated endpoints. For C1 continu-
ity, the distances between the points must be equal as well. This is illustrated
in Figure 15.13. Higher degrees of continuity can be created by properly posi-
tioning more points.
Geometric Intuition for B ´ezier Curves
B´ezier curves can be derived from geometric principles, as well as from the alge-
braic methods described above. We outlin e the geometric principles because they
provides intuition on how B´ezier curves work.
Imagine that we have a set of control points from which we want to create
a smooth curve. Simply connecting the points with lines (to form the control
polygon) will lead to something that is non-smooth. It will have sharp corners. We
could imagine “smoothing” this polygon by cutting off the sharp corners, yielding
a new polygon that is smoother, but still not “smooth” in the mathematical sense
(since the curve is still a polygon, and therefore only C1). We can repeat this
process, each time yielding a smoother polygon, as shown in Figure 15.14. In the
limit, that is if we repeated the process in ﬁnitely many times, we would obtain a
C
1 smooth curve.
Figure 15.14. Subdivision procedure for quadratic B´eziers. Each line segment is divided in half
and these midpoints are connected (blue points and lines). The interior control point is moved to the
midpoint of the new line segment (orange point).

390 15. Curves
Cut
Figure 15.15. By repeatedly cutting the corners off a polygon, we approach a smooth curve.
What we have done with corner cutting is deﬁning a subdivision scheme. That
is, we have de ﬁned curves by a process for breaking a simpler curve into smaller
pieces (e.g., subdividing it). The resulting curve is the limit curve that is achieved
by applying the process in ﬁnitely many times. If the subdivision scheme is de-
ﬁned correctly, the result will be a smooth curve, and it will have a parametric
form.
Let us consider applying corner cutting to a single corner. Given three points
(p0, p1, p2), we repeatedly “cut off the corners” as shown in Figure 15.15. At
each step, we divide each line segment in half, connect the midpoints, and then
move the corner point to the midpoint of the new line segment. Note that in this
process, new points are introduced, moved once, and then remain in this position
for any remaining iterations. The endpoints never move.
If we compute the “new” position for p
2 as the midpoint of the midpoints, we
get the expression
p′
2 = 1
2(1
2 p0 + 1
2 p1)+ 1
2(1
2 p1 + 1
2 p2).
The construction actually works for ot her proportions of distance along each
segment. If we let u be the distance between the beginning and the end of each
segment where we place the middle point, we can rewrite this expression as
p(u)=( 1 −u)((1−u)p0 +up1)+ u((1−u)p1 +up2).
Regrouping terms gives the quadratic B´ezier function:
B2(u)=( 1 −u)2p0 +2u(1−u)p1 +u2p2.
The de Casteljau Algorithm
O n en i c ef e a t u r eo fB ´ezier curves is that there is a very simple and general method
for computing and subdividing them. The method, called the de Casteljau algo-
rithm, uses a sequence of linear interpolations to compute the positions along the

15.6. Approximating Curves 391
1
32
41
32
4
Figure 15.16. An illustration of the de Casteljau algorithm for a cubic B´ ezier. The left-hand image
shows the construction for u =0 . 5. The right-hand image shows the construction for 0.25, 0.5, and
0.75.
B´ezier curve of arbitrary order. It is the generalization of the subdivision scheme
described in the previous section.
The de Casteljau algorithm begins by c onnecting every adjacent set of points
with lines, and ﬁnding the point on these lines that is the u interpolation, giving a
set ofn−1 points. These points are then connected with straight lines, those lines
are interpolated (again by u), giving a set of n−2 points. This process is repeated
until there is one point. An illustration of this process is shown in Figure 15.16.
The process of computing a point on a B´ezier segment also provides a method
for dividing the segment at the point. The intermediate points computed during
the de Casteljau algorithm form the new control points of the new, smaller seg-
ments, as shown in Figure 15.17.
The existence of a good algorithm for dividing B´ ezier curves makes divide-
and-conquer algorithms possible. For example, when drawing a B´ ezier curve
A
CB
D
CDAB
BC
AC BDAD
Figure 15.17. The de Casteljau algorithm is used to subdivide a cubic B´ ezier segment. The
initial points (black diamonds A, B, C, and D) are linearly interpolated to yield blue circles (AB, BC,
CD), which are linearly interpolated to yield orange circles (AC, BD), which are linearly interpolated
to give the point on the cubic AD. This process also has subdivided the B´ ezier segment with control
points A,B,C,D into two B´ezier segments with control points A, AB, AC, AD and AD, BD, CD, D.

392 15. Curves
segment, it is easy to check if the curve is close to being a straight line because it is
bounded by its convex hull. If the control points of the curve are all close to being
colinear, the curve can be drawn as a straight line. Otherwise, the curve can be
divided into smaller pieces, and the pro cess can be repeated. Similar algorithms
can be used for determining the intersec tion between two curves. Because of the
existence of such algorithms, other curve representations are often converted to
B´ezier form for processing.
15.6.2 B-Splines
B-splines provide a method for approximating a set of n points with a curve made
up of polynomials of degree d that gives C
(d−1) continuity. Unlike the B´ ezier
splines of the previous section, B-splines allow curves to be generated for any
desired degree of continuity (almos t up to the number of points). Because of
this, B-splines are a preferred way to specify very smooth curves (high degrees
of continuity) in computer graphics. If we want a C
2 or higher curve through an
arbitrary number of points, B-splines are probably the right method.
We can represent a curve using a linear combination of B-spline basis func-
tions. Since these basis functions are themselves splines, we call them basis
splines or B-splines for short. Each B-spline or basis function is made up of a
set of d +1 polynomials each of degree d. The methods of B-splines provide
general procedures for deﬁ ning these functions.
The term B-spline speci ﬁcally refers to one of the basis functions, not the
function created by the linear combination of a set of B-splines. However, there
is inconsistency in how the term is used in computer graphics. Commonly, a “B-
spline curve” is used to mean a curve repr esented by the linear combination of
B-splines.
The idea of representing a polynomial as the linear combination of other poly-
nomials has been discussed in Section 15.3.1 and 15.3.5. Representing a spline
as a linear combination of other splines was shown in Section 15.4.1. In fact, the
example given is a simple case of a B-spline.
The general notation for representin g a function as a linear combination of
other functions is
f(t)=
n∑
i=1
pibi(t), (15.15)
where the pi are the coef ﬁcients and the bi are the basis functions. If the coef ﬁ-
cients are points (e.g., 2 or 3 vectors), we refer to them as control points. The key
to making such a method work is to de ﬁne the b
i appropriately. B-splines provide
a very general way to do this.

15.6. Approximating Curves 393
A set of B-splines can be de ﬁned for a number of coef ﬁcientsn and a param-
eter value k.3 The value of k is one more than the degree of the polynomials used
to make the B-splines (k = d+1.)
B-splines are important because they provide a very general method for cre-
ating functions (that will be useful for representing curves) that have a number
of useful properties. A curve with n points made with B-splines with parameter
valuek:
 isC(k−2) continuous;
 is made of polynomials of degree k −1;
 has local control—any site on the curve only depends on k of the control
points;
 is bounded by the convex hull of the points;
 exhibits the variation diminishing property illustrated in Figure 15.12.
A curve created using B-splines does not necessarily interpolate its control points.
We will introduce B-splines by ﬁrst looking at a speci ﬁc, simple case to in-
troduce the concepts. We will then generalize the methods and show why they
are interesting. Because the method for c omputing B-splines is very general, we
delay introducing it until we have shown what these generalizations are.
Uniform Linear B-Splines
Consider a set of basis functions of the following form:
bi,2(t)=
⎧
⎪⎨
⎪⎩
t−i if i ≤t<i +1,
2−t+i if i+1 ≤t ≤i+2,
0o t h e r w i s e.
(15.16)
Each of these functions looks like a little triangular “hat” between i andi+2 with
its peak at i+1. Each is a piecewise polynomial, with knots at i,i+1 ,a n di+2.
Two of them are graphed in Figure 15.18.
Each of these functionsbi,2 is a ﬁrst-degree (linear) B-spline. Because we will
consider B-splines of other parameter values later, we denote these with the 2 in
the subscript.
3 The B-spline parameter is actually the order of the polynomials used in the B-splines. While this
terminology is not uniform in the literature, the use of the B-spline parameter k as a value one greater
than the polynomial degree is widely used, although some texts (see the chapter notes) write all of the
equations in terms of polynomial degree.

394 15. Curves
b1,2(t) b2,2(t)
3 40
1
012 012 3 40
1
Figure 15.18. B-splines with d =1o r k =2 .
Notice that we have chosen to put the lower edge of the B-spline (its ﬁrst knot)
ati. Therefore, the ﬁrst knot of the ﬁrst B-spline (i =1 )i sa t 1. Iteration over the
B-splines or elements of the coefﬁcient vector is from1 ton (see Equation 15.15).
When B-splines are implemented, as wel l as in many other discussions of them,
they often are numbered from 0 to n−1.
We can create a function from a set of n control points using Equation 15.15,
with these functions used for the bi to create an “overall function” that was in ﬂu-
enced by the coef ﬁcients. If we were to use these ( k =2 ) B-splines to de ﬁne the
overall function, we would de ﬁne a piecewise polynomial function that linearly
interpolates the coef ﬁcients pi between t = k and t = n +1 . Note that while
(k =2 ) B-splines interpolate all of their coef ﬁcients, B-splines of higher degree
do this under some speci ﬁc conditions that we will discuss in Section 15.6.3.
Some properties of B-splines can be seen in this simple case. We will write
these in the general form using k, the parameter, and n for the number of coef ﬁ-
cients or control points:
 Each B-spline has k +1 knots.
 Each B-spline is zero before its ﬁrst knot and after its last knot.
 The overall spline has lo cal control because each coef ﬁcient is only mul-
tiplied by one B-spline, and this B-spline is nonzero only between k +1
knots.
 The overall spline has n+k knots.
 Each B-spline is C(k−2) continuous, therefore the overall spline is C(k−2)
continuous.
 The set of B-splines sums to 1 for all parameter values between knots k and
n+1. This range is where there are k B-splines that are nonzero. Summing
to 1 is important because it means that the B-splines are shift invariant:
translating the control points will translate the entire curve.

15.6. Approximating Curves 395
 Between each of its knots, the B-spline is a single polynomial of degree
d = k −1. Therefore, the overall curve (that sums these together) can also
be expressed as a single, degree d polynomial between any adjacent knots.
In this example, we have chosen the knots to be uniformly spaced. We will con-
sider B-splines with nonuniform spacing later. When the knot spacing is uniform,
each of the B-splines is identical except for being shifted. B-splines with uniform
knot spacing are sometimes called uniform B-splines or periodic B-splines.
Uniform Quadratic B-Splines
The properties of B-splines listed in the previous section were intentionally writ-
ten for arbitrary n and k. A general procedure for constructing the B-splines will
be provided later, but ﬁrst, lets consider another speci ﬁc case with k =3 .
The B-spline b2,3 is shown in Figure 15.19. It is made of quadratic pieces
(degree 2), and has three of them. It is C1 continuous and is nonzero only within
the four knots that it spans. Notice that a quadratic B-spline is made of three
pieces, one between knot 1 and 2, one be tween knot 2 and 3, and one between
knot 3 and 4. In Section 15.6.3 we will see a general procedure for building these
functions. For now, we simply examine these functions:
b
i,3(t)=
⎧
⎪⎪
⎪
⎨
⎪⎪
⎪
⎩
1
2u2 if i ≤t<i +1 u = t−i,
−u2 +u+ 1
2 if i+1 ≤t<i +2 u = t−(i+1),
1
2(1−u)2 if i+2 ≤t<i +3 u = t−(i+2),
0o therwise.
(15.17)
In order to make the expressions simpler, we wrote the function for each part as
if it applied over the range 0 to1.
If we evaluate the overall function made from summing together the B-splines,
at any time only k (3 in this case) of them are nonzero. One of them will be in the
ﬁrst part of Equation 15.17, one will be in the second part, and one will be in the
third part. Therefore, we can think of any piece of the overall function as being
0
1
012345
Figure 15.19. The B-spline b2,3 with uniform knot spacing.

396 15. Curves
0
1
0123456789 1 0
Figure 15.20. The set of seven B-splines with k = 3 and uniform knot spacing
[ 1 ,2 ,3 ,4 ,5 ,6 ,7 ,8 ,1 0 ] .
made up of a degree d = k −1 polynomial that depends on k coefﬁcients. For
thek =3 case, we can write
f(u)= 1
2(1−u)2pi +(−u2 +u+ 1
2)pi+1 + 1
2u2pi+2
whereu = t−i. This deﬁnes the piece of the overall function when i ≤t<i +1.
If we have a set of n points, we can use the B-splines to create a curve. If we
have seven points, we will need a set of seven B-splines. A set of seven B-splines
for k =3 is shown in Figure 15.20. Notice that there are n +k (10) knots, that
the sum of the B-splines is 1 over the range k to n +1 (knots 3 through 8). A
curve speciﬁed using these B-splines and a set of points is shown in Figure 15.21.
Uniform Cubic B-Splines
Because cubic polynomials are so popular in computer graphics, the special case
of B-splines with k =4 is suf ﬁciently important that we consider it before dis-
cussing the general case. A B-spline of third degree is de ﬁned by four cubic
polynomial pieces. The general process by w hich these pieces are determined is
1
2
3
4
5
6
7
Figure 15.21. Curve made from seven quadratic ( k=3) B-splines, using seven control points.

15.6. Approximating Curves 397
b0(t)
b1(t – 1)
b3(t – 3)
b2(t – 2)
2
3
t = it  = i + 1 t = i + 2 t = i + 3 t = i + 4
Figure 15.22. The cubic (k = 4) B-spline with uniform knots.
described later, but the result is
bi,4(t)=
⎧
⎪⎪
⎪
⎪
⎪
⎪
⎪
⎪
⎨
⎪⎪
⎪
⎪
⎪
⎪
⎪⎪⎩
1
6u3 if i ≤t<i +1 u = t−i,
1
6(−3u3 +3u2 +3u+1) if i+1 ≤t<i +2 u = t−(i+1),
1
6(3u3 −6u2 +4) if i+2 ≤t<i +3 u = t−(i+2),
1
6(−u3 +3u2 −3u+1) if i+3 ≤t<i +4 u = t−(i+3),
0 otherwise.
(15.18)
This degree 3 B-spline is graphed for i =1 in Figure 15.22.
We can write the function for the overall curve between knots i+3 andi+4
as a function of the parameter u between 0 and 1 and the four control points that
inﬂuence it:
f(u)= 1
6(−u3 +3u2 −3u+1) pi + 1
6(3u3 −6u2 +4) pi+1
+ 1
6(−3u3 +3u2 +3u+1) pi+2 + 1
6u3pi+3.
This can be rewritten using the matrix notation of the previous sections, giving
a basis matrix for cubic B-splines of
Mb = 1
6
⎡
⎢⎢⎣
−13 −31
3 −630
−3030
1410
⎤
⎥⎥⎦.
Unlike the matrices that were derived from constraints in Section 15.5, this ma-
trix is created from the polynomials that are determined by the general B-spline
procedure deﬁned in the next section.

398 15. Curves
k = 3
4  =  k2  =  k
k = 5
Figure 15.23. B-spline curves using the same uniform set of knots and the same control points, for
various values of k. Note that as k increases, the valid parameter range for the curve shrinks.
15.6.3 Nonuniform B-Splines
One nice feature of B-splines is that they can be deﬁ ned for any k> 1. So if we
need a smoother curve, we can simply increase the value of k. This is illustrated
in Figure 15.23.
So far, we have said that B-splines generalize to any k> 1 and any n ≥d.
There is one last generalization to introduce before we show how to actually com-
pute these B-splines. B-splines are de ﬁned for any non-decreasing knot vector.
For a given n and k, the set of B-splines (and the function created by their
linear combination) has n + k knots. We can write the value of these knots as
a vector, that we will denote as t. For the uniform B-splines, the knot vector is
[1,2,3,...,n + k]. However, B-splines can be generated for any knot vector of
lengthn+k, providing the values are non-decreasing (e.g., ti+1 ≥ti).
There are two main reasons why nonuniform knot spacing is useful: it gives us
control over what parameter range of the overall function each coef ﬁcient affects,
and it allows us to repeat knots (e.g., create knots with no spacing in between) in
order to create functions with different properties around these points. The latter
will be considered later in this section.
The ability to specify knot values for B-splines is similar to being able to spec-
ify the interpolation sites for interpolating spline curves. It allows us to associate
curve features with parameter values. By specifying a nonuniform knot vector,
we specify what parameter range each coef ﬁcient of a B-spline curve affects. Re-
member that B-spline i is nonzero only between knot i and knot i+k. Therefore,
the coefﬁcient associated with it only affects the curve between these parameter
values.
One place where control over knot values is particularly useful is in inserting
or deleting knots near the beginning of a sequence. To illustrate this, consider a

15.6. Approximating Curves 399
curve deﬁned using linear B-splines ( k =2 ) as discussed in Section 15.6.2. For
n =4 , the uniform knot vector is [1,2,3,4,5,6]. This curve is controlled by a
set of four points and spans the parameter range t =2 to t =5 . The “end” of
the curve (t =5 ) interpolates the last control point. If we insert a new point in
the middle of the point set, we woul d need a longer knot vector. The locality
properties of the B-splines prevent this insertion from affecting the values of the
curve at the ends. The longer curve would s till interpolate its last control point
at its end. However, if we chose to keep the uniform knot spacing, the new knot
vector would be[1,2,3,4,5,6,7]. The end of the curve would be at t =6 , and the
parameter value at which the last control point is interpolated will be a different
parameter value than before the insertion. With nonuniform knot spacing, we can
use the knot vector [1,2,3,3.5,4,5,6] so that the ends of the curve are unaffected
by the change. The abilities to hav e nonuniform knot spacing makes the locality
property of B-splines an algebraic property, as well as a geometric one.
We now introduce the general method for de ﬁning B-splines. Given values
for the number of coef ﬁcients n, the B-spline parameter k, and the knot vector t
(which has length n+k), the following recursive equations de ﬁne the B-splines:
b
i,1,t(t)=
{
1i f ti ≤t< ti+1,
0o t h e r w i s e. (15.19)
bi,k,t(t)= t−ti
ti+k−1−ti
bi,k−1(t)+ ti+k−t
ti+k−ti+1
bi+1,k−1(t). (15.20)
This equation is know as the Cox–de Boor recurrence. It may be used to compute
speciﬁc values for speci ﬁc B-splines. However, it is more often applied alge-
braically to derive equations such as Equation 15.17 or 15.18.
As an example, consider how we would have derived Equation 15.17. Using
a uniform knot vector [1,2,3,... ],t i = i, and the value k =3 in Equation 15.20
yields
bi,3(t)= t−i
(i+2) −ibi,2 + (i+3) −t
(i+3) −(i+1) bi+1,2 (15.21)
= 1
2(t−i)bi,2 + 1
2(i+3− t)bi+1,2.
Continuing the recurrence, we must evaluate the recursive expressions:
bi,2(t)= t−i
(i+2− 1)−ibi,1 + (i+2) −t
(i+2) −(i+1) bi+1,1
=( t−i)bi,1 +(i+2− t)bi+1,1,

400 15. Curves
bi+1,2(t)= t−(i+1)
((i+1)+2− 1)−(i+1) bi+1,1
+ ((i+1)+2) −t
((i+1)+2) −((i+1)+1) b(i+1)+1,1
=( t−i+1)bi+1,1 +(i+3− t)bi+2,1.
Inserting these results into Equation 15.22 gives:
bi,3(t)= 1
2(t−i)((t−i)bi,1 +(i+2− t)bi+1,1)
+ 1
2(i+3− t)(t−i+1)bi+1,1 +(i+3− t)bi+2,1.
To see that this expression is equivalent to Equation 15.17, we note that each
of the (k =1 ) B-splines is like a switch, turning on only for a particular parameter
range. For instance, bi,1 is only nonzero between i andi+1. So, if i ≤t<i +1,
only the ﬁrst of the (k =1 ) B-splines in the expression is nonzero, so
bi,3(t)= 1
2(t−i)2 if i ≤t<i +1.
Similar manipulations give the other parts of Equation 15.17.
Repeated Knots and B-Spline Interpolation
While B-splines have many nice properties, functions de ﬁned using them gener-
ally do not interpolate the coef ﬁcients. This can be inconvenient if we are using
them to de ﬁne a curve that we want to interpolate a speciﬁ c point. W e give a
brief overview of how to interpolate a speciﬁ c point using B-splines here. A more
complete discussion can be found in the books listed in the chapter notes.
One way to cause B-splines to interpolate their coef ﬁcients is to repeat knots.
If all of the interior knots for a particular B-spline have the same value, then the
overall function will interpolate this B-spline’s coef ﬁcient. An example of this is
shown in Figure 15.24.
Interpolation by repeated knots comes at a high cost: it removes the smooth-
ness of the B-spline and the resulting overall function and represented curve.
However, at the beginning and end of the spline, where continuity is not an is-
sue, knot repetition is useful for creating endpoint interpolating B-splines. While
the ﬁrst (or last) knot’s value is not important for interpolation, for simplicity, we
make the ﬁrst (or last) k knots have the same value to achieve interpolation.
Endpoint interpolating quadratic B-splines are shown in Figure 15.25. The
ﬁrst two and last two B-splines are different than the uniform ones. Their expres-

15.6. Approximating Curves 401
(a) Uniform knots
 (b) Nonuniform knots
Figure 15.24. A curve parameterized by quadratic B-splines ( k = 3) with seven control points. On
the left, uniform knots vector [1,2,3,4,5,6,7,8,9,10] is used. On the right, the nonuniform knot spacing
[1,2,3,4,4,6,7,8,8,10] is used. The duplication of the 4th and 8th knot means that all interior knots of
the 3rd and 7th B-spline are equal, so the curve inte rpolates the control point associated with those
points.
sions can be derived through the use of the Cox–de Boor recurrence:
b1,3,[0,0,0,1,2,...](t)=
{
(1−t)2 if 0≤t< 1,
0o t h e r w i s e.
b2,3,[0,0,0,1,2,...](t)=
⎧
⎪⎨
⎪⎩
2u−3
2u2 if 0≤t< 1 u = t,
1
2(1−u)2 if 1≤t< 2 u = t−1,
0o t h e r w i s e .
15.6.4 NURBS
Despite all of the generality B-splines provide, there are some functions that can-
not be exactly represented using them. In particular, B-splines cannot represent
conic sections. To represent such curves, a ratio of two polynomials is used.
Nonuniform B-splines are used to represent both the numerator and the denom-
1
0
0123456
Figure 15.25. Endpoint interpolating quadratic ( k = 3) B-splines, for n = 8. The knot vector is
[0,0,0,1,2,3,4,5,6,6,6]. The ﬁrst and last two B-splines are aperiodic, while the middle four (shown as
dotted lines) are periodic and identical to the ones in Figure 15.20.

402 15. Curves
inator. The most general form of these are nonuniform rational B-splines, or
NURBS for short.
NURBS associate a scalar weight hi with every control point pi and use the
same B-splines for both:
f(u)=
∑n
i=1 hipibi,k,t
∑n
i=1 hibi,k,t
,
where bi,k,t are the B-splines with parameter k and knot vector t.
NURBS are very widely used to represent curves and surfaces in geometric
modeling because of the amazing versatility they provide, in addition to the useful
properties of B-splines.
15.7 Summary
In this chapter, we have discussed a number of representations for free-form
curves. The most important ones for computer graphics are:
 Cardinal splines use a set of cubic pieces to interpolate control points. They
are generally preferred to interpola ting polynomials because they are local
and easier to evaluate.
 B´ezier curves approximate their control points and have many useful prop-
erties and associated algorithms. For this reason, they are popular in graph-
ics applications.
 B-spline curves represent the curve as a linear combination of B-spline
functions. They are general and have many useful properties such as being
bounded by their convex hull and being variation diminishing. B-splines
are often used when smooth curves are desired.
Notes
The problem of representing shapes mathematically is an entire ﬁeld unto itself,
generally known as geometric modeling. Representing curves is just the begin-
ning and is generally a precursor to modeling surfaces and solids. A more thor-
ough discussion of curves can be found in most geometric modeling texts, see for
example Geometric Modeling (Mortenson, 1985) for a text that is accessible to
computer graphics students. Many geometric modeling books speci ﬁcally focus

15.7. Summary 403
on smooth curves and surfaces. Texts such as An Introduction to Splines for Use
in Computer Graphics (Bartels, Beatty, & Barsky, 1987), Curves and Surfaces for
CAGD: A Practical Guide (Farin, 2002) and Geometric Modeling with Splines:
An Introduction (E. Cohen, Riesenfeld, & Elber, 2001) provide considerable detail
about curve and surface representations. Other books focus on the mathematics
of splines; A Practical Guide to Splines (De Boor, 2001) is a standard reference.
The history of the development of curve and surface representations is com-
plex, see the chapter by Farin in Handbook of Computer Aided Geometric De-
sign (Farin, Hoschek, & Kim, 2002) or the book on the subject An Introduction
to NURBS: With Historical Perspective (D. F. Rogers, 2000) for a discussion.
Many ideas were independently developed by multiple groups who approached
the problems from different discip lines. Because of this, it can be dif ﬁcult to at-
tribute ideas to a single person or to point at the “original” sources. It has also led
to a diversity of notation, terminology, and ways of introducing the concepts in
the literature.
15.7.1 Exercises
For Exercises 1–4, ﬁnd the constraint matrix, the basis matrix, and the basis func-
tions. To invert the matrices you can use a program such as MA TLAB or OCT A VE
(a free MA TLAB-like system).
1. A line segment: parameterized with p
0 located 25% of the way along the
segment (u =0 .25), and p1 located 75% of the way along the segment.
2. A quadratic: parameterized with p0 as the position of the beginning point
(u =0 ), p1,t h e ﬁrst derivative at the beginning point, and p2, the second
derivative at the beginning point.
3. A cubic: its control points are equally spaced ( p0 has u =0 , p1 has u =
1/3, p2 hasu =2 /3,a n dp3 hasu =1 ).
4. A quintic: (a degree ﬁve polynomial, so the matrices will be6×6)w h e r ep0
is the beginning position, p1 is the beginning derivative, p2 is the middle
(u =0 .5) position, p3 is the ﬁrst derivative at the middle, p4 is the position
at the end, and p5 is the ﬁrst derivative at the end.
5. The Lagrange form (Equation (15.12)) can be used to represent the inter-
polating cubic of Exercise 3. Use it at several different parameter values to
conﬁrm that it does produce the same results as the basis functions derived
in Exercise 3.

404 15. Curves
6. Devise an arc-length parameterization for the curve represented by the para-
metric function
f(u)=( u,u2).
7. Given the four control points of a segment of a Hermite spline, compute the
control points of an equivalent B´ezier segment.
8. Use the de Casteljau algorithm to evaluate the position of the cubic B´ ezier
curve with its control points at (0,0), (0,1), (1,1) and (1,0) for parameter
valuesu =0 .5 andu =0 .75. Drawing a sketch will help you do this.
9. Use the Cox–de Boor recurrence to derive Equation (15.16).

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
