---
title: "19 Color"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 19 Color

19
Erik Reinhard and Garrett Johnson
Color
Photons are the carriers of optical information. They propagate through media
taking on properties associated with waves. At surface boundaries they inter-
act with matter, behaving more as particles. They can also be absorbed by the
retina, where the information they carry is transcoded into electrical signals that
are subsequently processed by the brain. It is only there that a sensation of color
is generated.
As a consequence, the study of color in all its guises touches upon several
different ﬁelds: physics for the propagation of light through space, chemistry for
its interaction with matter, and neuroscience and psychology for aspects relating
to perception and cognition of color (Reinhard et al., 2008).
In computer graphics, we traditionally take a simpli ﬁed view of how light
propagates through space. Photons travel al ong straight paths until they hit a sur-
face boundary and are then reﬂ ected according to a re ﬂection function of some
sort. A single photon will carry a certain amount of energy, which is represented
by its wavelength. Thus, a photon will have only one wavelength. The relation-
ship between its wavelength λand the amount of energy it carries ( ΔE)i sg i v e n
by
λΔE = 1239.9,
where ΔE is measured in electron volts (eV).
In computer graphics, it is not very ef ﬁcient to simulate single photons; in-
stead large collections of them are simul ated at the same time. If we take a very
large number of photons, each carrying a possibly different amount of energy,
493

494 19. Color
300 400 500 600 700 800 900
0
20
40
60
80
100
120
Wavelength (λ)
Relative Radiant Power
Figure 19.1. A spectrum describes how much energy is available at each wavelength λ,h e r e
measured as relative radiant power. This speci ﬁc spectrum represents average daylight.
then together they represent a spectrum. A spectrum can be thought of as a graph
where the number of photons is plotted a gainst wavelength. Because two photons
of the same wavelength carry twice as much energy as a single photon of that
wavelength, this graph can also be seen as a plot of energy against wavelength.
An example of a spectrum is shown in Figure 19.1. The range of wavelengths to
which humans are sensitive is roughly between 380 and 800 nanometers (nm).
When simulating light, it would there fore be possible to trace rays that each
carry a spectrum. A renderer that accomplishes this is normally called a spectral
renderer. From preceding chapters, it should be clear that we are not normally
going through the expense of building spectral renderers. Instead, we replace
spectra with representations that typically use red, green, and blue components.
The reason that this is possible at all has to do with human vision and will be
discussed later in this chapter.
Simulating light by tracing rays takes care of the physics of light, although it
should be noted that several properties of light, including, for instance, polariza-
tion, diffraction, and interference, are not modeled in this manner.
At surface boundaries, we normally model what happens with light by means
of a re ﬂectance function. These functions can be measured directly by means
of gonioreﬂectometers, leading to a large amount of tabled data, which can be
more compactly represented by various di fferent functions. Nonetheless, these
reﬂectance functions are empirical in nature, i.e., they abstract away the chemistry
that happens when a photon is absorbed and re-emitted by an electron. Thus,
reﬂectance functions are useful for modeling in computer graphics, but do not

19.1. Colorimetry 495
offer an explanation as to why certain wavelengths of light are absorbed and others
are re ﬂected. We can therefore not use re ﬂectance functions to explain why the
light reﬂected off a banana has a spectral composition that appears to us as yellow.
For that, we would have to study molecular orbital theory, a topic beyond the
scope of this book.
Finally, when light reaches the retina, it is transcoded into electrical signals
that are propagated to the brain. A large part of the brain is devoted to processing
visual signals, part of which gives rise to the sensation of color. Thus, even if
we know the spectrum of light that is re ﬂected off a banana, we do not know yet
why humans associate the term “yellow” with it. Moreover, as we will ﬁnd out in
the remainder of this chapter, our perception of color is vastly more complicated
than it would seem at ﬁrst glance. It changes with illumination, varies between
observers, and varies within an observer over time.
In other words, the spectrum of light coming off a banana is perceived in the
context of an environment. To predict how an observer perceives a “banana spec-
trum” requires knowledge of the environment that contains the banana as well as
the observer’s environment. In many instances, these two environments are the
same. However, when we are displaying a photograph of a banana on a moni-
tor, then these two environments will be different. As human visual perception
depends on the environment the observer is in, it may perceive the banana in the
photograph differently from how an observer directly looking at the banana would
perceive it. This has a signi ﬁcant impact on how we should deal with color and
illustrates the complexities associated with color.
To emphasize the crucial role that human vision plays, we only have to look
at the de ﬁnition of color: “Color is the aspect of visual perception by which an
observer may distinguish differences between two structure-free ﬁelds of view of
the same size and shape, such as may be caused by differences in the spectral
composition of the radiant energy concerned in the observation” (Wyszecki &
Stiles, 2000). In essence, without a human observer there is no color.
Luckily, much of what we know about color can be quanti ﬁed, so that we
can carry out computations to correct for the idiosyncrasies of human vision and
thereby display images that will appear to observers the way the designer of those
images intended. This chapter contains the theory and mathematics required to
do so.
19.1 Colorimetry
Colorimetry is the science of color measurement and description. Since color
is ultimately a human response, colo r measurement should begin with human

