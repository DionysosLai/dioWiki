---
title: Preface
source: https://pbr-book.org/4ed/Preface
author:
published:
created: 2026-04-10
description:
tags:
---
*\[Just as\] other information should be available to those who want to learn and understand, program source code is the only means for programmers to learn the art from their predecessors. It would be unthinkable for playwrights not to allow other playwrights to read their plays \[or to allow them\] at theater performances where they would be barred even from taking notes. Likewise, any good author is well read, as every child who learns to write will read hundreds of times more than it writes. Programmers, however, are expected to invent the alphabet and learn to write long novels all on their own. Programming cannot grow and learn unless the next generation of programmers has access to the knowledge and information gathered by other programmers before them.* —Erik Naggum

> *正如其他信息应当对那些想要学习和理解的人开放一样，程序源代码是程序员向前辈学习这门技艺的唯一途径。如果剧作家不允许其他剧作家阅读他们的剧本，或者在剧场演出时禁止他们做笔记，那将是不可想象的。同样，任何优秀的作家都博览群书，每个学习写作的孩子阅读的量都远超其写作的量。然而，人们却期望程序员自己发明字母表，并独自学会写长篇小说。除非下一代程序员能够获取前辈们积累的知识和信息，否则编程无法成长和进步。* ——Erik Naggum

Rendering is a fundamental component of computer graphics. At the highest level of abstraction, rendering is the process of converting a description of a three-dimensional scene into an image. Algorithms for animation, geometric modeling, texturing, and other areas of computer graphics all must pass their results through some sort of rendering process so that they can be made visible in an image. Rendering has become ubiquitous; from movies to games and beyond, it has opened new frontiers for creative expression, entertainment, and visualization.

> 渲染是计算机图形学的基础组成部分。在最高的抽象层次上，渲染是将三维场景的描述转换为图像的过程。动画、几何建模、纹理映射以及计算机图形学其他领域的算法都必须将其结果通过某种渲染过程才能在图像中可见。渲染已经无处不在——从电影到游戏乃至更广泛的领域，它为创意表达、娱乐和可视化开辟了新的前沿。

In the early years of the field, research in rendering focused on solving fundamental problems such as determining which objects are visible from a given viewpoint. As effective solutions to these problems have been found and as richer and more realistic scene descriptions have become available thanks to continued progress in other areas of graphics, modern rendering has grown to include ideas from a broad range of disciplines, including physics and astrophysics, astronomy, biology, psychology and the study of perception, and pure and applied mathematics. The interdisciplinary nature of rendering is one of the reasons that it is such a fascinating area of study.

> 在该领域的早期，渲染研究集中在解决一些基本问题上，比如确定从给定视点可以看到哪些物体。随着这些问题的有效解决方案被找到，以及得益于图形学其他领域的持续进步而产生了更丰富、更逼真的场景描述，现代渲染已经发展到涵盖来自众多学科的思想，包括物理学和天体物理学、天文学、生物学、心理学和感知研究，以及纯数学和应用数学。渲染的跨学科性质是它成为如此迷人的研究领域的原因之一。

This book presents a selection of modern rendering algorithms through the documented source code for a complete rendering system. Nearly all of the images in this book, including the one on the front cover, were rendered by this software. All of the algorithms that came together to generate these images are described in these pages. The system, pbrt, is written using a programming methodology called *literate programming* that mixes prose describing the system with the source code that implements it. We believe that the literate programming approach is a valuable way to introduce ideas in computer graphics and computer science in general. Often, some of the subtleties of an algorithm can be unclear or hidden until it is implemented, so seeing an actual implementation is a good way to acquire a solid understanding of that algorithm's details. Indeed, we believe that deep understanding of a number of carefully selected algorithms in this manner provides a better foundation for further study of computer graphics than does superficial understanding of many.

> 本书通过一个完整渲染系统的文档化源代码来呈现一系列现代渲染算法。本书中几乎所有的图像，包括封面上的图像，都是由该软件渲染生成的。生成这些图像所用到的全部算法都在本书中有所描述。该系统名为 pbrt，采用一种称为*文学编程*（literate programming）的编程方法论编写，将描述系统的散文与实现系统的源代码交织在一起。我们相信文学编程方法是在计算机图形学乃至整个计算机科学领域引入思想的一种有价值的方式。通常，算法的一些微妙之处在实现之前可能是不清楚或隐藏的，因此看到实际的实现是深入理解算法细节的好方法。事实上，我们认为以这种方式深入理解一些精心挑选的算法，比肤浅地了解许多算法能为进一步学习计算机图形学打下更好的基础。

In addition to clarifying how an algorithm is implemented in practice, presenting these algorithms in the context of a complete and nontrivial software system also allows us to address issues in the design and implementation of medium-sized rendering systems. The design of a rendering system's basic abstractions and interfaces has substantial implications for both the elegance of the implementation and the ability to extend it later, yet the trade-offs in this design space are rarely discussed.

> 除了阐明算法在实践中如何实现之外，在一个完整且非平凡的软件系统的背景下呈现这些算法，也使我们能够讨论中型渲染系统设计和实现中的问题。渲染系统基本抽象和接口的设计对实现的优雅性和后续扩展能力都有重大影响，然而这个设计空间中的权衡却很少被讨论。

pbrt and the contents of this book focus exclusively on *photorealistic rendering*, which can be defined variously as the task of generating images that are indistinguishable from those that a camera would capture in a photograph or as the task of generating images that evoke the same response from a human observer as looking at the actual scene. There are many reasons to focus on photorealism. Photorealistic images are crucial for special effects in movies because computer-generated imagery must often be mixed seamlessly with footage of the real world. In applications like computer games where all of the imagery is synthetic, photorealism is an effective tool for making the observer forget that he or she is looking at an environment that does not actually exist. Finally, photorealism gives a reasonably well-defined metric for evaluating the quality of the rendering system's output.

> pbrt 和本书的内容专注于*照片级真实感渲染*（photorealistic rendering），它可以被不同地定义为：生成与相机拍摄的照片无法区分的图像的任务，或者生成能使人类观察者产生与观看实际场景相同反应的图像的任务。聚焦于照片级真实感有许多原因。照片级真实感图像对于电影特效至关重要，因为计算机生成的图像必须常常与真实世界的影像无缝融合。在像电脑游戏这样所有图像都是合成的应用中，照片级真实感是让观察者忘记自己正在看一个实际上并不存在的环境的有效工具。最后，照片级真实感提供了一个定义相当明确的度量标准来评估渲染系统输出的质量。

### Audience

> ### 目标读者

There are three main audiences that this book is intended for. The first is students in graduate or upper-level undergraduate computer graphics classes. This book assumes existing knowledge of computer graphics at the level of an introductory college-level course, although certain key concepts such as basic vector geometry and transformations will be reviewed here. For students who do not have experience with programs that have tens of thousands of lines of source code, the literate programming style gives a gentle introduction to this complexity. We pay special attention to explaining the reasoning behind some of the key interfaces and abstractions in the system in order to give these readers a sense of why the system is structured in the way that it is.

> 本书面向三类主要读者。第一类是研究生或高年级本科生的计算机图形学课程学生。本书假设读者已具备大学入门课程水平的计算机图形学知识，但某些关键概念如基本向量几何和变换将在此处回顾。对于没有接触过数万行源代码规模程序的学生，文学编程风格为这种复杂性提供了温和的入门。我们特别注意解释系统中一些关键接口和抽象背后的推理，以便让这些读者理解系统为何采用这样的结构。

The second audience is advanced graduate students and researchers in computer graphics. For those doing research in rendering, the book provides a broad introduction to the area, and the pbrt source code provides a foundation that can be useful to build upon (or at least to use bits of source code from). For those working in other areas of computer graphics, we believe that having a thorough understanding of rendering can be helpful context to carry along.

> 第二类读者是计算机图形学方向的高级研究生和研究人员。对于从事渲染研究的人来说，本书提供了该领域的广泛介绍，而 pbrt 源代码提供了可以在其上构建的基础（或者至少可以从中使用部分源代码）。对于在计算机图形学其他领域工作的人，我们相信对渲染有透彻的理解可以成为有益的背景知识。

Our final audience is software developers in industry. Although many of the basic ideas in this book will be familiar to this audience, seeing explanations of the algorithms presented in the literate style may lead to new perspectives. pbrt also includes carefully crafted and debugged implementations of many algorithms that can be challenging to implement correctly; these should be of particular interest to experienced practitioners in rendering. We hope that delving into one particular organization of a complete and nontrivial rendering system will also be thought provoking to this audience.

