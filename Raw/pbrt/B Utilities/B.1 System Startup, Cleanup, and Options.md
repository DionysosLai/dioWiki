---
title: "System Startup, Cleanup, and Options"
source: "https://pbr-book.org/4ed/Utilities/System_Startup,_Cleanup,_and_Options"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## B.1 System Startup, Cleanup, and Options
> ## B.1 系统启动、清理和选项

Two structures that are defined in the [options.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/options.h) header represent various user-specified options that are generally not part of the scene description file but are instead specified using command-line arguments to pbrt.
> 在 [options.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/options.h) 头文件中定义的两个结构体表示各种用户指定的选项，这些选项通常不是场景描述文件的一部分，而是通过命令行参数指定给 pbrt 的。 pbrt ’s main() function allocates the structure and then overrides its default values as appropriate.

BasicPBRTOptions stores the options that are used in both the CPU and GPU rendering pipelines. How most of them are used should be self-evident, though seed deserves note: any time an [RNG](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG) is initialized in pbrt, the seed value in the options should be incorporated in the seed passed to its constructor. In this way, the renderer will generate independent images if the user specifies different –seed values using command-line arguments.

<<BasicPBRTOptions Definition>>=

struct BasicPBRTOptions { int seed = 0; bool quiet = false; bool disablePixelJitter = false, disableWavelengthJitter = false; bool disableTextureFiltering = false; bool forceDiffuse = false; bool useGPU = false; bool wavefront = false; RenderingCoordinateSystem renderingSpace = RenderingCoordinateSystem::CameraWorld; };

<<RenderingCoordinateSystem Definition>>=

enum class RenderingCoordinateSystem { [Camera](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#Camera), CameraWorld, World };

The PBRTOptions structure, not included here, inherits from [BasicPBRTOptions](#BasicPBRTOptions) and adds a number of additional options that are mostly used when processing the scene description and not during rendering. A number of these options are std::string s that are not accessible in GPU code. Splitting the options in this way allows GPU code to access a [BasicPBRTOptions](#BasicPBRTOptions) instance to get the particular option values that are relevant to it.

The options are passed to InitPBRT(), which should be called before any of pbrt ’s other classes or interfaces are used. It handles system-wide initialization and configuration. When rendering completes, CleanupPBRT() should be called so that the system can gracefully shut down. Both of these functions are defined in the file [pbrt.cpp](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/pbrt.cpp).

<<Initialization and Cleanup Function Declarations>>=

void InitPBRT(const PBRTOptions &opt); void CleanupPBRT();

In code that only runs on the CPU, the options can be accessed via a global variable.

<<Options Global Variable Declaration>>=

extern PBRTOptions \*Options;

For code that runs on both the CPU and GPU, options must be accessed through the GetOptions() function, which returns a copy of the options that is either stored in CPU or GPU memory, depending on which type of processor the code is executing.

<<Options Inline Functions>>=

const BasicPBRTOptions &GetOptions();

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**同章节**:
- [[B Utilities]]
- [[B.2 Mathematical Infrastructure]]
- [[B.3 User Interaction]]
- [[B.4 Containers and Memory Management]]
- [[B.5 Images]]
- [[B.6 Parallelism]]
- [[B.7 Statistics]]
