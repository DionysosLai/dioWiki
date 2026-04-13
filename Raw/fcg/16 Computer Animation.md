---
title: "16 Computer Animation"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 16 Computer Animation

16
Michael Ashikhmin
Computer Animation
Animation is derived from the Latin anima and means the act, process, or result
of imparting life, interest, spirit, motion, or activity. Motion is a de ﬁning property
of life and much of the true art of animation is about how to tell a story, show
emotion, or even express subtle details of human character through motion. A
computer is a secondary tool for achieving these goals—it is a tool which a skillful
animator can use to help get the result he wants faster and without concentrating
on technicalities in which he is not inte rested. Animation without computers,
which is now often called “traditional” animation, has a long and rich history of
its own which is continuously being written by hundreds of people still active in
this art. As in any established ﬁeld, some time-tested rules have been crystallized
which give general high-level guidance to how certain things should be done and
what should be avoided. These principl es of traditional animation apply equally
to computer animation, and we will discuss some of them in this chapter.
The computer, however, is more than just a tool. In addition to making the
animator’s main task less tedious, computers also add some truly unique abil-
ities that were simply not available or were extremely dif ﬁcult to obtain be-
fore. Modern modeling tools allow the relatively easy creation of detailed three-
dimensional models, rendering algor ithms can produce an impressive range of
appearances, from fully photorealistic to highly stylized, powerful numerical sim-
ulation algorithms can help to produce desired physics-based motion for partic-
ularly hard to animate objects, and motion capture systems give the ability to
405

406 16. Computer Animation
record and use real-life motion. These developments led to an exploding use
of computer animation techniques in motion pictures and commercials, automo-
tive design and architecture, medicine and scienti ﬁc research, among many other
areas. Completely new domains and appli cations have also appeared including
fully computer-animated feature ﬁlms, virtual/augmented reality systems, and, of
course, computer games.
Other chapters of this book cover many of the developments mentioned above
(for example, geometric modeling and rendering) more directly. Here, we will
provide an overview only of techniques and algorithms directly used to create and
manipulate motion. In particular, we will loosely distinguish and brie ﬂy describe
four main computer animation approaches:

Keyframing gives the most direct control to the animator who provides nec-
essary data at some moments in time and the computer ﬁlls in the rest.
 Procedural animation involves specially designed, often empirical, mathe-
matical functions and procedures whose output resembles some particular
motion.

Physics-based techniques solve differential equation of motion.
 Motion capture uses special equipment or techniques to record real-world
motion and then transfers this motion into that of computer models.
We do not touch upon the artistic side of the ﬁeld at all here. In general, we cannot
possibly do more here than just scratch the surface of the fascinating subject of
creating motion with a computer. We hope that readers truly interested in the
subject will continue their journey well beyond the material of this chapter.
16.1 Principles of Animation
In his seminal 1987 SIGGRAPH paper (Lasseter, 1987), John Lasseter brought
key principles developed as early as th e 1930’s by traditional animators of Walt
Disney studios to the attention of the then-ﬂ edgling computer animation com-
munity. Twelve principles were mentioned: squash and stretch, timing, antic-
ipation, follow through and overlapping action, slow-in and slow-out , staging,
arcs, secondary action, straight-ahead and pose-to-pose action , exaggeration,
solid drawing skill,a n dappeal. Almost two decades later, these time-tested rules,
which can make a difference between a nat ural and entertaining animation and a
mechanistic-looking and boring one, are as important as ever. For computer ani-
mation, in addition, it is very important to balance control and ﬂexibility given to

16.1. Principles of Animation 407
the animator with the full advantage of the computer’s abilitie s. Although these
principles are widely known, many fact ors affect how much attention is being
paid to these rules in practice. While a character animator working on a feature
ﬁlm might spend many hours trying to follow some of these suggestions (for ex-
ample, tweaking his timing to be just right), many game designers tend to believe
that their time is better spent elsewhere.
16.1.1 Timing
Timing, or the speed of action, is at the heart of any animation. How fast things
happen affects the meaning of action, emotional state, and even perceived weight
of objects involved. Depending on its speed, the same action, a turn of a charac-
ter’s head from left to right, can mean an ything from a reaction to being hit by a
heavy object to slowly seeking a book on a bookshelf or stretching a neck mus-
cle. It is very important to set timing appropriate for the speci ﬁc action at hand.
Action should occupy enough time to be noticed while avoiding too slow and
potentially boring motions. For computer animation projects involving recorded
sound, the sound provides a natural timing anchor to be followed. In fact, in most
productions, the actor’s voice is recorded ﬁrst and the complete animation is then
synchronized to this recording. Since large and heavy objects tend to move slower
than small and light ones (with less acceler ation, to be more precise), timing can
be used to provide signi ﬁcant information about the weight of an object.
16.1.2 Action Layout
At any moment during an animation, it should be clear to the viewer what idea (ac-
tion, mood, expression) is being presented. Good staging, or high-level planning
of the action, should lead a viewer’s eye to where the important action is currently
concentrated, effectively telling him “l ook at this, and now, look at this” without
using any words. Some familiarity with human perception can help us with this
difﬁcult task. Since human visual systems r eact mostly to relative changes rather
than absolute values of stimuli, a sudden motion in a still environment or lack of
motion in some part of a busy scene naturally draws attention. The same action
presented so that the silhouette of the object is changing can often be much more
noticeable compared with a frontal arra ngement (see Figure 16.1 (bottom left)).
On a slightly lower level, each action can be split into three parts: anticipation
(preparation for the action), the action itself, and follow-through (termination of
the action). In many cases, the action itself is the shortest part and, in some sense,

408 16. Computer Animation
Figure 16.1. Action layout. Left: Staging action prope rly is crucial for bringing attention to
currently important motion. The act of raising a hand would be prominent on the top but harder to
notice on the bottom. A change in nose length, on the c ontrary, might be completely invisible in the
ﬁrst case. Note that this might be intentionally hi dden, for example, to be suddenly revealed later.
Neither arrangement is particularly good if both m otions should be attended to. Middle: The amount
of anticipation can tell much about the following action. The action which is about to follow (throwing
a ball) is very short, but it is clear what is about to happen. The more wound up the character is, the
faster the following action is perceived to be. Right : The follow-through phase is especially important
for secondary appendages (hair) whose motion follows the leading part (head). The motion of the
head is very simple, but leads to nontrivial follow-through behavior of the hair itself. It is impossible
to create a natural animation without a follow-through phase and overlapping action in this case.
Figure courtesy Peter Shirley and Christina Villarruel.
the least interesting. For example, kicking a football might involve extensive
preparation on the part of the kicker and long “visual tracking” of the departing
ball with ample opportunities to show the stress of the moment, emotional state
of the kicker, and even the reaction to the expected result of the action. The action
itself (motion of the leg to kick the ball) is rather plain and takes just a fraction of
a second in this case.
The goal of anticipation is to prepare the viewer for what is about to hap-
pen. This becomes especially important if the action itself is very fast, greatly

16.1. Principles of Animation 409
important, or extremely dif ﬁcult. Creating a more extensive anticipation for such
actions serves to underscore these properties and, in case of fast events, makes
sure the action will not be missed (see Figure 16.1 (bottom center)).
In real life, the main action often causes one or more other overlapping ac-
tions. Different appendages or loose parts of the object typically drag behind the
main leading section and keep moving for a while in the follow-through part of
the main action as shown in Figure 16.1 (bottom right). Moreover, the next ac-
tion often starts before the previous one is completely over. A player might start
running while he is still tracking the ball he just kicked. Ignoring such natural
ﬂow is generally perceived as if there are pauses between actions and can result in
robot-like mechanical motion. While overl apping is necessary to keep the motion
natural, secondary action is often added by the animator to make motion more
interesting and achieve realistic complexity of the animation. It is important not
to allow secondary action to dominate the main action.
16.1.3 Animation T echniques
Several speciﬁc techniques can be used to make motion look more natural. The
most important one is probably squash and stretch which suggests to change the
shape of a moving object in a particular way as it moves. One would generally
stretch an object in the direction of motion and squash it when a force is ap-
plied to it, as demonstrated in Figure 16.2 for a classic animation of a bouncing
ball. It is important to preserve the total volume as this happens to avoid the il-
lusion of growing or shrinking of the object. The greater the speed of motion (or
the force), the more stretching (or squashing) is applied. Such deformations are
used for several reasons. For very fast motion, an object can move between two
Figure 16.2. Classic ex-
ample of applying the squash
and stretch principle. Note
that the volume of the bounc-
ing ball should remain roughly
the same throughout the ani-
mation.
sequential frames so quickly that ther e is no overlap between the object at the
time of the current frame and at the time of the previous frame which can lead
to strobing (a variant of aliasing). Having the object elongated in the direction of
motion can ensure better overlap and helps the eye to ﬁght this unpleasant effect.
Stretching/squashing can also be used to show ﬂexibility of the object with more
deformation applied for more pliable materials. If the object is intended to appear
as rigid, its shape is purposefully left the same when it moves.
Natural motion rarely happens along straight lines, so this should generally be
avoided in animation and arcs should be used instead. Similarly, no real-world
motion can instantly change its speed—this would require an inﬁ nite amount of
force to be applied to an object. It is desirable to avoid such situations in anima-
tion as well. In particular, the motion should start and end gradually ( slow in and
out). While hand-drawn animation is sometimes done via straight-ahead action

