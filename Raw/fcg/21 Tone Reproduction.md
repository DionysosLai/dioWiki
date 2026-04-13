---
title: "21 Tone Reproduction"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 21 Tone Reproduction

21
Erik Reinhard
T one Reproduction
As discussed in Chapter 20, the human visual system adapts to a wide range of
viewing conditions. Under normal viewing, we may discern a range of around 4
to 5 log units of illumination, i.e., the ratio between brightest and darkest areas
where we can see detail may be as large as 100,000 : 1. Through adaptation
processes, we may adapt to an even larger range of illumination. We call images
that are matched to the capabilities of the human visual system high dynamic
range.
Visual simulations routinely produce images with a high dynamic range
(Ward Larson & Shakespeare, 1998). Recen t developments in image-capturing
techniques allow multiple exposures to be aligned and recombined into a single
high dynamic range image (Debevec & Malik, 1997). Multiple exposure tech-
niques are also available for video. In addition, we expect future hardware to be
able to photograph or ﬁlm high dynamic range scenes directly. In general, we
may think of each pixel as a triplet of three ﬂoating point numbers.
As it is becoming easier to create high dynamic range imagery, the need to
display such data is rapidly increasing. Unfortunately, most current display de-
vices, monitors and printers, are only capable of displaying around 2 log units
of dynamic range. We consider such devices to be of low dynamic range. Most
images in existence today are represented with a byte-per-pixel-per-color chan-
nel, which is matched to current display devices, rather than to the scenes they
represent.
Typically, low dynamic range images are not able to represent scenes with-
out loss of information. A common example is an indoor room with an out-
559

560 21. T one Reproduction
Figure 21.1. With conventional photography, some parts of the scene may be under- or over-
exposed. To visualize the snooker table, the view through the window is burned out in the left image.
On the other hand, the snooker table will be too dark if the outdoor part of this scene is properly
exposed. Compare with Figure 21.2, which shows a high dynamic range image prepared for display
using a tone reproduction algorithm.
door area visible through the window. Humans are easily able to see details of
both the indoor part and the outside part. A conventional photograph typically
does not capture this full range of information—the photographer has to choose
whether the indoor or the outdoor part of the scene is properly exposed (see Fig-
ure 21.1). These decisions may be avoided by using high dynamic range imaging
and preparing these images for display using techniques described in this chapter
(see Figure 21.2).
There are two strategies available to display high dynamic range images. First,
Figure 21.2. A high dynamic range image
tonemapped for display using a recent tone re-
production operator (Reinhard & Devlin, 2005).
In this image, both the indoor part and the view
through the window are properly exposed.
we may develop display devices which
can directly accommodate a high dy-
namic range (Seetzen, Whitehead, &
Ward, 2003; Seetzen et al., 2004). Sec-
ond, we may prepare high dynamic
range images for display on low dy-
namic range display devices (Upstill,
1985). This is currently the more com-
mon approach and the topic of this
chapter. Although we foresee that high
dynamic range display devices will be-
come widely used in the (near) future,
the need to compress the dynamic range
of an image may diminish, but will not
disappear. In particular, printed media such as this book are, by their very nature,
low dynamic range.
Compressing the range of values of an image for the purpose of display on
a low dynamic range display device is called tonemapping or tone reproduction.

561
Figure 21.3. Linear scaling of high dynamic range images to ﬁt a given display device may cause
signiﬁcant detail to be lost (left and middle). The left image is linearly scaled. In the middle image
high values are clamped. For comparison, the right image is tonemapped, allowing details in both
bright and dark regions to be visible.
A simple compression function would be to normalize an image (see Figure 21.3
(left)). This constitutes a linear scaling which tends to be suf ﬁcient only if the dy-
namic range of the image is only marginally higher than the dynamic range of the
display device. For images with a higher dynamic range, small intensity differ-
ences will be quantized to the same display value such that visible details are lost.
In Figure 21.3 (middle) all pixel values larger than a user-speci ﬁed maximum are
set to this maximum (i.e., they are clamped). This makes the normalization less
dependent on noisy outliers, but here we lose information in the bright areas of
the image. For comparison, Figure 21.3 (right) is a tonemapped version showing
detail in both the dark and the bright regions.
In general, linear scaling will not be appropriate for tone reproduction. The
key issue in tone reproduction is then to compress an image while at the same
time preserving one or more attributes of the image. Different tone reproduction
algorithms focus on different attributes such as contrast, visible detail, brightness,
or appearance.
Figure 21.4. Image used for
demonstrating the goal of tone
reproduction in Figure 21.5.
Ideally, displaying a tonemapped image on a low dynamic range display de-
vice would create the same visual response in the observer as the original scene.
Given the limitations of display devices, this will not be achievable, although we
could aim for approximating this goal as closely as possible.
As an example, we created the high dynamic range image shown in Fig-
ure 21.4. This image was then tonemapped and displayed on a display device.
The display device itself was then placed in the scene such that it displays its own
background (Figure 21.5). In the ideal case, the display should appear transpar-

