---
title: "18 Light"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 18 Light

18
Light
In this chapter, we discuss the practical issues of measuring light, usually called
radiometry. The terms that arise in radiometry may at ﬁrst seem strange and have
terminology and notation that may be ha rd to keep straight. However, because
radiometry is so fundamental to computer graphics, it is worth studying radiome-
try until it sinks in. This chapter also covers photometry, which takes radiometric
quantities and scales them to estimate how much “useful” light is present. For
example, a green light may seem twice as bright as a blue light of the same power
because the eye is more sensitive to green light. Photometry attempts to quantify
such distinctions.
18.1 Radiometry
Although we can de ﬁne radiometric units in many systems, we use SI (Interna-
tional System of Units) units. Familiar SI units include the metric units of meter
(m)a n d gram (g). Light is fundamentally a propagating form of energy, so it is
useful to de ﬁne the SI unit of energy, which is the joule (J).
18.1.1 Photons
To aid our intuition, we will describe radiometry in terms of collections of large
numbers of photons, and this section establishes what is meant by a photon in this
479

480 18. Light
context. For the purposes of this chapter, a photon is a quantum of light that has
a position, direction of propagation, and a wavelength λ. Somewhat strangely,
the SI unit used for wavelength is nanometer (nm). This is mainly for historical
reasons, and1nm=10 −9 m. Another unit, the angstrom, is sometimes used, and
one nanometer is ten angstroms. A photon also has a speed c that depends only
on the refractive index n of the medium through which it propagates. Sometimes
the frequency f = c/λis also used for light. This is convenient because unlike
λand c, f does not change when the photon refracts into a medium with a new
refractive index. Another invariant measure is the amount of energy q carried by
a photon, which is given by the following relationship:
q = hf = hc
λ, (18.1)
where h =6 .63 ×10−34 Js is Plank’s Constant. Although these quantities can
be measured in any unit system, we will use SI units whenever possible.
18.1.2 Spectral Energy
If we have a large collection of photons, their total energy Q can be computed
by summing the energy qi of each photon. A reasonable question to ask is “How
is the energy distributed across wavelengths?” An easy way to answer this is to
partition the photons into bins, essentially histogramming them. We then have
an energy associated with an interval. For example, we can count all the energy
between λ= 500nm and λ= 600nm and have it turn out to be 10.2 J, and this
might be denoted q[500,600] = 10.2. If we divided the wavelength interval into
two 50 nm intervals, we might ﬁnd that q[500,550]= 5.2 and q[550,600]= 5.0.
This tells us there was a little more energy in the short wavelength half of the
interval[500,600]. If we divide into 25 nm bins, we might ﬁndq[500,525] = 2.5,
and so on. The nice thing about the system is that it is straightforward. The bad
thing about it is that the choice of the interval size determines the number.
A more commonly used system is to divide the energy by the size of the
interval. So instead of q[500,600] = 10.2we would have
Q
λ[500,600]= 10.2
100 =0 .12J(nm)−1.
This approach is nice, because the size o f the interval has much less impact on
the overall size of the numbers. An immediate idea would be to drive the interval
sizeΔλto zero. This could be awkward, because for a suf ﬁciently small Δλ, Q
λ
will either be zero or huge depending on whether there is a single photon or no

