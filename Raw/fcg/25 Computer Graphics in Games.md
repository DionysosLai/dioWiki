---
title: "25 Computer Graphics in Games"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 25 Computer Graphics in Games

25
Naty Hoffman
Computer Graphics in Games
Of all the applications of computer graphics, computer and video games attract
perhaps the most attention. The graphics methods selected for a given game have
a profound effect, not only on the game engine code, but also on the art asset
creation, and even sometimes on the gameplay, or core game mechanics.
Although game graphics rely on the mat erial in all of the preceding chap-
ters, two chapters are particularly germane. Games need to make highly ef ﬁcient
use of graphics hardware, so an understanding of the material in Chapter 17 is
important.
In this chapter, I will detail the speci ﬁc considerations that apply to graph-
ics in game development, from the platforms on which games run to the game
production process.
25.1 Platforms
Here, I use the term platform to refer to a speci ﬁc combination of hardware, op-
erating system, and API (application programming interface) for which a game
is designed. Games run on a large variety of platforms, ranging from virtual
machines used for browser-based games to dedicated game consoles using spe-
cialized hardware and APIs.
643

644 25. Computer Graphics in Games
In the past, it was common for games to be designed for a single platform.
The increasing cost of game development has made this rare; multiplatform game
development is now the norm. The incremental increase in development cost to
support multiple platforms is more than r epaid by a potential doubling or tripling
of the customer base.
Some platforms are quite loosely de ﬁned. For example, when developing a
game for the Windows PC platform, the developer must account for a very large
variety of possible hardware conﬁgurations. Games are even expected to run (and
run well) on PC con ﬁgurations that did not exist when the game was developed!
This is only possible due to the abstractions afforded by the APIs de ﬁning the
Windows platform.
One way in which developers account for wide variance in graphics perfor-
mance is by scaling—adjusting graphics quality in response to system capabil-
ities. This can ensure reasonable performance on low-end systems, while still
achieving competitive visuals on high-performance systems. This adjustment is
sometimes done automatically by pro ﬁling the system performance, but more of-
ten this control is left in the hands of the user, who can best judge his personal
preferences for quality versus speed. Display resolution is easiest to adjust, fol-
lowed by antialiasing quality. It is also fairly common to offer several quality
levels for visual effects such as shadows and motion blur, including the option of
turning the effect off entirely.
Differences in graphics performance can be so large that some machines may
not run the game at a playable frame rate, even with the lowest quality settings;
for this reason PC game developers publish minimum and recommended machine
speciﬁcations for each game.
As platforms, game consoles are strictly de ﬁned. When developing a game
for, e.g., Nintendo’s Wii console, the developer knows exactly what hardware the
game will run on. If the platform’s hardware implementation is changed (often
done to reduce manufacturing costs), the console manufacturer must ensure that
the new implementation behaves exactly like the previous one, including timing
and performance. This is not to say that the console developer’s task is easy; con-
sole APIs tend to be much less abstract and closer to the underlying hardware.
This gives console development its own set of dif ﬁculties. In some sense, mul-
tiplatform development (which commonly includes at least two different console
platforms and often Windows as well) is the hardest of all, since the multiplatform
game developer has neither the assurance of a ﬁxed platform or the convenience
of a single high-level API.
Browser-based virtual machines such as Adobe Flash are an interesting class
of game platforms. Although such virtual machines run on a wide class of hard-