562 21. T one Reproduction
Figure 21.5. After tonemapping the image in Figure 21.4 and displaying it on a monitor, the
monitor is placed in the scene approximately at the l ocation where the image was taken. Dependent on
the quality of the tone reproduction operator, the res ult should appear as if the monitor is transparent.
ent. Dependent on the quality of the tone reproduction operator, as well as the
nature of the scene being depicted, this goal may be more or less achievable.
21.1 Classiﬁcation
Although it would be possible to classify tone reproduction operators by which
attribute they aim to preserve, or for which task they were developed, we classify
algorithms according to their general tec hnique. This will enable us to show the
differences and similarities between a signi ﬁcant number of different operators,
and so, hopefully, contribute to the meaningful selection of speci ﬁc operators for
given tone reproduction tasks.
The main classiﬁcation scheme we follow hinges upon the realization that tone
reproduction operators are based on insights gained from various disciplines. In
particular, several operators are based on knowledge of human visual perception.
The human visual system detects light using photoreceptors located in the
retina. Light is converted to an electrical signal which is partially processed in
the retina and then transmitted to the brain. Except for the ﬁrst few layers of
cells in the retina, the signal derived from detected light is transmitted using im-
pulse trains. The information-carrying quantity is the frequency with which these
electrical pulses occur.
The range of light that the human visual system can detect is much larger
than the range of frequencies employed by the human brain to transmit infor-
mation. Thus, the human visual system effortlessly solves the tone reproduc-
tion problem—a large range of luminances is transformed into a small range of
frequencies of impulse trains. Emulating relevant aspects of the human visual
system is therefore a worthwhile approach to tone reproduction; this approach is
explained in more detail in Section 21.7.

21.2. Dynamic Range 563
A second class of operators is grounded in physics. Light interacts with sur-
faces and volumes before being absorbed by the photoreceptors. In computer
graphics, light interaction is generally modeled by the rendering equation. For
purely diffuse surfaces, this equation may be simpli ﬁed to the product between
light incident upon a surface (illumina nce), and this surface’s ability to re ﬂect
light (reﬂectance) (Oppenheim, Schafer, & Stockham, 1968).
Since re ﬂectance is a passive property of surfaces, for diffuse surfaces it is,
by de ﬁnition, low dynamic range—typically between 0.005 and 1 (Stockham,
1972). The re ﬂectance of a surface cannot be larger than 1, since then it would
reﬂect more light than was incident upon the surface. Illuminance, on the other
hand, can produce arbitrarily large values and is limited only by the intensity and
proximity of the light sources.
The dynamic range of an image is thus pr edominantly governed by the illu-
minance component. In the face of diffuse scenes, a viable approach to tone re-
production may therefore be to separate re ﬂectance from illuminance, compress
the illuminance component, and then recombine the image.
However, the assumption that all surfaces in a scene are diffuse is generally
incorrect. Many high dynamic range images depict highlights and/or directly
visible light sources (Figure 21.3). The luminance re ﬂected by a specular surface
may be almost as high as the light source it re ﬂects.
V arious tone reproduction operators currently used split the image into a high
dynamic range base layer and a low dynami c range detail layer. These layers
would represent illuminance and re ﬂectance if the depicte d scene were entirely
diffuse. For scenes containing directly visible light sources or specular highlights,
separation into base and detail layers still allows the design of effective tone re-
production operators, although no direct meaning can be attached to the separate
layers. Such operators are discussed in Section 21.5.
21.2 Dynamic Range
Conventional images are stored with one byte per pixel for each of the red, green
and blue components. The dynamic range afforded by such an encoding depends
on the ratio between smallest and largest representable value, as well as the step
size between successive values. Thus, for low dynamic range images, there are
only 256 different values per color channel.
High dynamic range images encode a signi ﬁcantly larger set of possible val-
ues; the maximum representable value may be much larger and the step size be-
tween successive values may be much smaller. The ﬁle size of high dynamic

564 21. T one Reproduction
range images is therefore generally larger as well, although at least one standard
(the OpenEXR high dynamic range ﬁle format (Kainz, Bogart, & Hess, 2003))
includes a very capable compression scheme.
A different approach to limit ﬁle sizes is to apply a tone reproduction operator
to the high dynamic data. The result may then be encoded in JPEG format. In
addition, the input image may be divided pixel-wise by the tonemapped image.
Figure 21.6. Dynamic
range of 2.65 log 2 units.
Figure 21.7. Dynamic
range of 3.96 log 2 units.
Figure 21.8. Dynamic
range of 4.22 log 2 units.
Figure 21.9. Dynamic
range of 5.01 log 2 units.
Figure 21.10. Dynamic
range of 6.56 log 2 units.
The result of this division can then be subsampled and stored as a small amount of
data in the header of the same JPEG image (G. Ward & Simmons, 2004). The ﬁle
size of such sub-band encoded images is of the same order as conventional JPEG
encoded images. Display programs can display the JPEG image directly or may
reconstruct the high dynamic range imag e by multiplying the tonemapped image
with the data stored in the header.
In general, the combination of smallest step size and ratio of the smallest and
largest representable values determines the dynamic range that an image encoding
scheme affords. For computer-generat ed imagery, an image is typically stored
as a triplet of ﬂoating point values before it is written to ﬁle or displayed on
screen, although more ef ﬁcient encoding schemes are possible (Reinhard, Ward,
Debevec, & Pattanaik, 2005). Since most display devices are still ﬁtted with eight-
bit D/A converters, we may think of tone reproduction as the mapping of ﬂoating
point numbers to bytes such that the result is displayable on a low dynamic range
display device.
The dynamic range of individual images is generally smaller, and is deter-
mined by the smallest and largest luminances found in the scene. A simplistic
approach to measure the dynamic range of an image may therefore compute the
ratio between the largest and smallest pixel value of an image. Sensitivity to out-
liers may be reduced by ignoring a small percentage of the darkest and brightest
pixels.
Alternatively, the same ratio may be expressed as a difference in the logarith-
mic domain. This measure is less sensitive to outliers. The images shown in the
margin on this page are examples of ima ges with different dynamic ranges. Note
that the night scene in this case does not have a smaller dynamic range than the
day scene. While all the values in the night scene are smaller, the ratio between
largest and smallest values is not.
However, the recording device or rendering algorithm may introduce noise
which will lower the useful dynamic range. Thus, a measurement of the dynamic
range of an image should factor in noise. A better measure of dynamic range
would therefore be a signal-to-noise ratio, expressed in decibels, as used in signal
processing.