18.1. Radiometry 481
photon in the interval. There are two schools of thought to solve that dilemma.
The ﬁrst is to assume that Δλis small, but not so small that the quantum nature of
light comes into play. The second is to assume that the light is a continuum rather
than individual photons, so a true derivative dQ/dλis appropriate. Both ways of
thinking about it are appropriate and lead to the same computational machinery.
In practice, it seems that most people who measure light prefer small, but ﬁnite,
intervals, because that is what they can measure in the lab. Most people who
do theory or computation prefer in ﬁnitesimal intervals, because that makes the
machinery of calculus available.
The quantityQ
λis called spectral energy, and it is an intensive quantity as op-
posed to an extensive quantity such as energy, length, or mass. Intensive quantities
can be thought of as density functions that tell the density of an extensive quantity
at an in ﬁnitesimal point. For example, the energy Q at a speci ﬁc wavelength is
probably zero, but the spectral energy (energy density) Qλ is a meaningful quan-
tity. A probably more familiar example is that the population of a country may
be 25 million, but the population at a point in that country is meaningless. How-
ever, the population density measured in people per square meter is meaningful,
provided it is measured over large enough areas. Much like with photons, popula-
tion density works best if we pretend that we can view population as a continuum
where population density never becomes granular even when the area is small.
We will follow the convention of graphics where spectral energy is almost al-
ways used, and energy is rarely used. This results in a proliferation of λsubscripts
if “proper” notation is used. Instead, we will drop the subscript and use Q to de-
note spectral energy. This can result in some confusion when people outside of
graphics read graphics papers, so be aware of this standards issue. Y our intuition
about spectral energy might be aided by imagining a measurement device with
a sensor that measures light energy Δq. If you place a colored ﬁlter in front of
the sensor that allows only light in the interval [λ−Δλ/2,λ+Δλ/2], then the
spectral energy at λisQ =Δq/Δλ.
18.1.3 Power
It is useful to estimate a rate of energy production for light sources. This rate is
called power, and it is measured in watts, W , which is another name for joules
per second. This is easiest to understand in a steady state , but because power is
an intensive quantity (a density over time), it is well deﬁ ned even when energy
production is varying over time. The units of power may be more familiar, e.g., a
100-watt light bulb. Such bulbs draw a pproximately 100 J of energy each second.
The power of the light produced will actually be less than 100 W because of

482 18. Light
heat loss, etc., but we can still use this example to help understand more about
photons. For example, we can get a feel for how many photons are produced in a
second by a 100 W light. Suppose the average photon produced has the energy of
a λ= 500nm photon. The frequency of such a photon is
f = c
λ= 3×108 ms−1
500×10−9 m =6 ×1014 s−1.
The energy of that photon is hf ≈4 × 10−19 J. That means a staggering 1020
photons are produced each second, even if the bulb is not very ef ﬁcient. This
explains why simulating a camera with a fast shutter speed and directly simulated
photons is an inef ﬁcient choice for producing images.
As with energy, we are really interested in spectral power measured in
W(nm)
−1. Again, although the formal standard symbol for spectral power is
Φλ, we will use Φwith no subscript for convenience and consistency with most
of the graphics literature. One thing to note is that the spectral power for a light
source is usually a smaller number than the power. For example, if a light emits
ap o w e ro f100 W evenly distributed over wavelengths 400 nm to 800 nm, then
the spectral power will be 100 W/400 nm = 0.25 W(nm)
−1. This is something to
keep in mind if you set the spectral power of light sources by hand for debugging
purposes.
The measurement device for spectral energy in the last section could be mod-
iﬁed by taking a reading with a shutter that is open for a time interval Δt centered
at time t. The spectral power would then be Φ=Δq/(ΔtΔλ).
18.1.4 Irradiance
The quantity irradiance arises naturally if you ask the question “How much light
hits this point?” Of course the answer is “none,” and again we must use a density
function. If the point is on a surface, it is natural to use area to de ﬁne our density
function. We modify the device from the last section to have a ﬁnite ΔA area
sensor that is smaller than the light ﬁeld being measured. The spectral irradiance
H is just the power per unit area ΔΦ/ΔA. Fully expanded this is
H = Δq
ΔA ΔtΔλ. (18.2)
Thus, the full units of irradiance are Jm−2s−1(nm)−1. Note that the SI units for
radiance include inverse-meter-squared for area and inverse-nanometer for wave-
length. This seeming inconsistency (using both nanometer and meter) arises be-
cause of the natural units for area and visible light wavelengths.

