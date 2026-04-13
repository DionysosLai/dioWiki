---
title: "24 Reflection Models"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 24 Reflection Models

24
Reﬂection Models
As we discussed in Chapter 18, the re ﬂective properties of a surface can be sum-
marized using the BRDF (Nicodemus, Richmond, Hsia, Ginsberg, & Limperis,
1977; Cook & Torrance, 1982). In this chapter, we discuss some of the most
visually important aspects of material properties and a few fairly simple models
that are useful in capturing these properties. There are many BRDF models in
use in graphics, and the models presented here are meant to give just an idea of
nondiffuse BRDFs.
24.1 Real-World Materials
Many real materials have a visible structure at normal viewing distances. For ex-
ample, most carpets have easily visible p ile that contributes to appearance. For
our purposes, such structure is not part of the material property but is, instead, part
of the geometric model. Structure whose details are invisible at normal viewing
distances, but which do determine macros copic material appearance, are part of
the material property. For example, the ﬁbers in paper have a complex appearance
under magni ﬁcation, but they are blurred together into an homogeneous appear-
ance when viewed at arm’s length. This di stinction between microstructure that
is folded into BRDF is somewhat arbitrary and depends on what one de ﬁnes as
“normal” viewing distance and visual acuity, but the distinction has proven quite
useful in practice.
In this section, we de ﬁne some categories of materials. Later in the chapter,
we present re ﬂection models that target each type of material. In the notes at the
627

628 24. Reﬂection Models
end of the chapter, some models that account for more exotic materials are also
discussed.
24.1.1 Smooth Dielectrics and Metals
Dielectrics are clear materials that refr act light; their basic properties were sum-
marized in Chapter 4. Metals reﬂ ect and refract light much like dielectrics, but
they absorb light very, very quickly. Thus , only very thin metal sheets are trans-
parent at all, e.g., the thin gold plating on some glass objects. For a smooth
material, there are only two important properties:
1. How much light is re ﬂected at each incident angle and wavelength.
2. What fraction of light is absorbed as it travels through the material for a
given distance and wavelength.
5%
95%
90%
90%
10%
1%
Glass
Glass
Figure 24.1. The amount of
light reﬂected and transmitted
by glass varies with the angle.
The amount of light transmitted is whatever is not re ﬂected (a result of energy
conservation). For a metal, in practice, we can assume all the light is immediately
absorbed. For a dielectric, the fractio n is determined by the constant used in
Beer’s Law as discussed in Chapter 13.
(1 – R)2
R2(1 – R)2
R4(1 – R)2
R
R(1 – R)2
R3(1 – R)2
Figure 24.2. Light is
repeatedly re ﬂected and re-
fracted by glass, with the frac-
tions of energy shown.
The amount of light re ﬂected is determined by the Fresnel equations as dis-
cussed in Chapter 4. These equations are straightforward, but cumbersome. The
main effect of the Fresnel equations is to increase the re ﬂectance as the incident
angle increases, particularly near grazing angles. This effect works for transmitted
light as well. These ideas are shown diagrammatically in Figure 24.1. Note that
the light is repeatedly reﬂ ected and refracted as shown in Figure 24.2. Usually
only one or two of the re ﬂected images is easily visible.
24.1.2 Rough Surfaces
If a metal or dielectric is roughened to a small degree, but not so small that diffrac-
tion occurs, then we can think of it as a surface with microfacets (Cook & Tor-
rance, 1982). Such surfaces behave specularly at a closer distance, but viewed
at a further distance seem to spread the light out in a distribution. For a metal,
an example of this rough surface might be brushed steel, or the “cloudy” side of
most aluminum foil.
For dielectrics, such as a sheet of gla ss, scratches or other irregular surface
features make the glass blur the re ﬂected and transmitted images that we can
normally see clearly. If the surface is heavily scratched, we call it translucent
rather than transparent. This is a somewhat arbitrary distinction, but it is usually
clear whether we would consider a glass translucent or transparent.

