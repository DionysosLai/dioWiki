---
title: "BasicScene and Final Object Creation"
source: "https://pbr-book.org/4ed/Processing_the_Scene_Description/BasicScene_and_Final_Object_Creation"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## C.3 BasicScene and Final Object Creation
> ## C.3 BasicScene 和最终对象创建

The responsibilities of the [BasicScene](#BasicScene) are straightforward: it takes scene entity objects and provides methods that convert them into objects for rendering.
> [BasicScene](#BasicScene) 的职责很直接：它接收场景实体对象并提供将它们转换为渲染对象的方法。 However, there are two factors that make its implementation not completely trivial. First, as discussed in Section [C.2](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#sec:basic-scene-builder), if the Import directive is used in the scene specification, there may be multiple [BasicSceneBuilder](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#BasicSceneBuilder) s that are concurrently calling [BasicScene](#BasicScene) methods. Therefore, the implementation must use mutual exclusion to ensure correct operation.

The second consideration is performance: we would like to minimize the time spent in the execution of [BasicScene](#BasicScene) methods, as time spent in them delays parsing the remainder of the scene description. System startup time is a facet of performance that is worth attending to, and so [BasicScene](#BasicScene) uses the asynchronous job capabilities introduced in Section [B.6.6](https://pbr-book.org/4ed/Utilities/Parallelism.html#sec:async-jobs-and-futures) to create scene objects while parsing proceeds when possible.

<<BasicScene Definition>>=

class BasicScene { public: << [BasicScene Public Methods](#fragment-BasicScenePublicMethods-0) >>

BasicScene(); void SetOptions(SceneEntity filter, SceneEntity film, CameraSceneEntity camera, SceneEntity [sampler](#BasicScene::sampler), SceneEntity integrator, SceneEntity accelerator); void AddNamedMaterial(std::string name, SceneEntity material); int AddMaterial(SceneEntity material); void AddMedium(MediumSceneEntity medium); void AddFloatTexture(std::string name, TextureSceneEntity texture); void AddSpectrumTexture(std::string name, TextureSceneEntity texture); void AddLight(LightSceneEntity light); int AddAreaLight(SceneEntity light); void AddShapes(pstd::span<ShapeSceneEntity> shape); void AddAnimatedShape(AnimatedShapeSceneEntity shape); void AddInstanceDefinition(InstanceDefinitionSceneEntity instance); void AddInstanceUses(pstd::span<InstanceSceneEntity> in); void Done(); [Camera](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#Camera) GetCamera() { cameraJobMutex.lock(); while (!camera) { pstd::optional< [Camera](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#Camera) > c = cameraJob-> [TryGetResult](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob::TryGetResult) (&cameraJobMutex); if (c) camera = \*c; } cameraJobMutex.unlock(); return camera; } [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) GetSampler() { [samplerJobMutex](#BasicScene::samplerJobMutex).lock(); while (![sampler](#BasicScene::sampler)) { pstd::optional< [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) > s = [samplerJob](#BasicScene::samplerJob) -> [TryGetResult](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob::TryGetResult) (& [samplerJobMutex](#BasicScene::samplerJobMutex)); if (s) [sampler](#BasicScene::sampler) = \*s; } [samplerJobMutex](#BasicScene::samplerJobMutex).unlock(); return [sampler](#BasicScene::sampler); } void CreateMaterials(const NamedTextures &sceneTextures, std::map<std::string, [Material](https://pbr-book.org/4ed/Textures_and_Materials/Material_Interface_and_Implementations.html#Material) > \*namedMaterials, std::vector< [Material](https://pbr-book.org/4ed/Textures_and_Materials/Material_Interface_and_Implementations.html#Material) > \*materials); std::vector< [Light](https://pbr-book.org/4ed/Light_Sources/Light_Interface.html#Light) > CreateLights(const NamedTextures &textures, std::map<int, pstd::vector< [Light](https://pbr-book.org/4ed/Light_Sources/Light_Interface.html#Light) > \*> \*shapeIndexToAreaLights); std::map<std::string, [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) > CreateMedia(); [Primitive](https://pbr-book.org/4ed/Primitives_and_Intersection_Acceleration/Primitive_Interface_and_Geometric_Primitives.html#Primitive) CreateAggregate( const NamedTextures &textures, const std::map<int, pstd::vector< [Light](https://pbr-book.org/4ed/Light_Sources/Light_Interface.html#Light) > \*> &shapeIndexToAreaLights, const std::map<std::string, [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) > &media, const std::map<std::string, [Material](https://pbr-book.org/4ed/Textures_and_Materials/Material_Interface_and_Implementations.html#Material) > &namedMaterials, const std::vector< [Material](https://pbr-book.org/4ed/Textures_and_Materials/Material_Interface_and_Implementations.html#Material) > &materials); std::unique\_ptr< [Integrator](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#Integrator) > CreateIntegrator([Camera](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#Camera) camera, [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) [sampler](#BasicScene::sampler), [Primitive](https://pbr-book.org/4ed/Primitives_and_Intersection_Acceleration/Primitive_Interface_and_Geometric_Primitives.html#Primitive) accel, std::vector< [Light](https://pbr-book.org/4ed/Light_Sources/Light_Interface.html#Light) > lights) const; NamedTextures CreateTextures();

<< [BasicScene Public Members](#fragment-BasicScenePublicMembers-0) >>

[SceneEntity](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity) integrator, accelerator; std::vector<ShapeSceneEntity> shapes; std::vector<AnimatedShapeSceneEntity> animatedShapes; std::vector<InstanceSceneEntity> instances; std::map<InternedString, InstanceDefinitionSceneEntity \*> instanceDefinitions;

private: <<BasicScene Private Methods>>

[Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) GetMedium(const std::string &name, const FileLoc \*loc); void startLoadingNormalMaps(const ParameterDictionary &parameters);

<< [BasicScene Private Members](#fragment-BasicScenePrivateMembers-0) >>

[AsyncJob](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob) < [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) > \*samplerJob = nullptr; mutable [ThreadLocal](https://pbr-book.org/4ed/Utilities/Parallelism.html#ThreadLocal) <Allocator> threadAllocators; [Camera](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#Camera) camera; [Film](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#Film) film; std::mutex cameraJobMutex; [AsyncJob](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob) < [Camera](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#Camera) > \*cameraJob = nullptr; std::mutex samplerJobMutex; [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) sampler; std::mutex mediaMutex; std::map<std::string, [AsyncJob](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob) < [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) > \*> mediumJobs; std::map<std::string, [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) > mediaMap; std::mutex materialMutex; std::map<std::string, [AsyncJob](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob) < [Image](https://pbr-book.org/4ed/Utilities/Images.html#Image) \*> \*> normalMapJobs; std::map<std::string, [Image](https://pbr-book.org/4ed/Utilities/Images.html#Image) \*> normalMaps; std::vector<std::pair<std::string, SceneEntity>> namedMaterials; std::vector<SceneEntity> materials; std::mutex lightMutex; std::vector< [AsyncJob](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob) < [Light](https://pbr-book.org/4ed/Light_Sources/Light_Interface.html#Light) > \*> lightJobs; std::mutex areaLightMutex; std::vector<SceneEntity> areaLights; std::mutex textureMutex; std::vector<std::pair<std::string, TextureSceneEntity>> serialFloatTextures; std::vector<std::pair<std::string, TextureSceneEntity>> serialSpectrumTextures; std::vector<std::pair<std::string, TextureSceneEntity>> asyncSpectrumTextures; std::set<std::string> loadingTextureFilenames; std::map<std::string, [AsyncJob](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob) < [FloatTexture](https://pbr-book.org/4ed/Textures_and_Materials/Texture_Interface_and_Basic_Textures.html#FloatTexture) > \*> floatTextureJobs; std::map<std::string, [AsyncJob](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob) < [SpectrumTexture](https://pbr-book.org/4ed/Textures_and_Materials/Texture_Interface_and_Basic_Textures.html#SpectrumTexture) > \*> spectrumTextureJobs; int nMissingTextures = 0; std::mutex shapeMutex, animatedShapeMutex; std::mutex instanceDefinitionMutex, instanceUseMutex;

};

<<BasicScene Method Definitions>>=

void BasicScene::SetOptions([SceneEntity](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity) filter, [SceneEntity](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity) film, [CameraSceneEntity](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#CameraSceneEntity) camera, [SceneEntity](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity) sampler, [SceneEntity](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity) integ, [SceneEntity](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity) accel) { << [Store information for specified integrator and accelerator](#fragment-Storeinformationforspecifiedintegratorandaccelerator-0) >>

[integrator](#BasicScene::integrator) = integ; [accelerator](#BasicScene::accelerator) = accel;

<<Immediately create filter and film>>

LOG\_VERBOSE("Starting to create filter and film"); Allocator alloc = threadAllocators.Get(); [Filter](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Image_Reconstruction.html#Filter) filt = [Filter](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Image_Reconstruction.html#Filter)::Create(filter.name, filter.parameters, &filter.loc, alloc); // It's a little ugly to poke into the camera's parameters here, but we // have this circular dependency that [Camera](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#Camera)::Create() expects a // [Film](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#Film), yet now the film needs to know the exposure time from // the camera.... Float exposureTime = camera.parameters.GetOneFloat("shutterclose", 1.f) - camera.parameters.GetOneFloat("shutteropen", 0.f); if (exposureTime <= 0) ErrorExit(&camera.loc, "The specified camera shutter times imply that the shutter " "does not open. A black image will result."); this->film = [Film](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#Film)::Create(film.name, film.parameters, exposureTime, camera.cameraTransform, filt, &film.loc, alloc); LOG\_VERBOSE("Finished creating filter and film");

<< [Enqueue asynchronous job to create sampler](#fragment-Enqueueasynchronousjobtocreatesampler-0) >>

[samplerJob](#BasicScene::samplerJob) = [RunAsync](https://pbr-book.org/4ed/Utilities/Parallelism.html#RunAsync) (\[sampler, this\]() { Allocator alloc = [threadAllocators](#BasicScene::threadAllocators).[Get](https://pbr-book.org/4ed/Utilities/Parallelism.html#ThreadLocal::Get) (); [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) res = this->film.[FullResolution](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#Film::FullResolution) (); return [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler)::Create(sampler.[name](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity::name), sampler.[parameters](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity::parameters), res, &sampler.[loc](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity::loc), alloc); });

<<Enqueue asynchronous job to create camera>>

cameraJob = RunAsync(\[camera,this\]() { LOG\_VERBOSE("Starting to create camera"); Allocator alloc = threadAllocators.Get(); [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) cameraMedium = GetMedium(camera.medium, &camera.loc); [Camera](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#Camera) c = [Camera](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#Camera)::Create(camera.name, camera.parameters, cameraMedium, camera.cameraTransform, this->film, &camera.loc, alloc); LOG\_VERBOSE("Finished creating camera"); return c; });

}

When SetOptions() is called, the specifications of the geometry and lights in the scene have not yet been parsed. Therefore, it is not yet possible to create the integrator (which needs the lights) or the acceleration structure (which needs the geometry). Therefore, their specification so far is saved in member variables for use when parsing is finished.

<<Store information for specified integrator and accelerator>>=

[integrator](#BasicScene::integrator) = integ; [accelerator](#BasicScene::accelerator) = accel;

<<BasicScene Public Members>>=

[SceneEntity](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity) integrator, accelerator;

However, it is possible to start work on creating the [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler), [Camera](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#Camera), [Filter](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Image_Reconstruction.html#Filter), and [Film](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#Film). While they could all be created in turn in the SetOptions() method, we instead use [RunAsync()](https://pbr-book.org/4ed/Utilities/Parallelism.html#RunAsync) to launch multiple jobs to take care of them. Thus, the SetOptions() method can return quickly, allowing parsing to resume, and creation of those objects can proceed in parallel as parsing proceeds if there are available CPU cores. Although these objects usually take little time to initialize, sometimes they do not: the RealisticCamera requires a second or so on a current CPU to compute exit pupil bounds and the [HaltonSampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Halton_Sampler.html#HaltonSampler) takes approximately 0.1 seconds to initialize its random permutations. If that work can be done concurrently with parsing the scene, rendering can begin that much more quickly.

<<Enqueue asynchronous job to create sampler>>=

[samplerJob](#BasicScene::samplerJob) = [RunAsync](https://pbr-book.org/4ed/Utilities/Parallelism.html#RunAsync) (\[sampler, this\]() { Allocator alloc = [threadAllocators](#BasicScene::threadAllocators).[Get](https://pbr-book.org/4ed/Utilities/Parallelism.html#ThreadLocal::Get) (); [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) res = this->film.[FullResolution](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#Film::FullResolution) (); return [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler)::Create(sampler.[name](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity::name), sampler.[parameters](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity::parameters), res, &sampler.[loc](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity::loc), alloc); });

The AsyncJob \* returned by [RunAsync()](https://pbr-book.org/4ed/Utilities/Parallelism.html#RunAsync) is held in a member variable. The [BasicScene](#BasicScene) constructor also initializes threadAllocators so that appropriate memory allocators are available depending on whether the scene objects should be stored in CPU memory or GPU memory.

<<BasicScene Private Members>>=

[AsyncJob](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob) < [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) > \*samplerJob = nullptr; mutable [ThreadLocal](https://pbr-book.org/4ed/Utilities/Parallelism.html#ThreadLocal) <Allocator> threadAllocators;

Briefly diverting from the [BasicScene](#BasicScene) implementation, we will turn to the [Sampler::Create()](#Sampler::Create) method that is called in the job that creates the [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler). (This method is defined in the file [samplers.cpp](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/samplers.cpp) with the rest of the [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) code.) It checks the provided sampler name against all the sampler names it is aware of, calling the appropriate object-specific creation method when it finds a match and issuing an error if no match is found. Thus, if the system is to be extended with an additional sampler, this is a second place in the code where the existence of the new sampler must be registered.

Most of the values that are passed to the object constructors are extracted from the [ParameterDictionary](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#ParameterDictionary) in the object-specific Create() methods, though some that are not in the available parameter list (like here, the uncropped image resolution) are directly passed as parameters to the Create() methods.

<<Sampler Method Definitions>>=

[Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler)::Create(const std::string &name, const [ParameterDictionary](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#ParameterDictionary) &parameters, [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) fullRes, const FileLoc \*loc, Allocator alloc) { [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) sampler = nullptr; if (name == "zsobol") sampler = [ZSobolSampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sobol_Samplers.html#ZSobolSampler)::Create(parameters, fullRes, loc, alloc); <<Create remainder of Sampler types>>

else if (name == "paddedsobol") sampler = PaddedSobolSampler::Create(parameters, loc, alloc); else if (name == "halton") sampler = HaltonSampler::Create(parameters, fullRes, loc, alloc); else if (name == "sobol") sampler = SobolSampler::Create(parameters, fullRes, loc, alloc); else if (name == "pmj02bn") sampler = PMJ02BNSampler::Create(parameters, loc, alloc); else if (name == "independent") sampler = IndependentSampler::Create(parameters, loc, alloc); else if (name == "stratified") sampler = StratifiedSampler::Create(parameters, loc, alloc); else [ErrorExit](https://pbr-book.org/4ed/Utilities/User_Interaction.html#ErrorExit) (loc, "%s: sampler type unknown.", name); if (!sampler) [ErrorExit](https://pbr-book.org/4ed/Utilities/User_Interaction.html#ErrorExit) (loc, "%s: unable to create sampler.", name); parameters.[ReportUnused](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#ParameterDictionary::ReportUnused) ();

return sampler; }

The fragment that handles the remainder of types of samplers, <<Create remainder of Sampler types>>, is not included here.

All the other base interface classes like [Light](https://pbr-book.org/4ed/Light_Sources/Light_Interface.html#Light), [Shape](https://pbr-book.org/4ed/Shapes/Basic_Shape_Interface.html#Shape), [Camera](https://pbr-book.org/4ed/Cameras_and_Film/Camera_Interface.html#Camera), and so forth provide corresponding Create() methods, all of which have the same general form.

BasicScene also provides methods that return these asynchronously created objects. All have a similar form, acquiring a mutex before harvesting the result from the asynchronous job if needed. Calling code should delay calling these methods as long as possible, doing as much independent work as it can to increase the likelihood that the asynchronous job has completed and that the [AsyncJob::GetResult()](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob::GetResult) calls do not stall.

<<BasicScene Public Methods>>=

[Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) GetSampler() { [samplerJobMutex](#BasicScene::samplerJobMutex).lock(); while (![sampler](#BasicScene::sampler)) { pstd::optional< [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) > s = [samplerJob](#BasicScene::samplerJob) -> [TryGetResult](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob::TryGetResult) (& [samplerJobMutex](#BasicScene::samplerJobMutex)); if (s) [sampler](#BasicScene::sampler) = \*s; } [samplerJobMutex](#BasicScene::samplerJobMutex).unlock(); return [sampler](#BasicScene::sampler); }

std::mutex samplerJobMutex; [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) sampler;

[Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) creation is also based on [RunAsync()](https://pbr-book.org/4ed/Utilities/Parallelism.html#RunAsync) ’s asynchronous job capabilities, though in that case a std::map of jobs is maintained, one for each medium. Note that it is important that a mutex be held when storing the AsyncJob \* returned by [RunAsync()](https://pbr-book.org/4ed/Utilities/Parallelism.html#RunAsync) in mediumJobs, since multiple threads may call this method concurrently if Import statements are used for multi-threaded parsing.

void BasicScene::AddMedium([MediumSceneEntity](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#MediumSceneEntity) medium) { << [Define create lambda function for](#fragment-DefinemonocreatelambdafunctionforuseMediumcreation-0) [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) creation>>

auto create = \[medium, this\]() { std::string type = medium.[parameters](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity::parameters).[GetOneString](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#ParameterDictionary::GetOneString) ("type", ""); <<Check for missing medium “type” or animated medium transform>>

if (type.empty()) ErrorExit(&medium.loc, "No parameter \\"string type\\" found for medium."); if (medium.renderFromObject.IsAnimated()) Warning(&medium.loc, "Animated transformation provided for medium. Only the " "start transform will be used.");

return [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium)::Create(type, medium.[parameters](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity::parameters), medium.renderFromObject.startTransform, &medium.[loc](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity::loc), [threadAllocators](#BasicScene::threadAllocators).[Get](https://pbr-book.org/4ed/Utilities/Parallelism.html#ThreadLocal::Get) ()); };

std::lock\_guard<std::mutex> lock([mediaMutex](#BasicScene::mediaMutex)); [mediumJobs](#BasicScene::mediumJobs) \[medium.[name](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity::name)\] = [RunAsync](https://pbr-book.org/4ed/Utilities/Parallelism.html#RunAsync) (create); }

std::mutex mediaMutex; std::map<std::string, [AsyncJob](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob) < [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) > \*> mediumJobs;

Creation of each [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) follows a similar form to [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) creation, though here the type of medium to be created is found from the parameter list; the MediumSceneEntity::name member variable holds the user-provided name to associate with the medium.

<<Define create lambda function for [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) creation>>=

auto create = \[medium, this\]() { std::string type = medium.[parameters](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity::parameters).[GetOneString](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#ParameterDictionary::GetOneString) ("type", ""); <<Check for missing medium “type” or animated medium transform>>

if (type.empty()) ErrorExit(&medium.loc, "No parameter \\"string type\\" found for medium."); if (medium.renderFromObject.IsAnimated()) Warning(&medium.loc, "Animated transformation provided for medium. Only the " "start transform will be used.");

return [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium)::Create(type, medium.[parameters](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity::parameters), medium.renderFromObject.startTransform, &medium.[loc](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#SceneEntity::loc), [threadAllocators](#BasicScene::threadAllocators).[Get](https://pbr-book.org/4ed/Utilities/Parallelism.html#ThreadLocal::Get) ()); };

All the media specified in the scene are provided to callers via a map from names to [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) objects.

<<BasicScene Method Definitions>>+=

std::map<std::string, [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) > BasicScene::CreateMedia() { [mediaMutex](#BasicScene::mediaMutex).lock(); if (![mediumJobs](#BasicScene::mediumJobs).empty()) { << [Consume results for asynchronously created](#fragment-ConsumeresultsforasynchronouslycreateduseMediumobjects-0) [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) objects>>

for (auto &m: [mediumJobs](#BasicScene::mediumJobs)) { while ([mediaMap](#BasicScene::mediaMap).find(m.first) == [mediaMap](#BasicScene::mediaMap).end()) { pstd::optional< [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) > med = m.second-> [TryGetResult](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob::TryGetResult) (&mediaMutex); if (med) [mediaMap](#BasicScene::mediaMap) \[m.first\] = \*med; } } [mediumJobs](#BasicScene::mediumJobs).clear();

} [mediaMutex](#BasicScene::mediaMutex).unlock(); return [mediaMap](#BasicScene::mediaMap); }

The asynchronously created Medium objects are consumed using calls to [AsyncJob::TryGetResult()](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob::TryGetResult), which returns the result if it is available and otherwise unlocks the mutex, does some of the enqueued parallel work, and then relocks it before returning. Thus, there is no risk of deadlock from one thread holding mediaMutex, finding that the result is not ready and working on enqueued parallel work that itself ends up trying to acquire mediaMutex.

<<Consume results for asynchronously created [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) objects>>=

for (auto &m: [mediumJobs](#BasicScene::mediumJobs)) { while ([mediaMap](#BasicScene::mediaMap).find(m.first) == [mediaMap](#BasicScene::mediaMap).end()) { pstd::optional< [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) > med = m.second-> [TryGetResult](https://pbr-book.org/4ed/Utilities/Parallelism.html#AsyncJob::TryGetResult) (&mediaMutex); if (med) [mediaMap](#BasicScene::mediaMap) \[m.first\] = \*med; } } [mediumJobs](#BasicScene::mediumJobs).clear();

<<BasicScene Private Members>>+=

std::map<std::string, [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) > mediaMap;

As much as possible, other scene objects are created similarly using [RunAsync()](https://pbr-book.org/4ed/Utilities/Parallelism.html#RunAsync). Light sources are easy to handle, and it is especially helpful to start creating image textures during parsing, as reading image file formats from disk can be a bottleneck for scenes with many such textures. However, extra attention is required due to the cache of images already read for textures (Section [10.4.1](https://pbr-book.org/4ed/Textures_and_Materials/Image_Texture.html#sec:texture-caching)). If an image file on disk is used in multiple textures, [BasicScene](#BasicScene) takes care not to have multiple jobs redundantly reading the same image. Instead, only one reads it and the rest wait. When those textures are then created, the image they need can be efficiently returned from the cache.

In return for the added complexity of this asynchronous object creation, we have found that for complex scenes it is not unusual for this version of pbrt to be able to start rendering 4 times more quickly than the previous version.

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**同章节**:
- [[C Processing the Scene Description]]
- [[C.1 Tokenizing and Parsing]]
- [[C.2 Managing the Scene Description]]
- [[C.4 Adding New Object Implementations]]