18.1. Radiometry 483
When the light is leaving a surface, e.g., when it is reﬂected, the same quantity
as irradiance is called radiant exitance , E. It is useful to have different words
for incident and exitant light, because the same point has potentially different
irradiance and radiant exitance.
18.1.5 Radiance
Although irradiance tells us how much li ght is arriving at a point, it tells us little
about the direction that light comes from. To measure something analogous to
what we see with our eyes, we need to be able to associate “how much light” with
a speciﬁc direction. We can imagine a simple device to measure such a quantity
(Figure 18.1). We use a small irradiance meter and add a conical “baf ﬂer” which
limits light hitting the counter to a range of angles with solid angle Δσ.T h e
response of the detector is as follows:
Not counted
Counted
Δσ
ΔA
Figure 18.1. By adding a
blinder that shows only a small
solid angle Δσto the irradi-
ance detector, we measure ra-
diance.
response = ΔH
Δσ
= Δq
ΔA ΔσΔt Δλ.
This is the spectral radiance of light traveling in space. Again, we will drop the
“spectral” in our discussion and assume that it is implicit.
Radiance is what we are usually co mputing in graphics programs. A won-
derful property of radiance is that it does not vary along a line in space. To see
why this is true, examine the two radiance detectors both looking at a surface
kd
Area=
ΔAσ (kd) 2
Area=
ΔAσd
2
Figure 18.2. The signal a radiance detector receives does not depend on the distance to the surface
being measured. This ﬁgure assumes the detectors are pointing at areas on the surface that are emitting
light in the same way.

484 18. Light
as shown in Figure 18.2. Assume the lines the detectors are looking along are
close enough together that the surface is emitting/re ﬂecting light “the same” in
both of the areas being measured. Because the area of the surface being sampled
is proportional to squared distance, and b ecause the light reaching the detector is
inversely proportional to squared distance, the two detectors should have the same
reading.
It is useful to measure the radiance hitting a surface. We can think of placing
the cone baf ﬂer from the radiance detector at a point on the surface and measur-
ing the irradiance H on the surface originating from directions within the cone
(Figure 18.3). Note that the surface “detector” is not aligned with the cone. For
this reason we need to add a co sine correction term to our de ﬁnition of radiance:
response = ΔH
Δσcosθ
= Δq
ΔAcosθΔσΔt Δλ.
As with irradiance and radiant exitance, it is useful to distinguish between radi-
ance incident at a point on a surface and exitant from that point. Terms for these
θ
ΔA/cosθ
ΔA
Figure 18.3. The irradiance
at the surface as masked by the
cone is smaller than that mea-
sured at the detector by a co-
sine factor.
concepts sometimes used in the graphics literature are surface radiance Ls for
the radiance of (leaving) a surface, and ﬁeld radianceLf for the radiance incident
at a surface. Both require the cosine ter m, because they both correspond to the
conﬁguration in Figure 18.3:
Ls = ΔE
Δσcosθ
Lf = ΔH
Δσcosθ.
Radiance and Other Radiometric Quantities
If we have a surface whose ﬁeld radiance is Lf , then we can derive all of the
other radiometric quantities from it. This is one reason radiance is considered the
“fundamental” radiometric quantity. For example, the irradiance can be expressed
as
H =
∫
all k
Lf(k)c o sθd σ .
n
θ
dσ
k
Figure 18.4. The direction
k has a differential solid angle
dσassociated with it.
This formula has several notational conventions that are common in graphics
that make such formulae opaque to readers not familiar with them (Figure 18.4).
First, k is an incident direction and can be thought of as a unit vector, a direction,
or a (θ,φ) pair in spherical coordinates with respect to the surface normal. The
direction has a differential solid angle dσassociated with it. The ﬁeld radiance is
potentially different for every direction, so we write it as a function L(k).

