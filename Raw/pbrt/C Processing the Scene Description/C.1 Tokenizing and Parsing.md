---
title: "Tokenizing and Parsing"
source: "https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## C.1 Tokenizing and Parsing
> ## C.1 词法分析和解析

Two functions expose pbrt 's scene-parsing capabilities, one taking one or more names of files to process in sequence, and the other taking a string that holds a scene description.
> 两个函数暴露了 pbrt 的场景解析能力，一个接受一个或多个要按顺序处理的文件名，另一个接受包含场景描述的字符串。 All of pbrt 's parsing code is in the files [parser.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/parser.h) and [parser.cpp](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/parser.cpp).

<<Scene Parsing Declarations>>=

void ParseFiles([ParserTarget](#ParserTarget) \*target, pstd::span<const std::string> filenames); void ParseString([ParserTarget](#ParserTarget) \*target, std::string str);

Rather than directly returning an object that represents the parsed scene, the parsing functions call methods of the provided [ParserTarget](#ParserTarget) to convey what they have found. ParserTarget is an abstract interface class that defines nearly 40 pure virtual functions, each one corresponding to a statement in a pbrt scene description file.

<<ParserTarget Definition>>=

class ParserTarget { public: << [ParserTarget Interface](#fragment-ParserTargetInterface-0) >>

virtual void Scale(Float sx, Float sy, Float sz, FileLoc loc) = 0; virtual void [Shape](https://pbr-book.org/4ed/Shapes/Basic_Shape_Interface.html#Shape) (const std::string &name, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0; virtual ~ParserTarget(); virtual void Option(const std::string &name, const std::string &value, FileLoc loc) = 0; virtual void Identity(FileLoc loc) = 0; virtual void Translate(Float dx, Float dy, Float dz, FileLoc loc) = 0; virtual void Rotate(Float angle, Float ax, Float ay, Float az, FileLoc loc) = 0; virtual void LookAt(Float ex, Float ey, Float ez, Float lx, Float ly, Float lz, Float ux, Float uy, Float uz, FileLoc loc) = 0; virtual void ConcatTransform(Float transform\[16\], FileLoc loc) = 0; virtual void [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) (Float transform\[16\], FileLoc loc) = 0; virtual void CoordinateSystem(const std::string &, FileLoc loc) = 0; virtual void CoordSysTransform(const std::string &, FileLoc loc) = 0; virtual void ActiveTransformAll(FileLoc loc) = 0; virtual void ActiveTransformEndTime(FileLoc loc) = 0; virtual void ActiveTransformStartTime(FileLoc loc) = 0; virtual void TransformTimes(Float start, Float end, FileLoc loc) = 0; virtual void ColorSpace(const std::string &n, FileLoc loc) = 0; virtual void PixelFilter(const std::string &name, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0; virtual void [Film](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#Film) (const std::string &type, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0; virtual void Accelerator(const std::string &name, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0; virtual void [Integrator](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#Integrator) (const std::string &name, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0; virtual void [Camera](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#Camera) (const std::string &, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0; virtual void MakeNamedMedium(const std::string &name, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0; virtual void [MediumInterface](https://pbr-book.org/4ed/Volume_Scattering/Media.html#MediumInterface) (const std::string &insideName, const std::string &outsideName, FileLoc loc) = 0; virtual void [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) (const std::string &name, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0; virtual void WorldBegin(FileLoc loc) = 0; virtual void AttributeBegin(FileLoc loc) = 0; virtual void AttributeEnd(FileLoc loc) = 0; virtual void Attribute(const std::string &target, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0; virtual void Texture(const std::string &name, const std::string &type, const std::string &texname, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0; virtual void [Material](https://pbr-book.org/4ed/Textures_and_Materials/Material_Interface_and_Implementations.html#Material) (const std::string &name, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0; virtual void MakeNamedMaterial(const std::string &name, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0; virtual void NamedMaterial(const std::string &name, FileLoc loc) = 0; virtual void LightSource(const std::string &name, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0; virtual void AreaLightSource(const std::string &name, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0; virtual void ReverseOrientation(FileLoc loc) = 0; virtual void ObjectBegin(const std::string &name, FileLoc loc) = 0; virtual void ObjectEnd(FileLoc loc) = 0; virtual void ObjectInstance(const std::string &name, FileLoc loc) = 0; virtual void EndOfFiles() = 0;

protected: <<ParserTarget Protected Methods>>

template <typename... Args> void ErrorExitDeferred(const char \*fmt, Args &&... args) const { errorExit = true; Error(fmt, std::forward<Args>(args)...); } template <typename... Args> void ErrorExitDeferred(const FileLoc \*loc, const char \*fmt, Args &&... args) const { errorExit = true; Error(loc, fmt, std::forward<Args>(args)...); } mutable bool errorExit = false;

};

For example, given the statement

Scale 2 2 4

in a scene description file, the parsing code will call its ParserTarget 's Scale() method.

<<ParserTarget Interface>>=

virtual void Scale(Float sx, Float sy, Float sz, FileLoc loc) = 0;

The provided FileLoc records the location of the corresponding statement in a file. If it is passed to the [Warning()](https://pbr-book.org/4ed/Utilities/User_Interaction.html#Warning), [Error()](https://pbr-book.org/4ed/Utilities/User_Interaction.html#Error), and [ErrorExit()](https://pbr-book.org/4ed/Utilities/User_Interaction.html#ErrorExit) functions, the resulting message includes this information so that it is easier for users to fix errors in their scene files.

<<FileLoc Definition>>=

struct FileLoc { std::string\_view filename; int line = 1, column = 0; };

Specifying ParserTarget as an abstract base class makes it easy to do a variety of things while parsing pbrt scene descriptions. For example, there is a FormattingParserTarget implementation of the ParserTarget interface that pretty-prints scene files and can upgrade scene files from the previous version of pbrt to conform to the current implementation's syntax. (FormattingParserTarget is not described any further in the book.) Section [C.2](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#sec:basic-scene-builder) will describe the [BasicSceneBuilder](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#BasicSceneBuilder) class, which also inherits from ParserTarget and builds an in-memory representation of the parsed scene.

pbrt 's scene description is easy to convert into tokens. Its salient properties are:

- Individual tokens are separated by whitespace.
- Strings are delimited using double quotes.
- One-dimensional arrays of values can be specified using square brackets: \[ \].
- Comments start with a hash character, #, and continue to the end of the current line.

We have not included pbrt 's straightforward tokenizer in the book text. (See the Tokenizer class in [parser.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/parser.h) and [parser.cpp](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/parser.cpp) for its implementation.)

Given a stream of tokens, the next task is parsing them. Some scene file statements have a fixed format (e.g., Scale, which expects three numeric values to follow). For each of those, the parser has fixed logic that looks for the expected number of values and checks that they have the correct types, issuing an error message if they are deficient. Other statements take lists of named parameters and values:

Shape "sphere" "float radius" 10 "float zmin" 0

Such named parameter lists are encoded by the parser in instances of the ParsedParameterVector class that are passed to [ParserTarget](#ParserTarget) interface methods. For example, the signature for the Shape() interface method is:

<<ParserTarget Interface>>+=

virtual void [Shape](https://pbr-book.org/4ed/Shapes/Basic_Shape_Interface.html#Shape) (const std::string &name, [ParsedParameterVector](#ParsedParameterVector) params, FileLoc loc) = 0;

One might ask: why tokenize and parse the files using a custom implementation and not use lexer and parser generators like flex, bison, or antlr? In fact, previous versions of pbrt did use flex and bison. However, when investigating pbrt 's performance in loading multi-gigabyte scene description files when rendering Disney's *Moana Island* scene ([Walt Disney Animation Studios 2018](https://pbr-book.org/4ed/Processing_the_Scene_Description/Further_Reading.html#cite:DisneyMoana)), we found that a substantial fraction of execution time was spent in the mechanics of parsing. Replacing that part of the system with a custom implementation substantially improved parsing performance. A secondary advantage of not using those tools is that doing so makes it easier to build pbrt on a variety of systems by eliminating the requirement of ensuring that they are installed.

ParsedParameterVector uses [InlinedVector](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#InlinedVector) to store a vector of parameters, avoiding the performance cost of dynamic allocation that comes with std::vector in the common case of a handful of parameters.

<<ParsedParameterVector Definition>>=

using ParsedParameterVector = [InlinedVector](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#InlinedVector) < [ParsedParameter](#ParsedParameter) \*, 8>;

<<ParsedParameter Definition>>=

class ParsedParameter { public: <<ParsedParameter Public Methods>>

ParsedParameter(FileLoc loc): loc(loc) {} void AddFloat(Float v); void AddInt(int i); void AddString(std::string\_view str); void AddBool(bool v); std::string ToString() const;

<< [ParsedParameter Public Members](#fragment-ParsedParameterPublicMembers-0) >>

std::string type, name; FileLoc loc; pstd::vector<Float> floats; pstd::vector<int> ints; pstd::vector<std::string> strings; pstd::vector<uint8\_t> bools; mutable bool lookedUp = false;

};

[ParsedParameter](#ParsedParameter) provides the parameter type and name as strings as well as the location of the parameter in the scene description file. For the first parameter in the sphere example above, type would store "float" and name would store "radius". Note that the parser makes no effort to ensure that the type is valid or that the parameter name is used by the corresponding statement; those checks are handled subsequently.

<<ParsedParameter Public Members>>=

std::string type, name; FileLoc loc;

Parameter values are provided in one of four formats, corresponding to the basic types used for parameter values in scene description files. (Values for higher-level parameter types like point3 are subsequently constructed from the corresponding basic type.) Exactly one of the following vectors will be non-empty in each provided [ParsedParameter](#ParsedParameter).

As before, the parser makes no effort to validate these—for example, if the user has provided string values for a parameter with "float" type, those values will be provided in strings with no complaint (yet).

pstd::vector<Float> floats; pstd::vector<int> ints; pstd::vector<std::string> strings; pstd::vector<uint8\_t> bools;

The lookedUp member variable is provided for the code related to extracting parameter values. It makes it easy to issue an error message if any provided parameters were not actually used by pbrt, which generally indicates a misspelling or other user error.

<<ParsedParameter Public Members>>+=

mutable bool lookedUp = false;

We will not discuss the remainder of the methods in the [ParserTarget](#ParserTarget) interface here, though we will see more of them in the [BasicSceneBuilder](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#BasicSceneBuilder) methods that implement them in Sections [C.2.3](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#sec:graphics-state) and [C.2.4](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#sec:specifying-scene-elements).