410 16. Computer Animation
with an animator starting at the ﬁrst frame and drawing one frame after another in
sequence until the end, pose-to-pose action, also known as keyframing,i sm u c h
more suitable for computer animation. In this technique, animation is carefully
Key frames (created ﬁrst)
Time
Straight ahead order of
frame creation
Figure 16.3. Keyfram-
ing (top) encourages detailed
action planning while straight-
ahead action (bottom) leads to
a more spontaneous result.
planned through a series of relatively s parsely spaced key frames with the rest
of the animation (in-between frames) ﬁlled in only after the keys are set (Fig-
ure 16.3). This allows more precise timing and allows the computer to take over
the most tedious part of the process—the creation of the in-between frames—
using algorithms presented in the next section.
Almost any of the techniques outlined above can be used with some reason-
able amount of exaggeration to achieve greater artistic effect or underscore some
speciﬁc property of an action or a character. The ultimate goal is to achieve some-
thing the audience will want to see, something which is appealing. Extreme com-
plexity or too much symmetry in a character or action tends to be less appealing.
To create good results, a traditional animator needs solid drawing skills.A n a l o -
gously, a computer animator should certainly understand computer graphics and
have a solid knowledge of the tools he uses.
16.1.4 Animator Control vs. Automatic Methods
In traditional animation, the animator has complete control over all aspects of the
production process and nothing prevents the ﬁnal product to be as it was planned
in every detail. The price paid for this ﬂexibility is that every frame is created by
hand, leading to an extremely time- and labor-consuming enterprise. In computer
animation, there is a clear tradeoff be tween, on the one hand, giving an animator
more direct control over the result, but asking him to contribute more work and,
on the other hand, relying on more automatic techniques which might require
setting just a few input parameters but o ffer little or no contro l over some of the
properties of the result. A good algorithm should provide suf ﬁcient ﬂexibility
while asking an animator only the information which is intuitive, easy to provide,
and which he himself feels is necessary fo r achieving the desired effect. While
perfect compliance with this requirement is unlikely in practice since it would
probably take something close to a mind-reading machine, we do encourage the
reader to evaluate any computer-animation technique from the point of view of
providing such balance.
16.2 Keyframing
The term keyframing can be misleading when applied to 3D computer animation
since no actual completed frames (i.e., images) are typically involved. At any

16.2. Keyframing 411
Frames
Scene parameters
Time
Figure 16.4. Different patterns of setting keys (black circles above) can be used simultaneously for
the same scene. It is assumed that there are more frames before, as well as after, this portion.
given moment, a 3D scene being animated is speci ﬁed by a set of numbers: the
positions of centers of all objects, their RGB colors, the amount of scaling applied
to each object in each axis, modeling transformations between different parts of
a complex object, camera position and orientation, light sources intensity, etc. To
animate a scene, some subset of these v alues have to change with time. One can,
of course, directly set these values at every frame, but this will not be particularly
efﬁcient. Short of that, some number of important moments in time (key frames
t
k) can be chosen along the timeline of animation for each of the parameters and
values of this parameter (key values fk) are set only for these selected frames.
We will call a combination (tk,fk) o fk e yf r a m ea n dk e yv a l u es i m p l yak e y .
Key frames do not have to be the same for different parameters, but it is often
logical to set keys at least for some of them simultaneously. For example, key
frames chosen for x-, y-a n d z-coordinates of a speci ﬁc object might be set at
exactly the same frames forming a single position vector key (t
k, pk).T h e s ek e y
 Frame positions
t
t
f Keys
df/dt
Figure 16.5. A continu-
ous curve f(t) is ﬁt through the
keys provided by the anima-
tor even though only values at
frame positions are of interest.
The derivative of this function
gives the speed of parameter
change and is at ﬁrst deter-
mined automatically by the ﬁt-
ting procedure.
frames, however, might be completely different from those chosen for the object’s
orientation or color. The closer key fra mes are to each other, the more control the
animator has over the result; however the cost of doing more work of setting the
keys has to be assessed. It is, therefore, typical to have large spacing between
keys in parts of the animation which are rel atively simple, concentrating them in
intervals where complex action occurs, as shown in Figure 16.4.
Once the animator sets the key (t
k,fk), the system has to compute values of
f for all other frames. Although we are ultimat ely interested only in a discrete set
of values, it is convenient to treat this as a classical interpolation problem which
ﬁts a continuous animation curve f(t) through a provided set of data points (Fig-
ure 16.5). Extensive discussion of curve- ﬁtting algorithms can be found in Chap-

412 16. Computer Animation
ter 15, and we will not repeat it here. Since the animator initially provides only the
keys and not the derivative (tangent), methods which compute all necessary infor-
mation directly from keys are preferable for animation. The speed of parameter
change along the curve is given by the derivative of the curve with respect to time
df /dt. Therefore, to avoid sudden jumps in velocity, C
1 continuity is typically
necessary. A higher degree of continuity is typically not required from animation
curves, since the second derivative, w hich corresponds to acceleration or applied
force, can experience very sudden changes in real-world situations (ball hitting a
solid wall), and higher derivatives do not directly correspond to any parameters of
physical motion. These consideration make Catmull-Rom splines one of the best
choices for initial animation curve creation.
Most animation systems give the animator the ability to perform interactive
ﬁne editing of this initial curve, including inserting more keys, adjusting existing
keys, or modifying automatically computed tangents. Another useful technique
which can help to tweak the shape of the curve is called TCB control (TCB stands
for tension, continuity, and bias). The idea is to introduce three new parameters
which can be used to modify the shape of the curve near a key through coordinated
adjustment of incoming and outgoing tangents at this point. For keys uniformly
spaced in time with distance Δt between them, the standard Catmull-Rom ex-
pression for incoming T
in
i and outgoing Tout
i tangents at an internal key (tk,fk)
can be rewritten as
Tin
k = Tout
k = 1
2Δt(fk+1 −fk)+ 1
2Δt(fk −fk−1).
Modiﬁed tangents of a TCB spline are
Tin
k = (1−t)(1−c)(1+ b)
2Δt (fk+1 −fk)+ (1−t)(1+ c)(1−b)
2Δt (fk −fk−1),
Tout
k = (1−t)(1+ c)(1+ b)
2Δt (fk+1 −fk)+ (1−t)(1−c)(1−b)
2Δt (fk −fk−1).
The tension parametert controls the sharpness of the curve near the key by scaling
both incoming and outgoing tangents. Larger tangents (lower tension) lead to a
ﬂatter curve shape near the key. Bias b allows the animator to selectively increase
the weight of a key’s neighbors locally pulling the curve closer to a straight line
connecting the key with its left ( b near 1, “overshooting” the action) or right ( b
near −1, “undershooting” the action) neighbors. A nonzero value of continuity c
makes incoming and outgoing tangents different allowing the animator to create
kinks in the curve at the key value. Practi cally useful values of TCB parameters
are typically con ﬁn e dt ot h ei n t e r v a l[−1;1] with defaults t = c = b =0 corre-
sponding to the original Catmull-Rom spline. Examples of possible curve shape
adjustments are shown in Figure 16.6.

