---
title: "12 Data Structures for Graphics"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## 12 Data Structures for Graphics

12
Data Structures for Graphics
Certain data structures seem to pop up rep eatedly in graphics applications, per-
haps because they address fundamental underlying ideas like surfaces, space, and
scene structure. This chapter talks about several basic and unrelated categories
of data structures that are among the most common and useful: mesh structures,
spatial data structures, scene graphs, and tiled multidimensional arrays.
For meshes, we discuss the basic storage schemes used for storing static
meshes and for transferring meshes to graphics APIs. We also discuss the winged-
edge data structure (Baumgart, 1974) and the related half-edge structure, which
are useful for managing models where the tessellation changes, such as in sub-
division or model simpli ﬁcation. Although these methods generalize to arbitrary
polygon meshes, we focus on the simpler case of triangle meshes here.
Next, the scene-graph data structure is presented. V arious forms of this data
structure are ubiquitous in graphics applications because they are so useful in
managing objects and transformations. All new graphics APIs are designed to
support scene graphs well.
For spatial data structures, we discuss three approaches to organizing models
in 3D space—bounding volume hierarchies, hierarchical space subdivision, and
uniform space subdivision—and the use of hierarchical space subdivision (BSP
trees) for hidden surface removal. The same methods are also used for other
purposes, including geometry culling and collision detection.
Finally, the tiled multidimensional array is presented. Originally developed
to help paging performance in applications where graphics data needed to be
swapped in from disk, such structures are now crucial for memory locality on
machines regardless of whether the array ﬁts in main memory.
281

282 12. Data Structures for Graphics
12.1 T riangle Meshes
Most real-world models are composed of complexes of triangles with shared ver-
tices. These are usually known as triangular meshes , triangle meshes ,o r trian-
gular irregular networks (TINs), and handling them ef ﬁciently is crucial to the
performance of many graphics programs. The kind of ef ﬁciency that is impor-
tant depends on the application. Meshes are stored on disk and in memory, and
we’d like to minimize the amount of storage consumed. When meshes are trans-
mitted across networks or from the CPU to the graphics system, they consume
bandwidth, which is often even more precious than storage. In applications that
perform operations on meshes, besides simply storing and drawing them—such
as subdivision, mesh editing, mesh co mpression, or other operations—ef ﬁcient
access to adjacency information is crucial.
Triangle meshes are generally used to represent surfaces, so a mesh is not just
a collection of unrelated triangles, but rather a network of triangles that connect to
one another through shared vertices and edges to form a single continuous surface.
This is a key insight about meshes: a mesh can be handled more ef ﬁciently than a
collection of the same number of unrelated triangles.
The minimum information required for a triangle mesh is a set of triangles
(triples of vertices) and the positions (i n 3D space) of their vertices. But many,
if not most, programs require the ability to store additional data at the vertices,
edges, or faces to support texture mapping, shading, animation, and other opera-
tions. V ertex data is the most common: each vertex can have material parameters,
texture coordinates, irradiances—any parameters whose values change across the
surface. These parameters are then linear ly interpolated across each triangle to
deﬁne a continuous function over the whole surface of the mesh. However, it is
also occasionally important to be able to store data per edge or per face.
12.1.1 Mesh T opology
The idea that meshes are surface-like can be formalized as constraints on the mesh
topology—the way the triangles connect together, without regard for the vertex
positions. Many algorithms will only work, or are much easier to implement, on a
mesh with predictable connectivity. The simplest and most restrictive requirement
on the topology of a mesh is for the surface to be a manifold. A manifold mesh is
“watertight”—it has no gaps and separates the space on the inside of the surface
from the space outside. It also looks like a surface everywhere on the mesh.
We’ll leave the precise def-
initions to the mathemati-
cians; see the chapter notes.
The term manifold comes from the mathematical ﬁeld of topology: roughly
speaking, a manifold (speci ﬁcally a two-dimensional manifold, or 2-manifold) is

12.1. T riangle Meshes 283
a surface in which a small neighborhood around any point could be smoothed out
into a bit of ﬂat surface. This idea is most clearly explained by counterexample:
if an edge on a mesh has three triangles connected to it, the neighborhood of a
point on the edge is different from the neighborhood of one of the points in the
interior of one of the triangl es, because it has an extra “ ﬁn” sticking out of it
(Figure 12.1). If the edge has exactly two triangles attached to it, points on the
Figure 12.1. Non-manifold
(left) and manifold (right) in-
terior edges.
edge have neighborhoods just like points in the interior, only with a crease down
the middle. Similarly, if the triangles sharing a vertex are in a con ﬁguration like
the left one in Figure 12.2, the neighborhood is like two pieces of surface glued
Figure 12.2. Non-manifold
(left) and manifold (right) in-
terior vertices.
together at the center, which can’t be ﬂattened without doubling it up. The vertex
with the simpler neighborhood shown at right is just ﬁne.
Many algorithms assume that meshes are manifold, and it’s always a good
idea to verify this property to prevent crashes or inﬁ nite loops if you are handed a
malformed mesh as input. This veri ﬁcation boils down to checking that all edges
are manifold and checking that all vertices are manifold by verifying the following
conditions:
 Every edge is shared by exactly two triangles.
 Every vertex has a single, complete loop of triangles around it.
Figure 12.1 illustrates how an edge can fail the ﬁrst test by having too many tri-
angles, and Figure 12.2 illustrates how a vertex can fail the second test by having
two separate loops of triangles attached to it.
Manifold meshes are convenient, but sometimes it’s necessary to allow meshes
to have edges, or boundaries. Such meshes are not manifolds—a point on the
boundary has a neighborhood that is cut off on one side. They are not necessarily
watertight. However, we can relax the requirements of a manifold mesh to those
for a manifold with boundary without causing problems for most mesh processing
algorithms. The relaxed conditions are:
Ok Ok Bad
Figure 12.3. Conditions
at the edge of a manifold with
boundary.
 Every edge is used by either one or two triangles.
 Every vertex connects to a single edge-connected set of triangles.
Figure 12.3 illustrates these conditions: from left to right, there is an edge with
one triangle, a vertex whose neighboring triangles are in a single edge-connected
set, and a vertex with two disconnected sets of triangles attached to it.
Finally, in many applications it’s important to be able to distinguish the “front”
or “outside” of a surface from the “back” or “inside”—this is known as the ori-
entation of the surface. For a single triangle, we de ﬁne orientation based on the
order in which the vertices are listed: the front is the side from which the trian-
gle’s three vertices are arranged in counterclockwise order. A connected mesh is
AB
C
D
AB
C
D
Ok Bad
Figure 12.4. Trian-
gles (B,A,C) and (D,C,A) are
consistently oriented, whereas
(B,A,C) and (A,C,D) are in-
consistently oriented.

284 12. Data Structures for Graphics
consistently oriented if its triangles all agree on which side is the front—and this
is true if and only if every pair of adjacen t triangles is consistently oriented.
In a consistently oriented pair of triangles, the two shared vertices appear in
opposite orders in the two triangles’ vertex lists (Figure 12.4). What’s important is
consistency of orientation—some systems de ﬁne the front using clockwise rather
than counterclockwise order.
Figure 12.5. A triangulated
M¨obius band, which is not ori-
entable.
Any mesh that has non-manifold edges can’t be oriented consistently. But
it’s also possible for a mesh to be a valid manifold with boundary (or even a
manifold), and yet have no consistent way to orient the triangles—they are not
orientable surfaces. An example is the M¨ obius band shown in Figure 12.5. This
is rarely an issue in practice, however.
12.1.2 Indexed Mesh Storage
A simple triangular mesh is shown in Figure 12.6. Y ou could store these three
triangles as independent entities, each of this form:
Triangle {
vector3 vertexPosition[3]
}
This would result in storing vertex b three times and the other vertices twice each
for a total of nine stored points (three vertices for each of three triangles). Or you
could instead arrange to share the common vertices and store only four, resulting
a
d
c
b
# Vertex 0 Vertex 1 Vertex 2
0( ax, ay, az)( bx, by, bz)( cx, cy, cz)
1( bx, by, bz) (d x, dy, dz)( cx, cy, cz)
2( ax, ay, az)
# Vertices
0 (0, 1, 2)
1 (1, 3, 2)
2 (0, 3, 1)
# Position
0( ax, ay, az)
1( bx, by, bz)
2( cx, cy, cz)
3( dx, dy, dz)
(dx, dy, dz) (b x, by, bz)
Separate triangles: Shared vertices:
Triangles Vertices
Figure 12.6. A three-triangle mesh with four vertices, repre sented with separate triangles (left) and
with shared vertices (right).