25.1. Platforms 645
ware from personal computers to mobile phones, the high degree of abstraction
provided by the virtual machine results in a stable and uni ﬁed development plat-
form. The relative ease of development for these platforms and the huge pool
of potential customers makes them increasingly attractive to game developers.
However, these platforms are deﬁ ned by the lowest common denominator of the
supported hardware, and virtual machines have lower performance than native
code on any given platform. For these reasons, such platforms are best suited to
games with modest graphics requirements.
Platforms can also be characterized by their openness to development, which
is a business or legal distinction rather than a technical one. For example, Win-
dows is open in the sense that development tools are widely available, and there
are no gatekeepers controlling access to the marketplace of Windows games. Ap-
ple’s iPhone is a somewhat more restricted platform in that all applications need
to pass a certiﬁcation process and certain classes of applications are banned out-
right. Consoles are the most restrictive game platforms, where access to the de-
velopment tools is tightly controlled. This is opening up somewhat with the in-
troduction of online console game marketplaces, which tend to be more open. A
particularly interesting example is Microsoft’s Xbox LIVE Community Games
service, where the development tools are freely available and the “gatekeeping”
is performed primarily by peer review. Games distributed through this service
must use a virtual machine platform provided by Microsoft for security
reasons.
The game platform determines many elements of the game experience. For
example, PC gamers use keyboard and mouse, while console gamers use special-
ized game controllers. Many console gam es support multiple players on the same
console, either sharing a screen or providing a window for each player. Due to the
difﬁculty of sharing keyboard and mouse, this type of play is not found on PC. A
handheld game system will have a different control scheme than a touch-screen
phone, etc.
Although game platforms vary widely, some common trends can be discerned.
Most platforms have multiple processing cores, divided between general-purpose
(CPU) and graphics-speci ﬁc (GPU). Performance gains over time are due mostly
to increases in core count; gains in individual core performance are modest. As
GPU cores grow in generality, the lines between GPU and CPU cores are increas-
ingly blurred. Storage capacity tends to in crease at a slower rate than processing
power, and communication bandwidth (between cores as well as between each
core and storage) grows at a slower pace still.

646 25. Computer Graphics in Games
25.2 Limited Resources
One of the primary challenges of game graphics is the need to manage multiple
pools of limited resources. Each platform imposes its own constraints on hard-
ware resources such as processing time, storage, and memory bandwidth. At a
higher level, development resources also need to be managed; there is a ﬁxed-size
team of programmers, artists, and game designers with limited time to complete
the game, hopefully without working too much overtime! This needs to be taken
into account when deciding which graphics techniques to adopt.
25.2.1 Processing Time
Early game developers only had to worry about budgeting a single processor.
Current game platforms contain multiple CPU and GPU cores. These pro-
cessors need to be carefully synchroni zed to avoid deadlocks or excessive
stalls.
Since the time consumed by a single rendering command is highly variable,
graphics processors are decoupled from the rest of the system via a command
buffer. This buffer acts as a queue; commands are deposited on one end and
the GPU reads rendering commands from the other. Increasing the size of this
buffer decreases the chances of GPU starvation. It is fairly common for games to
buffer an entire frame’s worth of rendering commands before sending them to the
GPU; this guarantees that GPU starvation does not occur. However, this approach
requires reserving enough storage space for two full frame’s worth of commands
(the GPU works on one, while the CPU deposits commands in the other). It
also increases the latency between the user’s input and the display, which can be
problematic for fast-paced games.
Processing budgets are determined by the frame rate, which is the frequency
at which the frame buffer is refreshed with new renderings of the scene. On ﬁxed
platforms (such as consoles), the frame rate experienced by the user is essentially
the same one seen by the game developer, so fairly strict frame–rate limits can be
imposed. Most games target a frame rate of 30 frames per second (fps); in games
where response latency is especially important, the target is often 60 fps. On
highly variable platforms (such as PCs), the frame-rate budgets are (by necessity)
deﬁned more loosely.
The required frame rate gives the graphics programmer a ﬁxed budget per
frame to work with. In the case of a 30 fps target, the CPU cores have 33 millisec-
onds to gather inputs, process the game logic, perform any physical simulations,
traverse the scene description, and sen d the rendering commands to the graphics