16.2. Keyframing 413
High tension, t > 0Low tension, t < 0
Low continuity, c < 0
High bias, b > 0Low bias, b < 0
High continuity, c > 0 Original spline, t = c = b = 0
Original spline, t = c = b = 0
Original spline, t = c = b = 0
Figure 16.6. Editing the default interpolating spline ( middle column) using TCB controls. Note
that all keys remain at the same positions.
16.2.1 Motion Controls
So far, we have described how to control the shape of the animation curve through
key positioning and ﬁne tweaking of tangent values at the keys. This, however,
is generally not suf ﬁcient when one would like to have control both over where
the object is moving, i.e., its path, and how fast it moves along this path. Given a
set of positions in space as keys, automatic curve- ﬁtting techniques can ﬁtac u r v e
through them, but resulting motion is onl y constrained by forcing the object to
arrive at a speci ﬁed key position p
k at the corresponding key frame tk, and noth-
ing is directly said about the speed of motion between the keys. This can create
problems. For example, if an object moves along the x-axis with velocity 11 me-
ters per second for 1 second and then with 1 meter per second for 9 seconds, it
will arrive at position x =2 0 after 10 seconds thus satisfying animator’s keys
(0,0) and (10, 20). It is rather unlikely that this jerky motion was actually de-
sired, and uniform motion with speed 2 meters/second is probably closer to what
the animator wanted when setting these keys. Although typica lly not displaying

414 16. Computer Animation
such extreme behavior, polynomial curves resulting from standard ﬁtting proce-
dures do exhibit nonuniform speed of motion between keys as demonstrated in
Figure 16.7. While this can be tolerable (within limits) for some parameters for
which the human visual system is not very good at determining nonuniformities
in the rate of change (such as color or even rate of rotation), we have to do bet-
ter for position p of the object where velocity directly corresponds to everyday
experience.
We will ﬁrst distinguish curve parameterization used during the ﬁtting proce-
dure from that used for animation. When a curve is ﬁt through position keys, we
will write the result as a function p(u) of some parameter u. This will describe
the geometry of the curve in space. The arc length s is the physical length of
t=0
t=9 t=12
t=6
t=3
Figure 16.7. All three mo-
tions are along the same 2D
path and satisfy the set of keys
at the tips of the black trian-
gles. The tips of the white
triangles show object position
at Δt = 1 intervals. Uni-
form speed of motion between
the keys (top) might be closer
to what the animator wanted,
but automatic ﬁtting proce-
dures could result in either of
the other two motions.
the curve. A natural way for the animator to control the motion along the now-
existing curve is to specify an extra function s(t) which corresponds to how far
along the curve the object should be at any given time. To get an actual position
in space, we need one more auxiliary function u(s) which computes a parame-
ter value u for given arc length s. The complete process of computing an object
position for a given time t is then given by composing these functions (see Fig-
ure 16.8):
p(t)= p(u(s(t))).
Several standard functions can be used as the distance-time function s(t).
One of the simplest is the linear function corresponding to constant velocity:
s(t)= vt with v = const. Another common example is the motion with con-
stant acceleration a (and initial speed v
0) which is described by the parabolic
s(t)= v0t + at2/2. Since velocity is changing gradually here, this function
can help to model desirable ease-in and ease-out behavior. More generally, the
slope of s(t) gives the velocity of motion with negative slope corresponding to
the motion backwards along the curve. To achieve most ﬂexibility, the ability to
P(u(s(t)))
P(u)t
s
s(t)
s(t)
u(s(t))
u
u(s)
s
Figure 16.8. To get position in space at a given time t, one ﬁrst utilizes user-speci ﬁed motion
control to obtain the distance along the curve s(t) and then computes the corresponding curve
parameter value u(s(t)). Previously ﬁtted curve P(u) can now be used to ﬁnd the position P(u(s(t))).

16.2. Keyframing 415
interactively edit s(t) is typically provided to the animator by the animation sys-
tem. The distance-time function is not the only way to control motion. In some
cases it might be more convenient for the user to specify a velocity-time function
v(t) or even an acceleration-time function a(t). Since these are correspondingly
ﬁrst and second derivatives of s(t), to use these type of controls, the system ﬁrst
recovers the distance-time function by integrating the user input (twice in the case
of a(t)).
The relationship between the curve parameter u and arc lengths is established
automatically by the system. In practice, the system ﬁrst determines arc length
dependance on parameter u (i.e., the inverse function s(u)). Using this function,
for any given S it is possible to solve the equation s(u)−S =0 with unknownu
obtainingu(S). For most curves, the function s(u) cannot be expressed in closed
analytic form and numeri cal integration is necessary (see Chapter 14). Standard
numerical root-ﬁnding procedures (such as the Newton-Raphson method, for ex-
ample) can then be directly used to solve the equation s(u)−S =0 foru.
u = 0.8
u = 0.4
u = 0.6 u = 1.0
u = 0
u = 0.2
s(u)u
0.2
0.4
1.0
0.8
0.6
0.0
2.5
8.5
7.0
5.0
4.0
0.0
Figure 16.9. To cre-
ate a tabular version of s(u),
the curve can be approximated
by a number of line segments
connecting points on the curve
positioned at equal parame-
ter increments. The table is
searched to ﬁnd the u-interval
for a given S. For the curve
above, for example, the value
of u corresponding to the po-
sition of S = 6.5 lies between u
=0 . 6a n du =0 . 8 .
An alternative technique is to approximate the curve itself as a set of linear
segments between points pi computed at some set of suf ﬁciently densely spaced
parameter values ui. One then creates a table of approximate arc lengths
s(ui) ≈
i∑
j=1
||pj −pj−1|| = s(ui−1)+ ||pi −pi−1||.
Since s(u) is a non-decreasing function of u, one can then ﬁnd the interval con-
taining the valueS by simple searching through the table (see Figure 16.9). Linear
interpolation of the interval’s u end values is then performed to ﬁnally ﬁnd u(S).
If greater precision is necessary, a few steps of the Newton-Raphson algorithm
with this value as the starting point can be applied.
16.2.2 Interpolating Rotation
The techniques presented above can be used to interpolate the keys set for most of
the parameters describing t he scene. Three-dimensional rotation is one important
motion for which more specialized interpolation methods and representations are
common. The reason for this is that applying standard techniques to 3D rotations
often leads to serious practical problems. Rotation (a change in orientation of an
object) is the only motion other than translation which leaves the shape of the
object intact. It therefore plays a sp ecial role in animating rigid objects.
There are several ways to specify the orientation of an object. First, trans-
formation matrices as described in Chapter 6 can be used. Unfortunately, naive

416 16. Computer Animation
X
YY
Z
XZ
Y Y
XZ X
Z
Rotate (X)
Rotate (Z) Rotate (Y)
Figure 16.10. Three Euler angles can be used to specify arbitrary object orientation through a
sequence of three rotations around coordinate ax es embedded into the object (axis Y always points
to the tip of the cone). Note that each rotation is given in a new coordinate system. Fixed angle
representation is very similar, but the coordinate axes it uses are ﬁx e di ns p a c ea n dd on o tr o t a t ew i t h
the object.
(element-by-element) interpolation of rotation matrices does not produce a correct
result. For example, the matrix “halfway” between 2D clock- and counterclock-
wise 90 degree rotation is the null matrix:
1
2
[ 01
−10
]
+ 1
2
[0 −1
10
]
=
[00
00
]
.
The correct result is, of course, the unit matrix corresponding to no rotation. Sec-
ond, one can specify arbitrary orientation as a sequence of exactly three rotations
around coordinate axes chosen in some speci ﬁc order. These axes can be ﬁxed in
space (ﬁxed-angle representation) or embedded into the object therefore changing
after each rotation ( Euler-angle representation as shown in Figure 16.10). These
three angles of rotation can be animated directly through standard keyframing,
but a subtle problem known as gimbal lock arises. Gimbal lock occurs if dur-
ing rotation one of the three rotation a xes is by accident aligned with another,
thereby reducing by one the number of available degrees of freedom as shown in
Figure 16.11 for a physical device. This effect is more common than one might
Rotate(X)
Rotate(Z)
Rotate(Y)
Original conﬁguration
Rotate(Z)
Rotate(Y)
Rotate(X)
Gimbal lock conﬁguration
Figure 16.11. In this exam-
ple, gimbal lock occurs when
a 90 degree turn around axis
Z is made. Both X and Y
rotations are now performed
around the same axis leading
to the loss of one degree of
freedom.
think—a single 90 degree turn to the right (or left) can potentially put an object
into a gimbal lock. Finally, any orientation can be speci ﬁed by choosing an appro-
priate axis in space and angle of rotation around this axis. While animating in this
representation is relatively straightforward, combining two rotations, i.e., ﬁnding
the axis and angle corresponding to a sequence of two rotations both represented
by axis and angle, is nontrivial. A special mathematical apparatus, quaternions
has been developed to make this representation suitable both for combining sev-
eral rotations into a single one and for animation.
Given a 3D vector v =( x,y,z ) and a scalar s, a quaternion q is formed by
combining the two into a four-component object: q =[ sxyz ]=[ s; v]. Several