12.1. T riangle Meshes 285
in a shared-vertex mesh. Logically, this data structure has triangles which point
to vertices which contain the vertex data:
Triangle {
Vertex v[3]
}
Vertex {
vector3 position // or other vertex data
}
Note that the entries in the v array are references, or pointers, to Vertex objects;
v[2]
v[0] v[1]
Figure 12.7. The triangle-
to-vertex references in a
shared-vertex mesh.
the vertices are not contained in the triangle.
In implementation, the vertices and tri angles are normally stored in arrays,
with the triangle-to-vertex refer ences handled by storing array indices:
IndexedMesh {
int tInd[nt][3]
vector3 verts[nv]
}
The index of the kth vertex of the ith triangle is found in tInd[i][k],a n dt h e
position of that vertex is stored in the corresponding row of the verts array;
see Figure 12.8 for an example. This way of storing a shared-vertex mesh is an
indexed triangle mesh.
Separate triangles or shared vertices will both work well. Is there a space
advantage for sharing vertices? If our mesh has nv vertices and nt triangles, and
if we assume that the data for ﬂoats, pointers, and ints all require the same storage
(a dubious assumption), the space requirements are as follows:
T12
p0
p1
p2
p3p9
p10
p4
p6
p5
p8
p7
T0
0
2
1
T1
2
2
2
1
1
1
0
0
0
T7
T8
T9
T10 T13
T15
T18
T19
T2
T3
T4
T5
T6
verts[0]
verts[1]
verts[2]
verts[3]
tInd[0]
tInd[1]
tInd[2]
tInd[3]
0, 2, 1
0, 3, 2
10, 2, 3
2, 10, 7
…
x0, y0, z0
x1, y1, z1
x2, y2, z2
x3, y3, z3
…
T17
T11
Figure 12.8. A larger triangle mesh, with part of its re presentation as an indexed triangle mesh.

286 12. Data Structures for Graphics
 T riangle.Three vectors per triangle, for 9nt units of storage;
 IndexedMesh. One vector per vertex and three ints per triangle, for 3nv +
3nt units of storage.
The relative storage requirements depend on the ratio of nt to nv .
Is this factor of two worth
the complication? I think
the answer is yes, and it be-
comes an even bigger win
as soon as you start adding
“properties” to the vertices.
As a rule of thumb, a large mesh has each vertex connected to about six tri-
angles (although there can be any number for extreme cases). Since each triangle
connects to three vertices, this means that there are generally twice as many tri-
angles as vertices in a large mesh: n
t ≈2nv . Making this substitution, we can
conclude that the storage requirements are 18nv for the Triangle structure and
9nv for IndexedMesh. Using shared vertices reduces storage requirements by
about a factor of two; and this seems to hold in practice for most implementations.
12.1.3 T riangle Strips and Fans
Indexed meshes are the most common in-memory representation of triangle
meshes, because they achieve a good bala nce of simplicity, convenience, and
compactness. They are also commonly used to transfer meshes over networks
and between the application and graphics pipeline. In applications where even
more compactness is desirable, the triangle vertex indices (which take up two-
thirds of the space in an indexed mesh with only positions at the vertices) can be
expressed more efﬁciently using triangle strips and triangle fans.
p1
p0
p5
p4
p3
p2
0 1
2
3
Figure 12.9. A triangle fan. A triangle fan is shown in Figure 12.9. In an indexed mesh, the triangles
array would contain [(0, 1, 2), (0, 2, 3), (0, 3, 4), (0, 4, 5)]. We are storing
12 vertex indices, although there are only six distinct vertices. In a triangle fan,
all the triangles share one common vertex, and the other vertices generate a set
of triangles like the vanes of a collapsible fan. The fan in the ﬁgure could be
speciﬁed with the sequence [0, 1, 2, 3, 4, 5]: the ﬁrst vertex establishes the center,
and subsequently each pair of adjacent vertices (1-2, 2-3, etc.) creates a triangle.
The triangle strip is a similar concept, but it is useful for a wider range of
meshes. Here, vertices are added altern ating top and bottom in a linear strip as
shown in Figure 12.10. The triangle strip in the ﬁgure could be speci ﬁed by the
s e q u e n c e[ 01234567 ] ,a n de v e r ys u b s e q u e n c eo ft h r e ea d jacent vertices (0-
1-2, 1-2-3, etc.) creates a triangle. For consistent orientation, every other triangle
needs to have its order reversed. In the example, this results in the triangles (0, 1,
p0
p1
p2
p3
p4
p5
p7
p6
4
2
0
1
3
5
Figure 12.10. A triangle
strip.
2), (2, 1, 3), (2, 3, 4), (4, 3, 5), etc. For each new vertex that comes in, the oldest
vertex is forgotten and the order of the two remaining vertices is swapped. See
Figure 12.11 for a larger example.

12.1. T riangle Meshes 287
p0
p1
p2
p3p9
p10
p4
p6
p5
p7
p8
*
verts[0] x0, y0, z0
x1, y1, z1
x2, y2, z2
x3, y3, z3
verts[1]
…
[0]
tStrips
4, 0, 1, 2, 5, 8
[1] 6, 9, 0, 3, 2, 10, 7
…
Figure 12.11. Two triangle strips in the context of a larger mesh. Note that neither strip can be
extended to include the triangle marked with an asterisk.
In both strips and fans, n+2 vertices sufﬁce to describe n triangles—a sub-
stantial savings over the 3n vertices required by a standard indexed mesh. Long
triangle strips will save approximately a factor of three if the program is vertex-
bound.
It might seem that triangle strips are only useful if the strips are very long,
but even relatively short strips already gain most of the bene ﬁts. The savings in
storage space (for only the vertex indices) are as follows:
strip length
 1
 2
 3
 4
 5
 6
 7
 8
 16
 100
 ∞
relative size
 1.00
 0.67
 0.56
 0.50
 0.47
 0.44
 0.43
 0.42
 0.38
 0.34
 0.33
So, in fact, there is a rather rapid diminishing return as the strips grow longer.
Thus, even for an unstructured mesh, it is worthwhile to use some greedy algo-
rithm to gather them into short strips.
12.1.4 Data Structures for Mesh Connectivity
Indexed meshes, strips, and fans are all good, compact representations for static
meshes. However, they do not readily allow for meshes to be modiﬁed. In order to
efﬁciently edit meshes, more complicated data structures are needed to ef ﬁciently
answer queries such as:
 Given a triangle, what are the three adjacent triangles?
 Given an edge, which two triangles share it?

288 12. Data Structures for Graphics
 Given a vertex, which faces share it?
 Given a vertex, which edges share it?
There are many data structures for triangle meshes, polygonal meshes, and polyg-
onal meshes with holes (see the notes at the end of the chapter for references). In
many applications the meshes are very large, so an ef ﬁcient representation can be
crucial.
The most straightforward, though bloated, implementation would be to have
three types, Vertex, Edge,a n d Triangle, and to just store all the relation-
ships directly:
Triangle {
Vertex v[3]
Edge e[3]
}
Edge {
Vertex v[2]
Triangle t[2]
}
Vertex {
Triangle t[]
Edge e[]
}
This lets us directly look up answers to the connectivity questions above, but
because this informa tion is all inter-related, it stor es more than is really needed.
Also, storing connectivity in vertices makes for variable-length data structures
(since vertices can have arbitrary numbers of neighbors), which are generally less
efﬁcient to implement. Rather than committing to store all these relationships
explicitly, it is best to deﬁne a class interface to answer these questions, behind
which a more efﬁcient data structure can hide. It turns out we can store only some
of the connectivity and ef ﬁciently recover the other information when needed.
The ﬁxed-size arrays in the Edge and Triangle classes suggest that it will
be more ef ﬁcient to store the connectivity information there. In fact, for polygon
meshes, in which polygons have arbitrary numbers of edges and vertices, only
edges have ﬁxed-size connectivity informati on, which leads to many traditional
mesh data structures being based on edges. But for triangle-only meshes, storing
connectivity in the (less numerous) faces is appealing.
A good mesh data structure should be reasonably compact and allow ef ﬁcient
answers to all adjacency queries. Ef ﬁcient means constant-time: the time to ﬁnd
neighbors should not depend on the size of the mesh. We’ll look at three data
structures for meshes, one based on triangles and two based on edges.

