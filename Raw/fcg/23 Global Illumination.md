---
title: "23 Global Illumination"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 23 Global Illumination

23
Global Illumination
Many surfaces in the real world receive most or all of their incident light from
other re ﬂective surfaces. This is often called indirect lighting or mutual illumi-
nation. For example, the ceilings of most rooms receive little or no illumination
directly from luminaires (light-emitting objects). The direct and indirect compo-
nents of illumination are shown in Figure 23.1.
Although accounting for the interre ﬂection of light between surfaces is
straightforward, it is potentially costly because all surfaces may re ﬂect any given
surface, resulting in as many as O(N
2) interactions for N surfaces. Because the
entire global database of objects may illu minate any given object, accounting for
indirect illumination is often called the global illumination problem.
There is a rich and complex literature on solving the global illumination prob-
lem (e.g., Appel, 1968; Goral, Torrance, Greenberg, & Battaile, 1984; Cook et al.,
Figure 23.1. In the left and middle images, the indirect and direct lighting, respectively, are
separated out. On the right, the sum of both com ponents is shown. Global illumination algorithms
account for both the direct and the indirect lighting.
613

614 23. Global Illumination
1984; Immel et al., 1986; Kajiya, 1986; Malley, 1988). In this chapter, we discuss
two algorithms as examples: particle tracing and path tracing. The ﬁrst is useful
for walkthrough applications such as maze games, and as a component of batch
rendering. The second is useful for realistic batch rendering. Then we discuss
separating out “direct” lighting where light takes exactly once bounce between
luminaire and camera.
23.1 Particle T racing for Lambertian Scenes
Recall the transport equation from Section 18.2:
Ls(ko)=
∫
all ki
ρ(ki, ko)Lf(ki)cos θidσi.
The geometry for this equation is shown in Figure 23.2. When the illuminated
point is Lambertian, this equation reduces to:
Ls = R
π
∫
all ki
Lf(ki)cos θidσi,
where R is the diffuse re ﬂectance. One way to approximate the solution to this
equation is to use ﬁnite element methods. First, we break the scene into N sur-
faces each with unknown surface radiance Li,r e ﬂectance Ri, and emitted radi-
ance Ei. This results in the set of N simultaneous linear equations
Li = Ei + Ri
π
N∑
j=1
kijLj,
where kij is a constant related to the original integral representation. We then
solve this set of linear equations, and we can render N constant-colored polygons.
This ﬁnite element approach is often called radiosity.
ko
n
–ki
θi
dσ
Figure 23.2. The geometry for the transport equation in its directional form.

23.1. Particle T racing for Lambertian Scenes 615
An alternative method to radiosity is to use a statistical simulation approach by
randomly following light “particles” from the luminaire through the environment.
This is a type of particle tracing. There are many algorithms that use some form
of particle tracing; we will discuss a form of particle tracing that deposits light
in the textures on triangles. First, we review some basic radiometric relations.
The radiance L of a Lambertian surface with area A is directly proportional to the
incident power per unit area:
L = Φ
πA, (23.1)
where Φis the outgoing power from the surface. Note that in this discussion, all
radiometric quantities are either spectral or RGB, depending on the implementa-
tion. If the surface has emitted power Φe, incident power Φi,a n dr eﬂectance R,
then this equation becomes
L = Φe +RΦi
πA .
If we are given a model with Φe andR speciﬁed for each triangle, we can proceed
luminaire by luminaire, ﬁring power in the form of particles from each luminaire.
We associate a texture map with each triangle to store accumulated radiance, with
all texels initialized to
L = Φe
πA.
If a given triangle has area A and nt texels, and it is hit by a particle carrying
powerφ, then the radiance of that texel is incremented by
ΔL = ntφ
πA.
Once a particle hits a surface, we increment the radiance of the texel it hits, prob-
abilistically decide whether to re ﬂect the particle, and if we re ﬂect it we choose a
direction and adjust its power.
Note that we want the particle to term inate at some point. For each surface we
can assign a re ﬂection probability p to each surface interaction. A natural choice
would be to let p = R as it is with light in nature. The particle would then scatter
around the environment, not losing or gaining any energy until it is absorbed.
This approach works well when the particles carry a single wavelength (Walter,
Hubbard, Shirley, & Greenberg, 1997). However, when a spectrum or RGB triple
is carried by the ray as is often implemented (Jensen, 2001), there is no single
R and some compromise for the value of p should be chosen. The power φ
′ for
reﬂected particles should be adjusted to acc ount for the possible extinction of the
particles:
φ′ = Rφ
p .