21.3. Color 565
Figure 21.11. Per-channel gamma correction may desaturate the image. The left image was
desaturated with a value of s = 0.5. The right image was not desaturated (s =1 ) .
21.3 Color
Tone reproduction operators normally comp ress luminance values, rather than
work directly on the red, green, and blue components of a color image. Af-
ter these luminance values have been compressed into display values Ld(x,y),
a color image may be reconstructed by keeping the ratios between color channels
the same as they were before compression (using s =1 ) (Schlick, 1994b):
I
r,d(x,y)=
(Ir(x,y)
Lv(x,y)
)s
Ld(x,y),
Ig,d(x,y)=
(Ig(x,y)
Lv(x,y)
)s
Ld(x,y),
Ib,d(x,y)=
(Ib(x,y)
Lv(x,y)
)s
Ld(x,y).
The results frequently appear over-satu rated, because human color perception is
nonlinear with respect to overall luminance level. This means that if we view an
image of a bright outdoor scene on a monitor in a dim environment, our eyes are
adapted to the dim environment rather than the outdoor lighting. By keeping color
ratios constant, we do not take this effect into account.
Alternatively, the saturation constant s may be chosen smaller than one. Such
per-channel gamma correction may desaturate the results to an appropriate level,
as shown in Figure 21.11 (Fattal, Lischinski, & Werman, 2002). A more compre-
hensive solution is to incorporate ideas from the ﬁeld of color appearance mod-
eling into tone reproduction operators (Pattanaik, Ferwerda, Fairchild, & Green-
berg, 1998; Fairchild & Johnson, 2004; Reinhard & Devlin, 2005).

566 21. T one Reproduction
Finally, if an example image with a representative color scheme is already
available, this color scheme may be applied to a new image. Such a mapping of
colors between images may be used for sub tle color correction, such as satura-
tion adjustment or for more creative c olor mappings. The ma pping proceeds by
converting both source and target images to a decorrelated color space. In such a
color space, the pixel valu es in each color channel may be treated independently
without introducing too many artifacts (Reinhard, Ashikhmin, Gooch, & Shirley,
2001).
Mapping colors from one image to another in a decorrelated color space
is then straightforward: compute the mean and standard deviation of all pixels
in the source and target images for the three color channels separately. Then,
Figure 21.12. Image used for demonstrating
the color transfer technique. Results are shown
in Figures 21.13 and 21.31.
shift and scale the target image so that in
each color channel the mean and stan-
dard deviation of the target image is the
same as the source image. The result-
ing image is then obtained by convert-
ing from the decorrelated color space
to RGB and clamping negative pixels to
zero. The dynamic range of the image
may have changed as a result of apply-
ing this algorithm. It is therefore recom-
mended to apply this algorithm on high
dynamic range images and apply a con-
ventional tone reproduction algorithm afterward. A suitable decorrelated color
space is the opponent space from Section 19.2.4.
The result of applying such a color transform to the image in Figure 21.12 is
shown in Figure 21.13.
Figure 21.13. The image on the left is used to adjust the colors of the image shown in Figure 21.12.
The result is shown on the right.

21.4. Image Formation 567
21.4 Image Formation
For now, we assume that an image is formed as the result of light being diffusely
reﬂected off of surfaces. Later in this chapter, we relax this constraint to scenes
directly depicting light sources and highlights. The luminance Lv of each pixel is
then approximated by the following product:
Lv(x,y)= r(x,y) Ev(x,y).
Here, r denotes the reﬂectance of a surface, and Ev denotes the illuminance. The
subscript v indicates that we are using photomet rically weighted quantities. Al-
ternatively, we may write this expression in the logarithmic domain (Oppenheim
et al., 1968):
D(x,y) = log(L
v(x,y))
= log(r(x,y) Ev(x,y))
= log(r(x,y)) +log(Ev(x,y)).
Photographic transparencies record images by varying the density of the material.
In traditional photography, this variation has a logarithmic relation with lumi-
nance. Thus, in analogy with common practice in photography, we will use the
term density representation (D) for log luminance. When represented in the log
domain, reﬂectance and illuminance become additive. This facilitates separation
of these two components, despite the fact that isolating either re ﬂectance or il-
luminance is an under-constrained problem. In practice, separation is possible
only to a certain degree and depends on the composition of the image. Nonethe-
less, tone reproduction could be based on disentangling these two components of
image formation, as shown in the following two sections.
21.5 Frequency-Based Operators
For typical diffuse scenes, the re ﬂectance component tends to exhibit high spatial
frequencies due to textured surfaces as well as the presence of surface edges. On
the other hand, illuminance tends to be a slowly varying function over space.
Since reﬂectance is low dynamic range and illuminance is high dynamic range,
we may try to separate the two component s. The frequency-dependence of both
reﬂectance and illuminance provides a solution. We may, for instance, compute
the Fourier transform of an image and attenuate only the low frequencies. This
compresses the illuminance component while leaving the re ﬂectance component