12.1. T riangle Meshes 289
The T riangle-Neighbor Structure
We can create a compact mesh data structure based on triangles by augmenting the
basic shared-vertex mesh with pointers from the triangles to the three neighboring
triangles, and a pointer from each vertex to one of the adjacent triangles (it doesn’t
matter which one); see Figure 12.12:
Triangle {
Triangle nbr[3];
Vertex v[3];
}
Vertex {
// ... per-vertex data ...
Triangle t; // any adjacent tri
}
v[2]
v[0] v[1]
nbr[1]nbr[2]
nbr[0] t
t
t
Figure 12.12. The
references between triangles
and vertices in the triangle-
neighbor structure.
In the array Triangle.nbr,t h e kth entry points to the neighboring triangle
that shares vertices k and k +1 . We call this structure the triangle-neighbor
structure. Starting from standard indexed mesh arrays, it can be implemented
with two additional arrays: one that stores the three neighbors of each triangle,
and one that stores a single neighboring triangle for each vertex (see Figure 12.13
for an example):
Mesh {
// ... per-vertex data ...
int tInd[nt][3]; // vertex indices
int tNbr[nt][3]; // indices of neighbor triangles
int vTri[nv]; // index of any adjacent triangle
}
T12
p0
p1
p2
p3p9
p10
p4
p6
p5
p8
p7
T0
0
2
1
T1
2
2
2
1
1
1
0
0
0
T7
T8
T9
T10 T13
T15
T18
T19
T2
T3
T4
T5
T6
[0]
[1]
[2]
[3]
…
[0] 0, 2, 1
1, 6, 7
10, 2, 0
3, 1, 12
2, 13, 4
[1]
[2]
[3]
0, 3, 2
10, 2, 3
2,10,7
…
…
[0] 0
[1]
[2]
[3]
6
3
1
vTri
tInd
tNbr T
17
T11
Figure 12.13. The triangle-neighbor structure as enc oded in arrays, and the sequence that is
followed in traversing the nei ghboring triangles of vertex 2.

290 12. Data Structures for Graphics
Clearly the neighboring triangles and vertices of a triangle can be found di-
rectly in the data structure, but by using t his triangle adjacency information care-
fully it is also possible to answer connectivity queries about vertices in constant
time. The idea is to move from triangle to triangle, visiting only the triangles
adjacent to the relevant vertex. If triangle t has vertex v as its kth vertex, then the
triangle t.nbr[k] is the next triangle around v in the clockwise direction. This
observation leads to the following algorithm t o traverse all the triangles adjacent
to a given vertex:
Of course, a real program
would do something with
the triangles as it found
them.
TrianglesOfVertex(v) {
t = v.t
do {
find i such that (t.v[i] == v)
t = t.nbr[i]
} while (t != v.t)
}
This operation ﬁnds each subsequent triangle in constant time—even though a
search is required to ﬁnd the position of the central vertex in each triangle’s vertex
list, the vertex lists have constant size so the search takes constant time. However,
that search is awkward and requires extra branching.
As m a l lr eﬁnement can avoid these searches. The problem is that once we
follow a pointer from one triangle to the next, we don’t know from which way
we came: we have to search the triangle’s vertices to ﬁnd the vertex that con-
nects back to the previous triangle. To solve this, instead of storing pointers to
neighboring triangles, we can store pointers to speci ﬁc edges of those triangles by
storing an index with the pointer:
Triangle {
Edge nbr[3];
Vertex v[3];
}
Edge { // the i-th edge of triangle t
Triangle t;
int i; // in {0,1,2}
}
Vertex {
// ... per-vertex data ...
Edge e; // any edge leaving vertex
}
In practice the Edge is stored by borrowing two bits of storage from the triangle
indext to store the edge index i, so that the total storage requirements remain the
same.

12.1. T riangle Meshes 291
In this structure the neighbor array for a triangle tells which of the neighboring
triangles’ edges are shared with the three edges of that triangle. With this extra
information, we always know where to ﬁnd the original triangle, which leads to
an invariant of the data structure: for any jth edge of any triangle t,
t.nbr[j].t.nbr[t.nbr[j].i].t == t.
Knowing which edge we came in through lets us know immediately which edge to
leave through in order to continue traversing around a vertex, leading to a stream-
lined algorithm:
TrianglesOfVertex(v) {
{t, i} = v.e;
do {
{t, i} = t.nbr[i];
i = (i+1) mod 3;
} while (t != v.e.t);
}
The triangle-neighbor structure is quite compact. For a mesh with only vertex
positions, we are storing four numbers (three coordinates and an edge) per vertex
and six (three vertex indices and three edges) per face, for a total of 4nv +6nt ≈
16nv units of storage per vertex, compared with 9nv for the basic indexed mesh.
The triangle neighbor structure as presented here works only for manifold
meshes, because it depends on returning to the starting triangle to terminate the
traversal of a vertex’s neighbors, which will not happen at a boundary vertex that
doesn’t have a full cycle of triangles. However, it is not dif ﬁcult to generalize
it to manifolds with boundary, by introducing a suitable sentinel value (such as
−1) for the neighbors of boundary triangles and taking care that the boundary
vertices point to the most counterclockwise neighboring triangle, rather than to
any arbitrary triangle.
The Winged-Edge Structure
One widely used mesh data structure that stores connectivity information at the
edges instead of the faces is the winged-edge data structure. This data struc-
ture makes edges the ﬁrst-class citizen of the data structure, as illustrated in Fig-
ures 12.14 and 12.15.
In a winged-edge mesh, each edge stor es pointers to the two vertices it con-
nects (the head and tail vertices), the two faces it is part of (the left and right
faces), and, most importantly, the next and previous edges i n the counterclock-
wise traversal of its left and right faces (Figure 12.16). Each vertex and face also
stores a pointer to a single, arbitrary edge that connects to it:

292 12. Data Structures for Graphics
e0
e1
e2
e12
e13
e14
e15
e3
e4
e5
e6
e7
e8
e9
e10
e18
e16 e17
e19
e3 e4
[0]
[1]
[2]
…
ln rp lp rn
1423
18 0 16 2
1 2 130
Winged-edge table
Figure 12.14. An example of a winged-edge mesh structure, stored in arrays.
fff
Edge Vertex
1
Vertex
2
Face
left
Edge EdgeVertex Face
d dC 2
d cB 1
a aA 0
e aD 3
Face
right
Pred
left
Succ
left
Pred
right
Succ
right
a
a
A
A
D3
3
0
0
fec b
b
eC D13dc a f
fC A32eebd
dBC
C
12ce fb
cB D
D
01
1
bae
e
d
d
bAB
B
02
2
ac
c
df
Figure 12.15. A tetrahedron and the associated elements for a winged-edge data structure. The
two small tables are not unique; each vertex and face stores any one of the edges with which it is
associated.

12.1. T riangle Meshes 293
Edge {
Edge lprev, lnext, rprev, rnext;
Vertex head, tail;
Face left, right;
}
Face {
// ... per-face data ...
Edge e; // any adjacent edge
}
Vertex {
// ... per-vertex data ...
Edge e; // any incident edge
}
lnext
lprev
rnext
rprev
Left Right
Head
Tail
Figure 12.16. The ref-
erences from an edge to the
neighboring edges, faces, and
vertices in the winged-edge
structure.
The winged-edge data structure supports constant-time access to the edges of
a face or of a vertex, and from those edges the adjoining vertices or faces can be
found:
EdgesOfVertex(v) {
e = v.e;
do {
if (e.tail == v)
e = e.lprev;
else
e = e.rprev;
} while (e != v.e);
}
EdgesOfFace(f) {
e = f.e;
do {
if (e.left == f)
e = e.lnext;
else
e = e.rnext;
} while (e != f.e);
}
These same algorithms and data structures will work equally well in a polygon
mesh that isn’t limited to triangles; this is one important advantage of edge-based
structures.
As with any data structure, the winged-edge data structure makes a variety of
time/space tradeoffs. For exa mple, we can eliminate the prev references. This
makes it more dif ﬁcult to traverse clockwise around faces or counterclockwise
around vertices, but when we need to know the previous edge, we can always
follow the successor edges in a circle until we get back to the original edge. This
saves space, but it makes some operations slower. (See the chapter notes for more
information on these tradeoffs).

294 12. Data Structures for Graphics
The Half-Edge Structure
The winged-edge structure is quite elegant, but it has one remaining awkward-
ness—the need to constantly check which way the edge is oriented before moving
to the next edge. This check is directly analogous to the search we saw in the basic
version of the triangle neighbor structure: we are looking to ﬁnd out whether we
entered the present edge from the head or from the tail. The solution is also almost
indistinguishable: rathe r than storing data for each edge, we store data for each
half-edge. There is one half-edge for each of the two triangles that share an edge,
and the two half-edges are oriented oppositely, each oriented consistently with its
own triangle.
The data normally stored in an edge is split between the two half-edges. Each
half-edge points to the face on its side of the edge and to the vertex at its head, and
each contains the edge pointers for its face. It also points to its neighbor on the
other side of the edge, from which the other half of the information can be found.
Like the winged-edge, a half-edge can contain pointers to both the previous and
next half-edges around its face, or only t o the next half-edge. We’ll show the
example that uses a single pointer.
Pair
Next
Left
Head
Figure 12.17. The ref-
erences from a half-edge to
its neighboring mesh compo-
nents.
HEdge {
HEdge pair, next;
Vertex v;
Face f;
}
Face {
// ... per-face data ...
HEdge h; // any h-edge of this face
}
Vertex {
// ... per-vertex data ...
HEdge h; // any h-edge pointing toward this vertex
}
Traversing a half-edge structure is just like traversing a winged-edge structure
except that we no longer need to check orientation, and we follow the pair
pointer to access the edges in the opposite face.
EdgesOfVertex(v) {
h = v.h;
do {
h = h.pair.next;
} while (h != v.h);
}

