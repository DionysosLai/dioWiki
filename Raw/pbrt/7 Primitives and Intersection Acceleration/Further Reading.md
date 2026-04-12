---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Primitives_and_Intersection_Acceleration/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
The stochastic alpha test implemented in Section [7.1.1](https://pbr-book.org/4ed/Primitives_and_Intersection_Acceleration/Primitive_Interface_and_Geometric_Primitives.html#sec:gprim) builds on ideas introduced in Enderton et al.’s stochastic approach for transparency ([^22]) and Wyman and McGuire’s hashed alpha testing algorithm ([^96]), both of which were focused on rasterization-based rendering.

After the introduction of the ray-tracing algorithm, an enormous amount of research was done to try to find effective ways to speed it up, primarily by developing improved ray-tracing acceleration structures. Arvo and Kirk’s chapter in *An Introduction to Ray Tracing* (Glassner [1989a](#cite:Glassner:IntroRayTracing)) summarizes the state of the art as of 1989 and still provides an excellent taxonomy for categorizing different approaches to ray intersection acceleration.

Kirk and Arvo ([^56]) introduced the unifying principle of *meta-hierarchies.* They showed that by implementing acceleration data structures to conform to the same interface as is used for primitives in the scene, it is easy to mix and match different intersection acceleration schemes. pbrt follows this model.

#### Grids

Fujimoto, Tanaka, and Iwata ([^27]) introduced uniform grids, a spatial subdivision approach where the scene bounds are decomposed into equally sized grid cells. More efficient grid-traversal methods were described by Amanatides and Woo ([^2]) and Cleary and Wyvill ([^19]). Snyder and Barr ([^78]) described a number of key improvements to this approach and showed the use of grids for rendering extremely complex scenes. Hierarchical grids, where grid cells with many primitives in them are themselves refined into grids, were introduced by Jevans and Wyvill ([^50]). More sophisticated techniques for hierarchical grids were developed by Cazals, Drettakis, and Puech ([^15]) and Klimaszewski and Sederberg ([^57]).

Ize et al. ([^47]) developed an efficient algorithm for parallel construction of grids. One of their interesting findings was that grid construction performance quickly became limited by memory bandwidth as the number of cores used increased.

Choosing an optimal grid resolution is important for getting good performance from grids. A good paper on this topic is by Ize et al. ([^48]), who provided a solid foundation for automatically selecting the resolution and for deciding when to refine into subgrids when using hierarchical grids. They derived theoretical results using a number of simplifying assumptions and then showed the applicability of the results to rendering real-world scenes. Their paper also includes a good selection of pointers to previous work in this area.

Lagae and Dutré ([2008a](#cite:Lagae08a)) described an innovative representation for uniform grids based on hashing that has the desirable properties that not only does each primitive have a single index into a grid cell, but also each cell has only a single primitive index. They showed that this representation has very low memory usage and is still quite efficient.

Hunt and Mark ([2008a](#cite:Hunt08a)) showed that building grids in perspective space, where the center of projection is the camera or a light source, can make tracing rays from the camera or light substantially more efficient. Although this approach requires multiple acceleration structures, the performance benefits from multiple specialized structures for different classes of rays can be substantial. Their approach is also notable in that it is in some ways a middle ground between rasterization and ray tracing.

#### Bounding Volume Hierarchies

Clark ([^18]) first suggested using bounding volumes to cull collections of objects for standard visible-surface determination algorithms. Building on this work, Rubin and Whitted ([^75]) developed the first hierarchical data structures for scene representation for fast ray tracing, although their method depended on the user to define the hierarchy. Kay and Kajiya ([^54]) implemented one of the first practical object subdivision approaches based on bounding objects with collections of slabs.

Goldsmith and Salmon ([^34]) described the first algorithm for automatically computing bounding volume hierarchies. Although their algorithm was based on estimating the probability of a ray intersecting a bounding volume using the volume’s surface area, it was much less effective than modern SAH BVH approaches. The first use of the SAH for BVH construction was described by Müller and Fellner ([^64]); another early application is due to Massó and López ([^66]).

The [BVHAggregate](https://pbr-book.org/4ed/Primitives_and_Intersection_Acceleration/Bounding_Volume_Hierarchies.html#BVHAggregate) implementation in this chapter is based on the construction algorithm described by Wald ([^87]) and Günther et al. ([^28]). The bounding box test is the one introduced by Williams et al. ([^92]). An even more efficient bounding box test that does additional precomputation in exchange for higher performance when the same ray is tested for intersection against many bounding boxes was developed by Eisemann et al. ([^21]); we leave implementing their method for an exercise. Ize’s robust ray–bounding box intersection algorithm ensures that the BVH is *watertight* and that valid intersections are not missed due to numeric error ([Ize 2013](#cite:Ize2013)).

The BVH traversal algorithm used in pbrt was concurrently developed by a number of researchers; see the notes by Boulos and Haines ([^13]) for more details and background. Another option for tree traversal is that of Kay and Kajiya ([^54]); they maintained a heap of nodes ordered by ray distance. On GPUs, which have relatively limited amounts of on-chip memory, maintaining a stack of to-be-visited nodes for each ray may have a prohibitive memory cost. Foley and Sugerman ([^26]) introduced a “stackless” kd-tree traversal algorithm that periodically backtracks and searches starting from the tree root to find the next node to visit, rather than storing all nodes to visit explicitly. Laine ([^59]) made a number of improvements to this approach, reducing the frequency of re-traversals from the tree root and applying the approach to BVHs. See also Binder and Keller ([^10]), who applied perfect hashing to finding subsequent nodes to visit with the stackless approach.

An innovative approach to BVH traversal is described by Hendrich et al. ([^40]), who created a spatio-directional 5D data structure that records a set of BVH nodes that are used to seed the traversal stack for sets of rays. Given a particular ray, traversal starts immediately with an appropriate stack, which in turn improves performance by entirely skipping processing of BVH nodes that are either certain to be intersected or certain not to be intersected for rays in a particular set.

A number of researchers have developed techniques for improving the quality of BVHs after construction. Yoon et al. ([^99]) and Kensler ([^55]) presented algorithms that make local adjustments to the BVH. See also Bittner et al. ([^11], [^12]), Karras and Aila ([^53]), and Meister and Bittner ([2018a](#cite:Meister2018a)) for further work in this area. An interesting approach was described by Gu et al. ([^36]), who constructed a BVH, traced a relatively small number of representative rays, and gathered statistics about how frequently each bounding box was intersected, and then tuned the BVH to be more efficient for rays with similar statistics.

Most current methods for building BVHs are based on top-down construction of the tree, first creating the root node and then partitioning the primitives into children and continuing recursively. An alternative approach was demonstrated by Walter et al. ([^90]), who showed that bottom-up construction, where the leaves are created first and then agglomerated into parent nodes, is a viable option. Gu et al. ([2013b](#cite:Gu2013b)) developed a much more efficient implementation of this approach and showed its suitability for parallel implementation, and Meister and Bittner ([2018b](#cite:Meister2018b)) described a bottom-up approach that is suitable for GPU implementation.

One shortcoming of BVHs is that even a small number of relatively large primitives that have overlapping bounding boxes can substantially reduce the efficiency of the BVH: many of the nodes of the tree will be overlapping, solely due to the overlapping bounding boxes of geometry down at the leaves. Ernst and Greiner ([^24]) proposed “split clipping” as a solution; the restriction that each primitive only appears once in the tree is lifted, and the bounding boxes of large input primitives are subdivided into a set of tighter subbounds that are then used for tree construction.

Dammertz and Keller ([2008a](#cite:Dammertz2008a)) observed that the problematic primitives are the ones with a large amount of empty space in their bounding box relative to their surface area, so they subdivided the most egregious triangles and reported substantial performance improvements. Stich et al. ([^80]) developed an approach that splits primitives during BVH construction, making it possible to only split primitives when an SAH cost reduction was found. See also Popov et al.’s paper ([^74]) on a theoretically optimal BVH partitioning algorithm and its relationship to previous approaches, and Karras and Aila ([^53]) for improved criteria for deciding when to split triangles. Woop et al. ([^94]) developed an approach to building BVHs for long, thin geometry like hair and fur; because this sort of geometry is quite thin with respect to the volume of its bounding boxes, it normally has poor performance with most acceleration structures. Ganestam and Doggett ([^29]) have proposed a splitting approach that has benefits to both BVH construction and traversal efficiency.

The memory requirements for BVHs can be significant. In our implementation, each node is 32 bytes. With up to 2 BVH nodes needed per primitive in the scene, the total overhead may be as high as 64 bytes per primitive. Cline et al. ([^20]) suggested a more compact representation for BVH nodes, at some expense of efficiency. First, they quantized the bounding box stored in each node using 8 or 16 bytes to encode its position with respect to the node’s parent’s bounding box. Second, they used *implicit indexing*, where the node ’s children are at positions and in the node array (assuming a branching factor). They showed substantial memory savings, with moderate performance impact. Bauszat et al. ([^6]) developed another space-efficient BVH representation. See also Segovia and Ernst ([^76]), who developed compact representations of both BVH nodes and triangle meshes. A BVH specialized for space-efficient storage of parametric surfaces was described by Selgrad et al. ([^77]) and an adoption of this approach for displaced subdivision surfaces was presented by Lier et al. ([2018a](#cite:Lier2018)).

Other work in the area of space-efficient BVHs includes that of Vaidyanathan et al. ([^83]), who introduced a reduced-precision representation of the BVH that still guarantees conservative intersection tests with respect to the original BVH. Liktor and Vaidyanathan ([^62]) introduced a BVH node representation based on clustering nodes that improves cache performance and reduces storage requirements for child node pointers. Ylitie et al. ([^97]) showed how to optimally convert binary BVHs into wider BVHs with more children at each node, from which they derived a compressed BVH representation that shows a substantial bandwidth reduction with incoherent rays. Vaidyanathan et al. ([^82]) developed an algorithm for efficiently traversing such wide BVHs using a small stack. Benthin et al. ([^8]) focused on compressing sets of adjacent leaf nodes of BVHs under the principle that most of the memory is used at the leaves, and Lin et al. ([^63]) described an approach that saves both computation and storage by taking advantage of shared planes among the bounds of the children of a BVH node.

Yoon and Manocha ([^98]) described algorithms for cache-efficient layout of BVHs and kd-trees and demonstrated performance improvements from using them. See also Ericson’s book ([^23]) for extensive discussion of this topic.

The linear BVH was introduced by Lauterbach et al. ([^60]); Morton codes were first described in a report by Morton ([^68]). Pantaleoni and Luebke ([^72]) developed the HLBVH generalization, using the SAH at the upper levels of the tree. They also noted that the upper bits of the Morton-coded values can be used to efficiently find clusters of primitives—both of these ideas are used in our HLBVH implementation. Garanzha et al. ([^31]) introduced further improvements to the HLBVH, most of them targeting GPU implementations. Vinkler et al. ([^84]) described improved techniques for mapping values to the Morton index coordinates that lead to higher-quality BVHs, especially for scenes with a range of primitive sizes.

Wald ([^88]) described an approach for high-performance parallel BVH construction on CPUs that uses the SAH throughout. More recently, Benthin et al. ([^9]) have described a two-level BVH construction technique based on building high-quality second-level BVHs for collections of objects in a scene, collecting them into a single BVH, and then iteratively refining the overall tree, including moving subtrees from one of the initial BVHs to another. Hendrich et al. ([^41]) described a related technique, quickly building an initial LBVH and then progressively building a higher-quality BVH based on it.

A comprehensive survey of work in bounding volume hierarchies, spanning construction, representation, traversal, and hardware acceleration, was recently published by Meister et al. ([^67]).

#### kd-trees

Glassner ([^32]) introduced the use of octrees for ray intersection acceleration. Use of the kd-tree for ray tracing was first described by Kaplan ([^52]). Kaplan’s tree construction algorithm always split nodes down their middle; MacDonald and Booth ([^65]) introduced the SAH approach, estimating ray–node traversal probabilities using relative surface areas. Naylor ([^70]) has also written on general issues of constructing good kd-trees. Havran and Bittner ([^38]) revisited many of these issues and introduced useful improvements. Adding a bonus factor to the SAH for tree nodes that are completely empty was suggested by Hurley et al. ([^44]). See Havran’s Ph.D. thesis ([^37]) for an excellent overview of high-performance kd-construction and traversal algorithms.

Jansen ([^49]) first developed the efficient ray-traversal algorithm for kd-trees. Arvo ([^4]) also investigated this problem and discussed it in a note in *Ray Tracing News*. Sung and Shirley ([^81]) described a ray-traversal algorithm’s implementation for a BSP-tree accelerator; our [KdTreeAggregate](https://pbr-book.org/4ed/Primitives_and_Intersection_Acceleration/Aggregates.html#KdTreeAggregate) traversal code (included in the online edition) is loosely based on theirs.

The asymptotic complexity of the kd-tree construction algorithm in pbrt is. Wald and Havran ([^89]) showed that it is possible to build kd-trees in time with some additional implementation complexity; they reported a to speedup in construction time for typical scenes.

The best kd-trees for ray tracing are built using “perfect splits,” where the primitive being inserted into the tree is clipped to the bounds of the current node at each step. This eliminates the issue that, for example, an object’s bounding box may intersect a node’s bounding box and thus be stored in it, even though the object itself does not intersect the node’s bounding box. This approach was introduced by Havran and Bittner ([^38]) and discussed further by Hurley et al. ([^44]), Wald and Havran ([^89]), and Soupikov et al. ([^79]). Even with perfect splits, large primitives may still be stored in many kd-tree leaves; Choi et al. ([^16]) suggested storing some primitives in interior nodes to address this issue.

kd-tree construction tends to be much slower than BVH construction (especially if “perfect splits” are used), so parallel construction algorithms are of particular interest. Work in this area includes that of Shevtsov et al. ([2007b](#cite:Shevtsov07b)) and Choi et al. ([^17]), who presented efficient parallel kd-tree construction algorithms with good scalability to multiple processors.

#### The Surface Area Heuristic

A number of researchers have investigated improvements to the SAH since its introduction to ray tracing by MacDonald and Booth ([^65]). Fabianowski et al. ([^25]) derived a version that replaces the assumption that rays are uniformly distributed throughout space with the assumption that ray origins are uniformly distributed inside the scene’s bounding box. Hunt and Mark ([2008b](#cite:Hunt08b)) introduced a modified SAH that accounts for the fact that rays generally are not uniformly distributed but rather that many of them originate from a single point or a set of nearby points (cameras and light sources, respectively). Hunt ([^42]) showed how the SAH should be modified when the “mailboxing” optimization is being used, and Vinkler et al. ([^85]) used assumptions about the visibility of primitives to adjust their SAH cost. Ize and Hansen ([^45]) derived a “ray termination surface area heuristic” (RTSAH), which they used to adjust BVH traversal order for shadow rays in order to more quickly find intersections with occluders. See also Moulin et al. ([^69]), who adapted the SAH to account for shadow rays being occluded during kd-tree traversal.

While the SAH has led to very effective kd-trees and BVHs, a number of researchers have noted that it is not unusual to encounter cases where a kd-tree or BVH with a higher SAH-estimated cost gives better performance than one with lower estimated cost. Aila et al. ([^1]) surveyed some of these results and proposed two additional heuristics that help address them; one accounts for the fact that most rays start on surfaces—ray origins are not actually randomly distributed throughout the scene—and another accounts for SIMD divergence when multiple rays traverse the hierarchy together. While these new heuristics are effective at explaining why a given tree delivers the performance that it does, it is not yet clear how to incorporate them into tree construction algorithms.

Evaluating the SAH can be costly, particularly when many different splits or primitive partitions are being considered. One solution to this problem is to only compute it at a subset of the candidate points—for example, along the lines of the bucketing approach used in the [BVHAggregate](https://pbr-book.org/4ed/Primitives_and_Intersection_Acceleration/Bounding_Volume_Hierarchies.html#BVHAggregate) in pbrt. Hurley et al. ([^44]) suggested this approach for building kd-trees, and Popov et al. ([^73]) discussed it in detail. Shevtsov et al. ([2007b](#cite:Shevtsov07b)) introduced the improvement of binning the full extents of triangles, not just their centroids.

Wodniok and Goesele constructed BVHs where the SAH cost estimate is not based on primitive counts and primitive bounds but is instead found by actually building BVHs for various partitions and computing their SAH cost ([Wodniok and Goesele 2016](#cite:Wodniok2016)). They showed a meaningful improvement in ray intersection performance, though at a cost of impractically long BVH construction times.

Hunt et al. ([^43]) noted that if you only have to evaluate the SAH at one point, for example, you do not need to sort the primitives but only need to do a linear scan over them to compute primitive counts and bounding boxes at the point. pbrt ’s implementation follows that approach. They also showed that approximating the SAH with a piecewise quadratic based on evaluating it at a number of individual positions, and using that to choose a good split, leads to effective trees. A similar approximation was used by Popov et al. ([^73]).

#### Other Topics in Acceleration Structures

Weghorst, Hooper, and Greenberg ([^91]) discussed the trade-offs of using various shapes for bounding volumes and suggested projecting objects to the screen and using a -buffer rendering to accelerate finding intersections for camera rays.

A number of researchers have investigated the applicability of general BSP trees, where the splitting planes are not necessarily axis aligned, as they are with kd-trees. Kammaje and Mora ([^51]) built BSP trees using a preselected set of candidate splitting planes. Budge et al. ([^14]) developed a number of improvements to their approach, though their results only approached kd-tree performance in practice due to a slower construction stage and slower traversal than kd-trees. Ize et al. ([^46]) showed a BSP implementation that renders scenes faster than kd-trees but at the cost of extremely long construction times.

There are many techniques for traversing a collection of rays through the acceleration structure together, rather than just one at a time. This approach (“packet tracing”) is an important component of many high-performance ray tracing approaches; it is discussed in more detail in Section [16.2.3](https://pbr-book.org/4ed/Retrospective_and_the_Future/Design_Alternatives.html#sec:packet-tracing).

Animated primitives present two challenges to ray tracers: first, renderers that try to reuse acceleration structures over multiple frames of an animation must update the acceleration structures if objects are moving. Lauterbach et al. ([^61]) and Wald et al. ([2007a](#cite:Wald07a)) showed how to incrementally update BVHs in this case, and Kopta et al. ([^58]) reused BVHs over multiple frames of an animation, maintaining their quality by updating the parts that bound moving objects. Garanzha ([^30]) suggested creating clusters of nearby primitives and then building BVHs of those clusters (thus lightening the load on the BVH construction algorithm).

A second challenge from animated primitives is that for primitives that are moving quickly, the bounding boxes of their full motion over the frame time may be quite large, leading to many unnecessary ray–primitive intersection tests. Notable work on this issue includes Glassner ([^33]), who generalized ray tracing (and an octree for acceleration) to four dimensions, adding time. More recently, Grünschloß et al. ([^35]) developed improvements to BVHs for moving primitives. See also Wald et al.’s ([2007b](#cite:Wald2007b)) survey paper on ray tracing animated scenes. Woop et al. ([^93]) described a generalization of BVHs that also allows nodes to split in time, with child nodes of such a split accounting for different time ranges.

An innovative approach to acceleration structures was suggested by Arvo and Kirk ([^5]), who introduced a 5D data structure that subdivided based on both 3D spatial and 2D ray directions. Another interesting approach for scenes described with triangle meshes was developed by Lagae and Dutré ([2008b](#cite:Lagae08b)): they computed a constrained tetrahedralization, where all triangle faces of the model are represented in the tetrahedralization. Rays are then stepped through tetrahedra until they intersect a triangle from the scene description. This approach is still a few times slower than the state of the art in kd-trees and BVHs but is an interesting new way to think about the problem.

There is a middle ground between kd-trees and BVHs, where the tree node holds a splitting plane for each child rather than just a single splitting plane. This refinement makes it possible to do object subdivision in a kd-tree-like acceleration structure, putting each primitive in just one subtree and allowing the subtrees to overlap, while still preserving many of the benefits of efficient kd-tree traversal. Ooi et al. ([^71]) first introduced this refinement to kd-trees for storing spatial data, naming it the “spatial kd-tree” (skd-tree). Skd-trees have been applied to ray tracing by a number of researchers, including Zachmann ([^100]), Woop et al. ([^95]), Wächter and Keller ([^86]), Havran et al. ([^39]), and Zuniga and Uhlmann ([^101]).

When spatial subdivision approaches like grids or kd-trees are used, primitives may overlap multiple nodes of the structure and a ray may be tested for intersection with the same primitive multiple times as it passes through the structure. Arnaldi, Priol, and Bouatouch ([^3]) and Amanatides and Woo ([^2]) developed the “mailboxing” technique to address this issue: each ray is given a unique integer identifier, and each primitive records the id of the last ray that was tested against it. If the ids match, then the intersection test is unnecessary and can be skipped.

While effective, mailboxing does not work well with a multi-threaded ray tracer. To address this issue, Benthin ([^7]) suggested storing a small per-ray hash table to record ids of recently intersected primitives. Shevtsov et al. ([2007a](#cite:Shevtsov07a)) maintained a small array of the last intersected primitive ids and searched it linearly before performing intersection tests. Although some primitives may still be checked multiple times with both of these approaches, they usually eliminate most redundant tests.

### References

[^1]: Aila, T., T. Karras, and S. Laine. 2013. On quality metrics of bounding volume hierarchies. In *Proceedings of High Performance Graphics 2013*, 101–7.

[^2]: Amanatides, J., and A. Woo. 1987. A fast voxel traversal algorithm for ray tracing. In *Proceedings of Eurographics ’87*, 3–10.

[^3]: Arnaldi, B., T. Priol, and K. Bouatouch. 1987. A new space subdivision method for ray tracing CSG modeled scenes. *The Visual Computer* *3*  (2), 98–108.

[^4]: Arvo, J. 1988. Linear-time voxel walking for octrees. *Ray Tracing News 1*  (5).

[^5]: Arvo, J., and D. Kirk. 1987. Fast ray tracing by ray classification. *Computer Graphics (SIGGRAPH ’87 Proceedings)* *21*  (4), 55–64.

[^6]: Bauszat, P., M. Eisemann, and M. Magnor. 2010. The minimal bounding volume hierarchy. *Vision, Modeling, and Visualization (2010)*, 227–34.

[^7]: Benthin, C. 2006. Realtime ray tracing on current CPU architectures. Ph.D. thesis, Saarland University.

[^8]: Benthin, C., I. Wald, S. Woop, and A. T. Áfra. 2018. Compressed-leaf bounding volume hierarchies. *Proceedings of High Performance Graphics (HPG ’18)*, 6:1–4.

[^9]: Benthin, C., S. Woop, I. Wald, and A. T. Áfra. 2017. Improved two-level BVHs using partial re-braiding. *Proceedings of High Performance Graphics (HPG ’17)*, 7:1–8.

[^10]: Binder, N., and A. Keller. 2016. Efficient stackless hierarchy traversal on GPUs with backtracking in constant time. *Proceedings of High Performance Graphics*, 41–50.

[^11]: Bittner, J., M. Hapala, and V. Havran. 2013. Fast insertion-based optimization of bounding volume hierarchies. *Computer Graphics Forum* *32*  (1), 85–100.

[^12]: Bittner, J., M. Hapala, and V. Havran. 2014. Incremental BVH construction for ray tracing. *Computers & Graphics* *47*, 135–44.

[^13]: Boulos, S., and E. Haines. 2006. Ray–box sorting. *Ray Tracing News* *19*  (1), [www.realtimerendering.com/resources/RTNews/html/rtnv19n1.html](http://www.realtimerendering.com/resources/RTNews/html/rtnv19n1.html)

[^14]: Budge, B., D. Coming, D. Norpchen, and K. Joy. 2008. Accelerated building and ray tracing of restricted BSP trees. *2008 IEEE Symposium on Interactive Ray Tracing*, 167–74.

[^15]: Cazals, F., G. Drettakis, and C. Puech. 1995. Filtering, clustering and hierarchy construction: A new solution for ray-tracing complex scenes. *Computer Graphics Forum* *14*  (3), 371–82.

[^16]: Choi, B., B. Chang, and I. Ihm. 2013. Improving memory space efficiency of kd-tree for real-time ray tracing. *Computer Graphics Forum* *32*  (7), 335–44.

[^17]: Choi, B., R. Komuravelli, V. Lu, H. Sung, R. L. Bocchino, S. V. Adve, and J. C. Hart. 2010. Parallel SAH k-D tree construction. In *Proceedings of High Performance Graphics 2010*, 77–86.

[^18]: Clark, J. H. 1976. Hierarchical geometric models for visible surface algorithms. *Communications of the ACM* *19*  (10), 547–54.

[^19]: Cleary, J. G., and G. Wyvill. 1988. Analysis of an algorithm for fast ray tracing using uniform space subdivision. *The Visual Computer* *4*  (2), 65–83.

[^20]: Cline, D., P. Egbert, J. Talbot, and D. Cardon. 2006. Two stage importance sampling for direct lighting. *Rendering Techniques 2006: 17th Eurographics Workshop on Rendering*, 103–14.

[^21]: Eisemann, M., M. Magnor, T. Grosch, and S. Müller. 2007. Fast ray/axis-aligned bounding box overlap tests using ray slopes. *Journal of Graphics, GPU, and Game Tools* *12*  (4), 35–46.

[^22]: Enderton, E., E. Sintorn, P. Shirley, and D. Luebke. 2010. Stochastic transparency. *Proceedings of the 2010 ACM SIGGRAPH Symposium on Interactive 3D Graphics and Games (I3D ’10)*, 157–64.

[^23]: Ericson, C. 2004. *Real-Time Collision Detection*. Morgan Kaufmann Series in Interactive 3D Technology. San Francisco: Morgan Kaufmann.

[^24]: Ernst, M., and G. Greiner. 2007. Early split clipping for bounding volume hierarchies. *IEEE Symposium on Interactive Ray Tracing*, 73–78.

[^25]: Fabianowski, B., C. Fowler, and J. Dingliana. 2009. A cost metric for scene-interior ray origins. *Short Paper Proceedings of the 30th Annual Conference of the European Association for Computer Graphics (Eurographics 2009)*, 49–50.

[^26]: Foley, T., and J. Sugerman. 2005. KD-tree acceleration structures for a GPU raytracer. *Proceedings of the ACM SIGGRAPH/EUROGRAPHICS Conference on Graphics Hardware*, 15–22.

[^27]: Fujimoto, A., T. Tanaka, and K. Iwata. 1986. Arts: Accelerated ray-tracing system. *IEEE Computer Graphics and Applications* *6*  (4), 16–26.

[^28]: Günther, J., S. Popov, H. P. Seidel, and P. Slusallek. 2007. Realtime ray tracing on GPU with BVH-based packet traversal. *IEEE Symposium on Interactive Ray Tracing*, 113–18.

[^29]: Ganestam, P., and M. Doggett. 2016. SAH guided spatial split partitioning for fast BVH construction. *Computer Graphics Forum* *35*  (2), 285–93.

[^30]: Garanzha, K. 2009. The use of precomputed triangle clusters for accelerated ray tracing in dynamic scenes. *Computer Graphics Forum (Proceedings of the 2009 Eurographics Symposium on Rendering)* *28*  (4), 1199–206.

[^31]: Garanzha, K., J. Pantaleoni, D. McAllister. 2011. Simpler and faster HLBVH with work queues. *Proceedings of High Performance Graphics 2011*, 59–64.

[^32]: Glassner, A. 1984. Space subdivision for fast ray tracing. *IEEE Computer Graphics and Applications* *4*  (10), 15–22.

[^33]: Glassner, A. 1988. Spacetime ray tracing for animation. *IEEE Computer Graphics & Applications* *8*  (2), 60–70.

[^34]: Goldsmith, J., and J. Salmon. 1987. Automatic creation of object hierarchies for ray tracing. *IEEE Computer Graphics and Applications* *7*  (5), 14–20.

[^35]: Grünschloß, L., M. Stich, S. Nawaz, and A. Keller. 2011. MSBVH: An efficient acceleration data structure for ray traced motion blur. *Proceedings of High Performance Graphics 2011*, 65–70.

[^36]: Gu, Y., Y. He, and G. E. Blelloch. 2015. Ray specialized contraction on bounding volume hierarchies. *Computer Graphics Forum* *34*  (7), 309–18.

[^37]: Havran, V. 2000. Heuristic ray shooting algorithms. Ph.D. thesis, Czech Technical University.

[^38]: Havran, V., and J. Bittner. 2002. On improving kd-trees for ray shooting. In *Proceedings of WSCG 2002 Conference*, 209–17.

[^39]: Havran, V., R. Herzog, and H.-P. Seidel. 2006. On the fast construction of spatial hierarchies for ray tracing. In *IEEE Symposium on Interactive Ray Tracing*, 71–80.

[^40]: Hendrich, J., A. Pospíšil, D. Meister, and J. Bittner. 2019. Ray classification for accelerated BVH traversal. *Computer Graphics Forum* *38*  (4), 49–56.

[^41]: Hendrich, J., D. Meister, and J. Bittner. 2017. Parallel BVH construction using progressive hierarchical refinement. *Computer Graphics Forum* *36*  (2), 487–94.

[^42]: Hunt, W. 2008. Corrections to the surface area metric with respect to mail-boxing. In *IEEE Symposium on Interactive Ray Tracing*, 77–80.

[^43]: Hunt, W., W. Mark, and G. Stoll. 2006. Fast kd-tree construction with an adaptive error-bounded heuristic. In *IEEE Symposium on Interactive Ray Tracing*, 81–88.

[^44]: Hurley, J., A. Kapustin, A. Reshetov, and A. Soupikov. 2002. Fast ray tracing for modern general purpose CPU. In *Proceedings of GraphiCon 2002*

[^45]: Ize, T., and C. Hansen. 2011. RTSAH traversal order for occlusion rays. *Computer Graphics Forum (Proceedings of Eurographics 2011)* *30*  (2), 295–305.

[^46]: Ize, T., I. Wald, and S. Parker. 2008. Ray tracing with the BSP tree. In *IEEE Symposium on Interactive Ray Tracing*, 159–66.

[^47]: Ize, T., I. Wald, C. Robertson, and S. G. Parker. 2006. An evaluation of parallel grid construction for ray tracing dynamic scenes. *IEEE Symposium on Interactive Ray Tracing*, 47–55.

[^48]: Ize, T., P. Shirley, and S. Parker. 2007. Grid creation strategies for efficient ray tracing. In *IEEE Symposium on Interactive Ray Tracing*, 27–32.

[^49]: Jansen, F. W. 1986. Data structures for ray tracing. In *Data Structures for Raster Graphics, Workshop Proceedings*, 57–73. New York: Springer-Verlag.

[^50]: Jevans, D., and B. Wyvill. 1989. Adaptive voxel subdivision for ray tracing. In *Proceedings of Graphics Interface 1989*, 164–72.

[^51]: Kammaje, R., and B. Mora. 2007. A study of restricted BSP trees for ray tracing. In *IEEE Symposium on Interactive Ray Tracing*, 55–62.

[^52]: Kaplan, M. R. 1985. The uses of spatial coherence in ray tracing. In *ACM SIGGRAPH Course Notes 11*

[^53]: Karras, T., and T. Aila. 2013. Fast parallel construction of high-quality bounding volume hierarchies. In *Proceedings of High Performance Graphics 2013*, 89–99.

[^54]: Kay, T., and J. Kajiya. 1986. Ray tracing complex scenes. In *Computer Graphics (SIGGRAPH ’86 Proceedings)*, Volume 20, 269–78.

[^55]: Kensler, A. 2008. Tree rotations for improving bounding volume hierarchies. In *IEEE Symposium on Interactive Ray Tracing*, 73–76.

[^56]: Kirk, D., and J. Arvo. 1988. The ray tracing kernel. In *Proceedings of Ausgraph ’88*, 75–82.

[^57]: Klimaszewski, K. S., and T. W. Sederberg. 1997. Faster ray tracing using adaptive grids. *IEEE Computer Graphics and Applications* *17*  (1), 42–51.

[^58]: Kopta, D., T. Ize, J. Spjut, E. Brunvand, A. Davis, and A. Kensler. 2012. Fast, effective BVH updates for animated scenes. In *Proceedings of the ACM SIGGRAPH Symposium on Interactive 3D Graphics and Games*, 197–204.

[^59]: Laine, S. 2010. Restart trail for stackless BVH traversal. In *Proceedings of High Performance Graphics 2010*, 107–11.

[^60]: Lauterbach, C., M. Garland, S. Sengupta, D. Luebke, and D. Manocha. 2009. Fast BVH construction on GPUs. *Computer Graphics Forum (Eurographics 2009 Conference Proceedings)* *28*  (2), 422–30.

[^61]: Lauterbach, C., S.-E. Yoon, D. Tuft, and D. Manocha. 2006. RT-DEFORM: Interactive ray tracing of dynamic scenes using BVHs. *IEEE Symposium on Interactive Ray Tracing*, 39–46.

[^62]: Liktor, G., and K. Vaidyanathan. 2016. Bandwidth-efficient BVH layout for incremental hardware traversal. *Proceedings of High Performance Graphics (HPG ’16)*, 51–61.

[^63]: Lin, D., K. Shkurko, I. Mallett, and C. Yuksel. 2019. Dual-split trees. *Symposium on Interactive 3D Graphics and Games (I3D 2019)*, 3:1–9.

[^64]: Müller, G., and D. W. Fellner. 1999. Hybrid scene structuring with application to ray tracing. *Proceedings of the International Conference on Visual Computing (ICVC ’99)*, 19–26.

[^65]: MacDonald, J. D., and K. S. Booth. 1990. Heuristics for ray tracing using space subdivision. *The Visual Computer* *6*  (3), 153–66.

[^66]: Massó, J. P. M., and P. G. López. 2003 Automatic hybrid hierarchy creation: A cost-model based approach. *Computer Graphics Forum* *22*  (1), 5–13.

[^67]: Meister, D., S. Ogaki, C. Benthin, M. J. Doyle, M. Guthe, and J. Bittner. 2021. A survey on bounding volume hierarchies for ray tracing. *Computer Graphics Forum (Eurographics State of the Art Report)* *40*  (2): 683–712.

[^68]: Morton, G. M. 1966. A computer oriented geodetic data base and a new technique in file sequencing. *IBM Technical Report*

[^69]: Moulin, M., N. Billen, and P. Dutré. 2015. Efficient visibility heuristics for kd-trees using the RTSAH. *Eurographics Symposium on Rendering–Experimental Ideas & Implementations*, 31–39.

[^70]: Naylor, B. 1993. Constructing good partition trees. In *Proceedings of Graphics Interface 1993*, 181–91.

[^71]: Ooi, B. C., K. McDonell, and R. Sacks-Davis. 1987. Spatial kd-tree: A data structure for geographic databases. In *Proceedings of the IEEE COMPSAC Conference*

[^72]: Pantaleoni, J., and D. Luebke. 2010. HLBVH: Hierarchical LBVH construction for real-time ray tracing of dynamic geometry. In *Proceedings of the Conference on High Performance Graphics 2010*, 87–95.

[^73]: Popov, S., J. Gunther, H. P. Seidel, and P. Slusallek. 2006. Experiences with streaming construction of SAH kd-trees. In *IEEE Symposium on Interactive Ray Tracing*, 89–94.

[^74]: Popov, S., R. Dimov, I. Georgiev, and P. Slusallek. 2009. Object partitioning considered harmful: Space subdivision for BVHs. In *Proceedings of High Performance Graphics 2009*, 15–22.

[^75]: Rubin, S. M., and T. Whitted. 1980. A 3-dimensional representation for fast rendering of complex scenes. *Computer Graphics* *14*  (3), 110–16.

[^76]: Segovia, B., and M. Ernst. 2010. Memory efficient ray tracing with hierarchical mesh quantization. In *Proceedings of Graphics Interface 2010*, 153–60.

[^77]: Selgrad, K., A. Lier, M. Martinek, C. Buchenau, M. Guthe, F. Kranz, H. Schäfer, and M. Stamminger. 2017. A compressed representation for ray tracing parametric surfaces. *ACM Transactions on Graphics* *36*  (1), 5:1–13.

[^78]: Snyder, J. M., and A. H. Barr. 1987. Ray tracing complex models containing surface tessellations. *Computer Graphics (SIGGRAPH ’87 Proceedings)*, Volume 21, 119–28.

[^79]: Soupikov, A., M. Shevtsov, and A. Kapustin. 2008. Improving kd-tree quality at a reasonable construction cost. In *IEEE Symposium on Interactive Ray Tracing*, 67–72.

[^80]: Stich, M., H. Friedrich, and A. Dietrich. 2009. Spatial splits in bounding volume hierarchies. In *Proceedings of High Performance Graphics 2009*, 7–14.

[^81]: Sung, K., and P. Shirley. 1992. Ray tracing with the BSP tree. In D. Kirk (ed.), *Graphics Gems III*, 271–74. San Diego: Academic Press.

[^82]: Vaidyanathan, K., C. Benthin, and S. Woop. 2019. Wide BVH traversal with a short stack. *High Performance Graphics (HPG ’19)*, 15–19.

[^83]: Vaidyanathan, K., T. Akenine-Möller, and M. Salvi. 2016. Watertight ray traversal with reduced precision. *High Performance Graphics (HPG ’16)*, 33–40.

[^84]: Vinkler, M., J. Bittner, and V. Havran. 2017. Extended Morton codes for high-performance bounding volume hierarchy construction. *High Performance Graphics (HPG ’17)*, 9:1–8.

[^85]: Vinkler, M., V. Havran, and J. Sochora. 2012. Visibility driven BVH build up algorithm for ray tracing. *Computers & Graphics* *36*  (4), 283–96.

[^86]: Wächter, C. A., and A. Keller. 2006. Instant ray tracing: The bounding interval hierarchy. In *Rendering Techniques 2006: 17th Eurographics Workshop on Rendering*, 139–49.

[^87]: Wald, I. 2007. On fast construction of SAH-based bounding volume hierarchies. In *IEEE Symposium on Interactive Ray Tracing*, 33–40.

[^88]: Wald, I. 2012. Fast construction of SAH BVHs on the Intel Many Integrated Core (MIC) architecture. *IEEE Transactions on Visualization and Computer Graphics* *18*  (1), 47–57.

[^89]: Wald, I., and V. Havran. 2006. On building fast kd-trees for ray tracing and on doing that in. In *IEEE Symposium on Interactive Ray Tracing*, 61–69.

[^90]: Walter, B., K. Bala, M. Kilkarni, and K. Pingali. 2008. Fast agglomerative clustering for rendering. In *IEEE Symposium on Interactive Ray Tracing*, 81–86.

[^91]: Weghorst, H., G. Hooper, and D. P. Greenberg. 1984. Improved computational methods for ray tracing. *ACM Transactions on Graphics* *3*  (1), 52–69.

[^92]: Williams, A., S. Barrus, R. K. Morley, and P. Shirley. 2005. An efficient and robust ray–box intersection algorithm. *Journal of Graphics, GPU, and Game Tools* *10*  (4), 49–54.

[^93]: Woop, S., A. T. Áfra, and C. Benthin. 2017. STBVH: A spatial-temporal BVH for efficient multi-segment motion blur. *Proceedings of High Performance Graphics (HPG ’17)*, 8:1–8.

[^94]: Woop, S., C. Benthin, I. Wald, G. S. Johnson, and E. Tabellion. 2014. Exploiting local orientation similarity for efficient ray traversal of hair and fur. In *Proceedings of High Performance Graphics 2014*, 41–49.

[^95]: Woop, S., G. Marmitt, and P. Slusallek. 2006. B-kd trees for hardware accelerated ray tracing of dynamic scenes. In *Graphics Hardware 2006: Eurographics Symposium Proceedings*, 67–76.

[^96]: Wyman, C., and M. McGuire. 2017. Hashed alpha testing. *Proceedings of the 21st ACM SIGGRAPH Symposium on Interactive 3D Graphics and Games (I3D ’17)*

[^97]: Ylitie, H., T. Karras, and S. Laine. 2017. Efficient incoherent ray traversal on GPUs through compressed wide BVHs. *High Performance Graphics (HPG ’17)*, 4:1–13.

[^98]: Yoon, S.-E., and D. Manocha. 2006. Cache-efficient layouts of bounding volume hierarchies. In *Computer Graphics Forum: Proceedings of Eurographics 2006* *25*  (3), 507–16.

[^99]: Yoon, S.-E., S. Curtis, and D. Manocha. 2007. Ray tracing dynamic scenes using selective restructuring. In *Proceedings of the Eurographics Symposium on Rendering*, 73–84.

[^100]: Zachmann, G. 2002. Minimal hierarchical collision detection. In *Proceedings of the ACM Symposium on Virtual Reality Software and Technology*, 121–28.

[^101]: Zuniga, M., and J. Uhlmann. 2006. Ray queries with wide object isolation and the S-tree. *Journal of Graphics, GPU, and Game Tools* *11*  (3), 27–45.