16.2. Keyframing 417
new operations are then deﬁned for quaternions. Quaternion addition simply sums
scalar and vector parts separately:
q1 +q2 ≡[s1 +s2; v1 + v2].
Multiplication by a scalar a gives a new quaternion
aq ≡[as; av].
More complex quaternion multiplication is de ﬁned as
q1 ·q2 ≡[s1s2 −v1v2; s1v2 +s2v1 + v1 × v2],
where× denotes a vector cross product. It is easy to see that, similar to matrices,
quaternion multiplication is associative, but not commutative. We will be inter-
ested mostly in normalized quaternions—those for which the quaternion norm
|q| =
√
s2 + v2 is equal to one. One ﬁnal deﬁnition we need is that of an inverse
quaternion:
q−1 =( 1/|q|)[s; −v].
To represent a rotation by angle φaround an axis passing through the origin
whose direction is given by the normalized vector n, a normalized quaternion
q =[ c o s (φ/2);sin(φ/2)n]
is formed. To rotate point p, one turns it into the quaternion qp =[ 0 ;p] and
 q2
q1
Figure 16.12. Interpolating
quaternions should be done on
the surface of a 3D unit sphere
embedded in 4D space. How-
ever, much simpler interpola-
tion along a 4D straight line
(open circles) followed by re-
projection of the results onto
the sphere (black circles) is of-
ten sufﬁcient.
computes the quaternion product
q′
p = q ·qp ·q−1
which is guaranteed to have a zero scalar part and the rotated point as its vector
part. Composite rotation is given simply by the product of quaternions represent-
ing each of the separate rotation steps. To animate with quaternions, one can treat
them as points in a four-dimensional space and set keys directly in this space. To
keep quaternions normalized, one should, strictly speaking, restrict interpolation
procedures to a unit sphere (a 3D object ) in this 4D space. However, a spherical
version of even linear interpolation (often called slerp) already results in rather
unpleasant math. Simple 4D linear interpolation followed by projection onto the
unit sphere shown in Figure 16.12 is much simpler and often sufﬁcient in practice.
Smoother results can be obtained via repeated application of a linear interpolation
procedure using the de Casteljau algorithm.

418 16. Computer Animation
16.3 Deformations
Although techniques for object deformation might be more properly treated as
modeling tools, they are traditionally discussed together with animation methods.
Probably the simplest example of an operation which changes object shape is a
nonuniform scaling. More generally, some function can be applied to local co-
ordinates of all points specifying the object (i.e., vertices of a triangular mesh
or control polygon of a spline surface), r epositioning these points and creating a
new shape: p
′ = f(p,γ) whereγis a vector of parameters used by the deforma-
tion function. Choosing different f (and combining them by applying one after
another) can help to create very interesting deformations. Examples of useful
simple functions include bend, twist, and taper which are shown in Figure 16.13.
Animating shape change is very easy in this case by keyframing the parameters
of the deformation function. Disadvantages of this technique include dif ﬁculty of
choosing the mathematical function for some nonstandard deformations and the
fact that the resulting deformation is global in the sense that the complete object,
and not just some part of it, is reshaped.
BendOriginal shape
Taper Twist
Figure 16.13. Popu-
lar examples of global defor-
mations. Bending and twist
angles, as well as the de-
gree of taper, can all be an-
imated to achieve dynamic
shape change.
To deform an object locally while providing more direct control over the re-
sult, one can choose a single vertex, move it to a new location and adjust vertices
within some neighborhood to follow the seed vertex. The area affected by the de-
formation and the speci ﬁc amount of displacement in d ifferent parts of the object
are controlled by an attenuation function whi ch decreases with distance (typically
computed over the object’s surface) to the seed vertex. Seed vertex motion can be
keyframed to produce animated shape change.
A more general deformation technique is called free-form deformation (FFD)
(Sederberg & Parry, 1986). A local (in m ost cases rectilinear) coordinate grid
is ﬁrst established to encapsulate the part of the object to be deformed, and co-
ordinates (s,t,u ) of all relevant points are computed with respect to this grid.
The user then freely reshapes the grid of lattice points P
ijk into a new distorted
lattice P′
ijk (Figure 16.14). The object is reconstructed using coordinates com-
puted in the original undistorted grid in the trivariate analog of B´ezier interpolants
(see Chapter 15) with distorted lattice points P′
ijk
serving as control points in this
expression:
P(s,u,t )=
L∑
i=0
(
i
L
)
(1−s)L−isi
M∑
j=0
(
j
M
)
(1−t)M−jtj
N∑
k=0
(
k
N
)
(1−u)N−kukP′
ijk
,
where L,M,N are maximum indices of lattice points in each dimension. In ef-
fect, the lattice serves as a low-resolution version of the object for the purpose of
deformation, allowing for a smooth shape change of an arbitrarily complex ob-

16.4. Character Animation 419
ject through a relatively small number o f intuitive adjustments. FFD lattices can
themselves be treated as regular objects by the system and can be transformed, an-
imated, and even further deformed if necessary, leading to corresponding changes
in the object to which the lattice is attached. For example, moving a deforma-
tion tool consisting of the original lattice and distorted lattice representing a bulge
across an object results in a bulge moving across the object.
Figure 16.14. Adjusting the
FFD lattice results in the de-
formation of the object.
16.4 Character Animation
Animation of articulated ﬁgures is most often performed through a combination
of keyframing and specialized deformation techniques. The character model in-
tended for animation typically consists of at least two main layers as shown in
Figure 16.15. The motion of a highly detailed surface representing the outer shell
or skin of the character is what the viewer will eventually see in the ﬁnal prod-
uct. The skeleton underneath it is a hierarchical structure (a tree) of joints which
provides a kinematic model of the ﬁgure and is used exclusively for animation.
In some cases, additional intermediate layer(s) roughly corresponding to muscles
are inserted between the skeleton and the skin.
Skeleton
Skin
pelvis
spine
collar
neck
head
rshoulder
elbow
wrist
lshoulder
rhip
knee
ankle
ball
toe
lhip
Figure 16.15. (Left) A hierarchy of joints, a skeleton, serves as a kinematic abstraction of the
character; (middle) repositioning the skeleton deforms a separate skin object attached to it; (right) a
tree data structure is used to represent the skeleton. For compactness, the internal structure of several
nodes is hidden (they are identical to a corresponding sibling).

420 16. Computer Animation
Each of the skeleton’s joints acts as a parent for the hierarchy below it. The
root represents the whole character and is positioned directly in the world coor-
dinate system. If a local transformation matrix which relates a joint to its parent
in the hierarchy is available, one can obtain a transformation which relates local
space of any joint to the world system (i.e., the system of the root) by simply con-
catenating transformations along the path from the root to the joint. To evaluate
the whole skeleton (i.e., ﬁnd position and orientation of all joints), a depth- ﬁrst
traversal of the complete tree of joints is performed. A transformation stack is a
natural data structure to help with this task. While traversing down the tree, the
current composite matrix is pushed on the stack and a new one is created by mul-
tiplying the current matrix with the one stored at the joint. When backtracking
to the parent, this extra transformation should be undone before another branch is
visited; this is easily done by simply popping the stack. Although this general and
simple technique for evaluating hierarchies is used throughout computer graphics,
in animation (and robotics) it is given a special name—forward kinematics (FK).
While general representations for all transformations can be used, it is common to
use specialized sets of parameters, such as link lengths or joint angles, to specify
skeletons. To animate with forward kinematics, rotational parameters of all joints
are manipulated directly. The technique also allows the animator to change the
distance between joints (link lengths), but one should be aware that this corre-
sponds to limb stretching and can often look rather unnatural.
Forward kinematics requires the user to set parameters for all joints involved
in the motion (Figure 16.16 (top)). Most of these joints, however, belong to in-
Original
After hip rotation
After knee rotation
Eﬀector motion
Hip and knee joint angles
computed automatically
IK solver connection
Figure 16.16. Forward kinematics (top) requires the animator to put all joints into correct position.
In inverse kinematic (bottom), parameters of some internal joints are computed based on desired end
effector motion.