12.2. Scene Graphs 295
h0
h3h5
h6
h1
h4 h2
h9
h10
hedge[0]
hedge[1]
hedge[2]
Pair Next
12
01 0
34
hedge[3] 29
hedge[4] 50
hedge[5]
…46
Figure 12.18. An example of a half-edge mesh structure, stored in arrays.
EdgesOfFace(f) {
h = f.h;
do {
h = h.next;
} while (h != f.h);
}
The vertex traversal here is clockwise, which is necessary because of omitting
the prev pointer from the structure.
Because half-edges are generally allocated in pairs (at least in a mesh with
no boundaries), many implementations can do away with the pair pointers. For
instance, in an implementation based on array indexing (such as shown in Fig-
ure 12.18), the array can be arra nged so that an even-numbered edge i always
pairs with edge i+1 and an odd-numbered edge j always pairs with edge j −1.
In addition to the simple traversal algorithms shown in this chapter, all three of
these mesh topology structures can support “mesh surgery” operations of various
sorts, such as splitting or collapsing vertices, swapping edges, adding or removing
triangles, etc.
12.2 Scene Graphs
A triangle mesh manages a collection of triangles that constitute an object in a
scene, but another universal problem in graphics applications is arranging the
objects in the desired positions. As we saw in Chapter 6, this is done using trans-
formations, but complex scenes can cont ain a great many transformations and
organizing them well makes the scene m uch easier to manipulate. Most scenes
admit to a hierarchical organization, and the transformations can be managed ac-
cording to this hierarchy using a scene graph.

296 12. Data Structures for Graphics
b
p
θ
φ
Figure 12.19. A hinged pendulum. On the left are the two pieces in their “local” coordinate
systems. The hinge of the bottom piece is at point b and the attachment for the bottom piece is at its
local origin. The degrees of freedom for the assembled object are the angles (θ,φ) and the location p
of the top hinge.
To motivate the scene-graph data structure, we will use the hinged pendulum
shown in Figure 12.19. Consider how we would draw the top part of the pendu-
lum:
M1 = rotate(θ)
M2 = translate(p)
M3 = M2M1
Apply M3 to all points in upper pendulum
The bottom is more complicated, but we can take advantage of the fact that it is
attached to the bottom of the upper pendulum at point b in the local coordinate
system. First, we rotate the lower pendulum so that it is at an angle φrelative to
its initial position. Then, we move it so that its top hinge is at point b. Now it is
M3
Mc
Figure 12.20. The scene
graph for the hinged pendulum
of Figure 12.19.
at the appropriate position in the local coordinates of the upper pendulum, and it
can then be moved along with that coordinate system. The composite transform
for the lower pendulum is:
M
a = rotate(φ)
Mb = translate(b)
Mc = MbMa
Md = M3Mc
Apply Md to all points in lower pendulum
Thus, we see that the lower pendulum not only lives in its own local coordinate
system, but also that coordinate system itself is moved along with that of the upper
pendulum.
We can encode the pendulum in a data structure that makes management of
these coordinate system issues easier, as shown in Figure 12.20. The appropriate
matrix to apply to an object is just the product of all the matrices in the chain from

12.3. Spatial Data Structures 297
the object to the root of the data structure. For example, consider the model of a
ferry that has a car that can move freely on the deck of the ferry, and wheels that
each move relative to the car as shown in Figure 12.21.
M0
M1
M2 M3
Figure 12.21. A ferry, a car
on the ferry, and the wheels of
the car (only two shown) are
stored in a scene-graph.
As with the pendulum, each object should be transformed by the product of
the matrices in the path from the root to the object:
 ferry transform using M0;
 car body transform using M0M1;
 left wheel transform using M0M1M2;
 left wheel transform using M0M1M3.
An efﬁcient implementation can be achieved using a matrix stack, a data structure
supported by many APIs. A matrix stack is manipulated using push and pop op-
erations that add and delete matrices from the right-hand side of a matrix product.
For example, calling:
push(M0)
push(M1)
push(M2)
creates the active matrix M = M0M1M2. A subsequent call to pop() strips the
last matrix added so that the active matrix becomes M = M0M1. Combining
the matrix stack with a recursive traversal of a scene graph gives us:
function traverse(node)
push(Mlocal)
draw object using composite matrix from stack
traverse(left child)
traverse(right child)
pop()
There are many variations on scene graphs but all follow the basic idea above.
12.3 Spatial Data Structures
In many, if not all, graphics applications, the ability to quickly locate geometric
objects in particular regions of space is important. Ray tracers need to ﬁnd objects
that intersect rays; interactive applications navigating an environment need to ﬁnd
the objects visible from any given viewpoint; games and physical simulations re-
quire detecting when and where objects collide. All these needs can be supported

298 12. Data Structures for Graphics
by various spatial data structures designed to organize objects in space so they
can be looked up ef ﬁciently.
In this section we will discuss examples of three general classes of spatial data
structures. Structures that group objects together into a hierarchy are object par-
titioning schemes: objects are divided into disjoint groups, but the groups may
end up overlapping in space. Structures that divide space into disjoint regions
are space partitioning schemes: space is divided into separate partitions, but one
object may have to intersect more than one partition. Space partitioning schemes
can be regular, in which space is divided i nto uniformly shaped pieces, or irregu-
lar, in which space is divided adaptivel y into irregular pieces, with smaller pieces
where there are more and smaller objects.
We will use ray tracing as the primary motivation while discussing these struc-
tures, though they can all also be used for view culling or collision detection. In
Chapter 4, all objects were looped over while checking for intersections. For N
objects, this is an O(N) linear search and is thus slow for large scenes. Like most
search problems, the ray-object inters ection can be computed in sub-linear time
using “divide and conquer” techniques, provided we can create an ordered data
structure as a preprocess. There are many techniques to do this.
This section discusses three of these techniques in detail: bounding volume
hierarchies (Rubin & Whitted, 1980; Whitted, 1980; Goldsmith & Salmon, 1987),
uniform spatial subdivision (Cleary, W yvill, Birtwistle, & V atti, 1983; Fujimoto,
Tanaka, & Iwata, 1986; Amanatides & W oo, 1987), and binary space partition-
ing (Glassner, 1984; Jansen, 1986; Havran, 2000). An example of the ﬁrst two
strategies is shown in Figure 12.22.
Figure 12.22. Left: a uniform partitioning of space. Ri ght: adaptive bounding-box hierarchy.
Image courtesy David DeMarle.

12.3. Spatial Data Structures 299
12.3.1 Bounding Boxes
A key operation in most intersection-accel eration schemes is computing the in-
tersection of a ray with a bounding box (Figure 12.23). This differs from conven-
tional intersection tests in that we do not need to know where the ray hits the box;
we only need to know whether it hits the box.
To build an algorithm for ray-box intersection, we begin by considering a 2D
ray whose direction vector has positive x and y components. We can generalize
this to arbitrary 3D rays later. The 2D bounding box is de ﬁned by two horizontal
and two vertical lines:
Ray
Bounding box
Figure 12.23. The ray
is only tested for intersection
with the surfaces if it hits the
bounding box.x = xmin,
x = xmax,
y = ymin,
y = ymax.
The points bounded by these lines can be described in interval notation:
(x,y) ∈[xmin,x max]×[ymin,y max].
As shown in Figure 12.24, the intersection test can be phrased in terms of these
intervals. First, we compute the ray p arameter where the ray hits the line x =
xmin:
txmin = xmin −xe
xd
.
We then make similar computations for txmax, tymin,a n d tymax. The ray hits the
box if and only if the intervals [txmin,t xmax] and [tymin,t ymax] overlap, i.e., their
intersection is nonempty. In pseudocode this algorithm is:
txmin =( xmin −xe)/xd
txmax =( xmax −xe)/xd
tymin =( ymin −ye)/yd
tymax =( ymax −ye)/yd
if(txmin >t ymax) or (tymin >t xmax) then
return false
else
return true
The if statement may seem non-obvious. To see the logic of it, note that there is
no overlap if the ﬁrst interval is either entirely to the right or entirely to the left of
the second interval.

300 12. Data Structures for Graphics
txmin
t    [txmin, txmax]
t    [tymin, tymax]
t    [txmin, txmax] [tymin, tymax]
txmax
tymin
tymax
Figure 12.24. The ray will be inside the interval x ∈[xmin, xmax] for some interval in its parameter
space t ∈[txmin, txmax]. A similar interval exists for the y interval. The ray intersects the box if it is
in both the x interval and y interval at the same time, i.e., the intersection of the two one-dimensional
intervals is not empty.
The ﬁrst thing we must address is the case when xd or yd is negative. If xd is
negative, then the ray will hitxmax before it hitsxmin. Thus the code for computing
txmin and txmax expands to:
if (xd ≥0) then
txmin =( xmin −xe)/xd
txmax =( xmax −xe)/xd
else
txmin =( xmax −xe)/xd
txmax =( xmin −xe)/xd
A similar code expansion must be made for the y cases. A major concern is that
horizontal and vertical rays have a zero value for yd and xd, respectively. This
will cause divide by zero which may be a problem. However, before addressing
this directly, we check whether IEEE ﬂoating point computation handles these
cases gracefully for us. Recall from Sectio n 1.5 the rules for divide by zero: for