616 23. Global Illumination
R = (0.5, 0.5, 0.5)
φ = (10, 10, 20)
φ = (15, 15, 30)φ = (6, 3, 6)
R = (0.2, 0.1, 0.1)
R = (0.75, 0.75, 0.75)
Figure 23.3. The path of a particle that survives with probability 0.5 and is absorbed at the last
intersection. The RGB power is shown for each path segment.
Note that p can be set to any positive constant less than one, and that this constant
can be different for each interaction. When p>R for a given wavelength, the
particle will gain power at that wavelength, and when p<R it will lose power
at that wavelength. The case where it gains power will not interfere with conver-
gence because the particle will stop scattering and be terminated at some point as
long as p< 1. For the remainder of this discussion we set p =0 .5. The path of a
single particle in such a system is shown in Figure 23.3.
A key part to this algorithm is that we scatter the light with an appropriate
distribution for Lambertian surfaces. As discussed in Section 14.4.1, we can ﬁnd
a vector with a cosine (Lambertian) distribution by transforming two canonical
random numbers (ξ
1,ξ2) as follows:
a =
(
cos(2πξ1)
√
ξ2,sin(2πξ1)
√
ξ2,
√
1−ξ2
)
. (23.2)
Note that this assumes the normal vector is parallel to the z-axis. For a triangle,
we must establish an orthonormal basis with w parallel to the normal vector. We
can accomplish this as follows:
w = n
∥n∥ ,
u = p1 −p0
∥p1 −p0∥ ,
v = w× u,

23.2. Path T racing 617
where pi are the vertices of the triangle. Then, by de ﬁnition, our vector in the
appropriate coordinates is
a =c o s( 2πξ1)
√
ξ2u+sin(2 πξ1)
√
ξ2v+
√
1−ξ2w. (23.3)
In pseudocode our algorithm for p =0 .5 and one luminaire is:
for(Each of n particles) do
RGBphi =Φ/n
compute uniform random point a on luminaire
compute random direction b with cosine density
done = false
while not done do
if(ray a+tb hits at some point c) then
addntRφ/(πA) to appropriate texel
if(ξ1 > 0.5) then
φ=2 Rφ
a = c
b = random direction with cosine density
else
done = true
Here ξi are canonical random numbers. Once this code has run, the texture maps
store the radiance of each triangle and can be rendered directly for any viewpoint
with no additional computation.
23.2 Path T racing
While particle tracing is well suited to precomputation of the radiances of diffuse
scenes, it is problematic for creatin g images of scenes with general BRDFs or
scenes that contain many objects. The mos t straightforward way to create images
of such scenes is to use path tracing (Kajiya, 1986). This is a probabilistic method
that sends rays from the eye and traces them back to the light. Often path tracing
is used only to compute the indirect lighting. Here we will present it in a way
that captures all lighting, which can be inef ﬁcient. This is sometimes called brute
force path tracing. In Section 23.3, more ef ﬁcient techniques for direct lighting
can be added.
In path tracing, we start with the full transport equation:
L
s(ko)= Le(ko)+
∫
all ki
ρ(ki, ko)Lf(ki)cos θidσi.

618 23. Global Illumination
Figure 23.4. In path tracing, a ray is followed through a pixel from the eye and scattered through
the scene until it hits a luminaire.
We use Monte Carlo integration to approximate the solution to this equation for
each viewing ray. Recall from Section 14.3, that we can use random samples to
approximate an integral:
∫
x∈S
g(x)dμ≈ 1
N
N∑
i=1
g(xi)
p(xi),
where the xi are random points with probability density function p. If we apply
this directly to the transport equation with N =1 we get
Ls(ko) ≈Le(ko)+ ρ(ki, ko)Lf(ki)cos θidσi
p(ki) .
So, if we have a way to select random directions ki with a known density p,w e
can get an estimate. The catch is that Lf(ki) is itself an unknown. Fortunately,
we can apply recursion and use a statistical estimate for Lf(ki) by sending a ray
in that direction to ﬁnd the surface seen in that direction. We end when we hit a
luminaire and Le is nonzero (Figure 23.4). This method assumes lights have zero
reﬂectance, or we would continue to recurse.
In the case of a Lambertian BRDF ( ρ= R/π), we can use a cosine density
function:
p(ki)= cosθi
π .