18.1. Radiometry 485
As an example, we can compute the irradiance H at a surface that has con-
stant ﬁeld radiance Lf in all directions. To integrate, we use a classic spherical
coordinate system and recall that the differential solid angle is
dσ≡sinθd θd φ ,
so the irradiance is
H =
∫ 2π
φ=0
∫ π
2
θ=0
Lf cosθsinθd θd φ
= πLf.
This relation shows us our ﬁrst occurrence of a potentially surprising constant π.
These factors of πoccur frequently in radiometry and are an artifact of how we
chose to measure solid angles, i.e., the area of a unit sphere is a multiple of π
rather than a multiple of one.
Similarly, we can ﬁnd the power hitting a surface by integrating the irradiance
across the surface area:
Φ=
∫
all x
H(x)dA,
where x is a point on the surface, and dA is the differential area associated with
that point. Note that we don’t have special terms or symbols for incoming ver-
sus outgoing power. That distinction does not seem to come up enough to have
encouraged the distinction.
18.1.6 BRDF
Because we are interested in surface app earance, we would like to characterize
how a surface re ﬂects light. At an intuitive level, for any incident light coming
from direction k
i, there is some fraction scattered in a small solid angle near the
outgoing direction ko. There are many ways we could formalize such a concept,
and not surprisingly, the standard way to do so is inspired by building a simple
measurement device. Such a device is shown in Figure 18.5, where a small light
source is positioned in direction k
i as seen from a point on a surface, and a detec-
tor is placed in direction ko. For every directional pair (ki, ko), we take a reading
with the detector.
Now we just have to decide how to measure the strength of the light source
and make our re ﬂection function independent of this strength. For example, if we
replaced the light with a brighter light, w e would not want to think of the surface
as reﬂecting light differently. We could place a radiance meter at the point being

486 18. Light
ki ko
Detector
Light
Figure 18.5. A simple measurement device for directional re ﬂectance. The positions of light and
detector are moved to each possible pair of directions. Note that both ki and ko point away from the
surface to allow reciprocity.
illuminated to measure the light. However, for this to get an accurate reading that
would not depend on the Δσof the detector, we would need the light to subtend a
solid angle bigger than Δσ. Unfortunately, the measurement taken by our roving
radiance detector in direction ko will also count light that comes from points
outside the new detector’s cone. So this does not seem like a practical solution.
Alternatively, we can place an irradian ce meter at the point on the surface be-
ing measured. This will take a reading that does not depend strongly on subtleties
of the light source geometry. This suggests characterizing re ﬂectance as a ratio:
ρ= L
s
H ,
where this fraction ρwill vary with incident and exitant directions ki and ko, H
is the irradiance for light position ki,a n dLs is the surface radiance measured in
direction ko. If we take such a measurement for all direction pairs, we end up
with a 4D function ρ(ki, ko). This function is called the bidirectional reﬂectance
distribution function (BRDF). The BRDF is all we need to know to characterize
the directional properties of how a surface reﬂ ects light.
Directional Hemispherical Reﬂectance
Given a BRDF, it is straightforward to ask, “What fraction of incident light is
reﬂected?” However, the answer is not so easy; the fraction re ﬂected depends on
the directional distribution of incoming light. For this reason, we typically only
set a fraction re ﬂected for a ﬁxed incident direction k
i. This fraction is called the
directional hemispherical reﬂectance. This fraction, R(ki) is deﬁned by
R(ki)= power in all outgoing directions ko
power in a beam from direction ki
.