12.3. Spatial Data Structures 301
any positive real number a,
+a/0=+ ∞;
−a/0= −∞.
Consider the case of a vertical ray where xd =0 and yd > 0. We can then
calculate
txmin = xmin −xe
0 ;
txmax = xmax −xe
0 .
There are three possibilities of interest:
1. xe ≤xmin (no hit);
2. xmin <x e <x max (hit);
3. xmax ≤xe (no hit).
For the ﬁrst case we have
txmin = positive number
0 ;
txmax = positive number
0 .
This yields the interval (txmin,t xmin)=( ∞,∞). That interval will not overlap
with any interval, so there will be no hit, as desired. For the second case, we have
txmin = negative number
0 ;
txmax = positive number
0 .
This yields the interval (txmin,t xmin)=( −∞,∞) which will overlap with all
intervals and thus will yield a hit as desired. The third case results in the interval
(−∞,−∞) which yields no hit, as desired. Because these cases work as desired,
we need no special checks for them. As is often the case, IEEE ﬂoating point
conventions are our ally. However, there is still a problem with this approach.
Consider the code segment:
if(xd ≥0) then
tmin =( xmin −xe)/xd
tmax =( xmax −xe)/xd

302 12. Data Structures for Graphics
else
tmin =( xmax −xe)/xd
tmax =( xmin −xe)/xd
This code breaks down when xd = −0. This can be overcome by testing on the
reciprocal of xd (A. Williams, Barrus, Morley, & Shirley, 2005):
a =1 /xd
if(a ≥0) then
tmin = a(xmin −xe)
tmax = a(xmax −xe)
else
tmin = a(xmax −xe)
tmax = a(xmin −xe)
12.3.2 Hierarchical Bounding Boxes
The basic idea of hierarchical bounding boxes can be seen by the common tactic
of placing an axis-aligned 3D bounding box around all the objects as shown in
Figure 12.25. Rays that hit the bounding box will actually be more expensive
to compute than in a brute force search, b ecause testing for intersection with the
box is not free. However, rays that miss the box are cheaper than the brute force
search. Such bounding boxes can be made hi erarchical by partitioning the set of
Bounding box
Ray
d
y = ymax
y = ymin
x = xminx = xmine
Figure 12.25. A2 Dr a y
e + t d is tested against a 2D
bounding box.
objects in a box and placing a box around each partition as shown in Figure 12.26.
The data structure for the hierarchy shown in Figure 12.27 might be a tree with
the large bounding box at the root and the two smaller bounding boxes as left and
right subtrees. These would in turn each point to a list of three triangles. The
intersection of a ray with this particular hard-coded tree would be:
Bounding box
Ray
Figure 12.26. The bound-
ing boxes can be nested by
creating boxes around subsets
of the model.
if(ray hits root box) then
if(ray hits left subtree box) then
check three triangles for intersection
if(ray intersects right subtree box) then
check other three triangles for intersection
if(an intersections returned from each subtree ) then
return the closest of the two hits
else if(a intersection is returned from exactly one subtree) then
return that intersection
else
return false
else
return false

12.3. Spatial Data Structures 303
Some observations related to this algorithm are that there is no geometric ordering
between the two subtrees, and there is no reason a ray might not hit both subtrees.
Indeed, there is no reason that the two subtrees might not overlap.
A key point of such data hierarchies is that a box is guaranteed to bound all
objects that are below it in the hierarchy, but they are not guaranteed to contain
all objects that overlap it spatially, as shown in Figure 12.27. This makes this
Figure 12.27. The gray
box is a tree node that points
to the three gray spheres, and
the thick black box points to
the three black spheres. Note
that not all spheres enclosed
by the box are guaranteed to
be pointed to by the corre-
sponding tree node.
geometric search somewhat more compli cated than a traditional binary search on
strictly ordered one-dimensional data. The reader may note that several possible
optimizations present themselves. We defer optimizations until we have a full
hierarchical algorithm.
If we restrict the tree to be binary and require that each node in the tree have a
bounding box, then this traversal code extends naturally. Further, assume that all
nodes are either leaves in the tree and contain a primitive, or that they contain one
or two subtrees.
The bvh-node class should be of type surface, so it should implement
surface::hit. The data it contains should be simple:
class bvh-node subclass of surface
virtual bool hit(ray e+td, real t
0, real t1, hit-record rec)
virtual box bounding-box()
surface-pointer left
surface-pointer right
box bbox
The traversal code can then be called recursively in an object-oriented style:
function bool bvh-node::hit(ray a+tb, real t
0, real t1,
hit-record rec)
if(bbox.hitbox(a+tb,t0, t1)) then
hit-record lrec, rrec
left-hit = (left ̸= NULL) and (left → hit(a+tb, t
0, t1,l r e c))
right-hit =(right̸= NULL) and(right→ hit(a+tb,t0,t1, rrec))
if(left-hit and right-hit) then
if(lrec.t< rrec.t) then
rec = lrec
else
rec = rrec
return true
else if (left-hit) then
rec = lrec
return true
else if (right-hit) then

304 12. Data Structures for Graphics
rec = rrec
return true
else
return false
else
return false
Note that because left and right point to surfaces rather than bvh-nodes
speciﬁcally, we can let the virtual functions take care of distinguishing between
internal and leaf nodes; the appropriate hit function will be called. Note that if
the tree is built properly, we can elimin ate the check for left being NULL. If we
want to eliminate the check for right being NULL, we can replace NULL right
pointers with a redundant pointer to left. This will end up checking left twice, but
will eliminate the check throughout the tree. Whether that is worth it will depend
on the details of tree construction.
There are many ways to build a tree for a bounding volume hierarchy. It is
convenient to make the tree binary, roughly balanced, and to have the boxes of
sibling subtrees not overlap too much. A heuristic to accomplish this is to sort
the surfaces along an axis before dividing them into two sublists. If the axes are
deﬁn e db ya ni n t e g e rw i t hx =0 ,y =1 ,a n dz =2 we have:
function bvh-node::create(object-array A, int AXIS)
N = A.length
if(N=1 )then
left = A[0]
right = NULL
bbox = bounding-box(A[0])
else if (N=2 )then
left-node = A[0]
right-node = A[1]
bbox = combine(bounding-box(A[0]), bounding-box(A[1]))
else
sort A by the object center along AXIS
left= new bvh-node(A[0..N/2−1],(AXIS+1)mod 3)
right = new bvh-node(A[N/2..N−1],(AXIS+1) mod 3)
bbox =
combine(left→ bbox, right → bbox)
The quality of the tree can be improved by carefully choosing AXIS each time.
One way to do this is to choose the axis such that the sum of the volumes of the
bounding boxes of the two subtrees is minimized. This change compared to ro-
tating through the axes will make little difference for scenes composed of isotopi-
cally distributed small objects, but it may help signi ﬁcantly in less well-behaved

12.3. Spatial Data Structures 305
scenes. This code can also be made more ef ﬁcient by doing just a partition rather
than a full sort.
Another, and probably better, way to build the tree is to have the subtrees
contain about the same amount of space rather than the same number of objects.
To do this we partition the list based on space:
function bvh-node::create(object-array A, int AXIS)
N = A.length
if(N =1 )then
left = A[0]
right = NULL
bbox = bounding-box( A[0])
else if (N =2 )then
left = A[0]
right = A[1]
bbox = combine(bounding-box(A[0]), bounding-box(A[1]))
else
ﬁnd the midpoint m of the bounding box of A along AXIS
partition A into lists with lengths k and(N −k) surroundingm
left = new bvh-node(A[0..k],(AXIS+1)mod 3)
right = new bvh-node(A[k+1 ..N −1],(AXIS +1)mod 3)
bbox = combine(left→ bbox, right → bbox)
Although this results in an unbalanced tree, it allows for easy traversal of empty
space and is cheaper to build because partitioning is cheaper than sorting.
12.3.3 Uniform Spatial Subdivision
Another strategy to reduce intersection tests is to divide space. This is funda-
mentally different from dividing objects as was done with hierarchical bounding
volumes:
 In hierarchical bounding volumes, each object belongs to one of two sibling
nodes, whereas a point in space may be inside both sibling nodes.
 In spatial subdivision, each point in space belongs to exactly one node,
whereas objects may belong to many nodes.
In uniform spatial subdivision, the scene i s p
 artitioned into axis-aligned boxes.
