---
title: "Adding New Object Implementations"
source: "https://pbr-book.org/4ed/Processing_the_Scene_Description/Adding_New_Object_Implementations"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## C.4 Adding New Object Implementations
> ## C.4 添加新的对象实现

To sum up various details that have been spread across multiple chapters, three main steps are required in order to add a new implementation of one of pbrt 's interface types:
> 总结分散在多个章节中的各种细节，要添加 pbrt 接口类型之一的新实现需要三个主要步骤：

1. The source files containing its implementation need to be added to the appropriate places in pbrt 's top-level CMakeLists.txt file, or they should be added to an appropriate preexisting source file so that they are compiled into the pbrt binary.
2. The name of the type should be added to the list of types provided to the [TaggedPointer](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#TaggedPointer) that the corresponding interface type inherits from; this can be done by editing the appropriate header file in the [base/](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/base/) directory.
3. The interface type's Create() method should be modified to create an instance of the new type when it has been specified in the scene description.

It is probably a good idea to implement a static Create() method in the new type that takes a [ParameterDictionary](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#ParameterDictionary) and such, to specify the object's parameters in the same way that the existing classes do, but doing so is not a requirement.