18.1. Radiometry 487
Note that this quantity is between zero and one for reasons of energy conservation.
If we allow the incident power Φi t oh i to nas m a l la r e aΔA, then the irradiance
is Φi/ΔA. Also, the ratio of the incoming power is just the ratio of the radiant
exitance to irradiance:
R(ki)= E
H.
The radiance in a particular direction resulting from this power is by the de ﬁnition
of BRDF:
L(ko)= Hρ(ki, ko)
= Φi
ΔA.
And from the de ﬁnition of radiance, we also have
L(ko)= ΔE
Δσo cosθo
,
where E is the radiant exitance of the small patch in direction ko.U s i n g t h e s e
two deﬁnitions for radiance we get
Hρ(ki, ko)= ΔE
Δσo cosθo
.
Rearranging terms, we get
ΔE
H = ρ(ki, ko)Δσo cosθo.
This is just the small contribution to E/H that is re ﬂected near the particular ko.
To ﬁnd the total R(ki), we sum over all outgoing ko. In integral form this is
R(ki)=
∫
all ko
ρ(ki, ko)cos θo dσo.
Ideal Diffuse BRDF
An idealized diffuse surface is called Lambertian. Such surfaces are impossible in
nature for thermodynamic reasons, but mathematically they do conserve energy.
The Lambertian BRDF has ρequal to a constant for all angles. This means the
surface will have the same radiance for all viewing angles, and this radiance will
be proportional to the irradiance.
If we compute R(k
i) for a a Lambertian surface with ρ= C we get
R(ki)=
∫
all ko
Ccosθo dσo
=
∫ 2π
φo=0
∫ π/2
θo=0
Ccosθo sinθo dθo dφo
= πC.

488 18. Light
Thus, for a perfectly re ﬂecting Lambertian surface (R =1 ), we have ρ=1 /π,
and for a Lambertian surface where R(ki)= r,w eh a v e
ρ(ki, ko)= r
π.
This is another example where the use of a steradian for the solid angle determines
the normalizing constant and thus introduces factors of π.
18.2 T ransport Equation
With the deﬁnition of BRDF, we can describe the radiance of a surface in terms of
the incoming radiance from all different directions. Because in computer graphics
we can use idealized mathematics that might be impractical to instantiate in the
lab, we can also write the BRDF in terms of radiance only. If we take a small part
of the light with solid angle Δσ
i with radiance Li and “measure” the reﬂ ected
radiance in direction ko due to this small piece of the light, we can compute
a BRDF (Figure 18.6). The irradiance due to the small piece of light is H =
Li cosθiΔσi. Thus the BRDF is
ρ= Lo
Li cosθiΔσi
.
This form can be useful in some situations. Rearranging terms, we can write down
the part of the radiance that is due to light coming from direction k
i:
ΔLo = ρ(ki, ko)Li cosθiΔσi.
If there is light coming from many directions Li(ki), we can sum all of them. In
integral form, with notation for surface and ﬁeld radiance, this is
Ls(ko)=
∫
all ki
ρ(ki, ko)Lf(ki)cos θidσi.
ko
n
–ki
θi
dσ
Figure 18.6. The geometry for the transport equation in its directional form.