496 19. Color
observation. The photodetectors in the human retina consist of rods and cones.
The rods are highly sensitive and come into play in low-light conditions. Under
normal lighting conditions, the cones are operational, mediating human vision.
There are three cone types and together they are primarily responsible for color
vision.
Although it may be possible to directly record the electrical output of cones
while some visual stimulus is being presented, such a procedure would be inva-
sive, while at the same time ignoring the sometimes substantial differences be-
tween observers. Moreover, much of the measurement of color was developed
well before such direct recording techniques were available.
The alternative is to measure color by means of measuring the human re-
sponse to patches of color. This leads to color matching experiments, which will
be described later in this section. Carrying out these experiments have resulted in
several standardized observers, which can be thought of as statistical approxima-
tions of actual human observers. First, however, we need to describe some of the
assumptions underlying the possibility of color matching, which are summarized
by Grassmann’s laws.
19.1.1 Grassmann’s Laws
Given that humans have three different cone types, the experimental laws of
color matching can be summed up as the tr ichromatic generalization (Wyszecki
& Stiles, 2000), which states that any color stimulus can be matched completely
with an additive mixture of three appropriately modulated color sources. This
feature of color is often used in practice, for instance by televisions and monitors
which reproduce many different colors by adding a mixture of red, green, and
blue light for each pixel. It is also the reason that renderers can be built using
only three values to describe each color.
The trichromatic generalization allows us to make color matches between any
given stimulus and an additive mixture of three other color stimuli. Hermann
Grassmann was the ﬁrst to describe the algebraic rules to which color match-
ing adheres. They are known as Grassma nn’s laws of additive color matching
(Grassmann, 1853) and are the following:

Symmetry law. If color stimulus A matches color stimulus B,t h e n B
matchesA.
 T ransitive law.IfA matches B and B matchesC,t h e nA matches C.
 Proportionality law. If A matches B,t h e nαAmatches αB,w h e r eαis a
positive scale factor.

19.1. Colorimetry 497
 Additivity law. If A matches B, C matches D,a n dA+C matches B +D,
then it follows that A+D matches B +C.
The additivity law forms the basis for color matching and colorimetry as a
whole.
19.1.2 Cone Responses
Each cone type is sensitive to a range of wavelengths, spanning most of the full
visible range. However, sensitivity to wavelengths is not evenly distributed, but
contains a peak wavelength at which sensitivity is greatest. The location of this
peak wavelength is different for each cone type. The three cone types are clas-
siﬁed as S, M, and L cones, where the letters stand for short, medium, and long,
indicating where in the visible spectrum the peak sensitivity is located.
The response of a given cone is then the magnitude of the electrical signal it
outputs, as a function of the spectrum of wavelengths incident upon the cone. The
cone response functions for each cone type as a function of wavelength λare then
given by L(λ), M(λ),a n dS(λ). They are plotted in Figure 19.2.
The actual response to a stimulus with a given spectral composition Φ(λ) is
then given for each cone type by
L =
∫
λ
Φ(λ) L(λ)dλ,
M =
∫
λ
Φ(λ) M(λ)dλ,
S =
∫
λ
Φ(λ) S(λ)dλ.
These three integrated responses are known as tristimulus values.
400 450 500 550 600 650 700
0.0
0.1
0.2
0.3
0.4
0.5
0.6
0.7
0.8
0.9
1.0
Wavelength (nm)
Sensitivity
L
M
S
Figure 19.2. The cone response functions for L, M, and S cones.

498 19. Color
19.1.3 Color Matching Experiments
Given that tristimulus values are created by integrating the product of two func-
tions over the visible range, it is immediately clear that the human visual system
does not act as a simple wavelength det ector. Rather, our photo-receptors act as
approximately linear integrators. As a result, it is possible to ﬁnd two different
spectral compositions, say Φ
1(λ) andΦ2(λ), that after integration yield the same
response (L,M,S ). This phenomenon is known as metamerism, an example of
which is shown in Figure 19.3.
Metamerism is the key feature of human vision that allows the construction of
color reproduction devices, including the color ﬁgures in this book and anything
reproduced on printers, televisions, and monitors.
Color matching experiments also rely o n the principle of metamerism. Sup-
pose we have three differently colored light sources, each with a dial to alter its
intensity. We call these three light sources primaries. We should now be able to
adjust the intensity of each in such a way that when mixed together additively,
the resulting spectrum integrates to a tristimulus value that matches the perceived
color of a fourth unknown light source. When we carry out such an experiment,
we have essentially matched our primaries to an unknown color. The positions of
our three dials are then a representation of the color of the fourth light source.
In such an experiment, we have used Grassmann’s laws to add the three spec-
tra of our primaries. We have also used metamerism, because the combined spec-
trum of our three primaries is almost certa inly different from the spectrum of the
L
M
S
400 450 500 550 600 650 700
Wavelength (nm)
Sensitivity
0.0
0.1
0.2
0.3
0.4
0.5
0.6
0.7
0.8
0.9
1.0
Φ
2
Φ 1
Figure 19.3. Two stimuliΦ1(λ) andΦ2(λ) leading to the same tristimulus values after integration.

