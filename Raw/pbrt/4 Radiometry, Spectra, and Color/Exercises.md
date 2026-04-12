---
title: "Exercises"
source: "https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Exercises"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---

1. ① How many photons would a 50-W lightbulb that emits light at the single wavelength emit in 1 second?

> ① 一个在单一波长处发光的 50 瓦灯泡在 1 秒内会发射多少个光子？

2. ① Compute the irradiance at a point due to a unit-radius disk units directly above its normal with constant outgoing radiance of 10 W/m². Do the computation twice, once as an integral over solid angle and once as an integral over area. (Hint: If the results do not match at first, see Section A.5.1.)

> ① 计算由一个单位半径圆盘（直接位于某点法线上方若干单位处，恒定出射辐射亮度为 10 W/m²）在该点产生的辐照度。计算两次，一次作为对立体角的积分，一次作为对面积的积分。（提示：如果结果一开始不匹配，请参见第 A.5.1 节。）

3. ① Similarly, compute the irradiance at a point due to a square quadrilateral with outgoing radiance of 10 W/m² that has sides of length 1 and is 1 unit directly above the point in the direction of its surface normal.

> ① 类似地，计算由一个出射辐射亮度为 10 W/m²、边长为 1、位于该点沿其表面法线方向正上方 1 个单位处的正方形四边形在该点产生的辐照度。

4. ② Modify the SampledSpectrum class to also store the wavelengths associated with the samples and their PDFs. Using pbrt's assertion macros, add checks to ensure that no computations are performed using SampledSpectrum values associated with different wavelengths. Measure the performance of pbrt with and without your changes. How much runtime overhead is there? Did you find any bugs in pbrt?

> ② 修改 SampledSpectrum 类，使其也存储与样本相关联的波长及其 PDF。使用 pbrt 的断言宏，添加检查以确保不会使用关联不同波长的 SampledSpectrum 值进行计算。测量有无你的更改时 pbrt 的性能。运行时开销有多大？你发现了 pbrt 中的任何 bug 吗？