23.3. Accurate Direct Lighting 619
A direction with this density can be chos en according to Equation (23.3). This
allows some cancellation of cosine terms in our estimate:
Ls(ko) ≈Le(ko)+ RLf(ki).
In pseudocode, such a path tracer for Lambertian surfaces would operate just
like the ray tracers described in Chapter 4, but the raycolor function would be
modiﬁed:
RGB raycolor(ray a+tb, int depth)
if(ray hits at some point c) then
RGBc = Le(−b)
if(depth < maxdepth) then
compute random direction d
returnc+R raycolor(c+sd,d e p t h+1)
else
return background color
This will result in a very noisy image unless either large luminaires or very large
numbers of samples are used. Note the color of the luminaires must be well above
one (sometimes thousands or tens of thousands) to make the surfaces have ﬁnal
colors near one, because only those rays tha t hit a luminaire by chance will make
a contribution, and most rays will contribute only a color near zero. To generate
the random direction d, we use the same technique as we do in particle tracing
(see Equation (23.2)).
In the general case, we might want to use spectral colors or use a more general
BRDF. In practice, we should have the material class contain member functions
to compute a random direction as well as compute the p associated with that
direction. This way materials can be added transparently to an implementation.
23.3 Accurate Direct Lighting
This section presents a more physically based method of direct lighting than
Chapter 10. These methods will be usef ul in making global illumination algo-
rithms more ef ﬁcient. The key idea is to send shadow rays to the luminaires as
described in Chapter 4, but to do so with careful bookkeeping based on the trans-
port equation from the previous chapter. The global illumination algorithms can
be adjusted to make sure they compute the direct component exactly once. For
example, in particle tracing, particles coming directly from the luminaire would
not be logged, so the particles would only encode indirect lighting. This makes

620 23. Global Illumination
nice looking shadows much more ef ﬁciently than computing direct lighting in the
context of global illumination.
23.3.1 Mathematical Framework
To calculate the direct light from one luminaire (light emitting object) onto a non-
emitting surface, we solve a form of the transport equation from Section 18.2:
Ls(x, ko)=
∫
all x′
ρ(ki, ko)Le(x′,−ki)v(x, x′)cos θi cosθ′
∥x−x′∥2 dA′ . (23.4)
Recall that Le is the emitted radiance of the source, v is a visibility function that
is equal to 1 if x “sees” x′ and zero otherwise, and the other variables are as
illustrated in Figure 23.5.
If we are to sample Equation (23.4) using Monte Carlo integration, we need
ko
x
n
θi
θ´
n´ x´
ki
–ki
Figure 23.5. The direct
lighting terms for Equa-
tion (23.4).
to pick a random point x′ on the surface of the luminaire with density function p
(so x′ ∼p). Just plugging into Equation (14.5) with one sample yields
Ls(x, ko) ≈ρ(ki, ko)Le(x′,−ki)v(x, x′)cos θi cosθ′
p(x′)∥x−x′∥2 . (23.5)
If we pick a uniform random point on the luminaire, then p =1 /A,w h e r eA is
the area of the luminaire. This gives
Ls(x, ko) ≈ρ(ki, ko)Le(x′,−ki)v(x, x′)Acosθi cosθ′
∥x−x′∥2 . (23.6)
We can use Equation (23.6) to sample planar (e.g., rectangular) luminaires in a
straightforward fashion. We simply pick a random point on each luminaire.
The code for one luminaire is:
color directLight( x, ko, n)
pick random point x′ with normal vector n′ on light
d = x′ −x
ki = d/∥d∥
if (ray x+td has no hits for t< 1−ϵ) then
returnρ(ki, ko)Le(x′,−ki)(n· d)(−n′ · d)/∥d∥4
else
return 0
The above code needs some extra tests such as clamping the cosines to zero if
they are negative. Note that the term ∥d∥4 comes from the distance squared term

23.3. Accurate Direct Lighting 621
Figure 23.6. V arious soft shadows on a backlit sphere with a square and an area light source. Top:
1 sample. Bottom: 100 samples. Note that the shape of the light source is less important than its size
in determining shadow appearance.
and the two cosines, e.g., n · d = ∥d∥cosθbecause d is not necessarily a unit
vector.
Several examples of soft shadows are shown in Figure 23.6.
23.3.2 Sampling a Spherical Luminaire
Though a sphere with center c and radiusR can be sampled using Equation (23.6),
this sampling will yield a very noisy im age because many samples will be on the
back of the sphere, and the cosθ′ term varies so much. Instead, we can use a more
complexp(x′) to reduce noise.
The ﬁrst nonuniform density we might try is p(x′) ∝cosθ′. This turns out to
be just as complicated as sampling with p(x′) ∝cosθ′/∥x′ −x∥2, so we instead
discuss that here. We observe that sampling on the luminaire this way is the