568 21. T one Reproduction
Figure 21.14. Bilateral ﬁltering removes small details but preserves sharp gradients (left). The
associated detail layer is shown on the right.
largely unaffected—the very ﬁrst digital tone reproduction operator known to us
takes this approach (Oppenheim et al., 1968).
More recently, other operators have al so followed this line of reasoning. In
particular, bilateral and trilateral ﬁlters were used to separate an image into base
and detail layers (Durand & Dorsey, 2002; Choudhury & Tumblin, 2003). Both
ﬁlters are edge-preserving smoothing operators which may be used in a variety of
different ways. Applying an edge-preserving smoothing operator to a density im-
age results in a blurred image in which sharp edges remain present (Figure 21.14
(left)). We may view such an image as a base layer. If we then pixel-wise divide
the high dynamic range image by the base layer, we obtain a detail layer which
contains all the high-frequency detail (Figure 21.14 (right)).
For diffuse scenes, base and detail layers are similar to representations of
illuminance and re ﬂectance. For images depicting highlights and light sources,
Figure 21.15. An image tonemapped using bi-
lateral ﬁltering. The base and detail layers shown
in Figure 21.14 are recombined after compress-
ing the base layer.
this parallel does not hold. However,
separation of an image into base and
detail layers is possible regardless of
the image’s content. By compressing
the base layer before recombining into
a compressed density image, a low dy-
namic range density image may be cre-
ated (Figure 21.15). After exponentia-
tion, a displayable image is obtained.
Edge-preserving smoothing opera-
t o r sm a ya l s ob eu s e dt oc o m p u t eal o c a l
adaptation level for each pixel, which
may be used in a spatially varying or local tone reproduction operator. We de-
scribe this use of bilateral and trilateral ﬁlters in Section 21.7.

21.6. Gradient-Domain Operators 569
Figure 21.16. The image on the left (tonemapped using gradient-domain compression) shows a
scene with highlights. These highlights show up as l arge gradients on the right, where the magnitude
of the gradients is mapped to a grayscale (black is a gradient of 0, white is the maximum gradient in
the image).
21.6 Gradient-Domain Operators
The arguments made for the frequency-ba sed operators in the preceding section
also hold for the gradient ﬁeld. Assuming that no light sources are directly visible,
the re ﬂectance component will be a constant function with sharp spikes in the
gradient ﬁeld. Similarly, the illuminance c omponent will cause small gradients
everywhere.
Humans are generally able to separate illuminance from re ﬂectance in typical
scenes. The perception of surface re ﬂectance after discounting the illuminant is
called lightness. To assess the lightness of an image depicting only diffuse sur-
faces, B. K. P . Horn was the ﬁrst to separate re ﬂectance and illuminance using a
gradient ﬁeld (Horn, 1974). He used simple thresholding to remove all small gra-
dients and then integrated the image, which involves solving a Poisson equation
using the Full Multigrid Method (Press, Teukolsky, V etterling, & Flannery, 1992).
The result is similar to an edge-preserving smoothing ﬁlter. This is accord-
ing to expectation since Oppenheim’s fre quency-based operator works under the
same assumptions of scene re ﬂectivity and image formation. In particular, Horn’s
work was directly aimed at “mini-worlds of Mondrians,” which are simpli ﬁed
versions of diffuse scenes which resemble the abstract paintings by the famous
Dutch painter Piet Mondrian.
Horn’s work cannot be employed directly as a tone reproduction operator,
since most high dynamic range images depict light sources. However, a relatively
small variation will turn this work into a suitable tone reproduction operator. If
light sources or specular surfaces are depic ted in the image, then large gradients
will be associated with the edges of light sources and highlights. These cause the
image to have a high dynamic range. An example is shown in Figure 21.16, where
the highlights on the snooker balls cause sharp gradients.