16.4. Character Animation 421
ternal nodes of the hierarchy, and their motion is typically not something the
animator wants to worry about. In most situations, the animator just wants them
to move naturally “on their own,” and one is much more interested in specify-
ing the behavior of the endpoint of a joint chain, which typically corresponds to
something performing a speci ﬁc action, such as an ankle or a tip of a ﬁnger. The
animator would rather have parameters of all internal joints be determined from
the motion of the end effector automatically by the system. Inverse kinematics
(IK) allows us to do just that (see Figure 16.16 (bottom)).
Let x be the position of the end effector and αbe the vector of parameters
needed to specify all internal joints along the chain from the root to the ﬁnal joint.
Sometimes the orientation of the ﬁnal joint is also directly set by the animator, in
which case we assume that the corresponding variables are included in the vector
x. For simplicity, however, we will write all speci ﬁc expressions for the vector:
x =( x
1,x2,x3)T.
Since each of the variables in x is a function of α, it can be written as a vector
equation x = F(α). If we change the internal joint parameters by a small amount
δα, a resulting change δx in the position of the end effector can be approximately
written as
δx = ∂F
∂αδα, (16.1)
where ∂F
∂αis the matrix of partial derivatives called the Jacobian:
∂F
∂α=
⎡
⎢⎣
∂f1
∂α1
∂f1
∂α2
... ∂f1
∂αn
∂f2
∂α1
∂f2
∂α2
... ∂f2
∂αn
∂f3
∂α1
∂f3
∂α2
... ∂f3
∂αn
⎤
⎥⎦.
At each moment in time, we know the desired position of the end effector (set by
the animator) and, of course, the effector’s current position. Subtracting the two,
we will get the desired adjustment δx. Elements of the Jacobian matrix are related
to changes in a coordinate of the end effector when a particular internal parameter
is changed while others remain ﬁxed (see Figure 16.17). These elements can
x
kneeΔ
Δx
Figure 16.17. Partial
derivative ∂x/∂αknee is
given by the limit of
Δx/Δαknee. Effector dis-
placement is computed while
all joints, except the knee, are
kept ﬁxed.
be computed for any given skeleton con ﬁguration using geometric relationships.
The only remaining unknowns in the system of equations (16.1) are the changes in
internal parametersα. Once we solve for them, we updateα= α+δαwhich gives
all the necessary information for the FK procedure to reposition the skeleton.
Unfortunately, the system (16.1) cannot usually be solved analytically and,
moreover, it is in most cases underconstrained, i.e., the number of unknown inter-
nal joint parameters αexceeds the number of variables in vector x. This means
that different motions of the skeleton can result in the same motion of the end

422 16. Computer Animation
effector. Some examples are shown on Figure 16.18. Many ways of obtain-
ing speci ﬁc solution for such systems are available, including those taking into
IK root Eﬀector
position
Figure 16.18. Multiple conﬁgurations of
internal joints can result in the same effector
position. (Top) disjoint “ ﬂipped” solutions;
(bottom) a continuum of solutions.
account natural constraints needed for some
real-life joints (bending a knee only in one
direction, for example). One should also
remember that the computed Jacobian ma-
trix is valid only for one speciﬁ cc o nﬁgura-
tion, and it has to be updated as the skele-
ton moves. The complete IK framework is
presented in Figure 16.19. Of course, the
root joint for IK does not have to be the
root of the whole hierarchy, and multiple IK
solvers can be applied to independent parts
of the skeleton. For example, one can use
separate solvers for right and left feet and
yet another one to help animate grasping
with the right hand, each with its own root.
A combination of FK and IK approaches is typically used to animate the skele-
ton. Many common motions (w alking or running cycles, g rasping, reaching, etc.)
exhibit well-known patterns of mutual joint motion making it possible to quickly
Old skeleton configuration
Update constraints
New values for internal joint parameters
Replace old skeleton
configuration with the new one
Yes, done
No
New skeleton configuration
Solve equation 1.1
Compute the Jacobian and
desired effector motion
Apply forward kinematic to reposition skeleton
Effector at desired position ?
Figure 16.19. A diagram of the inverse kinematic algorithm.

16.4. Character Animation 423
create naturally looking motion or even use a library of such “clips.” The ani-
mator then adjusts this generic result according to the physical parameters of the
character and also to give it more individuality.
When a skeleton changes its position, it acts as a special type of deformer
applied to the skin of the character. The motion is transferred to this surface by
assigning each skin vertex one ( rigid skinning )o rm o r e(smooth skinning ) joints
as drivers (see Figure 16.20). In the ﬁrst case, a skin vertex is simply frozen
Figure 16.20. Top:
Rigid skinning assigns skin
vertices to a speciﬁ c joint.
Those belonging to the elbow
joint are shown in black; Bot-
tom: Soft skinning can blend
the inﬂuence of several joints.
Weights for the elbow joint
are shown (lighter = greater
weight). Note smoother skin
deformation of the inner part
of the skin near the joint.
into the local space of the corresponding joint, which can be the one nearest in
space or one chosen directly by the user. The vertex then repeats whatever mo-
tion this joint experiences, and its position in world coordinates is determined by
standard FK procedure. Although it is simple, rigid skinning makes it dif ﬁcult
to obtain suf ﬁciently smooth skin deformation in areas near the joints or also for
more subtle effects resembling breathing or muscle action. Additional specialized
deformers called ﬂexors can be used for this purpose. In smooth skinning, several
joints can in ﬂuence a skin vertex according to some weight assigned by the ani-
mator, providing more detailed control over the results. Displacement vectors, d
i,
suggested by different joints affecting a given skin vertex (each again computed
with standard FK) are averaged according to their weights w
i to compute the ﬁ-
nal displacement of the vertex d =∑widi. Normalized weights ( ∑wi =1 )
are the most common but not fundamenta lly necessary. Setting smooth skinning
weights to achieve the desired effect is not easy and requires signi ﬁcant skill from
the animator.
16.4.1 Facial Animation
Skeletons are well suited for creating most motions of a character’s body, but they
are not very convenient for realistic facial animation. The reason is that the skin
of a human face is moved by muscles directly attached to it, contrary to other
parts of the body where the primary objective of the muscles is to move the bones
of the skeleton and any skin deformation is a secondary outcome. The result of
this facial anatomical arrangement is a very rich set of dynamic facial expressions
humans use as one of the main instruments of communication. We are all very
well trained to recognize such facial variations and can easily notice any unnatural
appearance. This not only puts special demands on the animator but also requires
a high-resolution geometric model of the face and, if photorealism is desired,
accurate skin reﬂection properties and textures.
While it is possible to set key poses of the face vertex-by-vertex and inter-
polate between them or directly simulate the behavior of the underlying mus-
cle structure using physics-based techniques (see Section 16.5), more specialized