24.2. Implementing Reﬂection Models 629
24.1.3 Diffuse Materials
A material is diffuse if it is matte, i.e., not shiny. Many surfaces we see are diffuse,
such as most stones, paper, and unﬁnished wood. To a ﬁrst approximation, diffuse
surfaces can be approximated with a Lambertian (constant) BRDF. Real diffuse
materials usually become somewhat specular for grazing angles. This is a subtle
effect, but can be important for realism.
24.1.4 T ranslucent Materials
Many thin objects, such as leaves and paper, both transmit and re ﬂect light dif-
fusely. For all practical purposes no clear image is transmitted by these objects.
These surfaces can add a hue shift to the transmitted light. For example, red paper
is red because it ﬁlters out non-red light for light that penetrates a short distance
into the paper, and then scatters back out. The paper also transmits light with a
red hue because the same mechanisms apply, but the transmitted light makes it all
the way through the paper. One implication of this property is that the transmitted
coefﬁcient should be the same in both directions.
24.1.5 Layered Materials
Diﬀuse
Specular
Polish
Diﬀuse substrate
Figure 24.3. Light hit-
ting a layered surface can be
reﬂected specularly, or it can
be transmitted and then scatter
diffusely off the substrate.
Many surfaces are composed of “layers” or are dielectrics with embedded parti-
cles that give the surface a diffuse property (Phong, 1975). The surface of such
materials reﬂects specularly as shown in Figure 24.3, and thus obeys the Fresnel
equations. The light that is transmitted is either absorbed or scattered back up
to the dielectric surface where it may or may not be transmitted. That light that
is transmitted, scattered, and then retransmitted in the opposite direction forms a
diffuse “reﬂection” component.
Note that the diffuse component also is attenuated with the degree of the angle,
because the Fresnel equations cause re ﬂection back into the surface as the angle
increases as shown in Figure 24.4. Thus, instead of a constant diffuse BRDF, one
that vanishes near the grazing angle is more appropriate.
75%90%
50%
10%
Figure 24.4. The light scat-
tered by the substrate is less
and less likely to make it out
of the surface as the angle rel-
ative to the surface normal in-
creases.
24.2 Implementing Reﬂection Models
A BRDF model, as described in Section 18.1.6, will produce a rendering which
is more physically based than the rendering we get from point light sources and
Phong-like models. Unfortunately, real BRDFs are typically quite complicated
and cannot be deduced from ﬁrst principles. Instead, they must either be measured

630 24. Reﬂection Models
and directly approximated from raw data , or they must be crudely approximated
in an empirical fashion. The latter empirical strategy is what is usually done, and
the development of such approximate models is still an area of research. This
section discusses several desirable properties of such empirical models.
First, physical constraints imply two properties of a BRDF model. The ﬁrst
constraint is energy conservation:
for all k
i,R(ki)=
∫
all ko
ρ(ki, ko)cos θo dσo ≤1.
If you send a beam of light at a surface from any direction ki, then the total
amount of light re ﬂected over all directions will be at most the incident amount.
The second physical property we expect all BRDFs to have is reciprocity:
for all ki, ko,ρ(ki, ko)= ρ(ko, ki).
Second, we want a clear separation between diffuse and specular components.
The reason for this is that, although there is a mathematically clean delta function
formulation for ideal specular components, delta functions must be implemented
as special cases in practice. Such special cases are only practical if the BRDF
model clearly indicates what is specular and what is diffuse.
Third, we would like intuitive parameters. For example, one reason the Phong
model has enjoyed such longevity is that its diffuse constant and exponent are
both clearly related to the intuitive properties of the surface, namely surface color
and highlight size.
Finally, we would like the BRDF function to be amenable to Monte Carlo
sampling. Recall from Chapter 14 that an integral can be sampled by N random
points x
i ∼p where p is deﬁned with the same measure as the integral:
∫
f(x)dμ≈ 1
N
N∑
j=1
f(xj)
p(xj).
Recall from Section 18.2 that the surface radiance in direction ko i sg i v e nb ya
transport equation:
Ls(ko)=
∫
all ki
ρ(ki, ko)Lf(ki)cos θidσi.
If we sample directions with pdf p(ki) as discussed in Chapter 23, then we can
approximate the surface radiance with samples:
Ls(ko) ≈ 1
N
N∑
j=1
ρ(kj, ko)Lf(kj)cos θj
p(kj) .