18.3. Photometry 489
This is often called the rendering equation in computer graphics (Immel, Cohen,
& Greenberg, 1986).
Sometimes it is useful to write the transport equation in terms of surface radi-
ances only (Kajiya, 1986). Note, that in a closed environment, the ﬁeld radiance
Lf(ki) comes from some surface with surface radiance Ls(−ki)= Lf(ki) (Fig-
ure 18.7). The solid angle subtended by the point x′ in the ﬁgure is given by
Δσi = ΔA′ cosθ′
∥x−x′∥2 ,
where ΔA′ the the area we associate with x′. Substituting for Δσi in terms of
n
n'
θ'
θi
x'
x
ki
–ki
Figure 18.7. The light com-
ing into one point comes from
another point.
ΔA′ suggests the following transport equation:
Ls(x, ko)=
∫
all x’ visible to x
ρ(ki, ko)Ls(x′, x−x′)cos θi cosθ′
∥x−x′∥2 dA′ .
Note that we are using a non-normalized vector x −x′ to indicate the direction
from x′ to x. Also note that we are writing Ls as a function of position and
direction.
The only problem with this new transport equation is that the domain of inte-
gration is awkward. If we introduce a vi sibility function, we can trade off com-
plexity in the domain with complexity in the integrand:
Ls(x, ko)=
∫
all x’
ρ(ki, ko)Ls(x′, x−x′)v(x, x′)cos θi cosθ′
∥x−x′∥2 dA′ ,
where
v(x, x′)=
{
1 if x and x’ are mutually visible,
0 otherwise.
18.3 Photometry
For every spectral radiometric quantity there is a related photometric quantity
that measures how much of that quantity is “useful” to a human observer. Given
a spectral radiometric quantity fr(λ), the related photometric quantity fp is
fp = 683lm
W
∫ 800 nm
λ=380 nm
¯y(λ)fr(λ) dλ,
where ¯y is the luminous efﬁciency function of the human visual system. This
function is zero outside the limits of integration above, so the limits could be
0 and ∞ and f
p would not change. The luminous ef ﬁciency function will be

490 18. Light
discussed in more detail in Chapter 19, but we discuss its general properties here.
The leading constant is to make the de ﬁnition consistent with historical absolute
photometric quantities.
The luminous ef ﬁciency function is not equally sensitive to all wavelengths
(Figure 18.8). For wavelengths below 380 nm (the ultraviolet range), the light is
not visible to humans and thus has a ¯y value of zero. From 380 nm it gradually
increases until λ= 555 nmwhere it peaks. This is a pure green light. Then, it
gradually decreases until it reaches the boundary of the infrared region at 800 nm.
y
380 800 λ555
Figure 18.8. The lumi-
nous ef ﬁciency function ver-
sus wavelength (nm).
The photometric quantity that is most commonly used in graphics is lumi-
nance, the photometric analog of radiance:
Y = 683lm
W
∫ 800 nm
λ=380 nm
¯y(λ)L(λ) dλ.
The symbol Y for luminance comes from colorimetry. Most other ﬁelds use the
symbolL; we will not follow that conventio n because it is too confusing to use L
for both luminance and spectral radiance. Luminance gives one a general idea of
how “bright” something is independent of the adaptation of the viewer. Note that
the black paper under noonday sun is subjectively darker than the lower luminance
white paper under moonlight; reading too much into luminance is dangerous, but
it is a very useful quantity for getting a quantitative feel for relative perceivable
light output. The unit lm stands for lumens. Note that most light bulbs are rated
in terms of the power they consume in watts, and the useful light they produce in
lumens. More ef ﬁcient bulbs produce more of their light where ¯y is large and thus
produce more lumens per watt. A “perfect” light would convert all power into
555nm light and would produce 683 lumens per watt. The units of luminance are
thus(lm/W)(W/(m
2sr)) = lm/(m2sr). The quantity one lumen per steradian is
deﬁned to be one candela (cd), so luminance is usually described in units cd/m2.
Frequently Asked Questions
•What is “intensity”?
The term intensity is used in a variety of contexts and its use varies with both era
and discipline. In practice, it is no longer meaningful as a speci ﬁc radiometric
quantity, but it is useful for intuitive discussion. Most papers that use it do so in
place of radiance.

18.3. Photometry 491
•What is “radiosity”?
The term radiosity is used in place of radiant exitance in some ﬁelds. It is also
sometimes used to describe world-space light transport algorithms.
Notes
A common radiometric quantity not described in this chapter is radiant intensity
(I), which is the spectral power per steradian emitted from an in ﬁnitesimal point
source. It should usually be avoided i n graphics programs because point sources
cause implementation problems. A more rigorous treatment of radiometry can
be found in Analytic Methods for Simulated Light Transport (Arvo, 1995). The
radiometric and photometric terms in this chapter are from the Illumination En-
gineering Society’s standard that is increasingly used by all ﬁelds of science and
engineering (American National Standa rd Institute, 1986). A broader discussion
of radiometric and appearance standards can be found in Principles of Digital
Image Synthesis (Glassner, 1995).
Exercises
1. For a diffuse surface with outgoing radianceL, what is the radiant exitance?
2. What is the total power exiting a diffuse surface with an area of 4m2 and a
radiance of L?
3. If a ﬂuorescent light and an incandescent light both consume 20 watts of
power, why is the ﬂuorescent light usually preferred?

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
**关联概念**: [[辐射度量学]]
**pbrt 对应**: pbrt Ch4 Radiometry, Spectra, and Color