424 16. Computer Animation
high-level approaches also exist. The static shape of a speci ﬁc face can be charac-
terized by a relatively small set of so-called conformational parameters (overall
scale, distance from the eye to the forehead, length of the nose, width of the jaws,
etc.) which are used to morph a generic face model into one with individual
features. An additional set of expressive parameters can be used to describe the
dynamic shape of the face for animation. Examples include rigid rotation of the
head, how wide the eyes are open, movement of some feature point from its static
position, etc. These are chosen so that most of the interesting expressions can be
obtained through some combination of parameter adjustments, therefore, allow-
ing a face to be animated via standard keyframing. To achieve a higher level of
control, one can use expressive parameters to create a set of expressions corre-
sponding to common emotions (neutral, sadness, happiness, anger, surprise, etc.)
and then blend these key poses to obtain a “slightly sad” or “angrily surprised”
face. Similar techniques can be used to perform lip-synch animation, but key
poses in this case correspond to different phonemes. Instead of using a sequence
of static expressions to describe a dynamic one, the Facial Action Coding Sys-
tem (FACS) (Eckman & Friesen, 1978) decomposes dynamic facial expressions
directly into a sum of elementary motions called action units (AUs). The set of
AUs is based on extensive psychological research and includes such movements
as raising the inner brow, wrinkling the nose, stretching lips, etc. Combining AUs
can be used to synthesize a necessary expression.
16.4.2 Motion Capture
Even with the help of the techniques described above, creating realistic-looking
character animation from s cratch remains a daunting task. It is therefore only
natural that much attention is directed to ward techniques which record an actor’s
motion in the real world and then apply it t o computer-generated characters. Two
main classes of such motion capture (MC) techniques exist: electromagnetic and
optical.
In electromagnetic motion capture, an e lectromagnetic sensor directly mea-
sures its position (and possibly orientation) in 3D, often providing the captured
results in real time. Disadvantages of this technique include signi ﬁcant equip-
ment cost, possible interference from nearby metal objects, and noticeable size
of sensors and batteries which can be an obstacle in performing high-amplitude
motions. In optical MC, small colored markers are used instead of active sensors
making it a much less intrusive procedure. Figure 16.21 shows the operation of
such a system. In the most basic arrangement, the motion is recorded by two cali-
brated video cameras, and simple triangulation is used to extract the marker’s 3D
position. More advanced computer vision algorithms used for accurate tracking

16.4. Character Animation 425
of multiple markers from video are computationally expensive, so, in most cases,
such processing is done of ﬂine. Optical tracking is generally less robust than
Figure 16.21. Optical
motion capture: markers at-
tached to a performer’s body
allow skeletal motion to be ex-
tracted. Image courtesy of Mo-
tion Analysis Corp.
electromagnetic. Occlusion of a given marker in some frames, possible misiden-
tiﬁcation of markers, and noise in images are just a few of the common problem
which have to be addressed. Introducing more cameras observing the motion from
different directions improves both accura cy and robustness, but this approach is
more expensive and it takes longer to process such data. Optical MC becomes
more attractive as available computati onal power increases and better computer
vision algorithms are developed. Because of low impact nature of markers, opti-
cal methods are suitable for delicate facial motion capture and can also be used
with objects other than humans—for examp le, animals or even tree branches in
the wind.
With several sensors or markers attached to a performer’s body, a set of time-
dependant 3D positions of some collection of points can be recorded. These track-
ing locations are commonly chosen near joints, but, of course, they still lie on skin
surface and not at points where actual bones meet. Therefore, some additional
care and a bit of extra processing is nece ssary to convert recorded positions into
those of the physical skeleton joints. F or example, putting two markers on oppo-
site sides of the elbow or ankle allows the system to obtain better joint position
by averaging locations of the two markers. Without such extra care, very notice-
able artifacts can appear due to offset joint positions as well as inherent noise
and insuf ﬁcient measurement accuracy. Becau se of physical inaccuracy during
motion, for example, character limbs can lose contact with objects they are sup-
posed to touch during walking or grasping, problems like foot-sliding (skating)
of the skeleton can occur. Most of thes e problems can be corrected by using in-
verse kinematics techniques which can explicitly force the required behavior of
the limb’s end.
Recovered joint positions can now be directly applied to the skeleton of a
computer-generated character. This procedure assumes that the physical dimen-
sions of the character are identical to those of the performer. Retargeting recorded
motion to a different character and, more generally, editing MC data, requires
signiﬁcant care to satisfy necessary constrai nts (such as maintaining feet on the
ground or not allowing an elbow to bend backwards) and preserve an overall nat-
ural appearance of the modi ﬁed motion. Generally, the greater the desired change
from the original, the less likely it will be possible to maintain the quality of the
result. An interesting approach to the problem is to record a large collection of
motions and stitch together short clips from this library to obtain desired move-
ment. Although this topic is currently a v ery active research area, limited ability
to adjust the recorded motion to the anim ator’s needs remains one of the main
disadvantages of motion capture technique.

426 16. Computer Animation
16.5 Physics-Based Animation
The world around us is governed by physical laws, many of which can be for-
malized as sets of partial or, in some simpler cases, ordinary differential equa-
tions. One of the original applications of computers was (and remains) solving
such equations. It is therefore only natural to attempt to use numerical techniques
developed over the several past decades to obtain realistic motion for computer
animation.
Because of its relative complexity and signi ﬁcant cost, physics-based anima-
tion is most commonly used in situations when other techniques are either un-
available or do not produce suf ﬁciently realistic results. Prime examples include
animation of ﬂuids (which includes many gaseous phase phenomena described
by the same equations—smoke, clouds, ﬁre, etc.), cloth simulation (an exam-
Figure 16.22. Realis-
tic cloth simulation is often
performed with physics-based
methods. In this example,
forces are due to collisions and
gravity.
ple is shown in Figure 16.22), rigid body motion, and accurate deformation of
elastic objects. Governing equations an d details of commonly used numerical
approaches are different in each of these cases, but many fundamental ideas and
difﬁculties remain applicable across appli cations. Many methods for numerically
solving ODEs and PDEs exist, but discussing them in details is far beyond the
scope of this book. To give the reader a ﬂavor of physics-based techniques and
some of the issues involved, we will brieﬂy mention here only the ﬁnite differ-
ence approach—one of the conceptually simplest and most popular families of
algorithms which has been applied to most, if not all, differential equations en-
countered in animation.
The key idea of this approach is to replace a differential equation with its dis-
crete analog—a difference equation. To do this, the continuous domain of interest
is represented by a ﬁnite set of points at which the solution will be computed. In
the simplest case, these are deﬁned on a uniform rectangular grid as shown in Fig-
ure 16.23. Every derivative present in the original ODE or PDE is then replaced
by its approximation through function values at grid points. One way of doing
this is to subtract the function value at a given point from the function value for
its neighboring point on the grid:
df(t)
dt ≈Δf
Δt = f(t+Δt)−f(t)
Δt or ∂f(x,t)
∂x ≈Δf
Δx = f(x+Δx,t)−f(x,t)
Δx .
(16.2)
These expressions are, of course, not the only way. One can, for example, use
f(t −Δt) instead of f(t) above and divide by 2Δt. For an equation containing
a time derivative, it is now possible to propagate values of an unknown function
forward in time in a sequence of Δt-size steps by solving the system of difference
equations (one at each spatial location) for unknown f(t +Δt). Some initial

16.5. Physics-Based Animation 427
t + Δt
xx  + Δxx x + Δx
t = 0, Initial conditions
Current
Figure 16.23. Two possible difference schemes for an equation involving derivatives ∂f/∂x and
∂f/∂t. (Left) An explicit scheme expresses unknown values (open circles) only through known values
at the current (orange circles) and possibly past ( blue circles) time; (Right) Implicit schemes mix
known and unknown values in a single equation making it necessary to solve all such equations as
a system. For both schemes, information about values on the right boundary is needed to close the
process.
conditions, i.e., values of the unknown function at t =0 , are necessary to start the
process. Other information, such as values on the boundary of the domain, might
also be required depending on the speci ﬁc problem.
The computation off(t+Δt) can be done easily for so-called explicit schemes
when all other values present are taken at the current time and the only unknown
in the corresponding difference equation f(t +Δt) is expressed through these
known values. Implicit schemes mix values at current and future times and might
use, for example,
f(x+Δx,t +Δt)−f(x,t +Δt)
Δx
as an approximation of ∂f
∂x. In this case one has to solve a system of algebraic
equations at each step.
The choice of difference scheme can dramatically affect all aspects of the
algorithm. The most obvious among them is accuracy. In the limit Δt → 0
or Δx → 0, expressions of the type in Equation (16.2) are exact, but for ﬁnite
step size some schemes allow better approximation of the derivative than others.
Stability of a difference scheme is related to how fast numerical errors, which are
always present in practice, can grow with time. For stable schemes this growth is
bounded, while for unstable ones it is exponential and can quickly overwhelm the
solution one seeks (see Figure 16.24). It is important to realize that while some
Stable
f (t)
t
Exact
Unstable
Figure 16.24. An unstable
solution might follow the ex-
act one initially, but can devi-
ate arbitrarily far from it with
time. Accuracy of a stable
solution might still be insuf ﬁ-
cient for a speciﬁc application.
inaccuracy in the solution is tolerable (and, in fact, accuracy demanded in physics
and engineering is rarely needed for animation), an unstable result is completely