24.3. Specular Reﬂection Models 631
This approximation will converge for any p that is nonzero where the integrand
is nonzero. However, it will only converge well if the integrand is not very large
relative to p. Ideally, p(k) should be approximately shaped like the integrand
ρ(kj, ko)Lf(kj)cos θj . In practice, Lf is complicated, and the best we can ac-
complish is to have p(k) shaped somewhat like ρ(k, ko)Lf(k)cos θ.
For example, if the BRDF is Lambertian, then it is constant and the “ideal”
p(k) is proportional to cosθ. Because the integral of p must be one, we can
deduce the leading constant:
∫
all k with θ<π /2
Ccosθdσ=1 .
This implies that C =1 /π,s ow eh a v e
p(k)= 1
πcosθ.
An acceptably ef ﬁcient implementation will result as long as p doesn’t get too
small when the integrand is nonzero. Thus, the constant pdf will also suf ﬁce:
p(k)= 1
2π.
This emphasizes that many pdfs may be acceptable for a given BRDF model.
24.3 Specular Reﬂection Models
For a metal, we typically specify the re ﬂectance at normal incidence R0(λ).T h e
reﬂectance should vary according to the Fr esnel equations, and a good approxi-
mation is given by (Schlick, 1994a)
R(θ,λ)= R0(λ)+( 1−R0 (λ))(1 −cosθ)5 .
This approximation allows us to just set the normal re ﬂectance of the metal either
from data or by eye.
For a dielectric, the same formula works for re ﬂectance. However, we can set
R0(λ) in terms of the refractive index n(λ):
R0(λ)=
(n(λ)−1
n(λ)+1
)2
.
Typically,n does not vary with wavelength, but for applications where dispersion
is important, n can vary. The refractive indices that are often useful include water
(n =1 .33), glass (n =1 .4 ton =1 .7), and diamond (n =2 .4).

632 24. Reﬂection Models
Figure 24.5. Renderings of polished tiles using coupled model. These images were produced using
a Monte Carlo path tracer. The sampling distribution for the diffuse term is cosθ/π.
24.4 Smooth-Layered Model
Reﬂection in matte/specular materials, such as plastics or polished woods, is gov-
erned by Fresnel equations at the surface and by scattering within the subsurface.
An example of this re ﬂection can be seen in the tiles in the renderings in Fig-
ure 24.5. Note that the blurring in the specular reﬂ ection is mostly vertical due
to the compression of apparent bump spacing in the view direction. This effect
causes the vertically streaked reﬂections seen on lakes on windy days; it can either
be modeled using explicit microgeometry and a simple smooth-surface re ﬂection
model or by a more general model that accounts for this asymmetry.
We could use the traditional Lambertian-specular model for the tiles, which
linearly mixes specular and Lambertian ter ms. In standard radiometric terms, this
can be expressed as
ρ(θ,φ,θ
′,φ′λ)= Rd(λ)
π +Rsρs(θ,φ,θ′,φ′),
where Rd(λ) is the hemispherical re ﬂectance of the matte term, Rs is the specu-
lar reﬂectance, and ρs is the normalized specular BRDF (a weighted Dirac delta
function on the sphere). This equation is a simpli ﬁed version of the BRDF where
Rs is independent of wavelength. The inde pendence of wavelength causes a high-
light that is the color of the luminaire, so a polished rather than a metal appearance
will be achieved. Ward (G. J. Ward, 1992) suggests to set R
d(λ)+ Rs ≤1 in
order to conserve energy. However, such models with constant Rs fail to show
the increase in specularity for steep viewing angles. This is the key point: in the
real world the relative proportions of matte and specular appearance change with
the viewing angle.