25.2. Limited Resources 647
hardware. In parallel, other tasks such as audio and network processing must be
handled, with their own required response times. While this is happening, the
GPU is typically executing the graphics commands submitted during the previous
frame.
In most cases, CPU cores are a homogeneous resource; all cores are the same,
and any of them are equally well suited to a given workload (there are some
exceptions, such as the Cell processor used in Sony’s PLA YST A TION 3 console).
In contrast, GPUs contain a heterogeneous mix of resources, each special-
ized to a certain set of tasks. Some of these resources consist of ﬁxed-function
hardware (for triangle rasterization, alpha blending, and texture sampling), and
some are programmable cores. On older GPUs, programmable cores were further
differentiated into vertex and pixel processing cores; newer GPU designs have
uniﬁed shader cores which can execute any of the programmable shader types.
Such heterogeneous resources are budgeted separately. Typically, at any point,
only one resource type will be the bottleneck, and the others will have excess ca-
pacity. On the one hand, this is good, since this capacity can be leveraged to
improve visual quality without decreasin g performance. On the other hand, it
makes it harder to improve performance, since decreasing usage of any of the
non-bottleneck resources will have no effect. Even decreasing usage of the bot-
tleneck resource may only improve performance slightly, depending on the degree
of utilization of the “next bottleneck.”
25.2.2 Storage
Game platforms, like any modern computing system, possess multi-stage stor-
age hierarchies, with smaller, faster memory types at the top and larger, slower
storage at the bottom. This arrangement is borne of engineering necessity, al-
though it does complicate life for the developer. Most platforms include optical
disc storage, which is extremely slow and is used mostly for delivery. On plat-
forms such as Windows, a lengthy installation process is performed once to move
all data from the optical disc onto the hard drive, which is signi ﬁcantly faster.
The optical disc is never used again (except as an anti-piracy measure). On con-
sole platforms, this is less common, although it does sometimes happen when a
hard drive is guaranteed to be present, as on Sony’s PLA YST A TION 3 console.
More often, the hard drive (if present) is only used as a cache for the optical
disc.
The next step up the memory hierarchy is RAM, which on many platforms is
divided into general system RAM and VRAM (video RAM) which bene ﬁts from
a high-speed interface to the graphics hardware. A game level may be too large to

648 25. Computer Graphics in Games
ﬁt in RAM, in which case the game developer needs to manage moving the data
in and out of RAM as needed. On platforms such as Windows, virtual memory
is often used for this. On console platf orms, custom data streaming and caching
systems are typically employed.
Finally, both the CPU and GPU boast various kinds of on-chip memory and
caches. These are extremely small and fa st and are usually managed by the graph-
ics API.
Graphics resources take up a lot of memory, so they are a primary focus of
storage budgets in game development. Textures are usually the greatest memory
consumers, followed by geometry (vertex data), and ﬁnally other types of graphics
data such as animations. Not all memory can be used for graphics—audio also
takes up a fair bit, and game logic may use sizeable data structures. As in the case
of processing time, budgeting tends to be somewhat looser on Windows, where
the exact amount of memory present on the user’s system is unknown and virtual
memory covers a multitude of sins. In contrast, memory budgeting on console
platforms is quite strict—often the l ead programmer keeps track of memory on a
spreadsheet and a programmer requiring more memory for their system needs to
beg, borrow, or steal it from someone else.
The various levels of the memory hierarchy differ not only in size, but also in
access speed. This has two separate dimensions: latency and bandwidth.
Latency is the time that elapses between a storage access request and its ﬁnal
fulﬁllment. This varies from a few clock cycles (for on-chip cache) to millions of
clock cycles (for data residing on optical disc). Latency is usually an issue for read
access (although write latency can also be an issue if the result needs to be read
back from memory soon after). In some cases, the read request is blocking,w h i c h
means that the processor core that submitted the read can do nothing else until the
request is ful ﬁlled. In other cases, the read is non-blocking; the processing core
can submit the read request, do other types of processing, and then use the results
of the read after it has arrived. Texture accesses by the GPU are an example of
non-blocking reads; an important aspect of GPU design is to ﬁnd ways to “hide”
texture read latency by performing unrelated computations while the texture read
is being ful ﬁlled.
For this latency hiding to work, there must be a suf ﬁcient amount of computa-
tion relative to texture accesses. This is an important consideration for the shader
writer; the optimal mix of computation vs. texture access keeps changing (in fa-
vor of more computation) as memory fails to keep up with increases in processing
power.
Bandwidth refers to the maximum rate of transfer to and from storage. It is
typically measured in gigabytes per second.