428 16. Computer Animation
meaningless, and one should avoid using unstable schemes. Generally, explicit
schemes are either unstable or can become unstable at larger step sizes while
implicit ones are unconditionally stable. Implicit schemes allows greater step size
(and, therefore, fewer steps) which is why they are popular despite the need to
solve a system of algebraic equations at each step. Explicit schemes are attractive
because of their simplicity if their stability conditions can be satisﬁed. Developing
a good difference scheme and corresponding algorithm for a speci ﬁc problem is
not easy, and for most standard situations it is well advised to use an existing
method. Ample literature discussing de tails of these techniques is available.
One should remember that, in many cases, just computing all necessary terms
in the equation is a dif ﬁcult and time-consuming task on its own. In rigid body
or cloth simulation, for example, most of the forces acting on the system are due
to collisions among objects. At each step during animation, one therefore has to
solve a purely geometric, but very nontri vial, problem of collision detection. In
such conditions, schemes which require fewer evaluations of such forces might
provide signiﬁcant computational savings.
Although the result of solving appropr iate time-dependant equations gives
very realistic motion, this approach has its limitations. First of all, it is very
hard to control the result of physics-base d animation. Fundamental mathematical
properties of these equations state that once the initial conditions are set, the solu-
tion is uniquely deﬁned. This does not leave much room for animator input and, if
the result is not satisfactory for some reason, one has only a few options. They are
mostly limited to adjusting initial conditio n used, changing physical properties of
the system, or even modifying the equations themselves by introducing arti ﬁcial
terms intended to “drive” the solution in the direction the animator wants. Making
such changes requires signi ﬁcant skill as well as understanding of the underlying
physics and, ideally, numerical methods. Without this knowledge, the realism
provided by physics-based animation can be destroyed or severe numerical prob-
lems might appear.
16.6 Procedural T echniques
Imagine that one could write (and implement on a computer) a mathematical func-
tion which outputs precisely the desired motion given some animator guidance.
Physics-based techniques outlined above can be treated as a special case of such
an approach when the “function” involved is the procedure to solve a particular
differential equation and “guidance” is the set of initial and boundary conditions,
extra equation terms, etc.

16.6. Procedural T echniques 429
However, if we are only concerned with the ﬁnal result, we do not have to
follow a physics-based approach. For example, a simple constant amplitude
wave on the surface of a lake can be directly created by applying the function
f(x,t)= Acos(ωt−kx + φ) with constant frequency ω, wave vector k and
phase φto get displacement at the 2D point x at time t. A collection of such
waves with random phases and appropriately chosen amplitudes, frequencies, and
wave vectors can result in a very realistic animation of the surface of water with-
out explicitly solving any ﬂuid dynamics equations. It turns out that other rather
simple mathematical functions can also create very interesting patterns or objects.
Several such functions, most based on lattice noises, have been described in Sec-
tion 11.5. Adding time dependance to these functions allows us to animate certain
complex phenomena much easier and cheaper than with physics-based techniques
while maintaining very high visual quality of the results. If noise(x) is the un-
derlying pattern-generating function, one can create a time-dependant variant of it
by moving the argument position through th e lattice. The simplest case is motion
with constant speed: timenoise(x,t)= noise(x+ vt), but more complex motion
through the lattice is, of course, also possible and, in fact, more common. One
such path, a spiral, is shown in Figure 16.25. Another approach is to animate pa-
rameters used to generate the noise function. This is especially appropriate if the
appearance changes signi ﬁcantly with time—a cloud becoming more turbulent,
for example. In this way one can animate the dynamic process of formation of
clouds using the function which generates static ones.
t = 0
Figure 16.25. Ap a t h
through the cube de ﬁning pro-
cedural noise is traversed to
animate the resulting pattern.
For some procedural techniques, time dependance is a more integral compo-
nent. The simplest cellular automata operate on a 2D rectangular grid where
a binary value is stored at each location (cell). To create a time varying pat-
tern, some user-provided rules for modifying these values are repeatedly applied.
Rules typically involve some set of conditions on the current value and that of
the cell’s neighbors. For example, the rules of the popular 2D Game of Life cel-
lular automaton invented in 1970 by British mathematician John Conway are the
following:
1. A dead cell (i.e., binary value at a giv en location is 0) with exactly three
live neighbors becomes a live cell (i.e., its value set to 1).
2. A live cell with two or three live neighbors stays alive.
3. In all other cases, a cell dies or remains dead.
Once the rules are applied to all grid locations, a new pattern is created and a
new evolution cycle can be started. Three sample snapshots of the live cell distri-
bution at different times are shown in Figure 16.26. More sophisticated automata

430 16. Computer Animation
simultaneously operate on several 3D grids of possibly ﬂoating point values and
can be used for modeling dynamics of clouds and other gaseous phenomena or
biological systems for which this apparatus was originally invented (note the ter-
minology). Surprising pattern complexity can arise from just a few well-chosen
rules, but how to write such rules to create the desired behavior is often not obvi-
ous. This is a common problem with procedural techniques: there is only limited,
if any, guidance on how to create new procedures or even adjust parameters of
existing ones. Therefore, a lot of tweaking and learning by trial-and-error (“by
experience”) is usually needed to unlock t he full potential of procedural methods.
Another interesting approach which was also originally developed to describe
biological objects is the technique called L-systems (after the name of their origi-
nal inventor, Astrid Lindenmayer). This approach is based on grammars or sets of
recursive rules for rewriting strings of symbols. There are two types of symbols:
terminal symbols stand for elements of somethin g we want to represent with a
grammar. Depending on their meaning, grammars can describe structure of trees
and bushes, buildings and whole cities, or programming and natural languages.
In animation, L-systems are most popular for representing plants and correspond-
ing terminals are instructions to the geometric modeling system: put a leaf (or a
branch) at a current position—we will use the symbol @ and just draw a circle,
move current position forward by some number of units (symbol f ), turn current
direction 60 degrees around world Z-axis (symbol +), pop (symbol [) or push
(symbol ]) current position/orientation, etc. Auxiliary nonterminal symbols (de-
noted by capital letters) have only seman tic rather than any direct meaning. They
are intended to be eventually rewritten through terminals. We start from the spe-
cial nonterminal start symbol S and keep applying grammar rules to the current
string in parallel, i.e., replace all nonterminals currently present to get the new
Figure 16.26. Several (non-consecutive) stages in the evolution of a Game of Life automaton. Live
cells are shown in black. Stable objects, oscillators, traveling patterns, and many other interesting
constructions can result from the application of very simple rules. Figure created using a program by
Alan Hensel.

16.7. Groups of Objects 431
string, until we end up with a string containing only terminals and no more sub-
stitution is therefore possible. This string of modeling instructions is then used to
B
B
A
B
A
B
B
AS
Figure 16.27. Consecutive
derivation steps using a sim-
ple L-system. Capital letters
denote nonterminals and illus-
trate positions at which corre-
sponding nonterminal will be
expanded. They are not part of
the actual output.
output the actual geometry. For example, a set of rules (productions)
S → A
A → [+B]fA
A → B
B → fB
B → f@
might result in the following sequence of rewriting steps demonstrated in Fig-
ure 16.27:
S ↦−→A ↦−→[+B]fA ↦−→[+fB]f[+B]fA ↦−→
[+ff@] f[+fB]fB ↦−→[+ff@] f[+ff@] ff@ .
As shown above, there are typically many different productions for the same non-
terminal allowing the generation of many different objects with the same gram-
mar. The choice of which rule to apply can depend on which symbols are located
next to the one being replaced (context-sensitivity) or can be performed at ran-
dom with some assigned probability for each rule (stochastic L-systems). More
complex rules can model interaction with the environment, such as pruning to a
particular shape, and parameters can be associated with symbols to control geo-
metric commands issued.
L-systems already capture plant t opology changes with time: each interme-
diate string obtained in the rewriting p rocess can be interpreted as a “younger”
version of the plant (see Figure 16.27). For more signi ﬁcant changes, different
productions can be in effect at different times allowing the structure of the plant
to change signi ﬁcantly as it grows. A young tree, for example, produces a lot of
new branches, while an older one branches only moderately.
V ery realistic plant models have been created with L-systems. However, as
with most procedural techniques, one needs some experience to meaningfully
apply existing L-systems, and writing new grammars to capture some desired
effect is certainly not easy.
16.7 Groups of Objects
To animate multiple objects one can, of course, simply apply standard techniques
described in the chapter so far to each of them. This works reasonably well for
a moderate number of independent objects whose desired motion is known in

