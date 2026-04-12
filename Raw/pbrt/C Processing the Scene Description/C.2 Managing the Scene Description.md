---
title: "Managing the Scene Description"
source: "https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## C.2 Managing the Scene Description
> ## C.2 管理场景描述

pbrt 's scene description files allow the user to specify various properties that then apply to the definition of subsequent objects in the scene.
> pbrt 的场景描述文件允许用户指定各种属性，这些属性随后适用于场景中后续对象的定义。 One example is a current material. Once the current material is set, all subsequent shapes are assigned that material until it is changed. In addition to the material, the current transformation matrix, RGB color space, an area light specification, and the current media are similarly maintained. We will call this collective information the *graphics state*. Tracking graphics state provides the advantage that it is not necessary to specify a material with every shape in the scene description, but it imposes the requirement that the scene processing code keep track of the current graphics state while the scene description is being parsed.

Managing this graphics state is the primary task of the BasicSceneBuilder, which implements the interface defined by [ParserTarget](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParserTarget). Its implementation is in the files [scene.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/scene.h) and [scene.cpp](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/scene.cpp). An initial [BasicSceneBuilder](#BasicSceneBuilder) is allocated at the start of parsing the scene description. Typically, it handles graphics state management for the provided scene description files. However, pbrt 's scene description format supports an Import directive that indicates that a file can be parsed in parallel with the file that contains it. (Import effectively discards any changes to the graphics state at the end of an imported file, which allows parsing of the current file to continue concurrently without needing to wait for the imported file.) A new [BasicSceneBuilder](#BasicSceneBuilder) is allocated for each imported file; it makes a copy of the current graphics state before parsing begins.

<<BasicSceneBuilder Definition>>=

class BasicSceneBuilder: public [ParserTarget](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParserTarget) { public: <<BasicSceneBuilder Public Methods>>

BasicSceneBuilder(BasicScene \*scene); void Option(const std::string &name, const std::string &value, FileLoc loc); void Identity(FileLoc loc); void Translate(Float dx, Float dy, Float dz, FileLoc loc); void Rotate(Float angle, Float ax, Float ay, Float az, FileLoc loc); void Scale(Float sx, Float sy, Float sz, FileLoc loc); void LookAt(Float ex, Float ey, Float ez, Float lx, Float ly, Float lz, Float ux, Float uy, Float uz, FileLoc loc); void ConcatTransform(Float transform\[16\], FileLoc loc); void [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) (Float transform\[16\], FileLoc loc); void CoordinateSystem(const std::string &, FileLoc loc); void CoordSysTransform(const std::string &, FileLoc loc); void ActiveTransformAll(FileLoc loc); void ActiveTransformEndTime(FileLoc loc); void ActiveTransformStartTime(FileLoc loc); void TransformTimes(Float start, Float end, FileLoc loc); void ColorSpace(const std::string &n, FileLoc loc); void PixelFilter(const std::string &name, ParsedParameterVector params, FileLoc loc); void [Film](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#Film) (const std::string &type, ParsedParameterVector params, FileLoc loc); void [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) (const std::string &name, ParsedParameterVector params, FileLoc loc); void Accelerator(const std::string &name, ParsedParameterVector params, FileLoc loc); void [Integrator](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#Integrator) (const std::string &name, ParsedParameterVector params, FileLoc loc); void [Camera](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#Camera) (const std::string &, ParsedParameterVector params, FileLoc loc); void MakeNamedMedium(const std::string &name, ParsedParameterVector params, FileLoc loc); void [MediumInterface](https://pbr-book.org/4ed/Volume_Scattering/Media.html#MediumInterface) (const std::string &insideName, const std::string &outsideName, FileLoc loc); void WorldBegin(FileLoc loc); void AttributeBegin(FileLoc loc); void AttributeEnd(FileLoc loc); void Attribute(const std::string &target, ParsedParameterVector params, FileLoc loc); void Texture(const std::string &name, const std::string &type, const std::string &texname, ParsedParameterVector params, FileLoc loc); void [Material](https://pbr-book.org/4ed/Textures_and_Materials/Material_Interface_and_Implementations.html#Material) (const std::string &name, ParsedParameterVector params, FileLoc loc); void MakeNamedMaterial(const std::string &name, ParsedParameterVector params, FileLoc loc); void NamedMaterial(const std::string &name, FileLoc loc); void LightSource(const std::string &name, ParsedParameterVector params, FileLoc loc); void AreaLightSource(const std::string &name, ParsedParameterVector params, FileLoc loc); void [Shape](https://pbr-book.org/4ed/Shapes/Basic_Shape_Interface.html#Shape) (const std::string &name, ParsedParameterVector params, FileLoc loc); void ReverseOrientation(FileLoc loc); void ObjectBegin(const std::string &name, FileLoc loc); void ObjectEnd(FileLoc loc); void ObjectInstance(const std::string &name, FileLoc loc); void EndOfFiles(); BasicSceneBuilder \*CopyForImport(); void MergeImported(BasicSceneBuilder \*); std::string ToString() const;

private: << [BasicSceneBuilder::GraphicsState Definition](#fragment-BasicSceneBuilder::GraphicsStateDefinition-0) >>

struct GraphicsState { << [GraphicsState Public Methods](#fragment-GraphicsStatePublicMethods-0) >>

GraphicsState(); template <typename F> void ForActiveTransforms(F func) { for (int i = 0; i < MaxTransforms; ++i) if ([activeTransformBits](#GraphicsState::activeTransformBits) & (1 << i)) [ctm](#GraphicsState::ctm) \[i\] = func([ctm](#GraphicsState::ctm) \[i\]); }

<< [GraphicsState Public Members](#fragment-GraphicsStatePublicMembers-0) >>

std::string currentInsideMedium, currentOutsideMedium; int currentMaterialIndex = 0; std::string currentMaterialName; std::string areaLightName; ParameterDictionary areaLightParams; FileLoc areaLightLoc; ParsedParameterVector shapeAttributes; ParsedParameterVector lightAttributes; ParsedParameterVector materialAttributes; ParsedParameterVector mediumAttributes; ParsedParameterVector textureAttributes; bool reverseOrientation = false; const [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace) \*colorSpace = [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace)::sRGB; TransformSet ctm; uint32\_t activeTransformBits = AllTransformsBits; Float transformStartTime = 0, transformEndTime = 1;

};

<< [BasicSceneBuilder Private Methods](#fragment-BasicSceneBuilderPrivateMethods-0) >>

class [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) [RenderFromObject](#BasicSceneBuilder::RenderFromObject) (int index) const { return pbrt::[Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) (([renderFromWorld](#BasicSceneBuilder::renderFromWorld) \* [graphicsState](#BasicSceneBuilder::graphicsState).ctm\[index\]).[GetMatrix](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform::GetMatrix) ()); } AnimatedTransform [RenderFromObject](#BasicSceneBuilder::RenderFromObject) () const { return { [RenderFromObject](#BasicSceneBuilder::RenderFromObject) (0), [graphicsState](#BasicSceneBuilder::graphicsState).[transformStartTime](#GraphicsState::transformStartTime), [RenderFromObject](#BasicSceneBuilder::RenderFromObject) (1), [graphicsState](#BasicSceneBuilder::graphicsState).[transformEndTime](#GraphicsState::transformEndTime) }; } bool CTMIsAnimated() const { return [graphicsState](#BasicSceneBuilder::graphicsState).ctm.IsAnimated(); }

<< [BasicSceneBuilder Private Members](#fragment-BasicSceneBuilderPrivateMembers-0) >>

[BasicScene](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#BasicScene) \*scene; enum class BlockState { OptionsBlock, WorldBlock }; BlockState currentBlock = BlockState::OptionsBlock; [GraphicsState](#BasicSceneBuilder::GraphicsState) graphicsState; static constexpr int StartTransformBits = 1 << 0; static constexpr int EndTransformBits = 1 << 1; static constexpr int AllTransformsBits = (1 << MaxTransforms) - 1; std::map<std::string, TransformSet> namedCoordinateSystems; class [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) renderFromWorld; [InternCache](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#InternCache) <class [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) > transformCache; std::vector< [GraphicsState](#BasicSceneBuilder::GraphicsState) > pushedGraphicsStates; struct ActiveInstanceDefinition { ActiveInstanceDefinition(std::string name, FileLoc loc): entity(name, loc){} std::mutex mutex; std::atomic<int> activeImports{1}; InstanceDefinitionSceneEntity entity; ActiveInstanceDefinition \*parent = nullptr; }; ActiveInstanceDefinition \*activeInstanceDefinition = nullptr; // Buffer these both to avoid mutex contention and so that they are // consistently ordered across runs. std::vector<ShapeSceneEntity> shapes; std::vector<InstanceSceneEntity> instanceUses; std::set<std::string> namedMaterialNames, mediumNames; std::set<std::string> floatTextureNames, spectrumTextureNames, instanceNames; int currentMaterialIndex = 0, currentLightIndex = -1; [SceneEntity](#SceneEntity) sampler; [SceneEntity](#SceneEntity) film, integrator, filter, accelerator; CameraSceneEntity camera;

};

As the entities in the scene are fully specified, they are passed along to an instance of the [BasicScene](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#BasicScene) class, which will be described in the next section. When parsing is being performed in parallel with multiple [BasicSceneBuilder](#BasicSceneBuilder) s, all share a single [BasicScene](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#BasicScene).

<<BasicSceneBuilder Private Members>>=

[BasicScene](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#BasicScene) \*scene;

In addition to storing a pointer to a BasicScene, the BasicSceneBuilder constructor sets a few default values so that if, for example, no camera is specified in the scene description, a basic 90 degree perspective camera is used. The fragment that sets these values is not included here.

<<BasicSceneBuilder Method Definitions>>=

BasicSceneBuilder::BasicSceneBuilder([BasicScene](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#BasicScene) \*scene): scene(scene) { <<Set scene defaults>>

camera.name = SceneEntity::internedStrings.Lookup("perspective"); sampler.name = SceneEntity::internedStrings.Lookup("zsobol"); filter.name = SceneEntity::internedStrings.Lookup("gaussian"); integrator.name = SceneEntity::internedStrings.Lookup("volpath"); accelerator.name = SceneEntity::internedStrings.Lookup("bvh"); film.name = SceneEntity::internedStrings.Lookup("rgb"); film.parameters = ParameterDictionary({}, [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace)::sRGB); ParameterDictionary dict({}, [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace)::sRGB); currentMaterialIndex = scene->AddMaterial(SceneEntity("diffuse", dict, {}));

}

pbrt scene descriptions are split into sections by the WorldBegin statement. Before WorldBegin is encountered, it is legal to specify global rendering options including the camera, film, sampler, and integrator, but shapes, lights, textures, and materials cannot yet be specified. After WorldBegin, all of that flips: things like the camera specification are fixed, and the rest of the scene can be specified. Some scene description statements, like those that modify the current transformation or specify participating media, are allowed in both contexts.

This separation of information can help simplify the implementation of the renderer. For example, consider a spline patch shape that tessellates itself into triangles. This shape might compute the size of its triangles based on the area of the screen that it covers. If the camera's position and the image resolution are fixed when the shape is created, then the shape can tessellate itself immediately at creation time.

An enumeration records which part of the scene description is currently being specified. Two macros that are not included here, VERIFY\_OPTIONS() and VERIFY\_WORLD(), check the current block against the one that is expected and issue an error if there is a mismatch.

enum class BlockState { OptionsBlock, WorldBlock }; BlockState currentBlock = BlockState::OptionsBlock;

### C.2.1 Scene Entities

Before further describing the BasicSceneBuilder 's operation, we will start by describing the form of its output, which is a high-level representation of the parsed scene. In this representation, all the objects in the scene are represented by various \*Entity classes.

SceneEntity is the simplest of them; it records the name of the entity (e.g., "rgb" or "gbuffer" for the film), the file location of the associated statement in the scene description, and any user-provided parameters. It is used for the film, sampler, integrator, pixel filter, and accelerator, and is also used as a base class for some of the other scene entity types.

<<SceneEntity Definition>>=

struct SceneEntity { <<SceneEntity Public Methods>>

SceneEntity() = default; SceneEntity(const std::string &name, ParameterDictionary parameters, FileLoc loc): name(internedStrings.Lookup(name)), parameters(parameters), loc(loc) {} std::string ToString() const { return StringPrintf("\[ SceneEntity name: %s parameters: %s loc: %s \]", name, parameters, loc); }

<< [SceneEntity Public Members](#fragment-SceneEntityPublicMembers-0) >>

[InternedString](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#InternedString) name; FileLoc loc; [ParameterDictionary](#ParameterDictionary) parameters; static [InternCache](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#InternCache) <std::string> internedStrings;

};

All the scene entity objects use [InternedString](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#InternedString) s for any string member variables to save memory when strings are repeated. (Often many are, including frequently used shape names like "trianglemesh" and the names of object instances that are used repeatedly.)

<<SceneEntity Public Members>>=

[InternedString](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#InternedString) name; FileLoc loc; [ParameterDictionary](#ParameterDictionary) parameters;

A single [InternCache](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#InternCache) defined as a public static member in SceneEntity is used for all string interning in this part of the system.

<<SceneEntity Public Members>>+=

static [InternCache](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#InternCache) <std::string> internedStrings;

Other entity types include the CameraSceneEntity, LightSceneEntity, TextureSceneEntity, MediumSceneEntity, ShapeSceneEntity, and AnimatedShapeSceneEntity. All have the obvious roles. There is furthermore an InstanceDefinitionSceneEntity, which represents an instance definition, and InstanceSceneEntity, which represents the use of an instance definition. We will not include the definitions of these classes in the text as they are all easily understood from their definitions in the source code.

### C.2.2 Parameter Dictionaries

Most of the scene entity objects store lists of associated parameters from the scene description file. While the [ParsedParameter](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter) is a convenient representation for the parser to generate, it does not provide capabilities for checking the validity of parameters or for easily extracting parameter values. To that end, [ParameterDictionary](#ParameterDictionary) adds both semantics and convenience to vectors of [ParsedParameter](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter) s. Thus, it is the class that is used for [SceneEntity::parameters](#SceneEntity::parameters).

<<ParameterDictionary Definition>>=

class ParameterDictionary { public: << [ParameterDictionary Public Methods](#fragment-ParameterDictionaryPublicMethods-0) >>

ParameterDictionary([ParsedParameterVector](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameterVector) params, const [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace) \*colorSpace); ParameterDictionary([ParsedParameterVector](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameterVector) params0, const [ParsedParameterVector](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameterVector) &params1, const [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace) \*colorSpace); std::string GetTexture(const std::string &name) const; std::vector< [RGB](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGB) > GetRGBArray(const std::string &name) const; // For --upgrade only pstd::optional< [RGB](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGB) > GetOneRGB(const std::string &name) const; // Unfortunately, this is most easily done here... Float UpgradeBlackbody(const std::string &name); void RemoveFloat(const std::string &); void RemoveInt(const std::string &); void RemoveBool(const std::string &); void RemovePoint2f(const std::string &); void RemoveVector2f(const std::string &); void RemovePoint3f(const std::string &); void RemoveVector3f(const std::string &); void RemoveNormal3f(const std::string &); void RemoveString(const std::string &); void RemoveTexture(const std::string &); void RemoveSpectrum(const std::string &); void RenameParameter(const std::string &before, const std::string &after); void RenameUsedTextures(const std::map<std::string, std::string> &m); const [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace) \*ColorSpace() const { return colorSpace; } std::string ToParameterList(int indent = 0) const; std::string ToParameterDefinition(const std::string &) const; std::string ToString() const; const FileLoc \*loc(const std::string &) const; const [ParsedParameterVector](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameterVector) &GetParameterVector() const { return params; } void FreeParameters(); Float GetOneFloat(const std::string &name, Float def) const; int GetOneInt(const std::string &name, int def) const; bool GetOneBool(const std::string &name, bool def) const; std::string GetOneString(const std::string &name, const std::string &def) const; [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) GetOnePoint2f(const std::string &name, [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) def) const; [Vector2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector2f) GetOneVector2f(const std::string &name, [Vector2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector2f) def) const; [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) GetOnePoint3f(const std::string &name, [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) def) const; [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) GetOneVector3f(const std::string &name, [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) def) const; [Normal3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Normals.html#Normal3f) GetOneNormal3f(const std::string &name, [Normal3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Normals.html#Normal3f) def) const; [Spectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#Spectrum) GetOneSpectrum(const std::string &name, [Spectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#Spectrum) def, [SpectrumType](#SpectrumType) spectrumType, Allocator alloc) const; std::vector<Float> GetFloatArray(const std::string &name) const; std::vector<int> GetIntArray(const std::string &name) const; std::vector<uint8\_t> GetBoolArray(const std::string &name) const; std::vector< [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) > GetPoint2fArray(const std::string &name) const; std::vector< [Vector2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector2f) > GetVector2fArray(const std::string &name) const; std::vector< [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) > GetPoint3fArray(const std::string &name) const; std::vector< [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) > GetVector3fArray(const std::string &name) const; std::vector< [Normal3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Normals.html#Normal3f) > GetNormal3fArray(const std::string &name) const; std::vector< [Spectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#Spectrum) > GetSpectrumArray(const std::string &name, [SpectrumType](#SpectrumType) spectrumType, Allocator alloc) const; std::vector<std::string> GetStringArray(const std::string &name) const; void ReportUnused() const;

private: <<ParameterDictionary Private Methods>>

template <ParameterType PT> typename ParameterTypeTraits<PT>::ReturnType lookupSingle( const std::string &name, typename ParameterTypeTraits<PT>::ReturnType defaultValue) const; template <ParameterType PT> std::vector<typename ParameterTypeTraits<PT>::ReturnType> lookupArray( const std::string &name) const; template <typename ReturnType, typename G, typename C> std::vector<ReturnType> lookupArray(const std::string &name, ParameterType type, const char \*typeName, int nPerItem, G getValues, C convert) const; std::vector< [Spectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#Spectrum) > extractSpectrumArray(const ParsedParameter &param, SpectrumType spectrumType, Allocator alloc) const; void remove(const std::string &name, const char \*typeName); void checkParameterTypes(); static std::string ToParameterDefinition(const ParsedParameter \*p, int indentCount);

<< [ParameterDictionary Private Members](#fragment-ParameterDictionaryPrivateMembers-0) >>

[ParsedParameterVector](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameterVector) params; const [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace) \*colorSpace = nullptr; int nOwnedParams;

};

Its constructor takes both a [ParsedParameterVector](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameterVector) and an [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace) that defines the color space of any RGB-valued parameters.

<<ParameterDictionary Public Methods>>=

ParameterDictionary([ParsedParameterVector](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameterVector) params, const [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace) \*colorSpace);

It directly stores the provided [ParsedParameterVector](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameterVector); no preprocessing of it is performed in the constructor—for example, to sort the parameters by name or to validate that the parameters are valid. An implication of this is that the following methods that look up parameter values have time complexity in the total number of parameters. For the small numbers of parameters that are provided in practice, this inefficiency is not a concern.

<<ParameterDictionary Private Members>>=

[ParsedParameterVector](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameterVector) params; const [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace) \*colorSpace = nullptr;

A [ParameterDictionary](#ParameterDictionary) can hold eleven types of parameters: Booleans, integers, floating-point values, points (2D and 3D), vectors (2D and 3D), normals, spectra, strings, and the names of [Texture](https://pbr-book.org/4ed/Textures_and_Materials/Texture_Interface_and_Basic_Textures.html#Texture) s that are used as parameters for [Material](https://pbr-book.org/4ed/Textures_and_Materials/Material_Interface_and_Implementations.html#Material) s and other [Texture](https://pbr-book.org/4ed/Textures_and_Materials/Texture_Interface_and_Basic_Textures.html#Texture) s. An enumeration of these types will be useful in the following.

<<ParameterType Definition>>=

enum class ParameterType { Boolean, Float, Integer, [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f), [Vector2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector2f), [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f), [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f), [Normal3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Normals.html#Normal3f), [Spectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#Spectrum), String, Texture };

For each parameter type, there is a method for looking up parameters that have a single data value. Here are the declarations of a few:

Float GetOneFloat(const std::string &name, Float def) const; int GetOneInt(const std::string &name, int def) const; bool GetOneBool(const std::string &name, bool def) const; std::string GetOneString(const std::string &name, const std::string &def) const;

These methods all take the name of the parameter and a default value. If the parameter is not found, the default value is returned. This makes it easy to write initialization code like:

Point3f center = params.GetOnePoint3f("center", Point3f(0, 0, 0));

The single value lookup methods for the other types follow the same form and so their declarations are not included here.

In contrast, if calling code wants to detect a missing parameter and issue an error, it should instead use the corresponding parameter array lookup method, which returns an empty vector if the parameter is not present. (Those methods will be described in a few pages.)

For parameters that represent spectral distributions, it is necessary to specify if the spectrum represents an illuminant, a reflectance that is bounded between 0 and 1, or is an arbitrary spectral distribution (e.g., a scattering coefficient). In turn, if a parameter has been specified using RGB color, the appropriate one of [RGBIlluminantSpectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBIlluminantSpectrum), [RGBAlbedoSpectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBAlbedoSpectrum), or [RGBUnboundedSpectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBUnboundedSpectrum) is used for the returned [Spectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#Spectrum).

[Spectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#Spectrum) GetOneSpectrum(const std::string &name, [Spectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#Spectrum) def, [SpectrumType](#SpectrumType) spectrumType, Allocator alloc) const;

<<SpectrumType Definition>>=

enum class SpectrumType { Illuminant, Albedo, Unbounded };

The parameter lookup methods make use of C++ type traits, which make it possible to associate additional information with specific types that can then be accessed at compile time via templates. This approach allows succinct implementations of the lookup methods. Here we will discuss the corresponding implementation for Point3f -valued parameters; the other types are analogous.

The implementation of GetOnePoint3f() requires a single line of code to forward the request on to the lookupSingle() method.

<<ParameterDictionary Method Definitions>>=

[Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) ParameterDictionary::GetOnePoint3f(const std::string &name, [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) def) const { return [lookupSingle](#ParameterDictionary::lookupSingle) < [ParameterType](#ParameterType::Point3f)::[Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) >(name, def); }

The following signature of the lookupSingle() method alone has brought us into the realm of template-based type information. lookupSingle() is itself a template method, parameterized by an instance of the [ParameterType](#ParameterType) enumeration. In turn, we can see that another template class, [ParameterTypeTraits](#ParameterTypeTraits), not yet defined, is expected to provide the type ReturnType, which is used for both lookupSingle 's return type and the provided default value.

<<ParameterDictionary Method Definitions>>+=

template <ParameterType PT> typename ParameterTypeTraits<PT>::ReturnType ParameterDictionary::lookupSingle(const std::string &name, typename ParameterTypeTraits<PT>::ReturnType defaultValue) const { << [Search params for parameter name](#fragment-Searchmonoparamsforparametermononame-0) >>

using traits = ParameterTypeTraits<PT>; for (const [ParsedParameter](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter) \*p: params) { if (p-> [name](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::name)!= [name](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::name) || p-> [type](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::type)!= traits::typeName) continue; << [Extract parameter values from p](#fragment-Extractparametervaluesfrommonop-0) >>

const auto &values = traits::GetValues(\*p);

<< [Issue error if an incorrect number of parameter values were provided](#fragment-Issueerrorifanincorrectnumberofparametervalueswereprovided-0) >>

if (values.empty()) [ErrorExit](https://pbr-book.org/4ed/Utilities/User_Interaction.html#ErrorExit) (&p-> [loc](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::loc), "No values provided for parameter \\"%s\\".", name); if (values.size()!= traits::nPerItem) [ErrorExit](https://pbr-book.org/4ed/Utilities/User_Interaction.html#ErrorExit) (&p-> [loc](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::loc), "Expected %d values for parameter \\"%s\\".", traits::nPerItem, name);

<< [Return parameter values as ReturnType](#fragment-ReturnparametervaluesasmonoReturnType-0) >>

p-> [lookedUp](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::lookedUp) = true; return traits::Convert(values.data(), &p-> [loc](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::loc));

}

return defaultValue; }

Each of the parameter types in the [ParameterType](#ParameterType) enumeration has a ParameterTypeTraits template specialization. Here is the one for Point3f:

<<Point3f ParameterTypeTraits Definition>>=

template <> struct ParameterTypeTraits< [ParameterType](#ParameterType::Point3f)::[Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) > { << [ParameterType::Point3f Type Traits](#fragment-ParameterType::Point3fTypeTraits-0) >>

using ReturnType = [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f); static constexpr char typeName\[\] = "point3"; static const auto &GetValues(const [ParsedParameter](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter) &param) { return param.[floats](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::floats); } static constexpr int nPerItem = 3; static [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) Convert(const Float \*f, const FileLoc \*loc) { return [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) (f\[0\], f\[1\], f\[2\]); }

};

All the specializations provide a type definition for ReturnType. Naturally, the ParameterType::Point3f specialization uses [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) for ReturnType.

<<ParameterType::Point3f Type Traits>>=

using ReturnType = [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f);

Type traits also provide the string name for each type.

static constexpr char typeName\[\] = "point3";

In turn, the search for a parameter checks not only for the specified parameter name but also for a matching type string.

<<Search params for parameter name >>=

using traits = ParameterTypeTraits<PT>; for (const [ParsedParameter](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter) \*p: params) { if (p-> [name](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::name)!= [name](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::name) || p-> [type](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::type)!= traits::typeName) continue; << [Extract parameter values from p](#fragment-Extractparametervaluesfrommonop-0) >>

const auto &values = traits::GetValues(\*p);

<< [Issue error if an incorrect number of parameter values were provided](#fragment-Issueerrorifanincorrectnumberofparametervalueswereprovided-0) >>

if (values.empty()) [ErrorExit](https://pbr-book.org/4ed/Utilities/User_Interaction.html#ErrorExit) (&p-> [loc](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::loc), "No values provided for parameter \\"%s\\".", name); if (values.size()!= traits::nPerItem) [ErrorExit](https://pbr-book.org/4ed/Utilities/User_Interaction.html#ErrorExit) (&p-> [loc](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::loc), "Expected %d values for parameter \\"%s\\".", traits::nPerItem, name);

<< [Return parameter values as ReturnType](#fragment-ReturnparametervaluesasmonoReturnType-0) >>

p-> [lookedUp](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::lookedUp) = true; return traits::Convert(values.data(), &p-> [loc](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::loc));

}

A static GetValues() method in each type traits template specialization returns a reference to one of the floats, ints, strings, or bools [ParsedParameter](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter) member variables. Note that using auto for the declaration of values makes it possible for this code in lookupSingle() to work with any of those.

<<Extract parameter values from p >>=

const auto &values = traits::GetValues(\*p);

For Point3f parameters, the parameter values are floating-point.

static const auto &GetValues(const [ParsedParameter](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter) &param) { return param.[floats](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::floats); }

Another trait, nPerItem, provides the number of individual values associated with each parameter. In addition to making it possible to check that the right number of values were provided in the GetOne\*() methods, this value is also used when parsing arrays of parameter values.

<<Issue error if an incorrect number of parameter values were provided>>=

if (values.empty()) [ErrorExit](https://pbr-book.org/4ed/Utilities/User_Interaction.html#ErrorExit) (&p-> [loc](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::loc), "No values provided for parameter \\"%s\\".", name); if (values.size()!= traits::nPerItem) [ErrorExit](https://pbr-book.org/4ed/Utilities/User_Interaction.html#ErrorExit) (&p-> [loc](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::loc), "Expected %d values for parameter \\"%s\\".", traits::nPerItem, name);

For each [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f), three values are expected.

static constexpr int nPerItem = 3;

Finally, a static Convert() method in the type traits specialization takes care of converting from the raw values to the returned parameter type. At this point, the fact that the parameter was in fact used is also recorded.

<<Return parameter values as ReturnType >>=

p-> [lookedUp](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::lookedUp) = true; return traits::Convert(values.data(), &p-> [loc](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::loc));

The Convert() method converts the parameter values, starting at a given location, to the return type. When arrays of values are returned, this method is called once per returned array element, with the pointer incremented after each one by the type traits nPerItem value. The current FileLoc is passed along to this method in case any errors need to be reported.

<<ParameterType::Point3f Type Traits>>+=

static [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) Convert(const Float \*f, const FileLoc \*loc) { return [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) (f\[0\], f\[1\], f\[2\]); }

Implementing the parameter lookup methods via type traits is more complex than implementing each one directly would be. However, this approach has the advantage that each additional parameter type effectively only requires defining an appropriate [ParameterTypeTraits](#ParameterTypeTraits) specialization, which is just a few lines of code. Further, that additional code is mostly declarative, which in turn is easier to verify as correct than multiple independent implementations of parameter processing logic.

The second set of parameter lookup functions returns an array of values. An empty vector is returned if the parameter is not found, so no default value need be provided by the caller. Here are the declarations of a few of them. The rest are equivalent, though GetSpectrumArray() also takes a [SpectrumType](#SpectrumType) and an [Allocator](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#Allocator) to use for allocating any returned [Spectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#Spectrum) values.

std::vector<Float> GetFloatArray(const std::string &name) const; std::vector<int> GetIntArray(const std::string &name) const; std::vector<uint8\_t> GetBoolArray(const std::string &name) const;

We will not include the implementations of any of the array lookup methods or the type traits for the other parameter types here. We also note that the methods corresponding to [Spectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#Spectrum) parameters are more complex than the other ones, since spectral distributions may be specified in a number of different ways, including as RGB colors, blackbody emission temperatures, and spectral distributions stored in files; see the source code for details.

Finally, because the user may misspell parameter names in the scene description file, the [ParameterDictionary](#ParameterDictionary) also provides a ReportUnused() function that issues an error if any of the parameters present were never looked up; the assumption is that in that case the user has provided an incorrect parameter. (This check is based on the values of the [ParsedParameter::lookedUp](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameter::lookedUp) member variables.)

<<ParameterDictionary Public Methods>>+=

void ReportUnused() const;

### C.2.3 Tracking Graphics State

All the graphics state managed by the [BasicSceneBuilder](#BasicSceneBuilder) is stored in an instance of the GraphicsState class.

<<BasicSceneBuilder::GraphicsState Definition>>=

struct GraphicsState { << [GraphicsState Public Methods](#fragment-GraphicsStatePublicMethods-0) >>

GraphicsState(); template <typename F> void ForActiveTransforms(F func) { for (int i = 0; i < MaxTransforms; ++i) if ([activeTransformBits](#GraphicsState::activeTransformBits) & (1 << i)) [ctm](#GraphicsState::ctm) \[i\] = func([ctm](#GraphicsState::ctm) \[i\]); }

<< [GraphicsState Public Members](#fragment-GraphicsStatePublicMembers-0) >>

std::string currentInsideMedium, currentOutsideMedium; int currentMaterialIndex = 0; std::string currentMaterialName; std::string areaLightName; ParameterDictionary areaLightParams; FileLoc areaLightLoc; ParsedParameterVector shapeAttributes; ParsedParameterVector lightAttributes; ParsedParameterVector materialAttributes; ParsedParameterVector mediumAttributes; ParsedParameterVector textureAttributes; bool reverseOrientation = false; const [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace) \*colorSpace = [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace)::sRGB; TransformSet ctm; uint32\_t activeTransformBits = AllTransformsBits; Float transformStartTime = 0, transformEndTime = 1;

};

A GraphicsState instance is maintained in a member variable.

GraphicsState graphicsState;

There is usually not much to do when a statement that modifies the graphics state is encountered in a scene description file. Here, for example, is the implementation of the method that is called when the ReverseOrientation statement is parsed. This statement is only valid in the world block, so that state is checked before the graphics state's corresponding variable is updated.

<<GraphicsState Public Members>>=

bool reverseOrientation = false;

The current RGB color space can be specified in both the world and options blocks, so there is no need to check the value of currentBlock in the corresponding method.

void BasicSceneBuilder::ColorSpace(const std::string &name, FileLoc loc) { if (const [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace) \*cs = [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace)::GetNamed(name)) [graphicsState](#BasicSceneBuilder::graphicsState).[colorSpace](#GraphicsState::colorSpace) = cs; else [Error](https://pbr-book.org/4ed/Utilities/User_Interaction.html#Error) (&loc, "%s: color space unknown", name); }

const [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace) \*colorSpace = [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace)::sRGB;

Many of the other method implementations related to graphics state management are similarly simple, so we will only include a few of the interesting ones in the following.

#### Managing Transformations

The *current transformation matrix* (CTM) is a widely used part of the graphics state. Initially the identity matrix, the CTM is modified by statements like Translate and Scale in scene description files. When objects like shapes and lights are defined, the CTM gives the transformation between their object coordinate system and world space.

The current transformation matrix is actually a pair of transformation matrices, each one specifying a transformation at a specific time. If the transformations are different, then they describe an animated transformation. A number of methods are available to modify one or both of the CTMs as well as to specify the time associated with each one.

GraphicsState stores these two CTMs in a ctm member variable. They are represented by a TransformSet, which is a simple utility class that stores an array of transformations and provides some routines for managing them. Its methods include an operator\[\] for indexing into the [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) s, an Inverse() method that returns a TransformSet that is the inverse, and IsAnimated(), which indicates whether the two [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) s differ from each other.

The activeTransformBits member variable is a bit-vector indicating which of the CTMs are active; the active [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) s are updated when the transformation-related API calls are made, while the others are unchanged. This mechanism allows the user to selectively modify the CTMs in order to define animated transformations.

TransformSet ctm; uint32\_t activeTransformBits = AllTransformsBits;

static constexpr int StartTransformBits = 1 << 0; static constexpr int EndTransformBits = 1 << 1; static constexpr int AllTransformsBits = (1 << MaxTransforms) - 1;

Only two transformations are currently supported. An exercise at the end of this appendix is based on relaxing this constraint.

<<MaxTransforms Definition>>=

constexpr int MaxTransforms = 2;

The methods that are called when a change to the current transformation is specified in the scene description are all simple. Because the CTM is used for both the rendering options and the scene description sections, there is no need to check the value of currentBlock in them. Here is the method called for the Identity statement, which sets the CTM to the identity transform.

void BasicSceneBuilder::Identity(FileLoc loc) { [graphicsState](#BasicSceneBuilder::graphicsState).[ForActiveTransforms](#GraphicsState::ForActiveTransforms) ( \[\](auto t) { return pbrt::[Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) (); }); }

ForActiveTransforms() is a convenience method that encapsulates the logic for determining which of the CTMs is active and for passing their current value to a provided function that returns the updated transformation.

<<GraphicsState Public Methods>>=

template <typename F> void ForActiveTransforms(F func) { for (int i = 0; i < MaxTransforms; ++i) if ([activeTransformBits](#GraphicsState::activeTransformBits) & (1 << i)) [ctm](#GraphicsState::ctm) \[i\] = func([ctm](#GraphicsState::ctm) \[i\]); }

Translate() postmultiplies the active CTMs with specified translation transformation.

void BasicSceneBuilder::[Translate](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Translate) (Float dx, Float dy, Float dz, FileLoc loc) { [graphicsState](#BasicSceneBuilder::graphicsState).[ForActiveTransforms](#GraphicsState::ForActiveTransforms) ( \[=\](auto t) { return t \* pbrt::[Translate](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Translate) ([Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) (dx, dy, dz)); }); }

The rest of the transformation methods are similarly defined, so we will not show their definitions here.

RenderFromObject() is a convenience method that returns the rendering-from-object transformation for the specified transformation index. It is called, for example, when a shape is specified. In the world specification block, the CTM specifies the world-from-object transformation, but because pbrt performs rendering computation in a separately defined rendering coordinate system (recall Section [5.1.1](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#sec:camera-coordinate-spaces)), the rendering-from-world transformation must be included to get the full transformation.

<<BasicSceneBuilder Private Methods>>=

class [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) RenderFromObject(int index) const { return pbrt::[Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) (([renderFromWorld](#BasicSceneBuilder::renderFromWorld) \* [graphicsState](#BasicSceneBuilder::graphicsState).ctm\[index\]).[GetMatrix](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform::GetMatrix) ()); }

The camera-from-world transformation is given by the CTM when the camera is specified in the scene description. renderFromWorld is therefore set in the BasicSceneBuilder::Camera() method (not included here), via a call to the [CameraTransform::RenderFromWorld()](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#CameraTransform::RenderFromWorld) method with the [CameraTransform](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#CameraTransform) for the camera.

class [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) renderFromWorld;

A second version of RenderFromObject returns an AnimatedTransform that includes both transformations.

<<BasicSceneBuilder Private Methods>>+=

GraphicsState also maintains the starting and ending times for the specified transformations.

<<GraphicsState Public Members>>+=

Float transformStartTime = 0, transformEndTime = 1;

A final issue related to [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) s is minimizing their storage costs. In the usual case of using 32-bit float s for pbrt 's [Float](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#Float) type, each [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) class instance uses 128 bytes of memory. Because the same transformation may be applied to many objects in the scene, it is worthwhile to reuse the same Transform for all of them when possible. The [InternCache](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#InternCache) class helps with this task, allocating and storing a single [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) for each unique transformation that is passed to its Lookup() method. In turn, classes like [Shape](https://pbr-book.org/4ed/Shapes/Basic_Shape_Interface.html#Shape) implementations are able to save memory by storing just a const Transform \* rather than a full [Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform).

#### Hierarchical Graphics State

When specifying the scene, it is useful to be able to make a set of changes to the graphics state, instantiate some scene objects, and then roll back to an earlier graphics state. For example, one might want to specify a base transformation to position a car model in a scene and then to use additional transformations relative to the initial one to place the wheels, the seats, and so forth. A convenient way to do this is via a stack of saved GraphicsState objects: the user can specify that the current graphics state should be copied and pushed on the stack and then later specify that the current state should be replaced with the state on the top of the stack.

This stack is managed by the AttributeBegin and AttributeEnd statements in pbrt 's scene description files. The former saves the current graphics state and the latter restores the most recent saved state. Thus, a scene description file might contain the following:

Material "diffuse" AttributeBegin Material "dielectric" Translate 5 0 0 Shape "sphere" "float radius" \[ 1 \] AttributeEnd Shape "sphere" "float radius" \[ 1 \]

The first sphere is affected by the translation and is bound to the dielectric material, while the second sphere is diffuse and is not translated.

[BasicSceneBuilder](#BasicSceneBuilder) maintains a vector of GraphicsState s for this stack.

void BasicSceneBuilder::AttributeBegin(FileLoc loc) { VERIFY\_WORLD("AttributeBegin"); [pushedGraphicsStates](#BasicSceneBuilder::pushedGraphicsStates).push\_back(graphicsState); }

std::vector< [GraphicsState](#BasicSceneBuilder::GraphicsState) > pushedGraphicsStates;

The AttributeEnd() method also checks to see if the stack is empty and issues an error if there was no matching AttributeBegin() call earlier.

void BasicSceneBuilder::AttributeEnd(FileLoc loc) { VERIFY\_WORLD("AttributeEnd"); <<Issue error on unmatched AttributeEnd >>

if (pushedGraphicsStates.empty()) { Error(&loc, "Unmatched AttributeEnd encountered. Ignoring it."); return; }

[graphicsState](#BasicSceneBuilder::graphicsState) = std::move([pushedGraphicsStates](#BasicSceneBuilder::pushedGraphicsStates).back()); [pushedGraphicsStates](#BasicSceneBuilder::pushedGraphicsStates).pop\_back(); }

### C.2.4 Creating Scene Elements

As soon as an entity in the scene is fully specified, [BasicSceneBuilder](#BasicSceneBuilder) passes its specification on to the [BasicScene](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#BasicScene). It is thus possible to immediately begin construction of the associated object that is used for rendering even as parsing the rest of the scene description continues. For brevity, in this section and in Section [C.3](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#sec:basic-scene) we will only discuss how this process works for [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) s and for the [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) objects that represent participating media. (Those two are representative of how the rest of the scene objects are handled.)

When a Sampler statement is parsed in the scene description, the following Sampler() method is called by the parser. All that needs to be done is to record the sampler's name and parameters; because the sampler may be changed by a subsequent Sampler statement in the scene description, it should not immediately be passed along to the [BasicScene](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#BasicScene).

void BasicSceneBuilder::[Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) (const std::string &name, [ParsedParameterVector](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameterVector) params, FileLoc loc) { [ParameterDictionary](#ParameterDictionary) dict(std::move(params), [graphicsState](#BasicSceneBuilder::graphicsState).[colorSpace](#GraphicsState::colorSpace)); VERIFY\_OPTIONS(" [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) "); sampler = [SceneEntity](#SceneEntity) (name, std::move(dict), loc); }

BasicSceneBuilder holds on to a [SceneEntity](#SceneEntity) for the sampler in a member variable until its value is known to be final.

<<BasicSceneBuilder Private Members>>+=

[SceneEntity](#SceneEntity) sampler;

Once the WorldBegin statement is parsed, the sampler, camera, film, pixel filter, accelerator, and integrator are all set; they cannot be subsequently changed. Thus, when the parser calls the WorldBegin() method of BasicSceneBuilder, each corresponding SceneEntity can be passed along to the [BasicScene](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#BasicScene). (This method also does some maintenance of the graphics state, resetting the CTM to the identity transformation and handling other details; that code is not included here.)

void BasicSceneBuilder::WorldBegin(FileLoc loc) { VERIFY\_OPTIONS("WorldBegin"); <<Reset graphics state for WorldBegin >>

currentBlock = BlockState::WorldBlock; for (int i = 0; i < MaxTransforms; ++i) graphicsState.ctm\[i\] = pbrt::[Transform](https://pbr-book.org/4ed/Geometry_and_Transformations/Transformations.html#Transform) (); graphicsState.activeTransformBits = AllTransformsBits; namedCoordinateSystems\["world"\] = graphicsState.ctm;

<< [Pass pre- WorldBegin entities to scene](#fragment-Passpre-monoWorldBeginentitiestomonoscene-0) >>

[scene](#BasicSceneBuilder::scene) -> [SetOptions](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#BasicScene::SetOptions) (filter, film, camera, sampler, integrator, accelerator);

}

All the entities are passed with a single method call; as we will see in the implementation of the SetOptions() method, having all of them at hand simultaneously makes it easier to start creating the corresponding objects for rendering.

<<Pass pre- WorldBegin entities to scene >>=

[scene](#BasicSceneBuilder::scene) -> [SetOptions](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#BasicScene::SetOptions) (filter, film, camera, sampler, integrator, accelerator);

There is not much more to do for media. MakeNamedMedium() begins with a check to make sure that a medium with the given name has not already been specified.

<<BasicSceneBuilder Method Definitions>>+=

void BasicSceneBuilder::MakeNamedMedium(const std::string &name, [ParsedParameterVector](https://pbr-book.org/4ed/Processing_the_Scene_Description/Tokenizing_and_Parsing.html#ParsedParameterVector) params, FileLoc loc) { <<Issue error if medium name is multiply defined>>

if (mediumNames.find(name)!= mediumNames.end()) { ErrorExitDeferred(&loc, "Named medium \\"%s\\" redefined.", name); return; } mediumNames.insert(name);

<< [Create ParameterDictionary for medium and call AddMedium()](#fragment-CreatemonoParameterDictionaryformediumandcallmonoAddMedium-0) >> }

Assuming the medium is not multiply defined, all that is to be done is to pass along a [MediumSceneEntity](#MediumSceneEntity) to the [BasicScene](https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation.html#BasicScene). This can be done immediately in this case, as there is no way for it to be subsequently changed during parsing.

<<Create ParameterDictionary for medium and call AddMedium() >>=

The other object specification methods follow the same general form, though the BasicSceneBuilder::Shape() method is more complex than the others. Not only does it need to check to see if an AreaLight specification is active and call BasicScene::AddAreaLight() if so, but it also needs to distinguish between shapes with animated transformations and those without, creating an [AnimatedShapeSceneEntity](#AnimatedShapeSceneEntity) or a [ShapeSceneEntity](#ShapeSceneEntity) as appropriate.