24.4. Smooth-Layered Model 633
One way to simulate the change in the matte appearance is to explicitly dampen
Rd(λ) asRs increases (Shirley, 1991):
ρ(θ,φ,θ′,φ′,λ)= Rf(θ)ρs(θ,φ,θ′,φ′)+ Rd(λ)(1−Rf(θ))
π ,
whereRf(θ) is the Fresnel reﬂectance for a polish-air interface. The problem with
this equation is that it is not reciprocal, as can been seen by exchanging θand θ′;
this changes the value of the matte damping factor because of the multiplication
by (1 −Rf(θ)). The specular term, a scaled Dirac delta function, is reciprocal,
but this does not make up for the non-reciprocity of the matte term. Although this
BRDF works well, its lack of reciprocity can cause some rendering methods to
have ill-deﬁned solutions.
We now present a model that produces the matte/specular tradeoff while re-
maining reciprocal and energy conserving. Because the key feature of the new
model is that it couples the matte and specular scaling coef ﬁcients, it is called a
coupled model (Shirley, Smits, Hu, & Lafortune, 1997).
Surfaces which have a glossy appearance are often a clear dielectric, such
as polyurethane or oil, with some subsurface structure. The specular (mirror-
like) component of the re ﬂection is caused by the smooth dielectric surface and
is independent of the structure below thi s surface. The magnitude of this specular
term is governed by the Fresnel equations.
The light that is not re ﬂected specularly at the surface is transmitted through
the surface. There, either it is ab sorbed by the subsurface, or it is reﬂ ected from
a pigment or a subsurface and transmitted back through the surface of the pol-
ish. This transmitted light forms the matte component of re ﬂection. Since the
matte component can only consist of the light that is transmitted, it will naturally
decrease in total magnitude for increasing angle.
To avoid choosing between physically plausible models and models with good
qualitative behavior over a range of incident angles, note that the Fresnel equa-
tions that account for the specular term, R
f(θ), are derived directly from the
physics of the dielectric-ai r interface. Therefore, th e problem must lie in the
matte term. We could use a full-blown simulation of subsurface scattering as
implemented, but this technique is both costly and requires detailed knowledge
of subsurface structure, which is usually neither known nor easily measurable.
Instead, we can modify the matte term to be a simple approximation that captures
the important qualitative angular behavior shown in Figure 24.4.
Let us assume that the matte term is not Lambertian, but instead is some other
function that depends only on θ, θ
′ and λ: ρm(θ,θ′,λ). We discard behavior
that depends on φor φ′ in the interest of simplicity. We try to keep the formu-
las reasonably simple because the physics of the matte term is complicated and

634 24. Reﬂection Models
sometimes requires unknown parameters. We expect the matte term to be close to
constant, and roughly rotationally symmetric (He et al., 1992).
An obvious candidate for the matte component ρm(θ,θ′,λ) that will be re-
ciprocal is the separable form kRm(λ)f(θ)f(θ′) for some constant k and matte
reﬂectance parameter Rm(λ). We could merge k and Rm(λ) into a single term,
but we choose to keep them separated because this makes it more intuitive to set
Rm(λ)—which must be between 0 and 1 for all wavelengths. Separable BRDFs
have been shown to have several computational advantages, thus we use the sep-
arable model:
ρ(θ,φ,θ′,φ′,λ)= Rf(θ)ρs(θ,φ,θ′,φ′)+ kRm(λ)f(θ)f(θ′).
We know that the matte component can only contain energy not re ﬂected in the
surface (specular) component. This means that for Rm(λ)=1 , the incident
and reﬂected energy are the same, which suggests the following constraint on the
BRDF for each incident θand λ:
Rf(θ)+2 πkf(θ)
∫ π
2
0
f(θ′)cos θ′ sinθ′dθ′ =1 . (24.1)
We can see that f(θ) must be proportional to (1 −Rf(θ)). If we assume that
matte components that absorb some energy have the same directional pattern as
this ideal, we get a BRDF of the form
ρ(θ,φ,θ
′,φ′,λ)= Rf(θ)ρs(θ,φ,θ′,φ′)+ kRm(λ)[1−Rf(θ)][1−Rf(θ′)].
We could now insert the full form of the Fresnel equations to get Rf(θ),a n dt h e n
use energy conservation to solve for constraints on k. Instead, we will use the
approximation discussed in Section 24.1.1 We ﬁnd that
f(θ) ∝(1−(1−cosθ)5).
Applying Equation (24.1) yields
k = 21
20π(1−R0). (24.2)
The full coupled BRDF is then
ρ(θ,φ,θ′,φ′,λ)=[
R0 +(1− cosθ)5(1−R0)
]
ρs(θ,φ,θ′,φ′)+
kRm(λ)
[
1−(1−cosθ)5][
1−(1−cosθ′)5]
. (24.3)