622 23. Global Illumination
u
x
v
n
n´
x´
c
R
θi
θ´
Luminaire
αmax
α
ki
w
Figure 23.7. Geometry for direct lighting at point x from a spherical luminaire.
same as using a constant density function q(ki)= const de ﬁned in the space of
directions subtended by the luminaire as seen from x. We now use a coordinate
system de ﬁned with x at the origin, and a right-handed orthonormal basis with
w =( c −x)/∥c −x∥,a n d v =( w × n)/∥(w × n)∥ (see Figure 23.7). We
also de ﬁne (α,φ) to be the azimuthal and polar angles with respect to the uvw
coordinate system.
The maximum αthat includes the spherical luminaire is given by
αmax =a r c s i n
( R
∥x−c∥
)
= arccos
√
1−
( R
∥x−c∥
)2
.
Thus, a uniform density (with respect to solid angle) within the cone of directions
subtended by the sphere is just the reciprocal of the solid angle 2π(1−cosαmax)
subtended by the sphere:
q(ki)= 1
2π
(
1−
√
1−
(
R
∥x−c∥
)2
).
And we get
[
cosα
φ
]
=
⎡
⎣1−ξ1 +ξ1
√
1−
(
R
∥x−c∥
)2
2πξ2
⎤
⎦.
This gives us the direction k
i.T o ﬁnd the actual point, we need to ﬁnd the ﬁrst
point on the sphere in that direction. The ray in that direction is just ( x + tki),

23.3. Accurate Direct Lighting 623
Figure 23.8. A sphere with L e = 1 touching a sphere of re ﬂectance 1. Where the two spheres
touch, the re ﬂective sphere should have L(x′) = 1 . Left: 1 sample. Middle: 100 samples. Right: 100
samples, close-up.
where ki is given by
ki =
⎡
⎣
ux vx wx
uy vy wy
uz vz wz
⎤
⎦
⎡
⎣
cosφsinα
sinφsinα
cosα
⎤
⎦.
We must also calculate p(x
′), the probability density function with respect to the
area measure (recall that the density function q is de ﬁned in solid angle space).
Since we know that q is a valid probability density function using the ωmeasure,
and we know that dΩ= dA(x′)cos θ′/∥x′ −x∥2, we can relate any probability
density function q(ki) with its associated probability density function p(x′):
q(ki)= p(x′)cos θ′
∥x′ −x∥2 . (23.7)
So we can solve for p(x′):
p(x′)= cosθ′
2π∥x′ −x∥2
(
1−
√
1−
(
R
∥x−c∥
)2
).
A good debugging case for this is shown in Figure 23.8.
23.3.3 Nondiffuse Luminaries
There is no reason the luminance of the luminaire cannot vary with both direction
and position. For example, it can vary with position if the luminaire is a television.
It can vary with direction for car headlights and other directional sources. Little
in our analysis need change from the previous sections, except that L
e(x′) must
change to Le(x′,−ki). The simplest way to vary the intensity with direction is to
use a Phong-like pattern with respect to the normal vector n′. To avoid using an
exponent in the term for the total light output, we can use the form
Le(x′,−ki)= (n+1)E(x′)
2π cos(n−1)θ′,