25.3. Optimization T echniques 649
25.2.3 Development Resources
Besides hardware resources, such as processing power and storage space, the
game graphics programmer also has to contend with a different kind of limited
resource—the time of his teammates! When selecting graphics techniques, the
engineering resources needed to implem ent each technique must be taken into ac-
count, as well as any tools necessary to co mpute the input data (in many cases,
tools can take signi ﬁcantly more time than implementing the technique itself).
Perhaps most importantly, the impact on artist productivity must be taken into ac-
count. Most graphics techniques use assets created by game artists, who comprise
by far the largest part of most modern game teams. The graphics programmer
must foster the artist’s pr oductivity and creativity, which will ultimately deter-
mine the visual quality of the game.
25.3 Optimization T echniques
Making wise use of these limited resources is the primary challenge of the game
graphics programmer. To this end, various optimization techniques are commonly
employed.
In many games, pixel shader processing is a primary bottleneck. Most GPUs
contain hierarchical depth-culling hardware which can avoid executing pixel
shaders on occluded surfaces. To make good use of this hardware, opaque objects
can be rendered back-to-front. Alternatively, optimal depth-culling usage can be
achieved by performing a depth prepass, i.e., rendering all the opaque objects into
the depth buffer (without any color output or pixel shaders) before rendering the
scene normally. This does incur some overhead (due to the need to render every
object twice), but in many cases the performance gain is worth it.
The fastest way to render an object is to not render it at all; thus any method
of discerning early on that an object is occluded can be useful. This saves not
only pixel processing but also vertex processing and even CPU time that would
be spent submitting the object to the graphics API. View frustum culling (see
Section 8.4.1) is universally employed, but in many games it is not suf ﬁcient.
High-level occlusion culling algorithms are often used, utilizing data structures
such as PVS (potentially visible sets) or BSP (binary spatial partitioning) trees to
quickly narrow down the pool of potentially visible objects.
Even if an object is visible, it may be at such a distance that most of its detail
can be removed without apparent effect. LOD (level-of-detail) algorithms render
different representations of an object based on distance (or other factors, such

650 25. Computer Graphics in Games
Figure 25.1. Two examples of game objects at a varying level of detail. The small inset images
show the relative sizes at which the simpli ﬁed models might be used. Upper row of images courtesy
Crytek; lower row courtesy Valve Corp.
as screen coverage or importance). This can save signi ﬁcant processing, vertex
processing in particular. Examples can be seen in Figure 25.1.
In many cases, processing can be performed before the game even starts. The
results of such preprocessing can be stored and used each frame, thus speeding
up the game. This is most commonly employed for lighting, where global illumi-
nation algorithms are utilized to comput e lighting throughout the scene and store
it in lightmaps and other data structures for later use.
25.4 Game T ypes
Since game requirements vary widely, the selection of graphics techniques is
driven by the exact type of game being developed.
The allocation of processing time depends strongly on the frame rate. Cur-
rently, most console games tend to target 30 frames per second, since this enables
much higher graphics quality. However, certain game types with fast gameplay
require very low latency, and such games typically render at 60 frames per second.

25.4. Game T ypes 651
This includes music games such as Guitar Hero and ﬁrst-person shooters such as
Call of Duty .
The frame rate determines the available time to render the scene. The compo-
sition of the scene itself also varies widely from game to game. Most games have
a division between background geometry (scenery, mostly static) and foreground
geometry (characters and dynamic objects). These are handled differently by the
rendering engine. For example, backgr ound geometry will often have lightmaps
containing precomputed lighting, which is not feasible for foreground objects.
Precomputed lighting is typically applied to foreground objects via some type of
volumetric representation which can take account of the changing position of each
object over time.
Some games have relatively enclosed environments, where the camera re-
mains largely in place. The purest examples are ﬁghting games such as the Street
Fighter series, but this is also true to some extent for games such as Devil May Cry
and God of W ar. These games have cameras that are not under direct player con-
trol, and the game play tends to move from one enclosed environment to another,
spending a signi ﬁcant amount of playing time in each. This allows the game de-
veloper to lavish large amounts of resources (processing, storage, and artist time)
on each room or enclosed environment, resulting in very high levels of graphics
ﬁdelity.
Other games have extremely large worlds, where the player can move about
freely. This is most true for “sandbox games” such as the Grand Theft Auto series
and online role-playing games such as W orld of W arcraft. Such games pose great
challenges to the graphics developer, since resource allocation is very dif ﬁcult
when during each frame the player can see a large extent of the world. Further
complicating things, the player can freely go to some formerly distant part of the
world and observe it from up close. Such games typically have changing time of
day, which makes precomputation of lighting dif ﬁcult at best, if not impossible.
Most games, such as ﬁrst-person shooters, are somewhere between the two
extremes. The player can see a fair amount of scenery each frame, but movement
through the game world is somewhat constrained. Many games also have a ﬁxed
time of day for each game level, for ease of lighting precomputation.
The number of foreground objects rendered also varies widely between game
types. Real-time strategy games such as the Command and Conquer series often
have many dozens, if not hundreds, of units visible on screen. Other types of
games have more limited quantities of visible characters, with ﬁghting games
at the opposite extreme, where only two characters are visible, each rendered
with extremely high detail. A distinction must be drawn between the number of
characters visible at any time (which affects budgeting of processing time) and