24.5. Rough-Layered Model 635
The results of running the coupled model is shown in Figure 24.5. Note that
for the high viewpoint, the specular re ﬂection is almost invisible, but it is clearly
visible in the low-angle photograph image, where the matte behavior is less obvi-
ous.
For reasonable values of refractive indices, R0 is limited to approximately the
range 0.03 to 0.06 (the value R0 =0 .05 was used for Figure 24.5). The value of
Rs in a traditional Phong model is harder to choose, because it typically must be
tuned for viewpoint in static images and tuned for a particular camera sequence
for animations. Thus, the coupled model is easier to use in a “hands-off” mode.
24.5 Rough-Layered Model
The previous model is ﬁne if the surface is smooth. However, if the surface is
not ideal, some spread is needed in the specular component. An extension of the
coupled model to this case is presented here (Ashikhmin & Shirley, 2000). At
a given point on a surface, the BRDF is a function of two directions, one in the
direction toward the light and one in the direction toward the viewer. We would
like to have a BRDF model that works for “common” surfaces, such as metal and
plastic, and has the following characteristics:
1.
Plausible. As deﬁned by Lewis (R. R. Lewis, 1994), this refers to the BRDF
obeying energy conservation and reciprocity.
2. Anisotropy . The material should model simple anisotropy, such as seen on
brushed metals.
3. Intuitive parameters. For material, such as plastics, there should be parame-
tersRd for the substrate and Rs for the normal specular re ﬂectance as well
as two roughness parameters nu and nv .
4. Fresnel behavior. Specularity should increase as the incident angle de-
creases.
5. Non-Lambertian diffuse term. The material should allow for a diffuse term,
but the component should be non-Lambertian to assure energy conservation
in the presence of Fresnel behavior.
6.
Monte Carlo friendliness. There should be some reasonable probability den-
sity function that allows straightforward Monte Carlo sample generation for
the BRDF.

636 24. Reﬂection Models
Light
Detector
k2
k1
n h
Figure 24.6. Geometry of re ﬂection. Note that k1, k2,a n d h share a plane, which usually does not
include n.
A BRDF with these properties is a Fresnel-weighted, Phong-style cosine lobe
model that is anisotropic.
We again decompose the BRDF into a specular component and a diffuse com-
ponent (Figure 24.6). Accordingly, we write our BRDF as the classical sum of
two parts:
ρ(k
1, k2)= ρs(k1, k2)+ ρd(k1, k2), (24.4)
where the ﬁrst term accounts for the specular re ﬂection (this will be presented in
the next section). While it is possible to use the Lambertian BRDF for the diffuse
term ρ
d(k1, k2) in our model, we will discuss a better solution in Section 24.5.2
and how to implement the model in Section 24.5.3. Readers who just want to
implement the model should skip to that section.
24.5.1 Anisotropic Specular BRDF
To model the specular behavior, we use a Phong-style specular lobe but make this
lobe anisotropic and incorporate Fresnel behavior while attempting to preserve
the simplicity of the initial mode. This BRDF is
ρ(k
1, k2)=
√
(nu +1)(nv +1)
8π
(n· h)nu cos2 φ+nv sin2 φ
(h· ki)max(cosθi,cosθo))F(ki·h). (24.5)
Again we use Schlick’s approximation to the Fresnel equation:
F(ki · h)= Rs +(1− Rs)(1−(ki · h))5, (24.6)
whereRs is the material’s reﬂectance for the normal incidence. Because ki · h =
ko · h, this form is reciprocal. We have an empirical model whose terms are