> 我们最后一类读者是业界的软件开发者。虽然本书中的许多基本思想对这类读者来说并不陌生，但以文学编程风格呈现的算法解释可能会带来新的视角。pbrt 还包含了许多精心编写和调试过的算法实现，这些算法要正确实现具有相当的挑战性；这些对于渲染领域的资深从业者应该特别有价值。我们希望深入研究一个完整且非平凡的渲染系统的特定组织方式，也能引发这类读者的思考。

### Overview and Goals

> ### 概述与目标

pbrt is based on the *ray-tracing* algorithm. Ray tracing is an elegant technique that has its origins in lens making; Carl Friedrich Gauß traced rays through lenses by hand in the 19th century. Ray-tracing algorithms on computers follow the path of infinitesimal rays of light through the scene until they intersect a surface. This approach gives a simple method for finding the first visible object as seen from any particular position and direction and is the basis for many rendering algorithms.

> pbrt 基于*光线追踪*（ray-tracing）算法。光线追踪是一种优雅的技术，起源于透镜制造；卡尔·弗里德里希·高斯在 19 世纪曾手工追踪光线穿过透镜。计算机上的光线追踪算法沿着无穷细的光线在场景中传播的路径，直到它们与某个表面相交。这种方法提供了一种简单的方式来找到从任何特定位置和方向看到的第一个可见物体，是许多渲染算法的基础。

pbrt was designed and implemented with three main goals in mind: it should be *complete*, it should be *illustrative*, and it should be *physically based*.

> pbrt 的设计和实现围绕三个主要目标：它应该是*完整的*（complete）、*有教学性的*（illustrative），以及*基于物理的*（physically based）。

Completeness implies that the system should not lack key features found in high-quality commercial rendering systems. In particular, it means that important practical issues, such as antialiasing, robustness, numerical precision, and the ability to efficiently render complex scenes should all be addressed thoroughly. It is important to consider these issues from the start of the system's design, since these features can have subtle implications for all components of the system and can be quite difficult to retrofit into the system at a later stage of implementation.

> 完整性意味着系统不应缺少高质量商业渲染系统中的关键功能。具体来说，这意味着重要的实际问题，如抗锯齿、鲁棒性、数值精度以及高效渲染复杂场景的能力，都应该被彻底解决。从系统设计之初就考虑这些问题非常重要，因为这些特性可能对系统的所有组件产生微妙的影响，而且在实现的后期阶段将它们改装进系统会非常困难。

Our second goal means that we tried to choose algorithms, data structures, and rendering techniques with care and with an eye toward readability and clarity. Since their implementations will be examined by more readers than is the case for other rendering systems, we tried to select the most elegant algorithms that we were aware of and implement them as well as possible. This goal also required that the system be small enough for a single person to understand completely. We have implemented pbrt using an extensible architecture, with the core of the system implemented in terms of a set of carefully designed interface classes, and as much of the specific functionality as possible in implementations of these interfaces. The result is that one does not need to understand all of the specific implementations in order to understand the basic structure of the system. This makes it easier to delve deeply into parts of interest and skip others, without losing sight of how the overall system fits together.

> 我们的第二个目标意味着我们尝试以谨慎的态度选择算法、数据结构和渲染技术，同时注重可读性和清晰性。由于与其他渲染系统相比，这些实现会被更多读者审视，我们尝试选择我们所知道的最优雅的算法并尽可能好地实现它们。这个目标还要求系统足够小，使一个人能够完全理解。我们使用可扩展的架构实现了 pbrt，系统的核心通过一组精心设计的接口类来实现，而尽可能多的具体功能则通过这些接口的实现来完成。其结果是，人们不需要理解所有具体实现就能理解系统的基本结构。这使得深入研究感兴趣的部分并跳过其他部分变得更容易，同时不会失去对整体系统如何组合在一起的把握。

There is a tension between the two goals of being complete and being illustrative. Implementing and describing every possible useful technique would not only make this book unacceptably long, but would also make the system prohibitively complex for most readers. In cases where pbrt lacks a particularly useful feature, we have attempted to design the architecture so that the feature could be added without altering the overall system design.

> 完整性和教学性这两个目标之间存在张力。实现和描述每一种可能有用的技术不仅会使本书的篇幅长到不可接受，而且还会使系统对大多数读者来说过于复杂。在 pbrt 缺少某个特别有用的功能的情况下，我们已尝试设计架构使得该功能可以在不改变整体系统设计的情况下添加。

The basic foundations for physically based rendering are the laws of physics and their mathematical expression. pbrt was designed to use the correct physical units and concepts for the quantities it computes and the algorithms it implements. pbrt strives to compute images that are *physically correct*; they accurately reflect the lighting as it would be in a real-world version of the scene. One advantage of the decision to use a physical basis is that it gives a concrete standard of program correctness: for simple scenes, where the expected result can be computed in closed form, if pbrt does not compute the same result, we know there must be a bug in the implementation. Similarly, if different physically based lighting algorithms in pbrt give different results for the same scene, or if pbrt does not give the same results as another physically based renderer, there is certainly an error in one of them. Finally, we believe that this physically based approach to rendering is valuable because it is rigorous. When it is not clear how a particular computation should be performed, physics gives an answer that guarantees a consistent result.

> 基于物理的渲染的基本基础物理定律及其数学表达。pbrt 被设计为对其计算的量和实现的算法使用正确的物理单位和概念。pbrt 力求计算出*物理正确*的图像——它们准确地反映了场景在真实世界版本中的光照情况。使用物理基础的一个优势是它提供了一个具体的程序正确性标准：对于简单场景，当预期结果可以用封闭形式计算时，如果 pbrt 没有计算出相同的结果，我们就知道实现中一定存在错误。类似地，如果 pbrt 中不同的基于物理的光照算法对同一场景给出不同的结果，或者如果 pbrt 与另一个基于物理的渲染器给出不同的结果，那么其中之一肯定存在错误。最后，我们相信这种基于物理的渲染方法之所以有价值，是因为它是严谨的。当不清楚某个特定计算应该如何执行时，物理学给出的答案能保证一致的结果。

Efficiency was given lower priority than these three goals. Since rendering systems often run for many minutes or hours in the course of generating an image, efficiency is clearly important. However, we have mostly confined ourselves to *algorithmic* efficiency rather than low-level code optimization. In some cases, obvious micro-optimizations take a backseat to clear, well-organized code, although we did make some effort to optimize the parts of the system where most of the computation occurs.

> 效率被赋予了比这三个目标更低的优先级。由于渲染系统在生成图像的过程中经常运行数分钟或数小时，效率显然很重要。然而，我们主要将自己限制在*算法*效率上，而非底层代码优化。在某些情况下，明显的微优化让位于清晰、组织良好的代码，不过我们确实在系统中计算量最大的部分做了一些优化工作。

In the course of presenting pbrt and discussing its implementation, we hope to convey some hard-learned lessons from years of rendering research and development. There is more to writing a good renderer than stringing together a set of fast algorithms; making the system both flexible and robust is a difficult task. The system's performance must degrade gracefully as more geometry or light sources are added to it or as any other axis of complexity is stressed.

> 在呈现 pbrt 并讨论其实现的过程中，我们希望传达多年渲染研究和开发中积累的一些来之不易的经验教训。编写一个好的渲染器不仅仅是将一组快速算法串联在一起；使系统既灵活又健壮是一项困难的任务。随着更多几何体或光源的加入，或者任何其他复杂性维度的增加，系统的性能必须能够优雅地降级。

The rewards for developing a system that addresses all these issues are enormous—it is a great pleasure to write a new renderer or add a new feature to an existing renderer and use it to create an image that could not be generated before. Our most fundamental goal in writing this book was to bring this opportunity to a wider audience. Readers are encouraged to use the system to render the example scenes in the pbrt software distribution as they progress through the book. Exercises at the end of each chapter suggest modifications to the system that will help clarify its inner workings and more complex projects to extend the system by adding new features.

> 开发一个解决所有这些问题的系统，其回报是巨大的——编写一个新的渲染器或向现有渲染器添加新功能，并用它创建以前无法生成的图像，这是一种巨大的快乐。我们写这本书最根本的目标是将这个机会带给更广泛的读者。我们鼓励读者在阅读本书的过程中使用该系统渲染 pbrt 软件分发包中的示例场景。每章末尾的练习建议对系统进行修改，这将有助于阐明其内部工作原理，以及通过添加新功能来扩展系统的更复杂项目。

