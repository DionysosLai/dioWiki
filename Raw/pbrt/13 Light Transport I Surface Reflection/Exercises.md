---
title: "Exercises"
source: "https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/Exercises"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
1. ② To further improve efficiency, Russian roulette can be applied to skip tracing many of the shadow rays that make a low contribution to the final image: to implement this approach, tentatively compute the potential contribution of each shadow ray to the final overall radiance value before tracing the ray. If the contribution is below some threshold, apply Russian roulette to possibly skip tracing the ray. Measure the effect your scheme has on Monte Carlo efficiency for a number of test scenes.

> 1. ② 为进一步提高效率，可以应用俄罗斯轮盘赌来跳过追踪对最终图像贡献低的许多阴影光线：在追踪光线之前，先临时计算每条阴影光线对最终总辐射亮度值的潜在贡献。如果贡献低于某个阈值，应用俄罗斯轮盘赌来可能跳过追踪该光线。测量你的方案对若干测试场景的蒙特卡洛效率的影响。

2. ② Read Veach's description of efficiency-optimized Russian roulette, which adaptively chooses a threshold for applying Russian roulette ([Veach 1997](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/Further_Reading.html#cite:VeachThesis); Section 10.4.1). Implement this algorithm in pbrt, and evaluate its effectiveness in comparison to manually setting these thresholds.

> 2. ② 阅读 Veach 关于效率优化俄罗斯轮盘赌的描述，它自适应地选择应用俄罗斯轮盘赌的阈值（Veach 1997; 第 10.4.1 节）。在 pbrt 中实现此算法，并评估其相对于手动设置这些阈值的有效性。

3. ② If a scene has an object with a material that causes all but one of the wavelengths in [SampledWavelengths](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#SampledWavelengths) to be terminated (e.g., due to dispersion), then rays may often undergo a number of scattering events before they hit such an object. In pbrt 's current implementation, the path's radiance estimate is divided by the wavelength PDF values once, in the [PixelSensor::ToSensorRGB()](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#PixelSensor::ToSensorRGB) method. An implication of this design is that all the lighting calculations along the path are affected by the termination of wavelengths, and not just the ones after it happens. The result is an increase in color noise in such images. Modify one or more integrators to instead perform this division by the current set of wavelength PDFs each time the radiance estimate being calculated is updated and not in [PixelSensor::ToSensorRGB()](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#PixelSensor::ToSensorRGB). Verify that the same image is computed for scenes without wavelength termination (other than minor differences due to round-off error). Is there any change in performance? Find a scene where this change improves the result and measure the reduction in MSE.

> 3. ② 如果场景中有一个材质会导致 SampledWavelengths 中除一个波长外的所有波长被终止（例如由于色散），那么光线可能在击中该物体之前经历多次散射事件。修改一个或多个积分器，使其在每次更新正在计算的辐射亮度估计值时除以当前波长 PDF 集，而不是在 PixelSensor::ToSensorRGB() 中执行。找到这种更改改善结果的场景并测量 MSE 的减少。

4. ② Measure how much time is spent in Monte Carlo evaluation in the [BSDF::rho()](https://pbr-book.org/4ed/Reflection_Models/BSDF_Representation.html#BSDF::rho) method when [VisibleSurface](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#VisibleSurface) s are being initialized in the [PathIntegrator](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/A_Better_Path_Tracer.html#PathIntegrator). Do so for both simple and complex scenes that include a variety of BSDF models. Then, improve the [BSDF](https://pbr-book.org/4ed/Reflection_Models/BSDF_Representation.html#BSDF) interface so that each [BxDF](https://pbr-book.org/4ed/Reflection_Models/BSDF_Representation.html#BxDF) can provide its own rho() implementation, possibly returning either an approximation or the closed-form reflectance. How much does performance improve as a result of your changes?

> 4. ② 测量在 PathIntegrator 中初始化 VisibleSurface 时，BSDF::rho() 方法中蒙特卡洛求值花费了多少时间。然后改进 BSDF 接口，使每个 BxDF 能提供自己的 rho() 实现。性能因此改善了多少？

5. ③ Implement a technique for generating samples from the product of the light and BSDF distributions; see for example the papers by Burke et al. ([2005](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/Further_Reading.html#cite:Burke2005)), Cline et al. ([2006](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/Further_Reading.html#cite:Cline2006)), Clarberg et al. ([2005](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/Further_Reading.html#cite:Clarberg05)), Rousselle et al. ([2008](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/Further_Reading.html#cite:Rousselle08)), and Hart et al. ([2020](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/Further_Reading.html#cite:Hart2020)). Compare the effectiveness of the approach you implement to the direct lighting calculation currently implemented in pbrt. Investigate how scene complexity (and, thus, how expensive shadow rays are to trace) affects the Monte Carlo efficiency of the two techniques.

> 5. ③ 实现一种从光源和 BSDF 分布乘积生成样本的技术。将你实现的方法的有效性与 pbrt 中当前实现的直接光照计算进行比较。

6. ② Clarberg and Akenine-Möller ([2008b](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/Further_Reading.html#cite:Clarberg2008b)) and Popov et al. ([2013](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/Further_Reading.html#cite:Popov2013)) both described algorithms that perform visibility caching—computing and interpolating information about light source visibility at points in the scene. Implement one of these methods and use it to improve the direct lighting calculation in pbrt. What sorts of scenes is it particularly effective for? Are there scenes for which it does not help?

> 6. ② Clarberg 和 Akenine-Möller (2008b) 以及 Popov 等人 (2013) 都描述了执行可见性缓存的算法。实现其中一种方法，并用它改进 pbrt 中的直接光照计算。

7. ③ Modify pbrt so that the user can flag certain objects in the scene as being important sources of indirect lighting, and modify the [PathIntegrator](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/A_Better_Path_Tracer.html#PathIntegrator) to sample points on those surfaces according to to generate some of the vertices in the paths it generates. Use multiple importance sampling to compute weights for the path samples, incorporating the probability that they would have been sampled both with BSDF sampling and with this area sampling. How much can this approach reduce variance and improve efficiency for scenes with substantial indirect lighting? How much can it hurt if the user flags surfaces that make little or no contribution or if multiple importance sampling is not used? Investigate generalizations of this approach that learn which objects are important sources of indirect lighting as rendering progresses so that the user does not need to supply this information ahead of time.

> 7. ③ 修改 pbrt 使用户可以标记场景中的某些物体为间接光照的重要来源，并修改 PathIntegrator 在这些表面上按比例采样点来生成其路径中的一些顶点。使用多重重要性采样来计算路径样本的权重。研究该方法在渲染过程中自动学习哪些物体是间接光照重要来源的推广。

8. ③ Implement a path guiding algorithm such as the one developed by Müller and collaborators ([Müller et al. 2017](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/Further_Reading.html#cite:Muller2017); [Müller 2019](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/Further_Reading.html#cite:Muller2019:ppg-notes)) or Reibold et al. ([2018](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/Further_Reading.html#cite:Reibold2018)). How much does your approach reduce error for scenes with highly varying indirect lighting? What is its effect on scenes with smoother lighting?

> 8. ③ 实现一种路径引导算法，例如 Müller 及其合作者（2017; 2019）或 Reibold 等人（2018）开发的算法。你的方法对具有高度变化间接光照的场景减少了多少误差？对具有更平滑光照的场景有什么影响？