24.5. Rough-Layered Model 637
Figure 24.7. Metallic spheres for exponents 10, 100, 1000, and 10,000 increasing both left to right
and top to bottom.
chosen to enforce energy conservation and reciprocity. A full rationalization for
the terms is given in the paper by Ashikhmin, listed in the chapter notes.
The specular BRDF of Equation (24.5) is useful for representing metallic sur-
faces where the diffuse component of re ﬂection is very small. Figure 24.7 shows
a set of metal spheres on a texture-mapped Lambertian plane. As the values of
parameters n
u and nv change, the appearance of the spheres shift from rough
metal to almost perfect mirror, and from highly anisotropic to the more familiar
Phong-like behavior.
24.5.2 Diffuse T erm for the Anisotropic Phong Model
It is possible to use a Lambertian BRDF together with the anisotropic specular
term; this is done for most models, but it does not necessarily conserve energy. A

638 24. Reﬂection Models
Figure 24.8. Three views for nu = nv = 400 and a diffuse substrate. Note the change in intensity of
the specular reﬂection.
better approach is a simple angle-dependent form of the diffuse component which
accounts for the fact that the amount of energy available for diffuse scattering
varies due to the dependence of the specular term’s total re ﬂectance on the inci-
dent angle. In particular, diffuse color of a surface disappears near the grazing
angle, because the total specular re ﬂectance is close to one. This well-known ef-
fect cannot be reproduced with a Lambertian diffuse term and is therefore missed
by most re ﬂection models.
Following a similar approach to the coupled model, we can ﬁnd a form of the
diffuse term that is compatible with the anisotropic Phong lobe:
ρ
d(k1, k2)= 28Rd
23π(1−Rs)
(
1−
(
1−cosθi
2
)5)(
1−
(
1−cosθo
2
)5)
.
(24.7)
HereRd is the diffuse re ﬂectance for normal incidence, and Rs is the Phong lobe
coefﬁcient. An example using this model is shown in Figure 24.8.
24.5.3 Implementing the Model
Recall that the BRDF is a combination of diffuse and specular components:
ρ(k1, k2)= ρs(k1, k2)+ ρd(k1, k2). (24.8)
The diffuse component is given in Equation (24.7); the specular component is
given in Equation (24.5). It is not necessary to call trigonometric functions to

24.5. Rough-Layered Model 639
compute the exponent, so the specular BRDF can be written:
ρ(k1, k2)=
√
(nu +1)(nv +1)
8π (n· h)
(nu(h·u)2 +nv (h·v)2 )/(1−(hn)2 )
(h·ki )max(cosθi,cosθo ) F(ki · h).
(24.9)
In a Monte Carlo setting, we are interested in the following problem: given k1,
generate samples of k2 with a distribution whose shape is similar to the cosine-
weighted BRDF. Note that greatly undersampling a large value of the integrand is
a serious error, while greatly oversamplin g a small value is acceptable in practice.
The reader can verify that the densities suggested below have this property.
A suitable way to construct a pdf for sampling is to consider the distribution
of half vectors that would give rise to our BRDF. Such a function is
ph(h)=
√
(nu +1)(nv +1)
2π (nh)nu cos2 φ+nv sin2 φ, (24.10)
where the constants are chosen to ensure it is a valid pdf.
We can just use the probability density function ph(h) of Equation (24.10) to
generate a random h. However, to evaluate the rendering equation, we need both
ar eﬂected vector ko and a probability density function p(ko). It is important to
note that if you generate h according to ph(h) and then transform to the resulting
ko:
ko = −ki +2( ki · h)h, (24.11)
the density of the resulting ko is not ph(ko). This is because of the difference in
measures in h and ko. So the actual density p(ko) is
p(ko)= ph(h)
4(kih). (24.12)
Note that in an implementation where the BRDF is known to be this model, the
estimate of the rendering equation is quite simple as many terms cancel out.
It is possible to generate an h vector whose corresponding vector k
o will point
inside the surface, i.e., cosθo < 0. The weight of such a sample should be set
to zero. This situation corresponds to the specular lobe going below the horizon
and is the main source of energy loss in the model. Clearly, this problem becomes
progressively less severe as n
u,nv become larger.
The only thing left now is to describe how to generate h vectors with the pdf
of Equation (24.10). We will start by generating h with its spherical angles in
the range (θ,φ) ∈[0, π
2] ×[0, π
2]. Note that this is only the ﬁrst quadrant of the
hemisphere. Given two random numbers (ξ1,ξ2) uniformly distributed in [0,1],
we can choose
φ=a r c t a n
(√
nu +1
nv +1 tan
(πξ1
2
))
, (24.13)

