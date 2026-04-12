---
title: "Exercises"
source: "https://pbr-book.org/4ed/Volume_Scattering/Exercises"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
1. ② The [GridMedium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#GridMedium) and [RGBGridMedium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#RGBGridMedium) classes use a relatively large amount of memory for complex volume densities. Determine their memory requirements when used with complex medium densities and modify their implementations to reduce memory use. One approach might be to detect regions of space with constant (or relatively constant) density values using an octree data structure and to only refine the octree in regions where the densities are changing. Another possibility is to use less memory to record each density value—for example, by computing the minimum and maximum densities and then using 8 or 16 bits per density value to interpolate between them. What sorts of errors appear when either of these approaches is pushed too far?

> 1. ② GridMedium 和 RGBGridMedium 类对复杂体积密度使用了相对大量的内存。确定它们在复杂介质密度下的内存需求，并修改其实现以减少内存使用。一种方法可能是使用八叉树数据结构检测具有恒定（或近似恒定）密度值的空间区域，并仅在密度变化的区域细化八叉树。另一种可能是使用更少的内存来记录每个密度值——例如，通过计算最小和最大密度，然后使用每个密度值 8 或 16 位在它们之间插值。当这两种方法被过度推向极端时，会出现什么样的误差？

2. ② Improve [GridMedium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#GridMedium) to allow specifying grids of arbitrary [Spectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#Spectrum) values to define emission. How much more memory does your approach use for blackbody emission distributions than the current implementation, which only stores floating-point temperatures in that case? How much memory does it use when other spectral representations are provided? Can you find ways of reducing memory use—for example, by detecting equal spectra and only storing them in memory once?

> 2. ② 改进 GridMedium 以允许指定任意 Spectrum 值的网格来定义发射。与当前仅存储浮点温度的实现相比，你的方法对于黑体发射分布使用了多少额外内存？当提供其他光谱表示时使用了多少内存？你能找到减少内存使用的方法吗——例如，通过检测相同的光谱并只在内存中存储一次？

3. ③ One shortcoming of the majorants computed by the [RGBGridMedium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#RGBGridMedium) is that they do not account for spectral variation in the scattering coefficients—although conservative, they may give a loose bound for wavelengths where the coefficients are much lower than the maximum values. Computing tighter majorants is not straightforward in a spectral renderer: in a renderer that used RGB color for rendering, it is easy to maintain a majorant grid of RGB values instead of Float s, though doing so is more difficult with a spectral renderer, for reasons related to why [RGBUnboundedSpectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBUnboundedSpectrum) values are stored in the grids for and and not RGB. (See the discussion of this topic before the << [Initialize majorantGrid voxel for RGB and](https://pbr-book.org/4ed/Volume_Scattering/Media.html#fragment-InitializemonomajorantGridvoxelforRGBsigmaaandsigmas-0) >> fragment.) Investigate this issue and develop an approach that better accounts for spectral variation in the scattering coefficients to return wavelength-varying majorants when [RGBGridMedium::SampleRay()](https://pbr-book.org/4ed/Volume_Scattering/Media.html#RGBGridMedium::SampleRay) is called. You might, for example, find a way to compute [RGBUnboundedSpectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBUnboundedSpectrum) values that bound the maximum of two or more others. How much overhead does your representation introduce? How much is rendering time improved for scenes with colored media due to more efficient sampling when it is used?

> 3. ③ RGBGridMedium 计算的主值的一个缺点是它们没有考虑散射系数的光谱变化——虽然是保守的，但对于系数远低于最大值的波长，它们可能给出松散的界。在光谱渲染器中计算更紧密的主值并不简单。研究这个问题并开发一种方法，在调用 RGBGridMedium::SampleRay() 时更好地考虑散射系数的光谱变化以返回波长变化的主值。你的表示引入了多少开销？使用它时，由于更有效的采样，对于具有彩色介质的场景，渲染时间改善了多少？

4. ② The Medium implementations that use the [MajorantGrid](https://pbr-book.org/4ed/Volume_Scattering/Media.html#MajorantGrid) all currently use fixed grid resolutions for it, regardless of the amount of variation in density in their underlying media. Read the paper by Yue et al. ([2011](https://pbr-book.org/4ed/Volume_Scattering/Further_Reading.html#cite:Yue2011)) and use their approach to choose those resolutions adaptively. Then, measure performance over a sweep of grid sizes with a variety of volume densities. Are there any cases where there is a significant performance benefit from a different grid resolution? Considering their assumptions and pbrt 's implementation, can you explain any discrepancies between grid sizes set with their heuristics versus the most efficient resolution in pbrt?

> 4. ② 使用 MajorantGrid 的 Medium 实现目前都对其使用固定的网格分辨率，而不管其底层介质中密度变化的程度。阅读 Yue 等人 (2011) 的论文，并使用他们的方法自适应地选择这些分辨率。然后，使用各种体积密度在一系列网格大小上测量性能。是否有任何情况下不同的网格分辨率带来了显著的性能提升？考虑到他们的假设和 pbrt 的实现，你能解释使用他们的启发式方法设置的网格大小与 pbrt 中最有效的分辨率之间的任何差异吗？

5. ② Read Wrenninge's paper ([2016](https://pbr-book.org/4ed/Volume_Scattering/Further_Reading.html#cite:Wrenninge2016)) on a time-varying density representation for motion blur in volumes and implement this approach in pbrt. One challenge will be to generate volumes in this representation; you may need to implement a physical simulation system in order to make some yourself.

> 5. ② 阅读 Wrenninge (2016) 关于用于体积运动模糊的时变密度表示的论文，并在 pbrt 中实现这种方法。一个挑战将是以这种表示生成体积；你可能需要实现一个物理模拟系统才能自己制作一些。