432 16. Computer Animation
advance. However, in many cases, some kind of coordinated action in a dynamic
environment is necessary. If only a few obj ects are involved, the animator can use
an artiﬁcial intelligence (AI)-based system to automatically determine immediate
tasks for each object based on some high-level goal, plan necessary motion, and
execute the plan. Many modern games use such autonomous objects to create
smart monsters or player’s collaborators.
Interestingly, as the number of objects in a group grows from just a few to
several dozens, hundreds, and thousands, individual members of a group must
have only very limited “intelligence” in order for the group as a whole to exhibit
what looks like coordinated goal-driven motion. It turns out that this ﬂocking
is emergent behavior which can arise as a result of limited interaction of group
members with just a few of their closest neighbors (Reynolds, 1987). Flocking
should be familiar to anyone who has obser ved the fascinatingly synchronized
motion of a ﬂock of birds or a school of ﬁsh. The technique can also be used to
control groups of animals moving over terrain or even a human crowd.
At any given moment, the motion of a member of a group, often called boid
when applied to ﬂocks, is the result of balancing several often contradictory ten-
dencies, each of which suggests its own velocity vector (see Figure 16.28). First,
there are external physical forces F acting on the boid, such as gravity or wind.
New velocity due to those forces can be computed directly through Newton’s law
as
v
physics
new = vold + FΔt/m.
Gravity
Goal
Velocity matching
Collision avoidance
Flock centering
Physical forces
Navigation module
Steering
Collision avoidance Goal
Pilot module
Physical constraints Desired velocity
Feasible velocity
Flight module
Boid control adjustments
Figure 16.28. (Left) Individual ﬂock member (boid) can experience several urges of different
importance (shown by line thickness) which have to be negotiated into a single velocity vector. A boid
is aware of only its limited neighborhood (circle). ( Right) Boid control is commonly implemented as
three separate modules.

16.7. Groups of Objects 433
Second, a boid should react to global environment and to the behavior of other
group members. Collision avoidance is one of the main results of such interac-
tion. It is crucial for ﬂocking that each group member has only limited ﬁeld of
view, and therefore is aware only of things happening within some neighborhood
of its current position. To avoid objects in the environment, the simplest, if imper-
fect, strategy is to set up a limited extent repulsive force ﬁeld around each such
object. This will create a second desired velocity vector v
col
 avoid
new ,a l s og i v e n
by Newton’s law. Interaction with other group members can be modeled by si-
multaneously applying different steering behaviors resulting in several additional
desired velocity vectors v
steer
new . Moving away from neighbors to avoid crowding,
steering toward ﬂock mates to ensure ﬂock cohesion, and adjusting a boid’s speed
to align with average heading of neighbors are most common. Finally, some addi-
tional desired velocity vectors vgoal
new are usually applied to achieve needed global
goals. These can be vectors along some path in space, following some speci ﬁc
designated leader of the ﬂock, or simply representing migratory urge of a ﬂock
member.
Once all vnew are determined, the ﬁnal desired vector is negotiated based on
priorities among them. Collision avoidance and velocity matching typically have
higher priority. Instead of simple averag ing of desired velocity vectors which can
lead to cancellation of urges and unnatural “moving nowhere” behavior, an ac-
celeration allocation strategy is used. Some ﬁxed total amount of acceleration is
made available for a boid a nd fractions of it are being given to each urge in order
of priority. If the total available acceler ation runs out, some lower priority urges
will have less effect on the motion or be completely ignored. The hope is that
once the currently most important task (collision avoidance in most situations) is
accomplished, other tasks can be taken care of in near future. It is also important
to respect some physical limitations of real objects, for example, clamping too
high accelerations or speeds to some realis tic values. Depending on the internal
complexity of the ﬂock member, the ﬁnal stage of animation might be to turn the
negotiated velocity vector into a speci ﬁc set of parameters (bird’s wing positions,
orientation of plane model in space, leg skeleton bone con ﬁguration) used to con-
trol a boid’s motion. A diagram of a system implementing ﬂocking is shown on
Figure 16.28 (right).
A much simpler, but still very useful, version of group control is implemented
by particle systems (Reeves, 1983). The number of particles in a system is typi-
cally much larger than number of boids in a ﬂock and can be in the tens or hun-
dreds of thousands, or even more. Moreover, the exact number of particles can
ﬂuctuate during animation with new particles being born and some of the old
ones destroyed at each step. Particles are typically completely independent from

434 16. Computer Animation
Particle source
Local wind field
Figure 16.29. After being emitted by a directional source, particles collide with an object and then
are blown down by a local wind ﬁeld once they clear the obstacle.
each other, ignoring one’s neighbors and in teracting with the environment only
by experiencing external forces and collisions with objects, not through collision
avoidance as was the case for ﬂocks. At each step during animation, the sys-
tem ﬁrst creates new particles with some initial parameters, terminates old ones,
and then computes necessary forces an d updates velocities and positions of the
remaining particles according to Newton’s law.
All parameters of a particle system (number of particles, particle life span,
initial velocity, and location of a particle, e tc.) are usually under the direct control
of the animator. Prime applications of particle systems include modeling ﬁre-
works, explosions, spraying liquids, smoke and ﬁre, or other fuzzy objects and
phenomena with no sharp boundaries. To achieve a realistic appearance, it is im-
portant to introduce some randomness to all parameters, for example, having a
random number of particles born (and destroyed) at each step with their velocities
generated according to some distribution. In addition to setting appropriate initial
parameters, controlling the motion of a particle system is commonly done by cre-
ating a speciﬁc force pattern in space—blowing a particle in a new direction once
it reaches some speci ﬁc location or adding a center of attraction, for example.
One should remember that with all their advantages, simplicity of implementa-
tion and ease of control being the prime ones, particle systems typically do not
provide the level of realism characteristic of true physics-based simulation of the
same phenomena.
Notes
In this chapter we have concentrated on techniques used in 3D animation. There
also exist a rich set of algorithms to help with 2D animation production and post-

16.7. Groups of Objects 435
processing of images created by computer graphics rendering systems. These
include techniques for cleaning up scanned-in artist drawings, feature extrac-
tion, automatic 2D in-betweening, colorization, image warping, enhancement and
compositing, and many others.
One of the most signi ﬁcant developments in the are a of computer animation
has been the increasing power and availability of sophisticated animation systems.
While different in their speci ﬁc set of features, internal structure, details of user
interface, and price, most such systems include extensive support not only for
animation, but also for modeling and rendering, turning them into complete pro-
duction platforms. It is also common to use these systems to create still images.
For example, many images for ﬁgures in this section were produced using Maya
software generously donated by Alias.
Large-scale animation production is an extremely complex process which typ-
ically involves a combined effort by dozens of people with different backgrounds
spread across many departments or even companies. To better coordinate this ac-
tivity, a certain production pipeline is established which starts with a story and
character sketches, proceeds to record necessary sound, build models, and rig
characters for animation. Once actual a nimation commences, it is common to
go back and revise the original designs, models, and rigs to ﬁx any discovered
motion and appearance problems. Setting up lighting and material properties is
then necessary, after which it is possible to start rendering. In most suf ﬁciently
complex projects, extensive postprocessing and compositing stages bring together
images from different sources and ﬁnalize the product.
We conclude this chapter by reminding the reader that in the ﬁeld of computer
animation, any technical sophistication is secondary to a good story, expressive
characters, and other artistic factors, most of which are hard or simply impossible
to quantify. It is safe to say that Snow White and her seven dwarfs will always
share the screen with green ogres and donkeys, and most of the audience will be
much more interested in the characters and the story rather than in which, if any,
computers (and in what exact way) helped to create them.

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
