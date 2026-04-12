---
title: "Exercises"
source: "https://pbr-book.org/4ed/Reflection_Models/Exercises"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
1. ① A consequence of Fermat's principle from optics is that light traveling from a point in a medium with index of refraction to a point in a medium with index of refraction will follow a path that minimizes the time to get from the first point to the second point. Snell's law can be shown to follow directly from this fact.

> ① 光学中费马原理的一个推论是，光从折射率为 η₁ 的介质中的一点传播到折射率为 η₂ 的介质中的一点时，会沿着使两点间传播时间最短的路径行进。可以证明斯涅尔定律直接由此推导而来。

	[![](https://pbr-book.org/4ed/Reflection_Models/pha09f56.svg)](https://pbr-book.org/4ed/Reflection_Models/pha09f56.svg)
	Figure 9.56: Derivation of Snell's Law. Snell's law can be derived using Fermat's principle, which says that light will follow the path that takes the least amount of time to pass between two points. The angle of refraction at the boundary between two media can thus be shown to be the one that minimizes the time spent going from to a point on the boundary plus the time spent traveling the distance from that point to.

> 图 9.56：斯涅尔定律的推导。斯涅尔定律可以使用费马原理推导，该原理指出光将沿着两点间传播时间最短的路径行进。因此可以证明两种介质边界处的折射角是使从起点到边界上某点的时间加上从该点到终点的时间最小化的角度。

	Consider light traveling between two points and separated by a planar boundary. The light could potentially pass through the boundary while traveling from to at any point on the boundary (see Figure [9.56](#fig:snell-fermat-derive), which shows two such possible points and ). Recall that the time it takes light to travel between two points in a medium with a constant index of refraction is proportional to the distance between them times the index of refraction in the medium. Using this fact, show that the point on the boundary that minimizes the total time to travel from to is the point where.

> 考虑在由平面边界分隔的两点之间传播的光。光可以在从一点到另一点的过程中在边界上的任意点穿过边界（参见图 9.56，显示了两个这样的可能点）。回忆在具有恒定折射率的介质中，光在两点间传播所需的时间与它们之间的距离乘以介质中的折射率成正比。利用这一事实，证明使从起点到终点总传播时间最小化的边界上的点就是满足斯涅尔定律的点。

2. ② Read the recent paper by d'Eon ([2021](https://pbr-book.org/4ed/Reflection_Models/Further_Reading.html#cite:dEon2021)) that describes a BRDF based on a model of the aggregate scattering of large collections of spherical particles that are themselves Lambertian. Implement this approach as a new [BxDF](https://pbr-book.org/4ed/Reflection_Models/BSDF_Representation.html#BxDF) in pbrt and render images comparing its visual appearance to that of the [DiffuseBxDF](https://pbr-book.org/4ed/Reflection_Models/Diffuse_Reflection.html#DiffuseBxDF).

> ② 阅读 d'Eon（2021）的最新论文，该论文描述了一种基于大量朗伯球形粒子集合散射模型的 BRDF。在 pbrt 中将此方法实现为新的 BxDF，并渲染图像比较其视觉外观与 DiffuseBxDF 的差异。

3. ③ Read the paper of Wolff and Kurlander ([1990](https://pbr-book.org/4ed/Reflection_Models/Further_Reading.html#cite:Wolff90)) and the course notes of Wilkie and Weidlich ([2012](https://pbr-book.org/4ed/Reflection_Models/Further_Reading.html#cite:Wilkie2012)) and apply some of the techniques described to modify pbrt to model the effect of light polarization. (A more in-depth review of the principles of polarization is provided by Collett ([1993](https://pbr-book.org/4ed/Reflection_Models/Further_Reading.html#cite:Collett93)).) Set up scenes and render images of them that demonstrate a significant difference when polarization is accurately modeled. For this, you will need to implement a polarized version of the Fresnel equations and add BSDFs that model optical elements like linear polarizers and retarders.

> ③ 阅读 Wolff 和 Kurlander（1990）的论文以及 Wilkie 和 Weidlich（2012）的课程笔记，并应用其中描述的一些技术来修改 pbrt 以模拟光偏振效应。（Collett（1993）提供了偏振原理的更深入综述。）设置场景并渲染图像，展示精确建模偏振时的显著差异。为此，你需要实现菲涅尔方程的偏振版本，并添加模拟线性偏振器和延迟片等光学元件的 BSDF。

4. ③ Construct a scene with an actual geometric model of a rough plane with a large number of mirrored microfacets, and illuminate it with an area light source. Place the camera in the scene such that a very large number of microfacets are in each pixel's area, and render images of this scene using hundreds or thousands of pixel samples. Compare the result to using a flat surface with a microfacet-based BRDF model. How well can you get the two approaches to match if you try to tune the microfacet BRDF parameters? Can you construct examples where images rendered with the true microfacets are actually visibly more realistic due to better modeling the effects of masking, self-shadowing, and interreflection between microfacets?

> ③ 构建一个场景，其中包含一个具有大量镜面微面元的粗糙平面的实际几何模型，并用面光源照明。将相机放置在场景中使得每个像素区域内有大量微面元，并使用数百或数千个像素样本渲染此场景的图像。将结果与使用基于微面元 BRDF 模型的平坦表面进行比较。如果尝试调整微面元 BRDF 参数，两种方法能匹配到什么程度？你能否构建使用真实微面元渲染的图像实际上更逼真的示例，因为更好地模拟了遮蔽、自阴影和微面元间互反射的效果？

5. ③ One shortcoming of the microfacet-based BSDFs in this chapter is that they do not account for multiple scattering among microfacets. Investigate previous work in this area, including the stochastic multiple scattering model of Heitz et al. ([2016b](https://pbr-book.org/4ed/Reflection_Models/Further_Reading.html#cite:Heitz2016:smith-multi)) and the analytic models of Lee et al. ([2018](https://pbr-book.org/4ed/Reflection_Models/Further_Reading.html#cite:Lee2018)) and Xie and Hanrahan ([2018](https://pbr-book.org/4ed/Reflection_Models/Further_Reading.html#cite:Xie2018)), and implement one of these approaches in pbrt. Then implement an approximate model for multiple scattering, such as the one presented by Kulla and Conty Estevez ([2017](https://pbr-book.org/4ed/Reflection_Models/Further_Reading.html#cite:Kulla2017)) or by Turquin ([2019](https://pbr-book.org/4ed/Reflection_Models/Further_Reading.html#cite:Turquin2019)). How do rendered images differ from pbrt 's current implementation? How closely do the approximate approaches match the more comprehensive ones? How does execution time compare?

> ③ 本章中基于微面元的 BSDF 的一个缺点是它们没有考虑微面元之间的多次散射。调查该领域的先前工作，包括 Heitz 等人（2016b）的随机多次散射模型以及 Lee 等人（2018）和 Xie 和 Hanrahan（2018）的解析模型，并在 pbrt 中实现其中一种方法。然后实现一个近似的多次散射模型，如 Kulla 和 Conty Estevez（2017）或 Turquin（2019）提出的模型。渲染图像与 pbrt 当前实现有何不同？近似方法与更全面的方法匹配程度如何？执行时间相比如何？

6. ③ Review the algorithms for efficiently finding an approximation of a material's normal distribution function and using that to measure BRDFs that are outlined in Section [9.8](https://pbr-book.org/4ed/Reflection_Models/Measured_BSDFs.html#sec:measured-bsdfs) and explained in more detail in Dupuy and Jakob ([2018](https://pbr-book.org/4ed/Reflection_Models/Further_Reading.html#cite:Dupuy2018)). Follow this approach to implement a *virtual gonioreflectometer*, where you provide pbrt with a description of the microgeometry of a complex surface (cloth, velvet, etc.) and its low-level reflection properties and then perform virtual measurements of the BSDF by simulating light paths in the microgeometry. Store the results of this simulation in the file format used by the [MeasuredBxDFData](https://pbr-book.org/4ed/Reflection_Models/Measured_BSDFs.html#MeasuredBxDFData) and then render images that compare using the tabularized representation to directly rendering the microgeometry. How do the images compare? How much more computationally efficient is using the [MeasuredBxDFData](https://pbr-book.org/4ed/Reflection_Models/Measured_BSDFs.html#MeasuredBxDFData)?

> ③ 回顾第 9.8 节概述并在 Dupuy 和 Jakob（2018）中详细解释的高效寻找材料法线分布函数近似并用其测量 BRDF 的算法。按照此方法实现一个*虚拟测角反射仪*，向 pbrt 提供复杂表面（布料、天鹅绒等）的微几何描述及其低级反射属性，然后通过在微几何中模拟光路来进行 BSDF 的虚拟测量。将模拟结果存储在 MeasuredBxDFData 使用的文件格式中，然后渲染图像，比较使用表格化表示与直接渲染微几何的效果。图像相比如何？使用 MeasuredBxDFData 在计算效率上高多少？

7. ② Marschner et al. ([2003](https://pbr-book.org/4ed/Reflection_Models/Further_Reading.html#cite:Marschner03)) note that human hair actually has an elliptical cross section that causes glints in human hair due to caustics; subsequent work by Khungurn and Marschner ([2017](https://pbr-book.org/4ed/Reflection_Models/Further_Reading.html#cite:Khungurn2017)) proposes a model that accounts for this effect and shows that it matches measurements of scattering from human hair well. Extend the [HairBxDF](https://pbr-book.org/4ed/Reflection_Models/Scattering_from_Hair.html#HairBxDF) implementation here, following their approach. One issue that you will need to address is that the returned by [Curve::Intersect()](https://pbr-book.org/4ed/Shapes/Curves.html#Curve::Intersect) is always perpendicular to the incident ray, which leads to different orientations of the azimuthal coordinate system. This is not an issue for the model we have implemented, since it operates only on the difference between angles in the hair coordinate system. For elliptical hairs, a consistent azimuthal coordinate system is necessary.

> ② Marschner 等人（2003）指出人类头发实际上具有椭圆形横截面，由于焦散而在人类头发中产生闪光；Khungurn 和 Marschner（2017）的后续工作提出了一个考虑此效应的模型，并表明它与人类头发散射的测量结果吻合良好。按照他们的方法扩展这里的 HairBxDF 实现。你需要解决的一个问题是 Curve::Intersect() 返回的法线始终垂直于入射光线，这导致方位角坐标系的不同取向。对于我们已实现的模型这不是问题，因为它仅在头发坐标系中的角度差上操作。对于椭圆形头发，需要一致的方位角坐标系。

8. ③ Read Yan et al.'s paper on fur scattering ([2015](https://pbr-book.org/4ed/Reflection_Models/Further_Reading.html#cite:Yan2015:fur)) and implement their model, which accounts for scattering in the medulla in fur. Render images that show the difference from accounting for this in comparison to the current implementation. You may want to also see Section 4.3 of Chiang et al. ([2016a](https://pbr-book.org/4ed/Reflection_Models/Further_Reading.html#cite:Chiang2016)), which discusses extensions for modeling the undercoat (which is shorter and curlier hair underneath the top level) and a more *ad hoc* approach to account for the influence of scattering from the medulla.

> ③ 阅读 Yan 等人关于皮毛散射的论文（2015）并实现他们的模型，该模型考虑了皮毛中髓质的散射。渲染图像以显示与当前实现相比考虑此效应的差异。你可能还想参阅 Chiang 等人（2016a）的第 4.3 节，其中讨论了模拟底毛（顶层下方较短且更卷曲的毛发）的扩展以及更加*临时性*的方法来考虑髓质散射的影响。

9. ③ Read one or more papers from the "Further Reading" section of this chapter on efficiently rendering glints, which are evident when the surface microstructure is large enough or viewed closely enough that the assumption of a continuous distribution of microfacet orientations is no longer valid. Then, choose one such approach and implement it in pbrt. Render images that show off the effects it is capable of producing.

> ③ 阅读本章"延伸阅读"部分中关于高效渲染闪光的一篇或多篇论文，当表面微结构足够大或观察足够近时，微面元取向连续分布的假设不再有效，此时闪光就会显现。然后选择一种这样的方法并在 pbrt 中实现。渲染图像以展示其能产生的效果。