570 21. T one Reproduction
Figure 21.17. An image tonemapped using
gradient-domain compression.
We could therefore compress a high
dynamic range image by attenuating
large gradients, rather than threshold-
ing the gradient ﬁeld. This approach
was taken by Fattal et al. who showed
that high dynamic range imagery may
be successfully compressed by integrat-
ing a compressed gradient ﬁeld (Fig-
ure 21.17) (Fattal et al., 2002). Fat-
tal’s gradient-domain compression is
not limited to diffuse scenes.
21.7 Spatial Operators
In the following sections, we discuss tone reproduction operators which apply
compression directly on pixels without transformation to other domains. Often
global and local operators are distinguished. Tone reproduction operators in the
former class change each pixel’s lumin ance values according to a compressive
function which is the same for each pixel. The term global stems from the fact that
many such functions need to be anchored to some values determined by analyzing
the full image. In practice, most operators use the geometric average ¯L
v to steer
the compression:
¯Lv =e x p
(
1
N
∑
x,y
log(δ+Lv(x,y)
)
. (21.1)
In Equation (21.1), a small constant δis introduced to prevent the average to be-
come zero in the presence of black pixels. The geometric average is normally
mapped to a prede ﬁned display value. The effect of mapping the geometric aver-
age to different display values is shown in Figure 21.18. Alternatively, sometimes
the minimum or maximum image lumina nce is used. The main challenge faced
in the design of a global operator lies in the choice of the compressive function.
On the other hand, local operators co mpress each pixel according to a speci ﬁc
compression function which is modulated by information derived from a selection
of neighboring pixels, rather than the full image. The rationale is that a bright
pixel in a bright neighborhood may be perceived differently than a bright pixel in
a dim neighborhood. Design challenges in the development of a local operator

21.7. Spatial Operators 571
Figure 21.18. Spatial tonemapping operator applied after mapping the geometric average to differ-
ent display values (left: 0.12, right: 0.38).
involves choosing the compressive function, the size of the local neighborhood
for each pixel, and the manner in which lo cal pixel values are used. In general,
local operators achieve better compression than global operators (Figure 21.19),
albeit at a higher computational cost.
Both global and local operators are often inspired by the human visual sys-
tem. Most operators employ one of two distinct compressive functions, which
is orthogonal to the distinction between local and global operators. Display val-
uesL
d(x,y) are most commonly derived from image luminances Lv(x,y) by the
Figure 21.19. A global tone reproduction operator (left) and a local tone reproduction operator
(right) (Reinhard, Stark, Shirl ey, & Ferwerda, 2002) of each image. The local operator shows more
detail; for example, the metal badge on the right shows better contrast and the highlights are crisper.

572 21. T one Reproduction
following two functional forms:
Ld(x,y)= Lv(x,y)
f(x,y) , (21.2)
Ld(x,y)= Lv(x,y)
Lv(x,y)+ fn(x,y). (21.3)
In these equations, f(x,y) may either be a constant or a function which varies per
pixel. In the former case, we have a global operator, whereas a spatially varying
function f(x,y) results in a local operator. The exponent n is usually a constant
which is ﬁxed for a particular operator.
Equation (21.2) divides each pixel’s luminance by a value derived from either
the full image or a local neighborhood. E quation (21.3) has an S-shaped curve on
a log-linear plot and is called a sigmoid for that reason. This functional form ﬁts
data obtained from measuring the electrical response of photoreceptors to ﬂashes
of light in various species. In the following sections, we discuss both functional
forms.
21.8 Division
Each pixel may be divided by a constant to bring the high dynamic range image
within a displayable range. Such a division essentially constitutes linear scaling,
as shown in Figure 21.3. While Figure 21.3 shows ad-hoc linear scaling, this
approach may be reﬁ ned by employing psychophysical data to derive the scaling
constant f(x,y)= k in Equation (21.2) (G. J. Ward, 1994; Ferwerda, Pattanaik,
Shirley, & Greenberg, 1996).
Alternatively, several approaches exist that compute a spatially varying di-
visor. In each of these cases, f(x,y) is a blurred version of the image, i.e.,
f(x,y)= L
blur
v (x,y). The blur is achieved by convolving the image with a
Gaussian ﬁlter (Chiu et al., 1993; Rahman, Jobson, & Woodell, 1996). In addi-
tion, the computation of f(x,y) by blurring the image may be combined with a
shift in white point for the purpose of color appearance modeling (Fairchild &
Johnson, 2002; G. M. Johnson & Fairchild, 2003; Fairchild & Johnson, 2004).
The size and the weight of the Gaussian ﬁlter has a profound impact on the
resulting displayable image. The Gaussian ﬁlter has the effect of selecting a
weighted local average. Tone reproduction is then a matter of dividing each pixel
by its associated weighted local average. If the size of the ﬁlter kernel is chosen
too small, then haloing artifacts will occur (Figure 21.20 (left)). Haloing is a com-
mon problem with local operators and is particularly evident when tone mapping
relies on division.

21.9. Sigmoids 573
Figure 21.20. Images tonemapped by dividing by Gaussian-blurred versions. The size of the ﬁlter
kernel is 64 pixels for the left image and 512 pixels for the right image. For division-based algorithms,
halo artifacts are minimized by choosing large ﬁlter kernels.
In general, haloing artifacts may be minimized in this approach by making the
ﬁlter kernel large (Figure 21.20 (right)). Reasonable results may be obtained by
choosing a ﬁlter size of at least one quarter of the image. Sometimes even larger
ﬁlter kernels are desirable to minimize artifacts. Note, that in the limit, the ﬁlter
size becomes as large as the image itself. In that case, the local operator becomes
global, and the extra compression normally afforded by a local approach is lost.
The functional form whereby each pixel is divided by a Gaussian-blurred pixel
at the same spatial position thus requires a n undesirable tradeoff between amount
of compression and severity of artifacts.
21.9 Sigmoids
Equation (21.3) follows a different func tional form from simple division, and,
therefore, affords a different tradeoff between amount of compression, presence
of artifacts, and speed of computation.
Sigmoids have several desirable properties. For very small luminance values,
the mapping is approximately linear, so that contrast is preserved in dark areas of
the image. The function has an asymptote at one, which means that the output
mapping is always bounded between 0 and 1.
In Equation (21.3), the function f(x,y) may be computed as a global con-
stant or as a spatially varying function. Following common practice in electro-
physiology, we call f(x,y) the semi-saturation constant. Its value determines
which values in the input image are optimally visible after tonemapping. In par-
ticular, if we assume that the exponent n equals 1, then luminance values equal
to the semi-saturation constant will be mapped to 0.5. The effect of choosing
different semi-saturation constants is shown in Figure 21.21.

574 21. T one Reproduction
10-3 10-2 10-1 100 101 102 103 104
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
Lw
Ld
Ld = Lw / (Lw + 1)
Ld = Lw / (Lw + 10)
Ld = Lw / (Lw + 100)
Figure 21.21. The choice of semi-saturation constant determines how input values are mapped to
display values.
The function f(x,y) may be computed in several different ways (Reinhard
et al., 2005). In its simplest form, f(x,y) is set to ¯Lv/k, so that the geometric
average is mapped to user parameter k (Figure 21.22) (Reinhard et al., 2002). In
this case, a good initial value for k is0.18, although for particularly bright or dark
scenes this value may be raised or lowered. Its value may be estimated from the
image itself (Reinhard, 2003). The exponent n in Equation (21.3) may be set to 1.
In this approach, the semi-saturation constant is a function of the geometric
average, and the operator is therefore global. A variation of this global opera-
tor computes the semi-saturation constant by linearly interpolating between the
Figure 21.22. A linearly scaled image (left) and an image tonemapped using sigmoidal compression
(right).

21.9. Sigmoids 575
Figure 21.23. Linear interpolation varies contrast in the tonemapped image. The parameter a is set
to 0.0 in the left image, and to 1.0 in the right image.
geometric average and each pixel’s luminance:
f(x,y)= aLv(x,y)+(1 −a) ¯Lv.
The interpolation is governed by user parameter a which has the effect of vary-
ing the amount of contrast in the displayable image (Figure 21.23) (Reinhard &
Devlin, 2005). More contrast means less visible detail in the light and dark areas
and vice versa. This interpolation may be viewed as a halfway house between a
fully global and a fully local operator by interpolating between the two extremes
without resorting to expensive blurring operations.
Although operators typically compress luminance values, this particular op-
erator may be extended to include a simple form of chromatic adaptation. It thus
presents an opportunity to adjust the level of saturation normally associated with
tonemapping, as discussed at the beginning of this chapter.
Rather than compress the luminance channel only, sigmoidal compression is
applied to each of the three color channels:
I
r,d(x,y)= Ir(x,y)
Ir(x,y)+ fn(x,y),
Ig,d(x,y)= Ig(x,y)
Ig(x,y)+ fn(x,y),
Ib,d(x,y)= Ib(x,y)
Ib(x,y)+ fn(x,y).
The computation of f(x,y) is also modi ﬁed to bilinearly interpolate between the
geometric average luminance and pixel luminance an d between each independent
color channel and the pixel’s luminance value. We therefore compute the geo-
metric average luminance value ¯L
v , as well as the geometric average of the red,
green, and blue channels ( ¯Ir , ¯Ig ,a n d¯Ib). From these values, we compute f(x,y)
for each pixel and for each color channel independently. We show the equation

576 21. T one Reproduction
Figure 21.24. Linear interpolation for color correction. The parameter c is set to 0.0 in the left
image, and to 1.0 in the right image.
for the red channel (fr(x,y)):
Gr(x,y)= cIr(x,y)+(1 −c)Lv(x,y),
¯Gr(x,y)= c ¯Ir +(1− c) ¯Lv,
fr(x,y)= aGr(x,y)+(1 −a) ¯Gr(x,y).
The interpolation parametera steers the amount of contrast as before, and the new
interpolation parameter c allows a simple form of color correction (Figure 21.24).
So far we have not discussed the value of the exponent n in Equation (21.3).
Studies in electrophysiology report values between n =0 .2 and n =0 .9 (Hood,
Finkelstein, & Buckingham, 1979). While the exponent may be user-speciﬁed, for
a wide variety of images we may estimate a reasonable value from the geometric
average luminance ¯Lv and the minimum and maximum luminance in the image
(Lmin and Lmax) with the following empirical equation:
n =0 .3+0 .7
( Lmax −¯Lv
Lmax −Lmin
)1.4
.
The several variants of sigmoidal compression shown so far are all global in na-
ture. This has the advantage that they are fast to compute, and they are very
suitable for medium to high dynamic range images. For very high dynamic range
images, it may be necessary to resort to a local operator, since this may give some
extra compression. A straightforward method to extend sigmoidal compression
replaces the global semi-saturation constant by a spatially varying function, which
may be computed in several different ways.
In other words, the function f(x,y) is so far assumed to be constant, but may
also be computed as a spatially localized average. Perhaps the simplest way to
accomplish this is to once more use a Gaussian-blurred image. Each pixel in

21.9. Sigmoids 577
a blurred image represents a locally av eraged value which may be viewed as a
suitable choice for the semi-saturation constant 1.
As with division-based operators discussed in the previous section, we have
to consider haloing artifacts. However, when an image is divided by a Gaussian-
blurred version of itself, the size of the Gaussian ﬁlter kernel needs to be large
in order to minimize halos. If sigmoids are used with a spatially variant semi-
saturation constant, the Gaussian ﬁlter kernel needs to be made small in order
to minimize artifacts. This is a signi ﬁcant improvement, since small amounts of
Gaussian blur may be efﬁciently computed directly in the spatial domain. In other
words, there is no need to resort to expensive Fourier transforms. In practice, ﬁlter
kernels of only a few pixels width are suf ﬁcient to suppress signi ﬁcant artifacts
while at the same time producing more local contrast in the tonemapped images.
One potential issue with Gaussian blur is that the ﬁlter blurs across sharp
contrast edges in the same way that it blurs small details. In practice, if there
Figure 21.25. Example image used to demon-
strate the scale selection mechanism shown in
Figure 21.26.
is a large contrast gradient in the neigh-
borhood of the pixel under considera-
tion, this causes the Gaussian-blurred
pixel to be signi ﬁcantly different from
the pixel itself. This is the direct cause
for halos. By using a very large ﬁl-
ter kernel in a division-based approach,
such large contrasts are averaged out.
In sigmoidal compression schemes,
a small Gaussian ﬁlter minimizes the
chances of overlapping with a sharp
contrast gradient. In that case, halos
still occur, but their size is such that they
usually go unnoticed and instead are perceived as enhancing contrast.
Another way to blur an image, while minimizing the negative effects of nearby
large contrast steps, is to avoid blurring over such edges. A simple, but compu-
tationally expensive way, is to compute a stack of Gaussian-blurred images with
different kernel sizes. For each pixel, we may choose the largest Gaussian that
does not overlap with a signi ﬁcant gradient.
In a relatively uniform neighborhood, the value of a Gaussian-blurred pixel
should be the same regardless of the ﬁlter kernel size. Thus, the difference be-
tween a pixel ﬁltered with two different Gaussians should be approximately zero.
This difference will only change signi ﬁcantly if the wider ﬁlter kernel overlaps
1 Although f(x,y) is now no longer a constant, we continue to refer to it as the semi-saturation
constant.

578 21. T one Reproduction
Figure 21.26. Scale selection mechanism: the left image shows the scale selected for each pixel of
the image shown in Figure 21.25; the darker the pixel, the smaller the scale. A total of eight different
scales were used to compute this image. The right image shows the local average computed for each
pixel on the basis of the neighborhood selection mechanism.
with a neighborhood containing a sharp contrast step, whereas the smaller ﬁlter
kernel does not.
It is possible, therefore, to ﬁnd the largest neighborhood around a pixel that
does not contain sharp edges by examining differences of Gaussians at different
kernel sizes. For the image shown in Figure 21.25, the scale selected for each pixel
is shown in Figure 21.26 (left). Such a scale selection mechanism is employed by
the photographic tone reproduction operator (Reinhard et al., 2002) as well as in
Ashikhmin’s operator (Ashikhmin, 2002).
Once the appropriate neighborhood for each pixel is known, the Gaussian-
blurred average Lblur for this neighborhood (shown on the right of Figure 21.26)
may be used to steer the semi-saturation constant, such as for instance employed
by the photographic tone reproduction operator:
L
d = Lw
1+L blur
.
An alternative, and arguably better, approach is to employ edge-preserving
smoothing operators, which are designed speci ﬁcally for removing small details
while keeping sharp contrasts in tact. Several such ﬁlters, such as the bilateral
ﬁlter (Figure 21.27), trilateral ﬁlter, Susan ﬁlter, the LCIS algorithm and the
mean shift algorithm are suitable, although some of them are expensive to com-
pute (Durand & Dorsey, 2002; Choudhury & Tumblin, 2003; Pattanaik & Y ee,
2002; Tumblin & Turk, 1999; Comaniciu & Meer, 2002).
21.10 Other Approaches
Although the previous sections together discuss most tone reproduction operators
to date, there are one or two operators that do not directly ﬁt into the above cate-

21.10. Other Approaches 579
Figure 21.27. Sigmoidal compression (left) and sigmoidal compression using bilateral ﬁltering to
compute the semi-saturation constant (right). Note the improved contrast in the sky in the right image.
gories. The simplest of these are variations of logarithmic compression, and the
other is a histogram-based approach.
Dynamic range reduction may be accomplis hed by taking the logarithm, pro-
vided that this number is greater than 1. Any positive number may then be non-
linearly scaled between 0 and 1 using the following equation:
L
d(x,y)= logb(1+ Lv(x,y))
logb(1+ Lmax) .
While the base b of the logarithm above is not speci ﬁed, any choice of base will
do. This freedom to choose the base of the logarithm may be used to vary the
base with input luminance, and thus achieve an operator that is better matched
to the image being compressed (Drago, Myszkowski, Annen, & Chiba, 2003).
This method uses Perlin and Hoffert’s bi as function which takes user parameter p
(Perlin & Hoffert, 1989):
bias
p(x)= xlog10(p)/log10(1/2).
Figure 21.28. Logarithmic compression using base 10 loga rithms (left) and logarithmic compres-
sion with varying base (right).

580 21. T one Reproduction
Making the base b dependent on luminance and smoothly interpolating bases be-
tween 2 and 10, the logarithmic mapping above may be re ﬁned:
Ld(x,y)= log10(1+ Lv(x,y))
log10(1+ Lmax) · 1
log10
(
2+8
((Lv(x,y)
Lmax
)log10(p)/log10(1/2))).
For user parameterp, an initial value of around0.85 tends to yield plausible results
(Figure 21.28 (right)).
Alternatively, tone reproduction may be based on histogram equalization. Tra-
ditional histogram equalization aims to give each luminance value equal probabil-
ity of occurrence in the output image. Greg Ward re ﬁnes this method in a manner
that preserves contrast (Ward Larson, Rushmeier, & Piatko, 1997).
First, a histogram is computed from the luminances in the high dynamic range
image. From this histogram , a cumulative histogram is computed such that each
bin contains the number of pixels that have a luminance value less than or equal
to the luminance value that the bin represents. The cumulative histogram is a
monotonically increasing function. Plo tting the values in each bin against the
luminance values represented by each bin therefore yields a function which may
be viewed as a luminance mapping function. Scaling this function, such that the
vertical axis spans the range of the display device, yields a tone reproduction
operator. This technique is called histogram equalization.
Ward further reﬁned this method by ensuring that the gradient of this function
never exceeds 1. This means, that if the difference between neighboring values
in the cumulative histogram is too large, this difference is clamped to 1.T h i s
avoids the problem that small changes in luminance in the input may yield large
differences in the output image. In other words, by limiting the gradient of the
cumulative histogram to 1, contrast is never exaggerated. The resulting algorithm
is called histogram adjustment (see Figure 21.29).
Figure 21.29. A linearly scaled image (left) and a histogram adjusted image (right). Image created
with the kind permission of the Albin Polasek museum, Winter Park, Florida.

21.11. Night T onemapping 581
21.11 Night T onemapping
The tone reproduction operators discussed so far nearly all assume that the im-
age represents a scene under photopic viewing conditions, i.e., as seen at normal
light levels. For scotopic scenes, i.e., very dark scenes, the human visual system
exhibits distinctly different behavior. In particular, perceived contrast is lower,
visual acuity (i.e., the smallest detail that we can distinguish) is lower, and every-
thing has a slightly blue appearance.
To allow such images to be viewed correctly on monitors placed in photopic
lighting conditions, we may preprocess the image such that it appears as if we
were adapted to a very dark viewing environment. Such preprocessing frequently
takes the form of a reduction in brightness and contrast, desaturation of the im-
age, blue shift, and a reduction in visual acuity (Thompson, Shirley, & Ferwerda,
2002).
A typical approach starts by conver ting the image from RGB to XYZ. Then,
scotopic luminance V may be computed for each pixel:
V = Y
[
1.33
(
1+ Y +Z
X
)
−1.68
]
.
This single channel image may then be scaled and multiplied by an em-
pirically chosen bluish gray. An example is shown in Figure 21.30. If some
Figure 21.30. Simulated night scene using the
image shown in Figure 21.12.
pixels are in the photopic range, then
the night image may be created by lin-
early blending the bluish-gray image
with the input image. The fraction to
use for each pixel depends on V .
Loss of visual acuity may be mod-
eled by low-pass ﬁltering the night im-
age, although this would give an incor-
rect sense of blurriness. A better ap-
proach is to apply a bilateral ﬁlter to re-
tain sharp edges while blurring smaller
details (Tomasi & Manduchi, 1998).
Finally, the color transfer technique outlined in Section 21.3 may also be used
to transform a day-lit image into a night scene. The effectiveness of this approach
depends on the availability of a suitable night image from which to transfer colors.
As an example, the image in Figure 21.12 is transformed into a night image in
Figure 21.31.

582 21. T one Reproduction
Figure 21.31. The image on the left is used to transform the image of Figure 21.12 into a night
scene, shown here on the right.
21.12 Discussion
Since global illumination algorithms na turally produce high dynamic range im-
ages, direct display of the resulting images is not possible. Rather than resort to
linear scaling or clamping, a tone reproduction operator should be used. Any tone
reproduction operator is better than using no tone reproduction. Dependent on the
requirements of the application, one of several operators may be suitable.
For instance, real-time rendering applications should probably resort to a sim-
ple sigmoidal compression, since these are fast enough to also run in real time.
In addition, their visual quality is often good enough. The histogram adjustment
technique (Ward Larson et al., 1997) may also be fast enough for real-time oper-
ation.
For scenes containing a very high dynamic range, better compression may
be achieved with a local operator. However, the computational cost is frequently
substantially higher, leaving these operators suitable only for noninteractive ap-
plications. Among the fastest of the local operators is the bilateral ﬁlter due to the
optimizations afforded by this technique (Durand & Dorsey, 2002).
This ﬁlter is interesting as a tone reproduction operator by itself, or it may
be used to compute a local adaptation lev el for use in a sigmoidal compression
function. In either case, the ﬁlter respects sharp contrast changes and smoothes
over smaller contrasts. This is an important feature that helps minimize halo
artifacts, which are a common problem with local operators.
An alternative approach to minimize halo artifacts is the scale selection mech-
anism used in the photographic tone reproduction operator (Reinhard et al., 2002),
although this technique is slower to compute.
In summary, while a large number of tone reproduction operators is cur-
rently available, only a small number of fundamentally different approaches exist.
Fourier-domain and gradient-domain operators are both rooted in knowledge of

21.12. Discussion 583
image formation. Spatial-domain operators are either spatially variant (local) or
global in nature. These operators are usually based on insights gained from study-
ing the human visual system (and the visual system of many other species).

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