652 25. Computer Graphics in Games
Figure 25.2. Crysis exempliﬁes the realistic and detailed graphics expected of ﬁrst-person shooters.
Image courtesy Crytek.
the number of unique characters which can potentially be visible at short notice
(which affects storage budgets).
The type or genre of game also determines audience expectations of the graph-
ics. For example, ﬁrst-person shooters have historically had very high levels of
graphics ﬁdelity, and this expectation drives the graphics design when developing
new games in that genre; see Figure 25.2. On the other hand, puzzle games have
typically had relatively simplistic graphics, so most game developers will not in-
vest large amounts of programming or art resources into developing photorealistic
graphics for such games.
Although most games aim for a photorealistic look, a few do attempt more
stylized rendering. One interesting example of this is Okami, which can be seen
in Figure 25.3.
The management of development resources also differs by game type. Most
games have a closed development cycle of one to two years, which ends after
the game ships. Recently it has become common to have downloadable content
(DLC), which can be purchased after the game ships, so some development re-
sources need to be reserved for that. Persistent-world online games have a never-
ending development process where new content is continually being generated,
at least as long as the game is economically viable (which may be a period of
decades).

25.5. The Game Production Process 653
Figure 25.3. An example of highly stylized, non-photorealistic rendering from the game Okami.
Image courtesy Capcom Entertainment, Inc.
The creative exploitation of the speci ﬁc requirements and restrictions of a
particular game is the hallmark of a skilled game graphics programmer. A good
example is the game LittleBigPlanet, which has a “two-and-a-half-dimensional”
game world comprising a small number of two-dimensional layers, as well as a
noninteractive background. Th e graphics quality of this game is excellent, driven
by the use of unusual rendering techniques specialized to this type of environ-
ment; see Figure 25.4.
25.5 The Game Production Process
The game production process starts with the basic game design or concept. In
some cases (such as sequels), the basic gameplay and visual design is clear, and
only incremental changes are made. In the case of a new game type, extensive
prototyping is needed to determine gameplay and design. Most cases sit some-
where in the middle, where there are som e new gameplay elements and the visual
design is somewhat open. After this step there may be a greenlight stage where

654 25. Computer Graphics in Games
Figure 25.4. The LittleBigPlanet developers took care to choose techniques that ﬁt the game’s
constraints, combining them in unusual ways to achieve stunning results. LittleBigPlanet © 2007 Sony
Computer Entertainment Europe. Developed by Media Molecule. LittleBigPlanet is a trademark of
Sony Computer Entertainment Europe.
some early demo or concept is shown to the game publisher to get approval (and
funding!) for the game.
The next step is typically pre-production. While other teams are working
on ﬁnishing up the last game, a small core team works on making any needed
changes to the game engine and production tool chain, as well as working out the
rough details of any new gameplay elements. This core team is working under a
strict deadline. After the existing gam e ships and the rest of the team comes back
from a well-deserved vacation, the entire tool chain and engine must be ready for
them. If the core team misses this deadline, several dozen developers may be left
idle—an extremely expensive proposition!
Full production is the next step, with the entire team creating art assets, de-
signing levels, tweaking gameplay, and implementing further changes to the game
engine. In a perfect world, everything done during this process would be used in
the ﬁnal game, but in reality there is an iterative nature to game development
which will result in some work being thrown out and redone. The goal is to min-
imize this with careful planning and prototyping.
When the game is functionally complete, the ﬁnal stage begins. The term
alpha release usually refers to the version which marks the start of extensive
internal testing, beta release to the one which marks the start of extensive external
testing, and gold release to the ﬁnal release submitted to the console manufacturer,