19.1. Colorimetry 499
fourth light source. However, the tristimulus values computed from these two
spectra will be identical, having produced a color match.
Note that we do not actually have to know the cone response functions to carry
out such an experiment. As long as we use the same observer under the same
conditions, we are able to match colors and record the positions of our dials for
each color. However, it is quite inconvenient to have to carry out such experiments
every time we want to measure colors. For this reason, we do want to know the
spectral cone response functions and average those for a set of different observers
to eliminate interobserver variability.
19.1.4 Standard Observers
If we perform a color matching experiment for a large range of colors, carried out
by a set of different observers, it is possible to generate an average color match-
ing dataset. If we speci ﬁcally use monochromatic light sources against which to
match our primaries, we can repeat this experiment for all visible wavelengths.
The resulting tristimulus values are then called spectral tristimulus values ,a n d
can be plotted against wavelength λ, shown in Figure 19.4.
By using a well-de ﬁned set of primary light sources, the spectral tristimulus
values lead to three color matching functions. The Commission Internationale
d’Eclairage (CIE) has de ﬁned three such primaries to be monochromatic light
sources of 435.8, 546.1, and 700 nm, respectively. With these three monochro-
400 450 500 550 600 650 700
−0.1
0.0
0.1
0.2
0.3
0.4
0.5
Wavelength (nm)
Sensitivity
r (λ)
g (λ)
b (λ)
Figure 19.4. Spectral tristimulus values averaged over many observers. The primaries where
monochromatic light sources with wavelengths of 435.8, 546.1, and 700 nm.

500 19. Color
matic light sources, all other visible wavelengths can be matched by adding differ-
ent amounts of each. The amount of each re quired to match a given wavelength λ
is encoded in color matching functions, given by ¯r(λ), ¯g(λ),a n d¯b(λ) and plotted
in Figure 19.4. Tristimulus values associated with these color matching functions
are termed R,G,a n dB.
Given that we are adding light, and light cannot be negative, you may have
noticed an anomaly in Figure 19.4: to create a match for some wavelengths, it
is necessary to subtract light. Although there is no such thing as negative light,
we can use Grassmann’s laws once more, and instead of subtracting light from
the mixture of primaries, we can add the same amount of light to the color that is
being matched.
The CIE ¯r(λ), ¯g(λ),a n d¯b(λ) color matching functions allow us to determine
if a spectral distribution Φ
1 matches a second spectral distribution Φ2 by simply
comparing the resulting tristimulus values obtained by integrating with these color
matching functions:
∫
λ
Φ1(λ)¯r(λ)=
∫
λ
Φ2(λ)¯r(λ),
∫
λ
Φ1(λ)¯g(λ)=
∫
λ
Φ2(λ)¯g(λ),
∫
λ
Φ1(λ)¯b(λ)=
∫
λ
Φ2(λ)¯b(λ).
Of course, a color match is only guaranteed if all three tristimulus values match.
The importance of these color matching functions lies in the fact that we are
now able to communicate and describe colors compactly by means of tristimulus
values. For a given spectral function, the CIE color matching functions provide a
precise way in which to calculate tristimulus values. As long as everybody uses
the same color matching functions, it should always be possible to generate a
match.
If the same color matching functions are not available, then it is possible to
transform one set of tristimulus values into a different set of tristimulus values
appropriate for a corresponding set of primaries. The CIE has de ﬁned one such
a transform for two speci ﬁc reasons. First, in the 1930s numerical integrations
were difﬁcult to perform, and even more so for functions that can be both posi-
tive and negative. Second, the CIE had already developed the photopic luminance
response function, CIE V(λ). It became desirable to have three integrating func-
tions, of which V(λ) is one and all three being positive over the visible range.
To create a set of positive color matching functions, it is necessary to de ﬁne
imaginary primaries. In other words, to reproduce any color in the visible spec-
trum, we need light sources that cannot be physically realized. The color match-

19.1. Colorimetry 501
400 450 500 550 600 650 700
0.0
0.2
0.4
0.6
0.8
1.0
1.2
1.4
1.6
1.8
Wavelength (nm)
Sensitivity
x (λ)
y (λ)
z (λ)
Figure 19.5. The CIE ¯x(λ), ¯y(λ),a n d¯z(λ) color matching functions.
ing functions that were settled upon by the CIE are named ¯x(λ), ¯y(λ),a n d ¯z(λ)
and are shown in Figure 19.5. Note that ¯y(λ) is equal to the photopic luminance
response function V(λ) and that each of these functions is indeed positive. They
are known as the CIE 1931 standard observer.
The corresponding tristimulus values are termed X, Y ,a n dZ, to avoid con-
fusion with R,G,a n dB tristimulus values that are normally associated with real-
izable primaries. The conversion from (R,G,B ) tristimulus values to (X,Y,Z )
tristimulus values is deﬁ ned by a simple 3× 3 transform:
⎡
⎣
X
Y
Z
⎤
⎦= 1
0.17697
⎡
⎣
0.4900 0.3100 0.2000
0.17697 0.81240 0.01063
0.0000 0.0100 0.9900
⎤
⎦·
⎡
⎣
R
G
B
⎤
⎦.
To calculate tristimulus values, we typically directly integrate the standard ob-
server color matching functions with the spectrum of interest Φ(λ), rather than go
through the CIE ¯r(λ), ¯g(λ),a n d¯b(λ) color matching functions ﬁrst, followed by
the above transformation. It allows us to calculate consistent color measurements
and also determine when two colors match each other.
19.1.5 Chromaticity Coordinates
Every color can be represented by a set of three tristimulus values (X,Y,Z ).W e
could de ﬁne an orthogonal coordinate system with X, Y ,a n d Z axes and plot
each color in the resulting 3D space. This is called a color space . The spatial
extent of the volume in which colors lie is then called the color gamut.