640 24. Reﬂection Models
and then use this value of φto obtain θaccording to
cosθ=( 1−ξ2)1/(nu cos2 φ+nv sin2 φ+1). (24.14)
To sample the entire hemisphere, we use the standard manipulation where ξ1 is
mapped to one of four possible functions depending on whether it is in [0,0.25),
[0.25,0.5), [0.5,0.75),o r [0.75,1.0). For example, for ξ1 ∈[0.25,0.5), ﬁnd
φ(1 −4(0.5 −ξ1)) via Equation (24.13), and then “ ﬂip” it about the φ= π/2
axis. This ensures full coverage and strati ﬁcation.
For the diffuse term, use a simpler appr oach and generate s amples according
to a cosine distribution. This is suf ﬁciently close to the complete diffuse BRDF
to substantially reduce variance of the Monte Carlo estimation.
Frequently Asked Questions
•My images look too smooth, even with a complex BRDF . What am I do-
ing wrong?
BRDFs only capture subpixel detail that is too small to be resolved by the eye.
Most real surfaces also have some small variations, such as the wrinkles in skin,
that can be seen. If you want true realism, some sort of texture or displacement
map is needed.
•How do I integrate the BRDF with texture mapping?
Texture mapping can be used to control a ny parameter on a surface. So any kinds
of colors or control parameters used by a BRDF should be programmable.
•I have very pretty code except for my material class. What am I doing
wrong?
Y ou are probably doing nothing wrong. Material classes tend to be the ugly thing
in everybody’s programs. If you ﬁnd a nice way to deal with it, please let me
know! My own code uses a shader architecture (Hanrahan & Lawson, 1990)
which makes the material include much of the rendering algorithm.
Notes
There are many BRDF models described in the literature, and only a few of
them have been described here. Others include (Cook & Torrance, 1982; He

24.5. Rough-Layered Model 641
et al., 1992; G. J. Ward, 1992; Oren & Nayar, 1994; Schlick, 1994a; Lafor-
tune, Foo, Torrance, & Greenberg, 1997; Stam, 1999; Ashikhmin, Premo ˇze, &
Shirley, 2000; Ershov, Kolchin, & Myszkowski, 2001; Matusik, P ﬁster, Brand, &
McMillan, 2003; Lawrence, Rusinkiewicz, & Ramamoorthi, 2004; Stark, Arvo,
& Smits, 2005). The desired characteristics of BRDF models is discussed in Mak-
ing Shaders More Physically Plausible (R. R. Lewis, 1994).
Exercises
1. Suppose that instead of the Lambertian BRDF we used a BRDF of the form
Ccos
a θi. What must C be to conserve energy?
2. The BRDF in Exercise 1 is not reciprocal. Can you modify it to be recipro-
cal?
3. Something like a highway sign is a retroreﬂector. This means that the
BRDF is large when ki and ko are near each other. Make a model inspired
by the Phong model that captures retrore ﬂection behavior while being re-
ciprocal and conserving energy.

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
**关联概念**: [[反射模型]] · [[BSDF]]
**pbrt 对应**: pbrt Ch9 Reflection Models