25.5. The Game Production Process 655
but different companies have slightly varying de ﬁnitions of these terms. In any
case, testing, or quality assurance (QA) is an important part of this phase, and it
involves testers at the game development studio, at the publisher, at the console
manufacturer, and possibly external QA contractors as well. These various rounds
of testing result in bug reports which are submitted back to the game developers
and worked on until the next release.
After the game ships, most of the developers go on vacation for a while, but
as m a l lt e a mm a yh a v et os t a yt ow o r ko npatches or downloadable content. In
the meantime, a small core team has been working on pre-production for the next
game.
Art asset creation is an aspect of game production that is particularly relevant
to graphics development, so I will go into it in some detail.
25.5.1 Asset Creation
While the exact process of art asset creation varies from game to game, the outline
I give here is fairly representative. In the past, a single artist would create an
entire asset from start to ﬁnish, but this process is now much more specialized,
involving people with different skill se ts working on each asset at various times.
Some of these stages have clear dependencies (for example, a character cannot be
animated until it is rigged and cannot be rigged before it is modeled). Most game
developers have well-deﬁned approval processes, where the art director or a lead
artist signs off on each stage before the asset is sent on to the next. Ideally an
asset proceeds through each stage exactly once, but in practice changes may be
made that require resubmission.
Initial Modeling
Typically the art asset creation process starts by modeling the object geometry.
This step is performed in a general- purpose modeling package such as Maya,
MAX or Softimage. The modeled geometry will be passed directly to the game
engine, so it is important to minimize vertex count while preserving good sil-
houettes. Character meshes must also be constructed so as to be amenable to
animation.
In this stage, a two-dimensional surface parameterization for textures is usu-
ally created. It is important that this parameterization be highly continuous, since
discontinuities require vert ex duplication and may cause ﬁltering artifacts. An
example of a mesh with its associated texture parameterization is shown in Fig-
ure 25.5.

656 25. Computer Graphics in Games
Figure 25.5. A mesh being modeled in Maya, with associated texture parameterization. Image
courtesy Keith Bruns.
Te x t u r i n g
In the past, texturing was a straightforward process of painting a color texture, typ-
ically in Photoshop. Now, specialized detail modeling packages such as ZBrush
or Mudbox are commonly used to sculpt ﬁne surface detail. Figures 25.6 and 25.7
show an example of this process.
If this additional detail were to be represented with actual geometry, millions
of triangles would be needed. Instead, the detail is commonly “baked” into a nor-
mal map which is applied onto the original, coarse mesh, as shown in Figures 25.8
and 25.9.
Besides normal maps, multiple textures containing surface properties such as
diffuse color, specular color, and smoothness (specular power) are also created.
These are either painted directly on the surface in the detail modeling application,
or in a two-dimensional application such as Photoshop. All of these texture maps
use the surface parameterization de ﬁned in the initial modeling phase. When the
texture is painted in a two-dimensional painting application, the artist must fre-
quently switch between the painting application and some other application which
can show a three-dimensional rendering of the object with the texture applied.
This iterative process is illustrated in Figures 25.10, 25.11, 25.12, and 25.13.

25.5. The Game Production Process 657
Figure 25.6. The mesh from Figure 25.5 has been brought into ZBrush for detail modeling. Image
courtesy Keith Bruns.
Figure 25.7. The mesh from Figure 25.6, with ﬁne detail added to it in ZBrush. Image courtesy
Keith Bruns.

658 25. Computer Graphics in Games
Figure 25.8. A visualization (in ZBrush) of the mesh from Figure 25.6, rendered with a normal
map derived from the detailed mesh in Figure 25.7. The bottom of the ﬁgure shows the interface for
ZBrush’s “Zmapper” tool, which was used to derive the normal map. Image courtesy Keith Bruns.
Figure 25.9. The normal map used in Figure 25.8. In this image, the red, green, and blue channels
of the texture contain the X, Y , and Z coordinates of the surface normals. Image courtesy Keith Bruns.