These boxes are all the same size, alt hough they are not necessarily cubes. The
ray traverses these boxes as shown in Figure 12.28. When an object is hit, the
traversal ends.

306 12. Data Structures for Graphics
Ray
Figure 12.28. In uniform spatial subdivision, the ray is tracked forward through cells until an object
in one of those cells is hit. In this example, only objects in the shaded cells are checked.
The grid itself should be a subclass of surface and should be implemented as
a 3D array of pointers to surface. For empty cells these pointers are NULL. For
cells with one object, the pointer points to that object. For cells with more than
one object, the pointer can point to a list, another grid, or another data structure,
such as a bounding volume hierarchy.
This traversal is done in an incremental fashion. The regularity comes from
the way that a ray hits each set of parallel planes, as shown in Figure 12.29. To
see how this traversal works, ﬁrst consider the 2D case where the ray direction
has positive x and y components and starts outside the grid. Assume the grid is
bounded by points (x
min,y min) and(xmax,y max). The grid has nx ×ny cells.
Figure 12.29. Although the pattern of cell hits seems irregular (left), the hits on sets of parallel
planes are very even.

12.3. Spatial Data Structures 307
Our ﬁrst order of business is to ﬁnd the index (i,j) of the ﬁrst cell hit by
the ray e +td. Then, we need to traverse the cells in an appropriate order. The
key parts to this algorithm are ﬁnding the initial cell (i,j) and deciding whether
to increment i or j (Figure 12.30). Note that when we check for an intersection
 Ray
Cell (i, j)
tlast
tynext
txnext
Figure 12.30. To decide
whether we advance right or
upward, we keep track of the
intersections with the next ver-
tical and horizontal boundary
of the cell.
with objects in a cell, we restrict the range of t to be within the cell (Figure 12.31).
Most implementations make the 3D array of type “pointer to surface.” To improve
the locality of the traversal, the array can be tiled as discussed in Section 12.5.
12.3.4 Axis-Aligned Binary Space Partitioning
a
Ray
Cell (i, j)
b
Figure 12.31. Only hits
within the cell should be re-
ported. Otherwise the case
above would cause us to report
hitting object b rather than ob-
ject a.
We can also partition space in a hierarchical data structure such as a binary space
partitioning tree (BSP tree). This is similar to the BSP tree used for visibility
sorting in Section 12.4, but it’s most common to use axis-aligned, rather than
polygon-aligned, cutting plane s for ray intersection.
A node in this structure contains a single cutting plane and a left and right
subtree. Each subtree contains all the objects on one side of the cutting plane.
Objects that pass through the plane are stored in in both subtrees. If we assume
the cutting plane is parallel to the yz plane at x = D, then the node class is:
class bsp-node subclass of surface
virtual bool hit(ray e+td, real t
0, real t1, hit-record rec)
virtual box bounding-box()
surface-pointer left
surface-pointer right
realD
Case 1
Case 2
Case 3
Case 4
x = D
Figure 12.32. The
four cases of how a ray re-
lates to the BSP cutting plane
x=D .
We generalize this to y and z cutting planes later. The intersection code can then
be called recursively in an object-oriented style. The code considers the four
cases shown in Figure 12.32. For our purposes, the origin of these rays is a point
at parameter t
0:
p = a+t0b.
The four cases are:
1. The ray only interacts with the left subtree, and we need not test it for
intersection with the cutting plane. It occurs for x
p <D andxb < 0.
2. The ray is tested against the left subtree, and if there are no hits, it is then
tested against the right subtree. We need to ﬁnd the ray parameter atx = D,
so we can make sure we only test for intersections within the subtree. This
case occurs for x
p <D andxb > 0.

308 12. Data Structures for Graphics
3. This case is analogous to case 1 and occurs for xp >D and xb > 0.
4. This case is analogous to case 2 and occurs for xp >D and xb < 0.
The resulting traversal code handling these cases in order is:
function bool bsp-node::hit(ray a+tb, real t0, real t1,
hit-record rec)
xp = xa +t0xb
if(xp <D ) then
if(xb < 0) then
return(left ̸= NULL) and(left→hit(a+tb, t0,t1,r e c))
t =( D −xa)/xb
if(t>t 1) then
return(left ̸= NULL) and(left→hit(a+tb, t0,t1,r e c))
if(left ̸= NULL) and(left→hit(a+tb, t0,t,r e c)) then
return true
return(right ̸= NULL) and(right→hit(a+tb, t,t1,r e c))
else
analogous code for cases 3 and 4
This is very clean code. However, to get it started, we need to hit some root object
that includes a bounding box so we can initialize the traversal, t0 andt1. An issue
we have to address is that the cutting plane may be along any axis. We can add
an integer index axis to the bsp-node class. If we allow an indexing operator
for points, this will result in some simple modiﬁ cations to the code above, for
example,
x
p = xa +t0xb
would become
up = a[axis]+ t0b[axis]
which will result in some additional a rray indexing, but will not generate more
branches.
While the processing of a single bsp-node is faster than processing a bvh-node,
the fact that a single surface may exist in more than one subtree means there are
more nodes and, potentially, a higher memory use. How “well” the trees are built
determines which is faster. Building the tree is similar to building the BVH tree.
We can pick axes to split in a cycle, and we can split in half each time, or we can
try to be more sophisticated in how we divide.

12.4. BSP T rees for Visibility 309
12.4 BSP T rees for Visibility
Another geometric problem in which spatial data structures can be used is deter-
mining the visibility ordering of objects in a scene with changing viewpoint.
If we are making many images of a ﬁxed scene composed of planar polygons,
from different viewpoints—as is often the case for applications such as games—
we can use a binary space partitioning scheme closely related to the method for
ray intersection discussed in the previous section. The difference is that for vis-
ibility sorting we use non–axis-aligned sp litting planes, so that the planes can be
made coincident with the polygons. This leads to an elegant algorithm known as
the BSP tree algorithm to order the sur faces from front to back. The key aspect
of the BSP tree is that it uses a preprocess t o create a data structure that is useful
for any viewpoint. So, as the viewpoint changes, the same data structure is used
without change.
12.4.1 Overview of BSP T ree Algorithm
The BSP tree algorithm is an example of a painter’s algorithm. A painter’s algo-
rithm draws every object from back-to-front, with each new polygon potentially
overdrawing previous polygons, as is shown in Figure 12.33. It can be imple-
mented as follows:
sort objects back to front relative to viewpoint
for each object do
draw object on screen
The problem with the ﬁrst step (the sort) is that the relative order of multiple
objects is not always well de ﬁned, even if the order of every pair of objects is.
This problem is illustrated in Figure 12.34 where the three triangles form a cycle.
The BSP tree algorithm works on any scene composed of polygons where
no polygon crosses the plane de ﬁned by any other polygon. This restriction is
then relaxed by a preprocessing step. For the rest of this discussion, triangles are
assumed to be the only primitive, but the ideas extend to arbitrary polygons.
The basic idea of the BSP tree can be illustrated with two triangles, T
1 and
T2.W e ﬁrst recall (see Section 2.5.3) the im plicit plane equation of the plane
containing T1: f1(p)=0 . The key property of implicit planes that we wish to
take advantage of is that for all points p+ on one side of the plane, f1(p+) > 0;
and for all points p− on the other side of the plane, f1(p−) < 0. Using this
property, we can ﬁnd out on which side of the plane T2 lies. Again, this assumes
all three vertices of T2 are on the same side of the plane. For discussion, assume

310 12. Data Structures for Graphics
Figure 12.33. A painter’s algorithm starts with a blank image and then draws the scene one object
at a time from back-to-front, overdrawing whatever is already there. This automatically eliminates
hidden surfaces.
thatT2 is on the f1(p) < 0 side of the plane. Then, we can draw T1 andT2 in the
right order for any eyepoint e:
if (f1(e) < 0) then
drawT1
drawT2
else
drawT2
drawT1
Figure 12.34. A cycle oc-
curs if a global back-to-front
ordering is not possible for a
particular eye position.
The reason this works is that if T2 and e are on the same side of the plane con-
taining T1, there is no way for T2 to be fully or partially blocked by T1 as seen

12.4. BSP T rees for Visibility 311
T1
Plane containing T1
T2
e
Figure 12.35. When e and T2 are on opposite sides of the plane containing T1,t h e ni ti ss a f et o
draw T2 ﬁrst and T1 second. If e and T2 are on the same side of the plane, then T1 should be drawn
before T2. This is the core idea of the BSP tree algorithm.
from e,s oi ti ss a f et od r a w T1 ﬁrst. If e and T2 are on opposite sides of the
plane containing T1,t h e nT2 cannot fully or partially block T1, and the opposite
drawing order is safe (Figure 12.35).
This observation can be generalized to many objects provided none of them
span the plane de ﬁned by T1. If we use a binary tree data structure with T1
as root, the negative branch of the tree contains all the triangles whose
vertices have fi(p) < 0,a n dt h e positive branch of the tree contains all the
triangles whose vertices have fi(p) > 0. We can draw in proper order
as follows:
function draw(bsptree tree, point e)
if (tree.empty) then
return
if (ftree.root(e) < 0) then
draw(tree.plus, e)
rasterize tree.triangle
draw(tree.minus, e)
else
draw(tree.minus, e)
rasterize tree.triangle
draw(tree.plus, e)
The nice thing about that code is that it will work for any viewpoint e,s ot h e
tree can be precomputed. Note that, if each subtree is itself a tree, where the root
triangle divides the other triangles into two groups relative to the plane containing
it, the code will work as is. It can be made slightly more ef ﬁcient by terminat-
ing the recursive calls one level higher, but the code will still be simple. A tree

