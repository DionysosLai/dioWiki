---
title: "Processing the Scene Description"
source: "https://pbr-book.org/4ed/Processing_the_Scene_Description"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## C Processing the Scene Description

In the discussion of pbrt 's main() function in Section [1.3.2](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#sec:pbrt-main) at the start of the book, we wrote that after the command-line arguments are processed, the scene description is parsed and converted into corresponding Shape s, Light s, Material s, and so forth. Thereafter, as we have discussed the implementations of those classes, we have not worried about when they are created or where the parameter values for their constructors come from. This appendix fills in that gap and explains the path from human-readable scene description files to corresponding C++ objects in memory.

> 在本书开头第 1.3.2 节对 pbrt 的 main() 函数的讨论中，我们写到在处理命令行参数后，场景描述被解析并转换为对应的 Shape、Light、Material 等。此后，在我们讨论这些类的实现时，我们没有关心它们何时被创建或其构造函数的参数值从何而来。本附录填补了这个空白，解释了从人类可读的场景描述文件到内存中对应 C++ 对象的路径。

The scene description is processed in three stages, each of which is described in successive sections of this appendix:

> 场景描述分三个阶段处理，每个阶段在本附录的连续各节中描述：

- The text file format that describes the scene is parsed. Each statement in the file causes a corresponding method to be called in a [ParserTarget](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParserTarget) class implementation.

> - 描述场景的文本文件格式被解析。文件中的每条语句导致在 ParserTarget 类实现中调用相应的方法。

- An instance of the [BasicSceneBuilder](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#BasicSceneBuilder) class, which implements the [ParserTarget](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParserTarget) interface, tracks graphics state such as the current material and transformation matrix as the file is parsed. For each entity in the scene (the camera, each light and shape, etc.), it produces a single object that represents the entity and its parameters.

> - BasicSceneBuilder 类的实例实现了 ParserTarget 接口，在文件解析时跟踪图形状态，如当前材质和变换矩阵。对于场景中的每个实体（相机、每个光源和形状等），它生成一个表示该实体及其参数的对象。

- A [BasicScene](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#BasicScene) instance collects the objects produced by the [BasicSceneBuilder](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#BasicSceneBuilder) and creates the corresponding object types that are used for rendering.

> - BasicScene 实例收集 BasicSceneBuilder 生成的对象，并创建用于渲染的相应对象类型。

Once the BasicScene is complete, it is passed to either the [RenderCPU()](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#RenderCPU) or [RenderWavefront()](https://pbr-book.org/4ed/Wavefront_Rendering_on_GPUs/Mapping_Path_Tracing_to_the_GPU.html#RenderWavefront) function, as appropriate. Those functions then create the final representation of the scene that they will use for rendering. For most types of scene objects (e.g., the [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler)), both call a [BasicScene](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#BasicScene) method that returns the object that corresponds to what was specified in the scene description. Those two functions diverge in how they represent the intersectable scene geometry. In [RenderCPU()](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#RenderCPU) as well as when the wavefront renderer is running on the CPU, the primitives and accelerators defined in Chapter [7](https://pbr-book.org/4ed/Primitives_and_Intersection_Acceleration.html#chap:acceleration) are used to represent it. With GPU rendering, shapes are converted to the representation expected by the GPU's ray-tracing API.

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**同章节**:
- [[C.1 Tokenizing and Parsing]]
- [[C.2 Managing the Scene Description]]
- [[C.3 BasicScene and Final Object Creation]]
- [[C.4 Adding New Object Implementations]]