25.5. The Game Production Process 659
Figure 25.10. An early version of a diffuse color texture for the mesh from Figure 25.8, shown in
Photoshop. Image courtesy Keith Bruns.
Figure 25.11. A rendering (in ZBrush) of the mesh with normal map and early diffuse color texture
(from Figure 25.10) applied. Image courtesy Keith Bruns.

660 25. Computer Graphics in Games
Figure 25.12. Final version of the color texture from Figure 25.10. Image courtesy Keith Bruns.
Figure 25.13. Rendering of the mesh with normal map and ﬁnal color texture (from Figure 25.12)
applied. Image courtesy Keith Bruns.

25.5. The Game Production Process 661
Figure 25.14. Shader conﬁguration in Maya. The interface on the right is used to select the shader,
assign textures to shader inputs, and set the values of non-texture shader inputs (such as the “Specular
Color” and “Specular Power” sliders). The renderi ng on the left is updated dynamically while these
properties are modiﬁed, enabling immediate visual feedback. Image courtesy Keith Bruns.
Shading
Shaders are typically applied in the same application used for initial modeling. In
this process, a shader (from the set of shaders de ﬁned for that game) is applied
to the mesh. The various textures resulting from the detail modeling stage are
applied as inputs to this shader, using the surface parameterization de ﬁned during
initial modeling. V arious othe r shader inputs are set via vi sual experimentation
(“tweaking”); see Figure 25.14.
Lighting
In the case of background scenery, lighting artists will typically start their work
after modeling, texturing, and shading have been completed. Light sources are
placed and their effect computed in a prepr ocessing step. The results of this pro-
cess are stored in lightmaps for later use by the rendering engine.
Animation
Character meshes undergo several additiona l steps related to animation. The pri-
mary method used to animate game characters is skinning. This requires a rig,
consisting of a hierarchy of transform nodes that is attached to the character, a

662 25. Computer Graphics in Games
Figure 25.15. Morph target interface in Maya. The bottom row shows four different morph targets,
and the model at the top shows the effects of combini ng several morph targets together. The interface
at the upper left is used to control the degree to which each morph target is applied. Image courtesy
Keith Bruns.
process known as rigging. The area of effect of each transform node is painted
onto a subset of mesh vertices. Finally, animators create animations that move,
rotate, and scale these transform nodes, “dragging” the mesh behind them.
A typical game character will have many dozens of animations, correspond-
ing to different modes of motion (walking, running, turning) as well as different
actions such as attacks. In the case of a main character, the number of animations
can be in the hundreds. Transitions between different animations also need to be
deﬁned.
For facial animation, another technique, called morph targets is sometimes
employed. In this technique, the mesh vertices are directly manipulated to deform
the mesh. Different copies of the deformed mesh are stored (e.g., for different
facial expressions) and combined by the game engine at runtime. The creation of
morph targets is shown in Figure 25.15.
Notes
There is a huge amount of information on real-time rendering and game pro-
gramming available, both in books and online. Here are some resources I can
recommend from personal familiarity.

25.5. The Game Production Process 663
Game Developer Magazine is a good source of information on game develop-
ment, as are slides from the talks given at the annual Game Developers Confer-
ence (GDC) and Microsoft’s Gamefest conference. The GPU Gems and ShaderX
book series also contain good information—all of the former and the ﬁrst two of
the latter are also available online.
Eric Lengyel’s Mathematics for 3D Game Programming & Computer Graph-
ics, now in its second edition, is a good reference for the various types of math
used in graphics and games. A speci ﬁc area of game programming that is closely
related to graphics is collision detection, for which Christer Ericson’s Real-Time
Collision Detection is the de ﬁnitive resource.
Since its ﬁrst edition in 1999, Eric Hain es and Tomas Akenine-M¨oller’s Real-
Time Rendering has endeavored to cover this fast-growing ﬁeld in a thorough
manner. As a longtime fan of this book, I was glad to have the opportunity to be
a coauthor on the third edition, which came out in mid-2008.
Reading is not enough—make sure you play a variety of games regularly to
get a good idea of the requirements of various game types, as well as the current
state of the art.
Exercises
1. Examine the visuals of two dissimilar games. What differences can you
deduce in the graphics requirements of these two games? Analyze the effect
on rendering time, storage budgets, etc.

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
**关联概念**: [[光线追踪]]
