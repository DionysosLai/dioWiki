---
title: "Exercises"
source: "https://pbr-book.org/4ed/Sampling_and_Reconstruction/Exercises"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
1. ② Modify pbrt so that the camera can report its sample requirements and then use this information when samples are requested to initialize CameraSamples. Render images and compare results. Do you see an improvement? How do results differ with different samplers?

> ② 修改 pbrt，使相机能报告其采样需求，然后在请求样本初始化 CameraSample 时使用此信息。渲染图像并比较结果。是否看到改善？不同采样器的结果有何差异？

2. ② Implement a Sampler based on *rank-1 lattices* for image synthesis, as described by Keller (2004) and Dammertz and Keller (2008b). Compare results to the other samplers in pbrt.

> ② 按照 Keller (2004) 和 Dammertz & Keller (2008b) 的描述，实现一个基于**秩-1 格点**的 Sampler 用于图像合成。与 pbrt 中其他采样器比较结果。

3. ② Implement a Sampler based on orthogonal array sampling, as described by Jarosz et al. (2019). Compare both MSE and Monte Carlo efficiency of this sampler to pbrt's current samplers.

> ② 按照 Jarosz 等人 (2019) 的描述，实现一个基于正交阵列采样的 Sampler。将该采样器的 MSE 和蒙特卡洛效率与 pbrt 当前采样器进行比较。

4. ③ Investigate derivative-based reconstruction using both function values and derivatives at sample points, as noted by Mitchell and Netravali (1988). Extend pbrt to support this technique, possibly using finite differencing and ray differentials.

> ③ 研究 Mitchell 和 Netravali (1988) 提出的基于导数的重建方法（同时使用函数值和采样点的导数）。扩展 pbrt 以支持此技术，可使用有限差分和光线微分的近似方法。

5. ③ Read papers on adaptive sampling and reconstruction techniques from the "Further Reading" section and implement one in pbrt. Measure the effectiveness using Monte Carlo efficiency to account for increased computational cost. How well does your implementation perform compared to non-adaptive samplers?

> ③ 阅读"Further Reading"中关于自适应采样与重建技术的论文，并在 pbrt 中实现其中一种。使用蒙特卡洛效率来衡量有效性，以考虑增加的计算成本。与非自适应采样器相比，你的实现表现如何？

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[采样与重建]]
- [[蒙特卡洛积分]]

**同章节**:
- [[8 Sampling and Reconstruction]]
- [[8.1 Sampling Theory]]
- [[8.2 Sampling and Integration]]
- [[8.3 Sampling Interface]]
- [[8.4 Independent Sampler]]
- [[8.5 Stratified Sampler]]
- [[8.6 Halton Sampler]]
- [[8.7 Sobol’ Samplers]]
- [[8.8 Image Reconstruction]]