312 12. Data Structures for Graphics
f1 > 0
f2 > 0
T2
T1
T3
T3
T2
Positive
Negative
T1
Figure 12.36. Three triangles and a BSP tree that is valid for them. The “positive” and “negative”
are encoded by right and left subtree position, respectively.
illustrating this code is shown in Figure 12.36. As discussed in Section 2.5.5, the
implicit equation for a point p on a plane containing three non-colinear points a,
b,a n dc is
f(p)=( (b−a)×(c−a))·(p−a)=0 . (12.1)
It can be faster to store the (A,B,C,D ) of the implicit equation of the form
f(x,y,z )= Ax+By +Cz +D =0 . (12.2)
Equations (12.1) and (12.2) are equivale nt, as is clear when you recall that the
gradient of the implicit equation is the normal to the triangle. The gradient of
Equation (12.2) is n =( A,B,C ) which is just the normal vector
n =( b−a)×(c−a).
We can solve for D by plugging in any point on the plane, e.g., a:
D = −Ax
a −Bya −Cza
= −n· a.

12.4. BSP T rees for Visibility 313
This suggests the form:
f(p)= n· p−n· a
= n·(p−a)
=0 ,
which is the same as Equation (12.1) once you recall that n is computed using the
cross product. Which form of the plane equation you use and whether you store
only the vertices, n and the vertices, or n,D, and the vertices, is probably a matter
of taste—a classic time-storage tradeoff that will be settled best by pro ﬁling. For
debugging, using Equation (12.1) is probably the best.
The only issue that prevents the code above from working in general is that
one cannot guarantee that a triangle can be uniquely classi ﬁed on one side of a
plane or the other. It can have two vertices on one side of the plane and the third
on the other. Or it can have vertices on the plane. This is handled by splitting the
triangle into smaller triangles using the plane to “cut” them.
12.4.2 Building the T ree
If none of the triangles in the dataset cross each other’s planes, so that all triangles
are on one side of all other triangles, a BSP tree that can be traversed using the
code above can be built using the following algorithm:
tree-root = node(T1)
fori ∈{2,...,N } do
tree-root.add(Ti)
function add ( triangleT )
if(f(a) < 0 and f(b) < 0 andf(c) < 0) then
if(negative subtree is empty) then
negative-subtree = node(T)
else
negative-subtree.add(T)
else if (f(a) > 0 andf(b) > 0 andf(c) > 0) then
if positive subtree is empty then
positive-subtree = node(T)
else
positive-subtree.add(T)
else
we have assumed this case is impossible

314 12. Data Structures for Graphics
The only thing we need to ﬁx is the case where the triangle crosses the dividing
plane, as shown in Figure 12.37. Assume, for simplicity, that the triangle has
vertices a and b on one side of the plane, and vertex c is on the other side. In this
case, we can ﬁnd the intersection points A and B and cut the triangle into three
new triangles with vertices
T1 =( a, b, A),
T2 =( b, B, A),
T3 =( A, B, c),
as shown in Figure 12.38. This order of vertices is important so that the direction
a
b
c
A
B
Figure 12.37. When a tri-
angle spans a plane, there will
be one vertex on one side and
two on the other.
of the normal remains the same as for the original triangle. If we assume that
f(c) < 0, the following code could add these three triangles to the tree assuming
the positive and negative subtrees are not empty:
positive-subtree = node (T1)
positive-subtree = node (T2)
negative-subtree = node (T3)
A precision problem that will plague a naive implementation occurs when a vertex
is very near the splitting plane. For example, if we have two vertices on one side of
the splitting plane and the other vertex is only an extremely small distance on the
a
b
c
B
A
Figure 12.38. When a tri-
angle is cut, we break it into
three triangles, none of which
span the cutting plane.
other side, we will create a new triangle almost the same as the old one, a triangle
that is a sliver, and a triangle of almost zero size. It would be better to detect this
as a special case and not split into three new triangles. One might expect this case
to be rare, but because many models have tessellated planes and triangles with
shared vertices, it occurs frequently, and thus must be handled carefully. Some
simple manipulations that accomplish this are:
function add( triangle T )
fa = f(a)
fb = f(b)
fc = f(c)
if(abs(fa) <ϵ ) then
fa = 0
if(abs(fb) <ϵ ) then
fb = 0
if(abs(fc) <ϵ ) then
fc = 0
if(fa ≤0 and fb ≤0 and fc ≤0) then
if(negative subtree is empty) then
negative-subtree = node(T)

12.4. BSP T rees for Visibility 315
else
negative-subtree.add(T)
else if (fa ≥0 and fb ≥0 and fc ≥0) then
if(positive subtree is empty) then
positive-subtree = node(T)
else
positive-subtree.add(T)
else
cut triangle into three triangles and add to each side
This takes any vertex whose f value is within ϵ of the plane and counts it as
positive or negative. The constant ϵ is a small positive real chosen by the user.
The technique above is a rare instance where testing for ﬂoating-point equality is
useful and works because the zero value is set rather than being computed. Com-
paring for equality with a computed ﬂoating-point value is almost never advisable,
but we are not doing that.
12.4.3 Cutting T riangles
Filling out the details of the last case “cut triangle into three triangles and add to
each side” is straightforward, but tedi ous. We should take advantage of the BSP
tree construction as a preprocess where highest ef ﬁciency is not key. Instead, we
should attempt to have a clean compact code. A nice trick is to force many of the
cases into one by ensuring that c is on one side of the plane and the other two
vertices are on the other. This is easily done with swaps. Filling out the details in
the ﬁnal else statement (assuming the subtrees are nonempty for simplicity) gives:
if(fa ∗fc ≥0) then
swap(fb,fc )
swap(b, c)
swap(fa,fb )
swap(a, b)
else if (fb ∗fc ≥0) then
swap(fa,fc )
sw
ap(a, c)
swap(fa,fb )
swap(a, b)
compute A
compute B
T1 =( a, b, A)
T2 =( b, B, A)

316 12. Data Structures for Graphics
T3 =( A, B, c)
if(fc ≥0) then
negative-subtree.add(T1)
negative-subtree.add(T2)
positive-subtree.add(T3)
else
positive-subtree.add(T1)
positive-subtree.add(T2)
negative-subtree.add(T3)
This code takes advantage of the fact that the product ofa andb are positive if they
have the same sign—thus, the ﬁrst if statement. If vertices are swapped, we must
do two swaps to keep the vertices ordered counterclockwise. Note that exactly
one of the vertices may lie exactly on the plane, in which case the code above will
work, but one of the generated triangles will have zero area. This can be handled
by ignoring the possibility, which is not th at risky, because the rasterization code
must handle zero-area triangles in screen space (i.e., edge-on triangles). Y ou can
also add a check that does not add zero-area triangles to the tree. Finally, you can
put in a special case for when exactly one of fa ,fb ,a n dfc is zero which cuts the
triangle into two triangles.
To compute A and B, a line segment and implicit plane intersection is needed.
For example, the parametric line connecting a and c is
p(t)= a+t(c−a).
The point of intersection with the plane n· p+D =0 is found by plugging p(t)
into the plane equation:
n·(a+t(c−a))+ D =0 ,
and solving for t:
t = − n· a+D
n·(c−a).
Calling this solution tA, we can write the expression for A:
A = a+tA(c−a).
A similar computation will give B.