The website for this book is located at [pbrt.org](http://pbrt.org/). This site includes links to the pbrt source code, scenes that can be downloaded to render with pbrt, and a bug tracker, as well as errata. Any errors in this text that are not listed in the errata can be reported to the email address *authors@pbrt.org*. We greatly value your feedback!

> 本书的网站位于 [pbrt.org](http://pbrt.org/)。该网站包含 pbrt 源代码的链接、可下载用于 pbrt 渲染的场景、缺陷跟踪系统以及勘误表。任何未列在勘误表中的文本错误可以报告到电子邮件地址 *authors@pbrt.org*。我们非常重视您的反馈！

### Changes Between The First and Second Editions

> ### 第一版与第二版之间的变化

Six years passed between the publication of the first edition of this book in 2004 and the second edition in 2010. In that time, thousands of copies of the book were sold, and the pbrt software was downloaded thousands of times from the book's website. The pbrt user base gave us a significant amount of feedback and encouragement, and our experience with the system guided many of the decisions we made in making changes between the version of pbrt presented in the first edition and the version in the second edition. In addition to a number of bug fixes, we also made several significant design changes and enhancements:

> 从 2004 年本书第一版出版到 2010 年第二版出版，中间过了六年。在此期间，数千册书被售出，pbrt 软件从本书网站被下载了数千次。pbrt 用户群给予了我们大量的反馈和鼓励，我们使用该系统的经验指导了我们在第一版和第二版之间对 pbrt 所做的许多改变决策。除了修复大量错误外，我们还做了几项重大的设计变更和改进：

- *Removal of the plugin architecture:* The first version of pbrt used a runtime plugin architecture to dynamically load code for implementations of objects like shapes, lights, integrators, cameras, and other objects that were used in the scene currently being rendered. This approach allowed users to extend pbrt with new object types (e.g., new shape primitives) without recompiling the entire rendering system. This approach initially seemed elegant, but it complicated the task of supporting pbrt on multiple platforms and it made debugging more difficult. The only new usage scenario that it truly enabled (binary-only distributions of pbrt or binary plugins) was actually contrary to our pedagogical and open-source goals. Therefore, the plugin architecture was dropped in this edition.

> - *移除插件架构：* pbrt 的第一个版本使用运行时插件架构来动态加载形状、光源、积分器、相机等对象实现的代码。这种方法允许用户通过新的对象类型（例如新的形状图元）扩展 pbrt，而无需重新编译整个渲染系统。这种方法最初看起来很优雅，但它增加了在多个平台上支持 pbrt 的复杂性，并且使调试更加困难。它真正启用的唯一新使用场景（仅二进制分发的 pbrt 或二进制插件）实际上与我们的教学和开源目标相悖。因此，插件架构在这一版中被放弃了。

- *Removal of the image-processing pipeline:* The first version of pbrt provided a tone-mapping interface that converted high-dynamic-range (HDR) floating-point output images directly into low-dynamic-range TIFFs for display. This functionality made sense in 2004, as support for HDR images was still sparse. In 2010, however, advances in digital photography had made HDR images commonplace. Although the theory and practice of tone mapping are elegant and worth learning, we decided to focus the new book exclusively on the process of image formation and skip the topic of image display. Interested readers should consult the book written by Reinhard et al. ([2010](https://pbr-book.org/4ed/Preface/Further_Reading.html#cite:Reinhard10)) for a thorough and modern treatment of the HDR image display process.

> - *移除图像处理管线：* pbrt 的第一个版本提供了色调映射接口，将高动态范围（HDR）浮点输出图像直接转换为低动态范围的 TIFF 格式以供显示。这一功能在 2004 年很有意义，因为当时对 HDR 图像的支持还很稀少。然而到了 2010 年，数码摄影的进步使 HDR 图像变得司空见惯。尽管色调映射的理论和实践很优雅且值得学习，但我们决定让新版书籍专注于图像形成的过程，跳过图像显示的主题。感兴趣的读者可以参考 Reinhard 等人（[2010](https://pbr-book.org/4ed/Preface/Further_Reading.html#cite:Reinhard10)）编写的书籍，该书对 HDR 图像显示过程进行了全面而现代的阐述。

- *Task parallelism:* Multicore architectures became ubiquitous, and we felt that pbrt would not remain relevant without the ability to scale to the number of locally available cores. We also hoped that the parallel programming implementation details documented in this book would help graphics programmers understand some of the subtleties and complexities in writing scalable parallel code.

> - *任务并行：* 多核架构变得无处不在，我们认为如果 pbrt 不具备扩展到本地可用核心数的能力，它将无法保持相关性。我们还希望本书中记录的并行编程实现细节能帮助图形程序员理解编写可扩展并行代码中的一些微妙之处和复杂性。

- *Appropriateness for "production" rendering:* The first version of pbrt was intended exclusively as a pedagogical tool and a stepping-stone for rendering research. Indeed, we made a number of decisions in preparing the first edition that were contrary to use in a production environment, such as limited support for image-based lighting, no support for motion blur, and a photon mapping implementation that was not robust in the presence of complex lighting. With much improved support for these features as well as support for subsurface scattering and Metropolis light transport, we feel that with the second edition, pbrt became much more suitable for rendering very high-quality images of complex environments.

> - *适用于"生产"渲染：* pbrt 的第一个版本仅作为教学工具和渲染研究的跳板。事实上，我们在准备第一版时做出了许多不利于生产环境使用的决定，如对基于图像的光照支持有限、不支持运动模糊，以及在复杂光照存在时光子映射实现不够健壮。随着对这些特性的大幅改进，以及对次表面散射和 Metropolis 光传输的支持，我们认为第二版使 pbrt 变得更加适合渲染复杂环境的高质量图像。

### Changes Between The Second and Third Editions

> ### 第二版与第三版之间的变化

With the passage of another six years, it was time to update and extend the book and the pbrt system. We continued to learn from readers' and users' experiences to better understand which topics were most useful to cover. Further, rendering research continued apace; many parts of the book were due for an update to reflect current best practices. We made significant improvements on a number of fronts:

> 又过了六年，是时候更新和扩展本书和 pbrt 系统了。我们继续从读者和用户的经验中学习，以更好地了解哪些主题最值得涵盖。此外，渲染研究继续快速发展；书中许多部分需要更新以反映当前的最佳实践。我们在多个方面做了重大改进：

- *Bidirectional light transport:* The third version of pbrt added a bidirectional path tracer, including full support for volumetric light transport and multiple importance sampling to weight paths. An all-new Metropolis light transport integrator used components of the bidirectional path tracer, allowing for a particularly succinct implementation of that algorithm.

> - *双向光传输：* pbrt 的第三个版本添加了双向路径追踪器，包括对体积光传输和多重重要性采样权重路径的完整支持。一个全新的 Metropolis 光传输积分器使用了双向路径追踪器的组件，使得该算法的实现特别简洁。

- *Subsurface scattering:* The appearance of many objects—notably, skin and translucent objects—is a result of subsurface light transport. Our implementation of subsurface scattering in the second edition reflected the state of the art in the early 2000s; we thoroughly updated both BSSRDF models and our subsurface light transport algorithms to reflect the progress made in ten subsequent years of research.

> - *次表面散射：* 许多物体的外观——特别是皮肤和半透明物体——是次表面光传输的结果。我们在第二版中的次表面散射实现反映了 2000 年代初的技术水平；我们彻底更新了 BSSRDF 模型和次表面光传输算法，以反映后续十年研究中取得的进步。

- *Numerically robust intersections:* The effects of floating-point round-off error in geometric ray intersection calculations have been a long-standing challenge in ray tracing: they can cause small errors to be present throughout the image. We focused on this issue and derived conservative (but tight) bounds of this error, which made our implementation more robust to this issue than previous rendering systems.

> - *数值稳健的求交：* 几何光线求交计算中浮点舍入误差的影响一直是光线追踪中的长期挑战：它们可能导致图像中到处出现小误差。我们专注于这个问题，推导出了保守（但紧致）的误差界限，使我们的实现比以前的渲染系统对此问题更加健壮。

- *Participating media representation:* We significantly improved the way that scattering media are described and represented in the system; this allows for more accurate results with nested scattering media. A new sampling technique enabled unbiased rendering of heterogeneous media in a way that cleanly integrated with all of the other parts of the system.

> - *参与介质表示：* 我们显著改进了系统中描述和表示散射介质的方式；这使得嵌套散射介质能获得更准确的结果。一种新的采样技术使得异质介质的无偏渲染成为可能，并以一种干净的方式与系统的所有其他部分集成。

- *Measured materials:* This edition added a new technique to represent and evaluate measured materials using a sparse frequency-space basis. This approach is convenient because it allows for exact importance sampling, which was not possible with the representation used in the previous edition.

> - *测量材质：* 这一版添加了一种新技术，使用稀疏频率空间基底来表示和评估测量材质。这种方法很方便，因为它允许精确的重要性采样，而这在前一版使用的表示方法中是不可能的。

- *Photon mapping:* A significant step forward for photon mapping algorithms has been the development of variants that do not require storing all of the photons in memory. We replaced pbrt 's photon mapping algorithm with an implementation based on stochastic progressive photon mapping, which efficiently renders many difficult light transport effects.

> - *光子映射：* 光子映射算法的一个重大进步是开发了不需要在内存中存储所有光子的变体。我们用基于随机渐进光子映射的实现替换了 pbrt 的光子映射算法，它能高效渲染许多困难的光传输效果。

- *Sample generation algorithms:* The distribution of sample values used for numerical integration in rendering algorithms can have a surprisingly large effect on the quality of the final results. We thoroughly updated our treatment of this topic, covering new approaches and efficient implementation techniques in more depth than before.

> - *样本生成算法：* 渲染算法中用于数值积分的样本值分布对最终结果的质量可能产生令人惊讶的巨大影响。我们彻底更新了对这一主题的处理，比以前更深入地涵盖了新方法和高效实现技术。

Many other parts of the system were improved and updated to reflect progress in the field: microfacet reflection models were treated in more depth, with much better sampling techniques; a new "curve" shape was added for modeling hair and other fine geometry; and a new camera model that simulates realistic lens systems was made available. Throughout the book, we made numerous smaller changes to more clearly explain and illustrate the key concepts in physically based rendering systems like pbrt.

> 系统的许多其他部分也被改进和更新以反映该领域的进步：微表面反射模型得到了更深入的处理，并配以更好的采样技术；新增了一种"曲线"形状用于建模头发和其他细小几何体；还提供了一种模拟真实透镜系统的新相机模型。在整本书中，我们做了许多较小的修改，以更清楚地解释和说明像 pbrt 这样的基于物理的渲染系统中的关键概念。

### Changes Between The Third and Fourth Editions

> ### 第三版与第四版之间的变化

Innovation in rendering algorithms has shown no sign of slowing down, and so in 2019 we began focused work on a fourth edition of the text. Not only does almost every chapter include substantial additions, but we have updated the order of chapters and ideas introduced, bringing Monte Carlo integration and the basic ideas of path tracing to the fore rather than saving them for the end.

> 渲染算法的创新没有放缓的迹象，因此在 2019 年我们开始集中力量编写第四版。不仅几乎每一章都包含了大量新增内容，而且我们更新了章节和思想引入的顺序，将蒙特卡洛积分和路径追踪的基本思想提到前面，而不是留到最后。

Capabilities of the system that have seen especially significant improvements include:

> 系统中获得特别显著改进的能力包括：

- *Volumetric scattering:* We have updated the algorithms that model scattering from participating media to the state of the art, adding support for emissive volumes, efficient sampling of volumes with varying densities, and robust support for chromatic media, where the scattering properties vary by wavelength.

> - *体积散射：* 我们将模拟参与介质散射的算法更新到了最新水平，增加了对发光体积的支持、对密度变化的体积的高效采样，以及对色散介质（散射属性随波长变化）的健壮支持。

- *Spectral rendering:* We have excised all use of RGB color for lighting calculations; pbrt now performs lighting calculations exclusively in terms of samples of wavelength-dependent spectral distributions. Not only is this approach more physically accurate than using RGB, but it also allows pbrt to accurately model effects like dispersion.

> - *光谱渲染：* 我们已经在光照计算中彻底去除了所有 RGB 颜色的使用；pbrt 现在完全以依赖波长的光谱分布采样来执行光照计算。这种方法不仅比使用 RGB 在物理上更准确，而且还允许 pbrt 准确地模拟色散等效果。

- *Reflection models:* Our coverage of the foundations of BSDFs and reflection models has been extensively revised, and we have expanded the range of BSDFs covered to include one that accurately models reflection from hair and another that models scattering from layered materials. The measured BRDF follows a new approach that can represent a wide set of materials' reflection spectra.

> - *反射模型：* 我们对 BSDF 和反射模型基础的讨论进行了广泛修订，并扩展了涵盖的 BSDF 范围，包括一个准确模拟头发反射的模型和一个模拟分层材质散射的模型。测量 BRDF 采用了一种新方法，可以表示多种材质的反射光谱。

- *Light sampling:* Not only have we improved the algorithms for sampling points on individual light sources to better reflect the state of the art, but this edition also includes support for *many-light sampling*, which makes it possible to efficiently render scenes with thousands or millions of light sources by carefully sampling just a few of them.

> - *光源采样：* 我们不仅改进了在单个光源上采样点的算法以更好地反映最新技术水平，而且这一版还包括对*多光源采样*的支持，它通过精心采样少数几个光源，使得高效渲染包含数千或数百万光源的场景成为可能。

- *GPU rendering:* This version of pbrt adds support for rendering on GPUs, which can provide 10–100 times higher ray tracing performance than CPUs. We have implemented this capability in a way so that almost all of the code presented in the book runs on both CPUs and GPUs, which has made it possible to localize discussion of GPU-related issues to Chapter [15](https://pbr-book.org/4ed/Wavefront_Rendering_on_GPUs.html#chap:gpu).

> - *GPU 渲染：* 这个版本的 pbrt 增加了对 GPU 渲染的支持，GPU 可以提供比 CPU 高 10-100 倍的光线追踪性能。我们以一种方式实现了这一能力，使得书中呈现的几乎所有代码都可以在 CPU 和 GPU 上运行，这使得可以将 GPU 相关问题的讨论集中到第 [15](https://pbr-book.org/4ed/Wavefront_Rendering_on_GPUs.html#chap:gpu) 章。

The system has seen numerous other improvements and additions, including a new bilinear patch shape, many updates to the sample-generation algorithms that are at the heart of Monte Carlo integration, support for outputting auxiliary information at each pixel about the visible surface geometry and reflection properties, and many more small improvements to the system.

> 系统还有许多其他改进和新增功能，包括一种新的双线性面片形状、对蒙特卡洛积分核心的样本生成算法的大量更新、支持在每个像素输出关于可见表面几何形状和反射属性的辅助信息，以及系统的更多小改进。

### Acknowledgments

> ### 致谢

Pat Hanrahan has contributed to this book in more ways than we could hope to acknowledge; we owe a profound debt to him. He tirelessly argued for clean interfaces and finding the right abstractions to use throughout the system, and his understanding of and approach to rendering deeply influenced its design. His willingness to use pbrt and this manuscript in his rendering course at Stanford was enormously helpful, particularly in the early years of its life when it was still in very rough form; his feedback throughout this process has been crucial for bringing the text to its current state. Finally, the group of people that Pat helped assemble at the Stanford Graphics Lab, and the open environment that he fostered, made for an exciting, stimulating, and fertile environment. Matt and Greg both feel extremely privileged to have been there.

> Pat Hanrahan 对本书的贡献之多超出了我们所能致谢的范围；我们对他深表感激。他不知疲倦地倡导清晰的接口和在整个系统中找到正确的抽象，他对渲染的理解和方法深深影响了系统的设计。他愿意在斯坦福的渲染课程中使用 pbrt 和本书手稿，这带来了巨大的帮助，特别是在早期阶段，当时它还处于非常粗糙的形式；他在整个过程中的反馈对于使文本达到目前的状态至关重要。最后，Pat 在斯坦福图形实验室帮助聚集的那群人，以及他营造的开放环境，创造了一个令人兴奋、充满激励和富有成果的环境。Matt 和 Greg 都为曾在那里而感到无比荣幸。

We owe a debt of gratitude to the many students who used early drafts of this book in courses at Stanford and the University of Virginia between 1999 and 2004. These students provided an enormous amount of feedback about the book and pbrt. The teaching assistants for these courses deserve special mention: Tim Purcell, Mike Cammarano, Ian Buck, and Ren Ng at Stanford, and Nolan Goodnight at Virginia. A number of students in those classes gave particularly valuable feedback and sent bug reports and bug fixes; we would especially like to thank Evan Parker and Phil Beatty. A draft of the manuscript of this book was used in classes taught by Bill Mark and Don Fussell at the University of Texas, Austin, and Raghu Machiraju at Ohio State University; their feedback was invaluable, and we are grateful for their adventurousness in incorporating this system into their courses, even while it was still being edited and revised.

> 我们要感谢 1999 年至 2004 年间在斯坦福大学和弗吉尼亚大学课程中使用本书早期草稿的众多学生。这些学生对本书和 pbrt 提供了大量反馈。这些课程的助教值得特别提及：斯坦福的 Tim Purcell、Mike Cammarano、Ian Buck 和 Ren Ng，以及弗吉尼亚的 Nolan Goodnight。那些课程中的一些学生提供了特别有价值的反馈并发送了错误报告和修复；我们特别要感谢 Evan Parker 和 Phil Beatty。本书手稿的草稿曾在得克萨斯大学奥斯汀分校由 Bill Mark 和 Don Fussell 教授的课程中使用，以及在俄亥俄州立大学由 Raghu Machiraju 教授的课程中使用；他们的反馈非常宝贵，我们感激他们敢于将这个系统纳入课程，即使当时它仍在编辑和修订中。

Matt Pharr would like to acknowledge colleagues and co-workers in rendering-related endeavors who have been a great source of education and who have substantially influenced his approach to writing renderers and his understanding of the field. Particular thanks go to Craig Kolb, who provided a cornerstone of Matt's early computer graphics education through the freely available source code to the rayshade ray-tracing system, and Eric Veach, who has also been generous with his time and expertise. Thanks also to Doug Shult and Stan Eisenstat for formative lessons in mathematics and computer science during high school and college, respectively, and most important to Matt's parents, for the education they have provided and continued encouragement along the way. Finally, thanks to NVIDIA for supporting the preparation of both the first and this latest edition of the book; at NVIDIA, thanks to Nick Triantos and Jayant Kolhe for their support through the final stages of the preparation of the first edition and thanks to Aaron Lefohn, David Luebke, and Bill Dally for their support of work on the fourth edition.

> Matt Pharr 要感谢在渲染相关工作中的同事和合作者，他们一直是很好的教育来源，并极大地影响了他编写渲染器的方法和对该领域的理解。特别感谢 Craig Kolb，他通过 rayshade 光线追踪系统的免费源代码为 Matt 早期的计算机图形学教育奠定了基石；还有 Eric Veach，他也慷慨地分享了自己的时间和专业知识。还要感谢 Doug Shult 和 Stan Eisenstat，分别在高中和大学期间给予了数学和计算机科学方面的启蒙教育，最重要的是感谢 Matt 的父母，感谢他们提供的教育和一路上持续的鼓励。最后，感谢 NVIDIA 对本书第一版和最新版准备工作的支持；在 NVIDIA，感谢 Nick Triantos 和 Jayant Kolhe 在第一版准备最后阶段的支持，以及感谢 Aaron Lefohn、David Luebke 和 Bill Dally 对第四版工作的支持。

Greg Humphreys is very grateful to all the professors and TAs who tolerated him when he was an undergraduate at Princeton. Many people encouraged his interest in graphics, specifically Michael Cohen, David Dobkin, Adam Finkelstein, Michael Cox, Gordon Stoll, Patrick Min, and Dan Wallach. Doug Clark, Steve Lyon, and Andy Wolfe also supervised various independent research boondoggles without even laughing once. Once, in a group meeting about a year-long robotics project, Steve Lyon became exasperated and yelled, "Stop telling me why it can't be done, and figure out how to do it!"—an impromptu lesson that will never be forgotten. Eric Ristad fired Greg as a summer research assistant after his freshman year (before the summer even began), pawning him off on an unsuspecting Pat Hanrahan and beginning an advising relationship that would span 10 years and both coasts. Finally, Dave Hanson taught Greg that literate programming was a great way to work and that computer programming can be a beautiful and subtle art form.

> Greg Humphreys 非常感谢在他作为普林斯顿大学本科生时容忍他的所有教授和助教。许多人鼓励了他对图形学的兴趣，特别是 Michael Cohen、David Dobkin、Adam Finkelstein、Michael Cox、Gordon Stoll、Patrick Min 和 Dan Wallach。Doug Clark、Steve Lyon 和 Andy Wolfe 还指导了各种独立研究项目，甚至一次都没有笑过。有一次，在一个关于为期一年的机器人项目的小组会议上，Steve Lyon 感到恼怒并大喊："别再告诉我为什么做不到了，想办法去做！"——这是一个永远不会被忘记的即兴教训。Eric Ristad 在 Greg 大一之后（甚至在暑假开始之前）就把他从暑期研究助理的职位上解雇了，把他推给了毫不知情的 Pat Hanrahan，从此开始了一段跨越 10 年和两个海岸的导师关系。最后，Dave Hanson 教会了 Greg 文学编程是一种很好的工作方式，计算机编程可以是一种美丽而微妙的艺术形式。

Wenzel Jakob was excited when the first edition of pbrt arrived in his mail during his undergraduate studies in 2004. Needless to say, this had a lasting effect on his career—thus Wenzel would like to begin by thanking his co-authors for inviting him to become a part of the third and fourth editions of this book. Wenzel is extremely indebted to Steve Marschner, who was his Ph.D. advisor during a fulfilling five years at Cornell University. Steve brought him into the world of research and remains a continuous source of inspiration. Wenzel is also thankful for the guidance and stimulating research environment created by the other members of the graphics group, including Kavita Bala, Doug James, and Bruce Walter. Wenzel spent a wonderful postdoc with Olga Sorkine Hornung, who introduced him to geometry processing. Olga's support for Wenzel's involvement in the third edition of this book is deeply appreciated.

> Wenzel Jakob 在 2004 年本科期间收到 pbrt 第一版时非常兴奋。不用说，这对他的职业生涯产生了持久的影响——因此 Wenzel 首先要感谢他的合著者邀请他参与本书的第三版和第四版。Wenzel 非常感激 Steve Marschner，他在康奈尔大学充实的五年博士期间担任他的导师。Steve 带他进入了研究的世界，并一直是持续不断的灵感来源。Wenzel 还感谢图形组其他成员创造的指导和充满激励的研究环境，包括 Kavita Bala、Doug James 和 Bruce Walter。Wenzel 与 Olga Sorkine Hornung 度过了一段美好的博士后时光，她向他介绍了几何处理。Olga 对 Wenzel 参与本书第三版的支持深受感激。

We would especially like to thank the reviewers who read drafts in their entirety; all had insightful and constructive feedback about the manuscript at various stages of its progress. For providing feedback on both the first and second editions of the book, thanks to Ian Ashdown, Per Christensen, Doug Epps, Dan Goldman, Eric Haines, Erik Reinhard, Pete Shirley, Peter-Pike Sloan, Greg Ward, and a host of anonymous reviewers. For the second edition, thanks to Janne Kontkanen, Bill Mark, Nelson Max, and Eric Tabellion. For the fourth edition, we are grateful to Thomas Müller and Per Christensen, who both offered extensive feedback that has measurably improved the final version.

> 我们特别要感谢那些完整阅读了草稿的审稿人；他们在手稿的各个进展阶段都提供了富有洞察力和建设性的反馈。感谢对第一版和第二版都提供了反馈的 Ian Ashdown、Per Christensen、Doug Epps、Dan Goldman、Eric Haines、Erik Reinhard、Pete Shirley、Peter-Pike Sloan、Greg Ward，以及众多匿名审稿人。对于第二版，感谢 Janne Kontkanen、Bill Mark、Nelson Max 和 Eric Tabellion。对于第四版，我们感谢 Thomas Müller 和 Per Christensen，他们都提供了广泛的反馈，可衡量地改善了最终版本。

Many experts have kindly explained subtleties in their work to us and guided us to best practices. For the first and second editions, we are also grateful to Don Mitchell, for his help with understanding some of the details of sampling and reconstruction; Thomas Kollig and Alexander Keller, for explaining the finer points of low-discrepancy sampling; Christer Ericson, who had a number of suggestions for improving our kd-tree implementation; and Christophe Hery and Eugene d'Eon for helping us with the nuances of subsurface scattering.

> 许多专家慷慨地向我们解释了他们工作中的微妙之处，并指导我们采用最佳实践。对于第一版和第二版，我们还要感谢 Don Mitchell 帮助我们理解采样和重建的一些细节；Thomas Kollig 和 Alexander Keller 解释了低差异采样的精细要点；Christer Ericson 提出了改进我们 kd-tree 实现的若干建议；以及 Christophe Hery 和 Eugene d'Eon 帮助我们处理次表面散射的细微之处。

For the third edition, we would especially like to thank Leo Grünschloß for reviewing our sampling chapter; Alexander Keller for suggestions about topics for that chapter; Eric Heitz for extensive help with details of microfacets and reviewing our text on that topic; Thiago Ize for thoroughly reviewing the text on floating-point error; Tom van Bussel for reporting a number of errors in our BSSRDF code; Ralf Habel for reviewing our BSSRDF text; and Toshiya Hachisuka and Anton Kaplanyan for extensive review and comments about our light transport chapters.

> 对于第三版，我们特别要感谢 Leo Grünschloß 审阅了我们的采样章节；Alexander Keller 对该章主题的建议；Eric Heitz 在微表面细节方面的广泛帮助和对我们相关文本的审阅；Thiago Ize 对浮点误差文本的彻底审阅；Tom van Bussel 报告了我们 BSSRDF 代码中的若干错误；Ralf Habel 审阅了我们的 BSSRDF 文本；以及 Toshiya Hachisuka 和 Anton Kaplanyan 对我们光传输章节的广泛审阅和评论。

For the fourth edition, thanks to Alejandro Conty Estevez for reviewing our treatment of many-light sampling; Eugene d'Eon, Bailey Miller, and Jan Novák for comments on the volumetric scattering chapters; Eric Haines, Simon Kallweit, Martin Stich, and Carsten Wächter for reviewing the chapter on GPU rendering; Karl Li for feedback on a number of chapters; Tzu-Mao Li for his review of our discussion of inverse and differentiable rendering; Fabrice Rousselle for feedback on machine learning and rendering; and Gurprit Singh for comments on our discussion of Fourier analysis of Monte Carlo integration. We also appreciate extensive comments and suggestions from Jeppe Revall Frisvad on pbrt 's treatment of reflection models in previous editions.

> 对于第四版，感谢 Alejandro Conty Estevez 审阅了我们对多光源采样的处理；Eugene d'Eon、Bailey Miller 和 Jan Novák 对体积散射章节的评论；Eric Haines、Simon Kallweit、Martin Stich 和 Carsten Wächter 审阅了 GPU 渲染章节；Karl Li 对多个章节的反馈；Tzu-Mao Li 审阅了我们关于逆向和可微渲染的讨论；Fabrice Rousselle 对机器学习与渲染的反馈；以及 Gurprit Singh 对我们蒙特卡洛积分傅里叶分析讨论的评论。我们还感谢 Jeppe Revall Frisvad 对 pbrt 在以前版本中反射模型处理的广泛评论和建议。

For improvements to pbrt 's implementation in this edition, thanks to Pierre Moreau for his efforts in debugging pbrt 's GPU support on Windows and to Jim Price, who not only found and fixed numerous bugs in the early release of pbrt 's source code, but who also contributed a better representation of chromatic volumetric media than our original implementation. We are also very appreciative of Anders Langlands and Luca Fascione of Weta Digital for providing an implementation of their *PhysLight* system, which has been incorporated into pbrt 's PixelSensor class and light source implementations.

> 对于这一版中 pbrt 实现的改进，感谢 Pierre Moreau 在调试 pbrt 的 Windows GPU 支持方面的努力，以及 Jim Price，他不仅在 pbrt 源代码的早期发布中发现并修复了大量错误，还贡献了比我们原始实现更好的色散体积介质表示。我们还非常感谢 Weta Digital 的 Anders Langlands 和 Luca Fascione 提供了他们的 *PhysLight* 系统实现，该系统已被整合到 pbrt 的 PixelSensor 类和光源实现中。

Many people have reported errors in the text of previous editions or bugs in pbrt. We'd especially like to thank Solomon Boulos, Stephen Chenney, Per Christensen, John Danks, Mike Day, Kevin Egan, Volodymyr Kachurovskyi, Kostya Smolenskiy, Ke Xu, and Arek Zimny, who have been especially prolific.

> 许多人报告了以前版本的文本中的错误或 pbrt 中的缺陷。我们特别要感谢 Solomon Boulos、Stephen Chenney、Per Christensen、John Danks、Mike Day、Kevin Egan、Volodymyr Kachurovskyi、Kostya Smolenskiy、Ke Xu 和 Arek Zimny，他们尤其高产。

For their suggestions and bug reports, we would also like to thank Rachit Agrawal, Frederick Akalin, Thomas de Bodt, Mark Bolstad, Brian Budge, Jonathon Cai, Bryan Catanzaro, Tzu-Chieh Chang, Mark Colbert, Yunjian Ding, Tao Du, Marcos Fajardo, Shaohua Fan, Luca Fascione, Etienne Ferrier, Nigel Fisher, Jeppe Revall Frisvad, Robert G. Graf, Asbjørn Heid, Steve Hill, Wei-Feng Huang, John "Spike" Hughes, Keith Jeffery, Greg Johnson, Aaron Karp, Andrew Kensler, Alan King, Donald Knuth, Martin Kraus, Chris Kulla, Murat Kurt, Larry Lai, Morgan McGuire, Craig McNaughton, Don Mitchell, Swaminathan Narayanan, Anders Nilsson, Jens Olsson, Vincent Pegoraro, Srinath Ravichandiran, Andy Selle, Sébastien Speierer, Nils Thuerey, Eric Veach, Ingo Wald, Zejian Wang, Xiong Wei, Wei-Wei Xu, Tizian Zeltner, and Matthias Zwicker. Finally, we would like to thank the *LuxRender* developers and the *LuxRender* community, particularly Terrence Vergauwen, Jean-Philippe Grimaldi, and Asbjørn Heid; it has been a delight to see the rendering system they have built from pbrt 's foundation, and we have learned from reading their source code and implementations of new rendering algorithms.

> 对于他们的建议和错误报告，我们还要感谢 Rachit Agrawal、Frederick Akalin、Thomas de Bodt、Mark Bolstad、Brian Budge、Jonathon Cai、Bryan Catanzaro、Tzu-Chieh Chang、Mark Colbert、Yunjian Ding、Tao Du、Marcos Fajardo、Shaohua Fan、Luca Fascione、Etienne Ferrier、Nigel Fisher、Jeppe Revall Frisvad、Robert G. Graf、Asbjørn Heid、Steve Hill、Wei-Feng Huang、John "Spike" Hughes、Keith Jeffery、Greg Johnson、Aaron Karp、Andrew Kensler、Alan King、Donald Knuth、Martin Kraus、Chris Kulla、Murat Kurt、Larry Lai、Morgan McGuire、Craig McNaughton、Don Mitchell、Swaminathan Narayanan、Anders Nilsson、Jens Olsson、Vincent Pegoraro、Srinath Ravichandiran、Andy Selle、Sébastien Speierer、Nils Thuerey、Eric Veach、Ingo Wald、Zejian Wang、Xiong Wei、Wei-Wei Xu、Tizian Zeltner 和 Matthias Zwicker。最后，我们要感谢 *LuxRender* 的开发者和 *LuxRender* 社区，特别是 Terrence Vergauwen、Jean-Philippe Grimaldi 和 Asbjørn Heid；看到他们在 pbrt 基础上构建的渲染系统令人欣喜，我们也从阅读他们的源代码和新渲染算法实现中受益良多。

Special thanks to Martin Preston and Steph Bruning from Framestore for their help with our being able to use a frame from *Gravity* (image courtesy of Warner Bros. and Framestore), and to Weta Digital for their help with the frame from *Alita: Battle Angel* (© 2018 Twentieth Century Fox Film Corporation, All Rights Reserved).

> 特别感谢 Framestore 的 Martin Preston 和 Steph Bruning 帮助我们获得使用《地心引力》中一帧画面的许可（图像由 Warner Bros. 和 Framestore 提供），以及感谢 Weta Digital 帮助获得使用《阿丽塔：战斗天使》中一帧画面的许可（© 2018 Twentieth Century Fox Film Corporation，保留所有权利）。

#### Production

> #### 制作

For the production of the first edition, we would also like to thank our editor Tim Cox for his willingness to take on this slightly unorthodox project and for both his direction and patience throughout the process. We are very grateful to Elisabeth Beller (project manager), who went well beyond the call of duty for the book; her ability to keep this complex project in control and on schedule was remarkable, and we particularly thank her for the measurable impact she had on the quality of the final result. Thanks also to Rick Camp (editorial assistant) for his many contributions along the way. Paul Anagnostopoulos and Jacqui Scarlott at Windfall Software did the book's composition; their ability to take the authors' homebrew literate programming file format and turn it into high-quality final output while also juggling the multiple unusual types of indexing we asked for is greatly appreciated. Thanks also to Ken DellaPenta (copyeditor) and Jennifer McClain (proofreader), as well as to Max Spector at Chen Design (text and cover designer) and Steve Rath (indexer).

> 对于第一版的制作，我们还要感谢编辑 Tim Cox 愿意接手这个略显非传统的项目，以及他在整个过程中的指导和耐心。我们非常感谢项目经理 Elisabeth Beller，她的付出远超职责要求；她能够控制这个复杂项目并保持进度，这非常了不起，我们特别感谢她对最终成果质量的可衡量影响。同时感谢编辑助理 Rick Camp 在过程中的诸多贡献。Windfall Software 的 Paul Anagnostopoulos 和 Jacqui Scarlott 完成了本书的排版；他们能够将作者自制的文学编程文件格式转化为高质量的最终输出，同时还要处理我们要求的多种不寻常的索引类型，这非常值得赞赏。还要感谢 Ken DellaPenta（文字编辑）和 Jennifer McClain（校对），以及 Chen Design 的 Max Spector（文本和封面设计师）和 Steve Rath（索引编制者）。

For the second edition, we would like to thank Greg Chalson, who talked us into expanding and updating the book; Greg also ensured that Paul Anagnostopoulos at Windfall Software would again do the book's composition. We would like to thank Paul again for his efforts in working with this book's production complexity. Finally, we would also like to thank Todd Green, Paul Gottehrer, and Heather Scherer at Elsevier.

> 对于第二版，我们要感谢 Greg Chalson，是他说服我们扩展和更新本书；Greg 还确保了 Windfall Software 的 Paul Anagnostopoulos 再次负责本书的排版。我们再次感谢 Paul 在处理本书制作复杂性方面的努力。最后，我们还要感谢 Elsevier 的 Todd Green、Paul Gottehrer 和 Heather Scherer。

For the third edition, we would like to thank Todd Green, who oversaw that go-round, and Amy Invernizzi, who kept the train on the rails throughout that process. We were delighted to have Paul Anagnostopoulos at Windfall Software part of this process for a third time; his efforts have been critical to the book's high production value, which is so important to us.

> 对于第三版，我们要感谢 Todd Green 负责了那一轮的工作，以及 Amy Invernizzi 在整个过程中保持了项目的正常运行。我们很高兴 Windfall Software 的 Paul Anagnostopoulos 第三次参与这个过程；他的努力对于本书的高制作质量至关重要，这对我们非常重要。

The fourth edition saw us moving to MIT Press; many thanks to Elizabeth Swayze for her enthusiasm for bringing us on board, guidance through the production process, and ensuring that Paul Anagnostopoulos would again handle composition. Our deepest thanks to Paul for coming back for one more edition with us, and many thanks as well to MaryEllen Oliver for her superb work on copyediting and proofreading.

> 第四版使我们转移到了 MIT Press；非常感谢 Elizabeth Swayze 热情地将我们引入，指导我们完成制作过程，并确保 Paul Anagnostopoulos 再次负责排版。我们深深感谢 Paul 再次回来与我们合作又一版，同时也非常感谢 MaryEllen Oliver 在文字编辑和校对方面的出色工作。

#### The Online Edition

> #### 在线版

As of November 1, 2023, the full text of the fourth edition is available online for free. Many thanks to MIT Press and Elizabeth Swayze for their support of a freely-available version of the book.

> 自 2023 年 11 月 1 日起，第四版的全文可在网上免费获取。非常感谢 MIT Press 和 Elizabeth Swayze 支持本书的免费版本。

A number of open source systems have been instrumental to the development of the online version of *Physically Based Rendering*. We'd specifically like to thank the developers of [Bootstrap](https://getbootstrap.com/), [JERI](https://jeri.io/), [MathJax](https://www.mathjax.org/), and [JQuery](https://jquery.com/). We'd also like to thank Impallari Type for the design of the [Domine](https://fonts.google.com/specimen/Domine) font that we use for body text; Christian Robertson for the design of the [Roboto Mono](https://fonts.google.com/specimen/Roboto+Mono) font that we use for code; and the designers of the [Font Awesome](https://fontawesome.com/) fonts.

> 一些开源系统对《Physically Based Rendering》在线版的开发起到了至关重要的作用。我们特别要感谢 [Bootstrap](https://getbootstrap.com/)、[JERI](https://jeri.io/)、[MathJax](https://www.mathjax.org/) 和 [JQuery](https://jquery.com/) 的开发者们。我们还要感谢 Impallari Type 设计了我们用于正文的 [Domine](https://fonts.google.com/specimen/Domine) 字体；Christian Robertson 设计了我们用于代码的 [Roboto Mono](https://fonts.google.com/specimen/Roboto+Mono) 字体；以及 [Font Awesome](https://fontawesome.com/) 字体的设计者们。

We'd also like to thank everyone who supported the earlier online edition through *Patreon*; as of 1 November 2023: 3Dscan, Abdelhakim Deneche, Alain Galvan, Andréa Machizaud, Aras Pranckevicius, Arman Uguray, Ben Bass, Claudia Doppioslash, Dong Feng, Enrico, Filip Strugar, Haralambi Todorov, Jaewon Jung, Jan Walter, Jendrik Illner, Jim Price, Joakim Dahl, Jonathan Stone, KrotanHill, Laura Reznikov, Malte Nawroth, Mauricio Vives, Mrinal Deo, Nathan Vegdahl, Pavel Panchekha, Pratool Gadtaula, Saad Ahmed, Scott Pilet, Shin Watanabe, Steve Watts Kennedy, Tom Hulton-Harrop, Torgrim Boe Skaarsmoen, William Newhall, Yining Karl Li, and Yury Mikhaylov. We have, however, closed the *Patreon* with the launch of the fourth edition.

> 我们还要感谢所有通过 *Patreon* 支持早期在线版的人；截至 2023 年 11 月 1 日的支持者名单：3Dscan、Abdelhakim Deneche、Alain Galvan、Andréa Machizaud、Aras Pranckevicius、Arman Uguray、Ben Bass、Claudia Doppioslash、Dong Feng、Enrico、Filip Strugar、Haralambi Todorov、Jaewon Jung、Jan Walter、Jendrik Illner、Jim Price、Joakim Dahl、Jonathan Stone、KrotanHill、Laura Reznikov、Malte Nawroth、Mauricio Vives、Mrinal Deo、Nathan Vegdahl、Pavel Panchekha、Pratool Gadtaula、Saad Ahmed、Scott Pilet、Shin Watanabe、Steve Watts Kennedy、Tom Hulton-Harrop、Torgrim Boe Skaarsmoen、William Newhall、Yining Karl Li 和 Yury Mikhaylov。不过，随着第四版的发布，我们已经关闭了 *Patreon*。

Although the book is posted online for anyone to read for free, the text of the book remains © Copyright 2004–2023 Matt Pharr, Wenzel Jakob, and Greg Humphreys under a [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/) license. The book figures are licensed with a [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) license with the thought that they may be useful when teaching graphics courses.

> 尽管本书已在网上发布供任何人免费阅读，但书中文本的版权仍属于 © Copyright 2004–2023 Matt Pharr、Wenzel Jakob 和 Greg Humphreys，采用 [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/) 许可证。书中的图表采用 [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) 许可证，考虑到它们在教授图形学课程时可能有用。

#### Scenes, Models, and Data

> #### 场景、模型和数据

Many people and organizations have generously provided scenes and models for use in this book and the pbrt distribution. Their generosity has been invaluable in helping us create interesting example images throughout the text.

> 许多人和组织慷慨地提供了场景和模型，用于本书和 pbrt 的分发。他们的慷慨对于帮助我们在全书中创建有趣的示例图像是无价的。

We are most grateful to Guillermo M. Leal Llaguno of Evolución Visual, [www.evvisual.com](http://www.evvisual.com/), who modeled and rendered the iconic *San Miguel* scene that was featured on the cover of the second edition and is still used in numerous figures in the book. We would also especially like to thank Marko Dabrovic ([www.3lhd.com](http://www.3lhd.com/)) and Mihovil Odak at RNA Studios ([www.rna.hr](http://www.rna.hr/)), who supplied a bounty of models and scenes used in earlier editions of the book, including the Sponza atrium, the Sibenik cathedral, and the Audi TT car model that can be seen in Figure [16.1](https://pbr-book.org/4ed/Retrospective_and_the_Future/pbrt_over_the_Years.html#fig:tt-pbrt-v1-v4) of this edition. Many thanks are also due to Florent Boyer, who provided the contemporary house scene used in some of the images in Chapter chap:bidir-methods.

> 我们最感谢 Evolución Visual 的 Guillermo M. Leal Llaguno（[www.evvisual.com](http://www.evvisual.com/)），他建模并渲染了标志性的 *San Miguel* 场景，该场景曾出现在第二版封面上，至今仍在书中众多图片中使用。我们还特别感谢 Marko Dabrovic（[www.3lhd.com](http://www.3lhd.com/)）和 RNA Studios 的 Mihovil Odak（[www.rna.hr](http://www.rna.hr/)），他们提供了早期版本中使用的大量模型和场景，包括 Sponza 中庭、Sibenik 大教堂以及可以在本版图 [16.1](https://pbr-book.org/4ed/Retrospective_and_the_Future/pbrt_over_the_Years.html#fig:tt-pbrt-v1-v4) 中看到的 Audi TT 汽车模型。还要感谢 Florent Boyer，他提供了双向方法章节中部分图像使用的现代住宅场景。

We sincerely thank Jan-Walter Schliep, Burak Kahraman, and Timm Dapper of Laubwerk ([www.laubwerk.com](http://www.laubwerk.com/)) for creating the *Countryside* landscape scene that was on the cover of the previous edition of the book and is used in numerous figures in this edition.

> 我们真诚地感谢 Laubwerk 的 Jan-Walter Schliep、Burak Kahraman 和 Timm Dapper（[www.laubwerk.com](http://www.laubwerk.com/)），他们创建了曾出现在上一版封面上并在本版众多图片中使用的 *Countryside* 乡村景观场景。

Many thanks to Angelo Ferretti of Lucydreams ([www.lucydreams.it](http://www.lucydreams.it/)) for licensing the *Watercolor* and *Kroken* scenes, which have provided a wonderful cover image for this edition, material for numerous figures, and a pair of complex scenes that exercise pbrt 's capabilities.

> 非常感谢 Lucydreams 的 Angelo Ferretti（[www.lucydreams.it](http://www.lucydreams.it/)）授权使用 *Watercolor* 和 *Kroken* 场景，它们为这一版提供了精美的封面图像、众多图片的素材，以及一对锻炼 pbrt 能力的复杂场景。

Jim Price kindly provided a number of scenes featuring interesting volumetric media; those have measurably improved the figures for that topic. Thanks also to Beeple for making the *Zero Day* and *Transparent Machines* scenes available under a permissive license and to Martin Lubich for the Austrian Imperial Crown model. Finally, our deepest thanks to Walt Disney Animation Studios for making the production-complexity *Moana Island* scene available as well as providing the detailed volumetric cloud model.

> Jim Price 慷慨地提供了一些展示有趣体积介质的场景；这些场景显著改善了该主题的配图。还要感谢 Beeple 在宽松许可证下提供了 *Zero Day* 和 *Transparent Machines* 场景，以及 Martin Lubich 提供了奥地利皇冠模型。最后，我们深深感谢华特迪士尼动画工作室提供了制作级复杂度的 *Moana Island*（莫阿纳岛）场景以及详细的体积云模型。

The bunny, Buddha, and dragon models are courtesy of the Stanford Computer Graphics Laboratory's scanning repository. The "killeroo" model is included with permission of Phil Dench and Martin Rezard (3D scan and digital representations by headus, design and clay sculpt by Rezard). The dragon model scan used in Chapter [9](https://pbr-book.org/4ed/Reflection_Models.html#chap:reflection-models) is courtesy of Christian Schüller, and our thanks to Yasutoshi Mori for the material orb and the sports car model. The glass used to illustrate caustics in Chapter chap:bidir-methods is thanks to Simon Wendsche. The head model used to illustrate subsurface scattering was made available by Infinite Realities, Inc. under a Creative Commons Attribution 3.0 license. Thanks also to "tyrant monkey" for the BMW M6 car model and "Wig42" for the breakfast table scene; both were posted to [blendswap.com](http://blendswap.com/), also under a Creative Commons Attribution 3.0 license.

> 兔子、佛像和龙模型由斯坦福计算机图形实验室的扫描存储库提供。"killeroo"模型经 Phil Dench 和 Martin Rezard 许可使用（3D 扫描和数字表示由 headus 完成，设计和泥塑由 Rezard 完成）。第 [9](https://pbr-book.org/4ed/Reflection_Models.html#chap:reflection-models) 章中使用的龙模型扫描由 Christian Schüller 提供，感谢 Yasutoshi Mori 提供的材质球和跑车模型。双向方法章节中用于展示焦散的玻璃感谢 Simon Wendsche 提供。用于展示次表面散射的头部模型由 Infinite Realities, Inc. 在 Creative Commons Attribution 3.0 许可证下提供。还要感谢"tyrant monkey"提供的 BMW M6 汽车模型和"Wig42"提供的早餐桌场景；两者都发布在 [blendswap.com](http://blendswap.com/) 上，同样采用 Creative Commons Attribution 3.0 许可证。

We have made use of numerous environment maps from the *PolyHaven* website ([polyhaven.com](http://polyhaven.com/)) for HDR lighting in various scenes; all are available under a Creative Commons CC0 license. Thanks to Sergej Majboroda and Greg Zaal, whose environment maps we have used.

> 我们使用了 *PolyHaven* 网站（[polyhaven.com](http://polyhaven.com/)）上的众多环境贴图用于各种场景的 HDR 光照；所有这些都在 Creative Commons CC0 许可证下提供。感谢 Sergej Majboroda 和 Greg Zaal，我们使用了他们的环境贴图。

Marc Ellens provided spectral data for a variety of light sources, and the spectral RGB measurement data for a variety of displays is courtesy of Tom Lianza at X-Rite. Our thanks as well to Danny Pascale ([www.babelcolor.com](http://www.babelcolor.com/)) for allowing us to include his measurements of the spectral reflectance of a color chart. Thanks to Mikhail Polyanskiy for index of refraction data via [refractiveindex.info](http://refractiveindex.info/) and to Anders Langlands, Luca Fascione, and Weta Digital for camera sensor response data that is included in pbrt.

> Marc Ellens 提供了各种光源的光谱数据，各种显示器的光谱 RGB 测量数据由 X-Rite 的 Tom Lianza 提供。我们还要感谢 Danny Pascale（[www.babelcolor.com](http://www.babelcolor.com/)）允许我们使用他对色卡光谱反射率的测量数据。感谢 Mikhail Polyanskiy 通过 [refractiveindex.info](http://refractiveindex.info/) 提供的折射率数据，以及 Anders Langlands、Luca Fascione 和 Weta Digital 提供的包含在 pbrt 中的相机传感器响应数据。

### About The Cover

> ### 关于封面

The *Watercolor* scene on the cover was created by Angelo Ferretti of Lucydreams ([www.lucydreams.it](http://www.lucydreams.it/)). It requires a total of 2 GiB of on-disk storage for geometry and 836 MiB for texture maps. Come rendering, the scene description requires 15 GiB of memory to store over 33 million unique triangles, 412 texture maps, and associated data structures.

> 封面上的 *Watercolor* 场景由 Lucydreams 的 Angelo Ferretti（[www.lucydreams.it](http://www.lucydreams.it/)）创建。它的几何数据需要 2 GiB 的磁盘存储空间，纹理贴图需要 836 MiB。在渲染时，场景描述需要 15 GiB 的内存来存储超过 3300 万个独立三角形、412 张纹理贴图以及相关数据结构。

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[光线追踪]]
- [[文学编程]]