624 23. Global Illumination
whereE(x′) is the radiant exitance (power per unit area) at point x′,a n dn is the
Phong exponent. Y ou get a diffuse light for n =1 . If the light is nonuniform
across its area, e.g., as a television set is, then E will not be a constant.
Frequently Asked Questions
•My pixel values are no longer in some sensible zero-to-one range. What
should I display?
Y ou should use one of the tone reproduction techniques described in Chapter 21.
•What global illumination techniques are used in practice?
For batch rendering of complex scenes, path tracing with one level of re ﬂection
is often used. Path tracing is often augmented with a particle tracing prepro-
cess as described in Jensen’s book in the chapter notes. For walkthrough games,
some form of world-space preprocess is often used, such as the particle tracing
described in this chapter. For scenes w ith very complicated specular transport, an
elegant but involved method, Metropolis Light Transport (V each & Guibas, 1997)
may be the best choice.
•How does the ambient component relate to global illumination?
For diffuse scenes, the radiance of a surface is proportional to the product of the
irradiance at the surface and the re ﬂectance of the surface. The ambient com-
ponent is just an approximation to the irradiance scaled by the inverse of π.S o
although it is a crude approximation, there can be some methodology to guess-
ing it (M. F. Cohen, Chen, Wallace, & Greenberg, 1988), and it is probably more
accurate than doing nothing, i.e., using zero for the ambient term. Because the
indirect irradiance can vary widely with in a scene, using a different constant for
each surface can be used for better results ra ther than using a global ambient term.
•Why do most algorithms compute direct lighting using traditional ray
tracing?
Although global illumination algorithms au tomatically compute direct lighting,
and it is, in fact, slightly more compli cated to make them compute only indi-
rect lighting, it is usually faster to compute direct lighting separately. There are
three reasons for this. First, indirect lighting tends to be smooth compared to

23.3. Accurate Direct Lighting 625
Figure 23.9. A comparison between a rendering and a photo. Image courtesy Sumant Pattanaik
and the Cornell Program of Computer Graphics.
direct lighting (see Figure 23.1) so coarser representations can be used, e.g., low-
resolution texture maps for particle tracing. The second reason is that light sources
tend to be small, and it is rare to hit them by chance in a “from the eye” method
such as path tracing, while direct shadow rays are ef ﬁcient. The third reason is
that direct lighting allows stratiﬁed sampling, so it converges rapidly compared to
unstratiﬁed sampling. The issue of strati ﬁcation is the reason that shadow rays are
used in Metropolis Light Transport despite the stability of its default technique for
dealing with direct lighting as just one type of path to handle.
•How artiﬁcial is it to assume ideal diffuse and specular behavior?
For environments that have only matte an d mirrored surfaces, the Lambertian/
specular assumption works well. A comparison between a rendering using that
assumption and a photograph is shown in Figure 23.9.
•How many shadow rays are needed per pixel?
Typically between 16 and 400. Using narrow penumbra, a large ambient term (or
a large indirect component), and a masking texture (Ferwerda, Shirley, Pattanaik,
& Greenberg, 1997) can reduce the number needed.
•How do I sample something like a ﬁlament with a metal reﬂector where
much of the light is reﬂected from the ﬁlament?
Typically, the whole light is replaced by a simple source that approximates its
aggregate behavior. For viewing rays, the complicated source is used. So a car

626 23. Global Illumination
headlight would look complex to the viewer, but the lighting code might see sim-
ple disk-shaped lights.
•Isn’t something like the sky a luminaire?
Y es, and you can treat it as one. However, such large light sources may not be
helped by direct lighting; the brute-force techniques are likely to work better.
Notes
Global illumination has its roots in the ﬁelds of heat transfer and illumination en-
gineering as documented in Radiosity: A Programmer’s Perspective (Ashdown,
1994). Other good books related to g lobal illumination include Radiosity and
Global Illumination (M. F. Cohen & Wallace, 1993), Radiosity and Realistic
Image Synthesis (Sillion & Puech, 1994), Principles of Digital Image Synthe-
sis (Glassner, 1995), Realistic Image Synthesis Using Photon Mapping (Jensen,
2001), Advanced Global Illumination (Dutr´e, Bala, & Bekaert, 2002), and Phys-
ically Based Rendering (Pharr & Humphreys, 2004). The probabilistic methods
discussed in this chapter are from Monte Carlo T echniques for Direct Lighting
Calculations (Shirley, Wang, & Zimmerman, 1996).
Exercises
1. For a closed environment, where every surface is a diffuse re ﬂector and
emittor with reﬂectance R and emitted radiance E, what is the total radi-
ance at each point? Hint: for R =0 .5 and E =0 .25 the answer is 0.5.
This is an excellent debugging case.
2. Using the de ﬁnitions from Chapter 18, verify Equation (23.1).
3. If we want to render a typically sized room with textures at centimeter-
square resolution, approximately how many particles should we send to get
an average of about 1000 hits per texel?
4. Develop a method to take random samples with uniform density from a
disk.
5. Develop a method to take random samples with uniform density from a
triangle.
6. Develop a method to take uniform random samples on a “sky dome” (the
inside of a hemisphere).

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
**关联概念**: [[光传输方程]] · [[路径追踪]] · [[蒙特卡洛积分]]
**pbrt 对应**: pbrt Ch13-14 Light Transport