12.5. Tiling Multidimensional Arrays 317
12.4.4 Optimizing the T ree
The efﬁciency of tree creation is much less of a concern than tree traversal because
it is a preprocess. The traversal of the BSP tree takes time proportional to the
number of nodes in the tree. (How well balanced the tree is does not matter.)
There will be one node for each triangle, incl uding the triangles that are created
as a result of splitting. This number can depend on the order in which triangles
are added to the tree. For example, in Figure 12.39, if T
1 is the root, there will be
two nodes in the tree, but if T2 is the root, there will be more nodes, because T1
will be split.
It is dif ﬁcult to ﬁnd the “best” order of triangles to add to the tree. For N
triangles, there are N! orderings that are possible. So trying all orderings is not
usually feasible. Alternatively, some pr edetermined number of orderings can be
Figure 12.39. Using T1 as
the root of a BSP tree will re-
sult in a tree with two nodes.
Using T
2 as the root will re-
quire a cut and thus make a
larger tree.
tried from a random collection of permutations, and the best one can be kept for
the ﬁnal tree.
The splitting algorithm described above splits one triangle into three trian-
gles. It could be more ef ﬁcient to split a triangle into a triangle and a con-
vex quadrilateral. This is probably not worth it if all input models have only
triangles, but would be easy to support for implementations that accommodate
arbitrary polygons.
12.5 Tiling Multidimensional Arrays
Effectively utilizing the memory hierarchy is a crucial task in designing algo-
rithms for modern architectures. Making sure that multidimensional arrays have
data in a “nice” arrangement is accomplished by tiling, sometimes also called
bricking. A traditional 2D array is stored as a 1D array together with an indexing
mechanism; for example, an N
x by Ny array is stored in a 1D array of length
NxNy and the 2D index (x,y) (which runs from (0,0) to(Nx−1,Ny−1))m a p s
to the 1D index (running from 0 toNxNy −1) using the formula
j = 2
j = 1
j = 0
i = 0 i = 1 i = 2 i = 3
8
4
0
9
5
1
10
6
2
11
7
3
Figure 12.40. The memory
layout for an untiled 2D array
with Nx = 4 and Ny = 3.
index = x+Nxy.
An example of how that memory lays out is shown in Figure 12.40. A problem
with this layout is that although two adjacent array elements that are in the same
row are next to each other in memory, two adjacent elements in the same column
will be separated by N
x elements in memory. This can cause poor memory lo-
cality for large Nx. The standard solution to this is to use tiles to make memory

318 12. Data Structures for Graphics
locality for rows and columns more equal. An example is shown in Figure 12.41
where 2 × 2 tiles are used. The details of indexing such an array are discussed in
the next section. A more complicated example, with two levels of tiling on a 3D
array, is covered after that.
A key question is what size to make the tiles. In practice, they should be
similar to the memory-unit size on the machine. For example, if we are using
j = 2
j = 1
j = 0
i = 0 i = 1 i = 2 i = 3
8
2
0
9
3
1
12
6
4
13
7
5
Figure 12.41. The memory
layout for a tiled 2D array with
Nx = 4 and Ny = 3 and 2 ×
2 tiles. Note that padding on
the top of the array is needed
because Ny is not a multiple of
the tile size two.
16-bit (2-byte) data values on a machine with 128-byte cache lines, 8 × 8 tiles ﬁt
exactly in a cache line. However, using 32-bit ﬂoating-point numbers, which ﬁt
32 elements to a cache line, 5 × 5 tiles are a bit too small and 6 × 6 tiles are a
bit too large. Because there are also coarser-sized memory units such as pages,
hierarchical tiling with similar logic can be useful.
12.5.1 One-Level Tiling for 2D Arrays
If we assume anNx×Ny array decomposed into squaren×n tiles (Figure 12.42),
then the number of tiles required is
Bx = Nx/n,
By = Ny/n.
Here, we assume that n divides Nx and Ny exactly. When this is not true, the
array should be padded. For example, if Nx =1 5 and n =4 ,t h e nNx should
be changed to 16. To work out a formula for indexing such an array, we ﬁrst ﬁnd
n
n
Ny elements
By = Ny/n blocks
Nx elements
Bx = Nx/n blocks
Figure 12.42. A tiled 2D array composed of Bx × By tiles each of size n by n.

12.5. Tiling Multidimensional Arrays 319
the tile indices (bx,by) that give the row/column for the tiles (the tiles themselves
form a 2D array):
bx = x÷n,
by = y ÷n,
where ÷ is integer division, e.g., 12÷5=2 . If we order the tiles along rows as
shown in Figure 12.40, then the index of the ﬁrst element of the tile (bx,by) is
index = n2(Bxby +bx).
The memory in that tile is arranged like a traditional 2D array as shown in Fig-
ure 12.41. The partial offsets (x′,y′) inside the tile are
x′ = x mod n,
y′ = y mod n,
where mod is the remainder operator, e.g., 12 mod 5 = 2. Therefore, the offset
inside the tile is
offset = y′n+x′.
Thus the full formula for ﬁnding the 1D index element (x,y) in anNx×Ny array
withn×n tiles is
index = n2(Bxby +bx)+ y′n+x′,
= n2((Nx ÷n)(y ÷n)+ x÷n)+( y mod n)n+(x mod n).
This expression contains many intege r multiplication, divide and modulus oper-
ations, which are costly on some processors. When n is a power of two, these
operations can be converted to bitshifts and bitwise logical operations. However,
as noted above, the ideal size is not always a power of two. Some of the mul-
tiplications can be converted to shift/add operations, but the divide and modulus
operations are more problematic. The indices could be computed incrementally,
but this would require tracking counters, with numerous comparisons and poor
branch prediction performance.
However, there is a simple solution; note that the index expression can be
written as
index = F
x(x)+ Fy(y),
where
Fx(x)= n2(x÷n)+( x mod n),
Fy(y)= n2(Nx ÷n)(y ÷n)+( y mod n)n.
We tabulate Fx and Fy , and use x and y to ﬁnd the index into the data array.
These tables will consist of Nx and Ny elements, respectively. The total size of
the tables will ﬁt in the primary data cache of the processor, even for very large
data set sizes.

320 12. Data Structures for Graphics
12.5.2 Example: T wo-Level Tiling for 3D Arrays
Effective TLB utilization is also becoming a crucial factor in algorithm perfor-
mance. The same technique can be used to improve TLB hit rates in a 3D array
TLB: translation lookaside
buffer, a cache that is part
of the virtual memory sys-
tem.
by creating m×m×m bricks of n×n×n cells. For example, a 40×20×19
volume could be decomposed into 4×2×2 macrobricks of 2×2×2 bricks of
5 × 5 × 5 cells. This corresponds to m =2 and n =5 . Because 19 cannot be
factored by mn =1 0, one level of padding is needed. Empirically useful sizes
arem =5 for 16-bit datasets and m =6 for ﬂoat datasets.
The resulting index into the data array can be computed for any (x,y,z ) triple
with the expression
index =( ( x÷n)÷m)n3m3((Nz ÷n)÷m)((Ny ÷n)÷m)
+((y ÷n)÷m)n3m3((Nz ÷n)÷m)
+((z ÷n)÷m)n3m3
+((x÷n)m o dm)n3m2
+((y ÷n)m o dm)n3m
+((z ÷n)m o dm)n3
+(x mod (n2))n2
+(y mod n)n
+(z mod n),
where Nx, Ny and Nz are the respective sizes of the dataset.
Note that, as in the simpler 2D one-level case, this expression can be written as
index = Fx(x)+ Fy(y)+ Fz(z),
where
Fx(x)=( ( x÷n)÷m)n3m3((Nz ÷n)÷m)((Ny ÷n)÷m)
+((x÷n)m o dm)n3m2
+(x mod n)n2,
Fy(y)=( ( y ÷n)÷m)n3m3((Nz ÷n)÷m)
+((y ÷n)m o dm)n3m+
+(y mod n)n,
Fz(z)=( ( z ÷n)÷m)n3m3
+((z ÷n)m o dm)n3
+(z mod n).

12.5. Tiling Multidimensional Arrays 321
Frequently Asked Questions
•Does tiling really make that much difference in performance?
On some volume rendering applications, a two-level tiling strategy made as much
as a factor-of-ten performance difference. When the array does not ﬁti nm a i n
memory, it can effectively prevent thra shing in some applications such as image
editing.
•How do I store the lists in a winged-edge structure?
For most applications, it is feasible to use arrays and indices for the references.
However, if many delete operations are to be performed, then it is wise to use
linked lists and pointers.
Notes
The discussion of the winged-edge data structure is based on the course notes
of Ching-Kuang Shene (Shene, 2003). There are smaller mesh data structures
than winged-edge. The tradeoffs in using such structures is discussed in Directed
Edges—A Scalable Representation for Triangle Meshes (Campagna, Kobbelt, &
Seidel, 1998). The tiled-array discussion is based on Interactive Ray Tracing for
V olume Visualization (S. Parker et al., 1999). A structure similar to the trian-
gle neighbor structure is discussed in a technical report by Charles Loop (Loop,
2000). A discussion of manifolds can be found in an introductory topology
text (Munkres, 2000).
Exercises
1. What is the memory difference for a simple tetrahedron stored as four in-
dependent triangles and one stored in a winged-edge data structure?
2. Diagram a scene graph for a bicycle.
3. How many look-up tables are needed for a single-level tiling of an n-
dimensional array?
4. GivenN triangles, what is the minimum number of triangles that could be
added to a resulting BSP tree? What is the maximum number?

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
**关联概念**: [[BVH加速结构]] · [[光线追踪]]
**pbrt 对应**: pbrt Ch7 Primitives and Acceleration