502 19. Color
Visualizing colors in a 3D color space is fairly dif ﬁcult. Moreover, the Y -
value of any color corresponds to its luminance, by virtue of the fact that ¯y(λ)
equals V(λ). We could therefore project tristimulus values to a 2D space which
approximates chromatic information, i. e., information which is independent of
luminance. This projection is called a chromaticity diagram and is obtained by
normalization while at the same time removing luminance information:
x = X
X +Y +Z,
y = Y
X +Y +Z,
z = Z
X +Y +Z.
Given that x + y + z equals 1, the z-value is redundant, allowing us to plot the
x and y chromaticities against each other in a chromaticity diagram. Although x
and y by themselves are not suf ﬁcient to fully describe a color, we can use these
two chromaticity coordinates and one of the three tristimulus values, traditionally
Y , to recover the other two tristimulus values:
X = x
y Y,
Z = 1−x−y
y Y.
By plotting all monochromatic (spectral) colors in a chromaticity diagram,
we obtain a horseshoe-shaped curve. The points on this curve are called spectrum
loci. All other colors will generate points lying inside this curve. The spectrum
locus for the 1931 standard observer is shown in Figure 19.6. The purple line
550
500
y
600
400
700
0.800
0.1
0.2
0.3
0.4
0.5
0.6
0.7
0.8
0.9
0.1 0.2 0.3 0.4 0.5 0.6 0.7
x
Figure 19.6. The spectrum locus for the CIE 1931 standard observer.

19.1. Colorimetry 503
0.9
0.8
0.7
0.6
0.5
0.4
0.3
0.2
0.1
00 0.1 0.2 0.3 0.4 0.5
x
y
0.6 0.7 0.8
Figure 19.7. The chromaticity boundaries of the CIE RGB primaries at 435.8, 546.1, and 700 nm
(solid) and a typical HDTV (dashed).
between either end of the horseshoe does not represent a monochromatic color,
but rather a combination of short and long wavelength stimuli.
A (non-monochromatic) primary can be integrated over all visible wave-
lengths, leading to (X,Y,Z ) tristimulus values, and subsequently to an (x,y)
chromaticity coordinate, i.e., a point on a chromaticity diagram. Repeating this
for two or more primaries yields a set of points on a chromaticity diagram that can
be connected by straight lines. The volume spanned in this manner represents the
range of colors that can be reproduced by the additive mixture of these primaries.
Examples of three-primary systems are shown in Figure 19.7.
Chromaticity diagrams provide insight into additive color mixtures. However,
they should be used with care. First, the interior of the horseshoe should not
be colored, as any color reproduction system will have its own primaries and
can only reproduce some parts of the chromaticity diagram. Second, as the CIE
color matching functions do not represent human cone sensitivities, the distance
between any two points on a chromaticity diagram is not a good indicator for how
differently these colors will be perceived.
A more uniform chromaticity diagram was developed to at least in part ad-
dress the second of these problems. The CIE u
′v′ chromaticity diagram provides
a perceptually more uniform spacing and is therefore generally preferred over
(x,y) chromaticity diagrams. It is computed from (X,Y,Z ) tristimulus values
by applying a different normalization,
u′ = 4X
X +15Y +3Z,
v′ = 9Y
X +15Y +3Z.

504 19. Color
u´
v´
550
500
400
00
0.1
0.2
0.3
0.4
0.5
0.6
0.1 0.2 0.3 0.4 0.5 0.6 0.7
600 700
Figure 19.8. The CIE u′v′ chromaticity diagram.
and can alternatively be computed directly from (x,y) chromaticity coordinates:
u′ = 4x
−2x+12y+3 ,
v′ = 9y
−2x+12y+3 .
AC I Eu′v′ chromaticity diagram is shown in Figure 19.8.
19.2 Color Spaces
As explained above, each color can be repr esented by three numbers, for instance
deﬁned by (X,Y,Z ) tristimulus values. However, its primaries are imaginary,
meaning that it is not possible to construct a device that has three light sources
(all positive) that can reproduce all colors in the visible spectrum.
For the same reason, image encoding and computations on images may not
be practical. There is, for instance, a large number of possible XYZ values that
do not correspond to any physical color. This would lead to inef ﬁcient use of
available bits for storage and to a higher r equirement for bit-depth to preserve
visual integrity after image processing. Although it may be possible to build a
capture device that has prima ries that are close to the CIE XYZ color matching
functions, the cost of hardware and image processing make this an unattractive
option. It is not possible to build a display that corresponds to CIE XYZ .F o r
these reasons, it is necessary to design other color spaces: physical realizability,
efﬁcient encoding, perceptual uniformity, and intuitive color speci ﬁcation.
The CIE XYZ color space is still actively used, mostly for the conversion
between other color spaces. It can be seen as a device-independent color space.

19.2. Color Spaces 505
Other color spaces can then be deﬁned in terms of their relationship to CIE XYZ ,
which is often speci ﬁed by a speci ﬁc transform. For instance, linear and additive
trichromatic display devices can be transformed to and from CIE XYZ by means
of a simple 3 × 3 matrix. Some nonlinear additional transform may also be speci-
ﬁed, for instance to minimize perceptual errors when data is stored with a limited
bit-depth, or to enable display directly on devices that have a nonlinear relation-
ship between input signal and the amount of light emitted.
19.2.1 Constructing a Matrix T ransform
For a display device with three primaries, say red, green, and blue, we can mea-
sure the spectral composition of the emitted light by sending the color vectors
(1,0,0), (0,1,0),a n d (0,0,1). These vectors represent the three cases namely
where one of the primaries is full on, and the other two are off. From the measured
spectral output, we can then compute the corresponding chromaticity coordinates
(x
R,yR), (xG,yG),a n d(xB,yB).
The white point of a display is de ﬁned as the spectrum emitted when the color
vector(1,1,1) is sent to the display. Its corresponding chromaticity coordinate is
(xW,yW). The three primaries and the white point characterize the display and
are each required to construct a transforma tion matrix between the display’s color
space and CIE XYZ .
These four chromaticity coordinates can be extended to chromaticity triplets
reconstructing thez-coordinate fromz =1 −x−y, leading to triplets(xR,yR,zR),
(xG,yG,zG), (xB,yB,zB),a n d(xW,yW,zW). If we know the maximum lumi-
nance of the white point, we can compute its corresponding tristimulus value
(X
W,YW,ZW) and then solve the following set of equations for the luminance
ratio scalars SR,SG,a n dSB :
XW = xR SR +xG SG +xB SB,
YW = yR SR +yG SG +yB SB,
ZW = zR SR +zG SG +zB SB.
The conversion between RGB and XYZ is then given by
⎡
⎣
X
Y
Z
⎤
⎦=
⎡
⎣
x
R SR xG SG xB SB
yR SR yG SG yB SB
zR SR zG SG zB SB
⎤
⎦
⎡
⎣
R
G
B
⎤
⎦.
The luminance of any given color can be computed by evaluating the middle row
of a matrix constructed in this manner:
Y = y
R SR R+yG SG G+yB SB B.

506 19. Color
RGB White
x 0.6400 0.3000 0.1500 0.3127
y 0.3300 0.6000 0.0600 0.3290
Table 19.1. The (x,y) chromaticity coordinates for the primaries and white point speci ﬁed by
ITU-R BT.709. The sRGB standard also uses these primaries and white point.
To convert between XYZ and RGB of a given device, the above matrix can
simply be inverted.
If an image is represented in an RGB color space for which the primaries and
white point are unknown, then the next best thing is to assume that the image was
encoded in a standard RGB color space. A reasonable choice is then to assume
that the image was speci ﬁed according to ITU-R BT.709, which is the speci ﬁca-
tion used for encoding and broadcasting o f HDTV . Its primaries and white point
are speciﬁed in Table 19.1. Note that the same primaries and white point are used
to de ﬁne the well-known sRGB color space. The transformation between this
RGB color space and CIE XYZ is and vice versa given by
⎡
⎣
X
Y
Z
⎤
⎦=
⎡
⎣
0.4124 0.3576 0.1805
0.2126 0.7152 0.0722
0.0193 0.1192 0.9505
⎤
⎦
⎡
⎣
R
G
B
⎤
⎦;
⎡
⎣
R
G
B
⎤
⎦=
⎡
⎣
3.2405 −1.5371 −0.4985
−0.9693 1.8706 0.0416
0.0556 −0.2040 1.0572
⎤
⎦
⎡
⎣
X
Y
Z
⎤
⎦.
By su
bstituting the maximum RGB values of the device, we can compute
the white point. For ITU-R BT.709, the maximum values are (RW,GW,BW)=
(100,100,100), leading to a white point of (XW,YW,ZW)=( 9 5.05,100.00,
108.90).
In addition to a linear transformation, the sRGB color space is characterized
by a subsequent nonlinear transform. The nonlinear encoding is given by
RsRGB =
{
1.055R1/2.4 −0.055 R> 0.0031308,
12.92RR ≤0.0031308;
GsRGB =
{
1.055G1/2.4 −0.055 G> 0.0031308,
12.92GG ≤0.0031308;
BsRGB =
{
1.055B1/2.4 −0.055 B> 0.0031308,
12.92BB ≤0.0031308.
This nonlinear encoding helps minimize perceptual errors due to quantization er-
rors in digital applications.

19.2. Color Spaces 507
19.2.2 Device-Dependent RGB Spaces
As each device typically has its own set of primaries and white point, we call the
associated RGB color spaces device-depe ndent. It should be noted that even if all
these devices operate in an RGB space, they may have very different primaries
and white points. If we therefore have an image speci ﬁed in some RGB space, it
may appear very different to us, depending upon which device we display it.
This is clearly an undesirable situati on, resulting from a lack of color man-
agement. However, if the image is speci ﬁed in a known RGB color space, it can
ﬁrst be converted to XYZ, which is device independent, and then subsequently it
can be converted to the RGB space of the device on which it will be displayed.
There are several other RGB color spaces that are well de ﬁned. They each
consist of a linear matrix transform followed by a nonlinear transform, akin to the
aforementioned sRGB color space. The non linear transform can be parameterized
as follows:
R
nonlinear =
{
(1+ f)Rγ−ft < R ≤1,
sR 0 ≤R ≤t;
Gnonlinear =
{
(1+ f)Gγ−ft < G ≤1,
sG 0 ≤G ≤t;
Bnonlinear =
{
(1+ f)Bγ−ft < B ≤1,
sB 0 ≤B ≤t.
The parameters s, f , t and γ, together with primaries and white point, specify a
class of RGB color spaces that are used i n various industries. Several common
transformations are listed in Table 19.2.
19.2.3 LMS Cone Space
The aforementioned cone signals can be expressed in terms of the CIE XYZ color
space. The matrix transform to compute LMS signals from XYZ and vice versa
are given by
⎡
⎣
L
M
S
⎤
⎦=
⎡
⎣
0.38971 0.68898 −0.07868
−0.22981 1.18340 0.04641
0.00000 0.00000 1.00000
⎤
⎦
⎡
⎣
X
Y
Z
⎤
⎦;
⎡
⎣
X
Y
Z
⎤
⎦=
⎡
⎣
1.91019 −1.11214 0.20195
0.37095 0.62905 0.00000
0.00000 0
 .00000 1.00000
⎤
⎦
⎡
⎣
L
M
S
⎤
⎦.

508 19. Color
Color space XYZ to RGB matrix RGB to XYZ matrix Nonlinear transform
sRGB
⎡
⎣
3.2405 −1.5371 −0.4985
−0.9693 1. 8760 0. 0416
0.0556 −0.2040 1. 0572
⎤
⎦
⎡
⎣
0.4124 0. 3576 0. 1805
0.2126 0. 7152 0. 0722
0.0193 0. 1192 0. 9505
⎤
⎦
γ =1 /2.4 ≈0.42
f =0 .055
s =1 2 .92
t =0 .0031308
Adobe RGB (1998)
⎡
⎣
2.0414 −0.5649 −0.3447
−0.9693 1. 8760 0. 0416
0.0134 −0.1184 1. 0154
⎤
⎦
⎡
⎣
0.5767 0. 1856 0. 1882
0.2974 0. 6273 0. 0753
0.0270 0. 0707 0. 9911
⎤
⎦
γ =
1
2 51
256
≈ 1
2.2
f =N .A.
s =N .A.
t =N .A.
HDTV (HD-CIF)
⎡
⎣
3.2405 −1.5371 −0.4985
−0.9693 1. 8760 0. 0416
0.0556 −0.2040 1. 0572
⎤
⎦
⎡
⎣
0.4124 0. 3576 0. 1805
0.2126 0. 7152 0. 0722
0.0193 0. 1192 0. 9505
⎤
⎦
γ =0 .45
f =0 .099
s =4 .5
t =0 .018
NTSC (1953)/
ITU-R BT.601-4
⎡
⎣
1.9100 −0.5325 −0.2882
−0.9847 1. 9992 −0.0283
0.0583 −0.1184 0. 8976
⎤
⎦
⎡
⎣
0.6069 0. 1735 0. 2003
0.2989 0. 5866 0. 1145
0.0000 0. 0661 1. 1162
⎤
⎦
γ =0 .45
f =0 .099
s =4 .5
t =0 .018
PAL /SE CAM
⎡
⎣
3.0629 −1.3932 −0.4758
−0.9693 1. 8760 0. 0416
0.0679 −0.2289 1. 0694
⎤
⎦
⎡
⎣
0.4306 0. 3415 0. 1783
0.2220 0. 7066 0. 0713
0.0202 0. 1296 0. 9391
⎤
⎦
γ =0 .45
f =0 .099
s =4 .5
t =0 .018
SMPTE-C
⎡
⎣
3.5054 −1.7395 −0.5440
−1.0691 1. 9778 0. 0352
0.0563 −0.1970 1. 0502
⎤
⎦
⎡
⎣
0.3936 0. 3652 0. 1916
0.2124 0. 7010 0. 0865
0.0187 0. 1119 0. 9582
⎤
⎦
γ =0 .45
f =0 .099
s =4 .5
t =0 .018
SMPTE-240M
⎡
⎣
2.042 −0.565 −0.345
−0.894 1. 815 0. 032
0.064 −0.129 0. 912
⎤
⎦
⎡
⎣
0.567 0. 190 0. 193
0.279 0. 643 0. 077
0.000 0. 073 1. 016
⎤
⎦
γ =0 .45
f =0 .1115
s =4 .0
t =0 .0228
Wide Gamut
⎡
⎣
1.4625 −0.1845 −0.2734
−0.5228 1. 4479 0. 0681
0.0346 −0.0958 1. 2875
⎤
⎦
⎡
⎣
0.7164 0. 1010 0. 1468
0.2587 0. 7247 0. 0166
0.0000 0. 0512 0. 7740
⎤
⎦
γ =N .A.
f =N .A.
s =N .A.
t =N .A.
Table 19.2. Transformations for standard RGB color spaces (after (Pascale, 2003)).
This transform is known as the Hunt-Pointer-Estevez transform (Hunt, 2004) and
is used in chromatic adaptation transform s as well as in color appearance model-
ing.
19.2.4 CIE 1976 L∗a∗b∗
Color opponent spaces are characterized by a channel representing an achromatic
channel (luminance), as well as two channels encoding color opponency. These
are frequently red-green and yellow-blue channels. These color opponent chan-

19.2. Color Spaces 509
nels thus encode two chromaticities along one axis, which can have both positive
and negative values. For instance, a red-green channel encodes red for positive
values and green for negative values. The value zero encodes a special case: neu-
tral which is neither red or green. The yellow-blue channel works in much the
same way.
As at least two colors are encoded on each of the two chromatic axes, it is not
possible to encode a mixture of red and green. Neither is it possible to encode
yellow and blue simultaneously. While this may seem a disadvantage, it is known
that the human visual system computes similar attributes early in the visual path-
way. As a result, humans are not able to perceive colors that are simultaneously
red and green, or yellow and blue. We do not see anything resembling reddish-
green, or yellowish-blue. We are, howeve r, able to perceive mixtures of colors
such as yellowish-red (orange) or greenish-blue, as these are encoded across the
chromatic channels.
The most relevant color opponent system for computer graphics is the CIE
1976 L
∗a∗b∗color model. It is a perceptually more or less uniform color space,
useful, among other things, for the computation of color differences. It is also
known as CIELAB.
The input to CIELAB are the stimulus (X,Y,Z ) tristimulus values as well as
the tristimulus values of a diffuse white re ﬂecting surface that is lit by a known il-
luminant, (X
n,Yn,Zn). CIELAB therefore goes beyond being an ordinary color
space, as it takes into account a patch of color in the context of a known illumina-
tion. It can thus be seen as a rudime ntary color appearance space.
The three channels de ﬁned in CIELAB are L
∗, a∗,a n db∗.T h e L∗ channel
encodes the lightness of the color, i.e., the perceived re ﬂectance of a patch with
tristimulus value(X,Y,Z ).T h ea∗andb∗are chromatic opponent channels. The
transform between XYZ and CIELAB is given by
⎡
⎣
L∗
a∗
b∗
⎤
⎦=
⎡
⎣
0 116 0 −16
500 −500 0 0
0 200 −200 0
⎤
⎦
⎡
⎢⎢⎣
f (X/X
n)
f (Y/Yn)
f (Z/Zn)
1
⎤
⎥⎥⎦.
The function f is deﬁned as
f(r)=
⎧
⎨
⎩
3√
r forr> 0.008856,
7.787r+ 16
116 forr ≤0.008856.
As can be seen from this formulation, the chromatic channels do depend on the
luminanceY . Although this is perceptually accurate, it means that we cannot plot
the values of a∗andb∗in a chromaticity diagram. The lightness L∗is normalized

510 19. Color
between 0 and 100 for black and white. Although the a∗and b∗channels are not
explicitly constrained, they are typically in the range [−128,128].
As CIELAB is approximately perceptually linear, it is possible to take two
colors, convert them to CIELAB, and then estimate the perceived color difference
by computing the Euclidean distance be tween them. This leads to the following
color difference formula:
ΔE∗
ab =
[
(ΔL∗)2 +(Δa∗)2 +(Δb∗)2
]1/2
.
The letter E stands for difference in sensation (in German, Emp ﬁndung) (Judd,
1932).
Finally, the inverse transform between CIELAB and XYZ is given by
X = Xn
⎧
⎪⎪⎨
⎪⎪
⎩
(L
∗
116 + a∗
500 + 16
116
)3
if L∗> 7.9996,
1
7.787
(L∗
116 + a∗
500
)
if L∗≤7.9996,
Y = Yn
⎧
⎪⎨
⎪⎩
(L∗
116 + 16
116
)3
if L∗> 7.9996,
1
7.787
L∗
116 if L∗≤7.9996,
Z = Zn
⎧
⎪⎪
⎨
⎪⎪⎩
(L
∗
116 − b∗
200 + 16
116
)3
if L∗> 7.9996,
1
7.787
(L∗
116 − b∗
200
)
if L∗≤7.9996.
19.3 Chromatic Adaptation
The CIELAB color space just described takes as input both a tristimulus value of
the stimulus and the tristimulus value of light re ﬂected off a white diffuse patch.
As such, it forms the beginnings of a system in which the viewing environment is
taken into account.
The environment in which we observe objects and images has a large inﬂuence
on how we perceive those objects. The range of viewing environments that we
encounter in daily life is very large, from sunlight to starlight and from candlelight
to ﬂuorescent light. The lighting conditions not only constitute a very large range
in the amount of light that is present, but also vary greatly in the color of the
emitted light.

19.3. Chromatic Adaptation 511
The human visual system accommodates these changes in the environment
through a process called adaptation. Thre e different types of adaptation can be
distinguished, namely light adaptation, dark adaptation, and chromatic adaptation.
Light adaptation refers to the changes that occur when we move from a very dark
to a very light environment. When this happens, at ﬁrst we are dazzled by the
light, but soon we adapt to the new situation and begin to distinguish objects in
our environment. Dark adaptation refers to the opposite—when we go from a
light environment to a dark environment. At ﬁrst, we see very little, but after a
given amount of time, details will start to emerge. The time needed to adapt to
the dark is generally much longer than for light adaptation.
Chromatic adaptation refers to our ability to adapt, and largely ignore, vari-
ations in the color of the illumination. Chromatic adaptation is, in essence, the
biological equivalent of the white balancing operation that is available on most
modern cameras. The human visual system effectively normalizes the viewing
conditions to present a visual experience that is fairly consistent. Thus, we ex-
hibit a certain amount of color constancy: object re ﬂectances appear relatively
constant despite variations in illumination.
Although we are able to largely ignore changes in viewing environment, we
are not able to do so completely. For in stance, colors appear much more col-
orful on a sunny day than they do on a cloudy day. Although the appearances
have changed, we do not assume that object re ﬂectances themselves have actually
changed their physical properties. We thus understand that the lighting conditions
have inﬂuenced the overall color appearance.
Nonetheless, color constancy does apply to chromatic content. Chromatic
adaptation allows white objects to appear white for a large number of lighting
conditions, as shown in Figure 19.9.
v´
u´
0.5
0.7 0.6 0.5 0.4 0.3 0.2 0.1 0.0
0.0
0.1
0.2
0.3
0.4
Figure 19.9. A series of light sources plotted in the CIE u′v′ chromaticity diagram. A white piece
of paper illuminated by any of these light sources maintains a wh ite color appearance.

512 19. Color
400 450 500 550 600 650 700
0.0
0.1
0.2
0.3
0.4
0.5
0.6
0.7
0.8
0.9
1.0
Wavelength (nm)
Sensitivity
Color representing
CIE A rendered into
the sRGB color spaceL
M
S
CIE A
Solid lines: relative cone responses
Dashed lines: relative adapted cone responses
Figure 19.10. An example of von Kries–style independent photoreceptor gain control. The relative
cone responses (solid line) and the relative adapted cone responses to CIE illuminant A (dashed) are
shown. The separate patch of color represents CIE illuminant A rendered into the sRGB color space.
Computational models of chromatic adaptation tend to focus on the gain con-
trol mechanism in the cones. One of the simplest models assumes that each cone
adapts independently to the energy that i t absorbs. This means that different cone
types adapt differently dependent on th e spectrum of the light being absorbed.
Such adaptation can then be modeled as an adaptive and independent rescaling of
the cone signals:
L
a = αL ,
Ma = βM ,
Sa = γS ,
where (La,Ma,Sa) are the chromatically adapted cone signals, and α, β,a n dγ
are the independent gain controls which are determined by the viewing environ-
ment. This type of independent adaptation is also known as von-Kries adaptation.
An example is shown in Figure 19.10.
The adapting illumination can be measured off a white surface in the scene. In
the ideal case, this would be a Lambertian surface. In a digital image, the adapting
illumination can also be approximated as the maximum tristimulus values of the
scene. The light measured or computed in this manner is the adapting white, given
by(L
w,Mw,Sw). V on Kries adaptation is then simply a scaling by the reciprocal

19.3. Chromatic Adaptation 513
of the adapting white, carried out in cone response space:
⎡
⎣
La
Ma
Sa
⎤
⎦=
⎡
⎢⎢⎢⎢⎣
1
Lw
00
0 1
Mw
0
00 1
Sw
⎤
⎥⎥⎥⎥⎦
⎡
⎣
L
M
S
⎤
⎦.
In many cases, we are interested in what stimulus should be generated under
one illumination to match a given color under a different illumination. For ex-
ample, if we have a colored patch illuminated by daylight, we may ask ourselves
what tristimulus values should be generated to create a matching color patch that
will be illuminated by incandescent light.
We are thus interested in computing corresponding colors, which can be
achieved by cascading two chromatic adaptation calculations. In essence, the pre-
viously mentioned von Kries transform divides out the adapting illuminant—in
our example, the daylight illumination. If we subsequently multiply in the incan-
descent illuminant, we have computed a c orresponding color. If the two illumi-
nants are given by(L
w,1,Mw,1,Sw,1) and(Lw,2,Mw,2,Sw,2), the corresponding
color(Lc,Mc,Sc) is given by
⎡
⎣
Lc
Mc
Sc
⎤
⎦=
⎡
⎣
L
w,2 00
0 Mw,2 0
00 Sw,2
⎤
⎦
⎡
⎢⎢
⎢
⎢
⎢⎣
1
Lw,1
00
0 1
Mw,1
0
00 1
Sw,1
⎤
⎥⎥
⎥
⎥
⎥⎦
⎡
⎣
L
M
S
⎤
⎦.
There are several more complicated a nd, therefore, more accurate chromatic
adaptation transform in existence (Reinhard et al., 2008). However, the simple
von Kries model remains remarkably eff ective in modeling chromatic adaptation
and can thus be used to achieve white balancing in digital images.
The importance of chromatic adaptation in the context of rendering, is that we
have moved one step closer to taking into account the viewing environment of the
observer, without having to correct for it by adjusting the scene and rerendering
our imagery. Instead, we can model and render our scenes, and then, as an image
postprocess, correct for the illumination of the viewing environment. To ensure
that white balancing does not introduce artifacts, however, it is important to ensure
that the image is rendered to a ﬂoating-point format. If rendered to traditional 8-
bit image formats, the chromatic adaptation transform may amplify quantization
errors.

514 19. Color
19.4 Color Appearance
While colorimetry allows us to accurate ly specify and communicate color in a
device-independent manner, and chromatic adaptation allows us to predict color
matches across changes in illumination, these tools are still insufﬁcient to describe
what colors actually look like.
To predict the actual perception of an object, we need to know more informa-
tion about the environment and take that information into account. The human
visual system is constantly adapting to its environment, which means that the per-
ception of color will be strongly in ﬂuenced by such changes. Color appearance
models take into account measurements of the stimulus itself, as well as the view-
ing environment. This means that the resulting description of color is independent
of viewing condition.
The importance of color appearance modeling can be seen in the following
example. Consider an image being displayed on an LCD screen. When making
a print of the same image and viewing it in a different context, more often than
not the image will look markedly different. Color appearance models can be
used to predict the changes required to g enerate an accurate cross-media color
reproduction (Fairchild, 2005).
Although color appearance modeling offers important tools for color repro-
duction, actual implementations tend to be relatively complicated and cumber-
some in practical use. It can be anticipat ed that this situation may change over
time. However, until then, we leave their d escription to more specialized text-
books (Fairchild, 2005).
Notes
Of all the books on color theory, Reinhard et al.’s work (Reinhard et al., 2008) is
most directly geared toward engineering disciplines, including computer graph-
ics, computer vision, and image processing. Other general introductions to color
theory are given by Berns (Berns, 2000) and Stone (Stone, 2003). Wyszecki and
Stiles have produced a comprehensive vo lume of data and formulae, forming an
indispensable reference work (Wyszecki & Stiles, 2000). For color reproduction,
we recommend Hunt’s book (Hunt, 2004). Color appearance models are compre-
hensively described in Fairchild’s book (Fairchild, 2005). For color issues related
to video and HDTV Poynton’s book is essential (Poynton, 2003).

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
**关联概念**: [[辐射度量学]]
**pbrt 对应**: pbrt Ch4 Radiometry, Spectra, and Color
