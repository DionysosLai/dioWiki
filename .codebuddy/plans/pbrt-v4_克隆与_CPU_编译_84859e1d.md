---
name: pbrt-v4 克隆与 CPU 编译
overview: 将 mmp/pbrt-v4 克隆到 /Users/laiwugong/Desktop/Git/，安装 CMake，通过 CMake 编译 CPU 版本。跳过 CUDA/OptiX（macOS 无 NVIDIA GPU）。
todos:
  - id: brew-cmake
    content: 通过 Homebrew 安装 CMake
    status: completed
  - id: clone-repo
    content: 递归克隆 pbrt-v4 仓库（含 submodule）到 /Users/laiwugong/Desktop/Git/pbrt-v4
    status: completed
    dependencies:
      - brew-cmake
  - id: cmake-build
    content: CMake 配置并编译 pbrt-v4（CPU Release 版本）
    status: completed
    dependencies:
      - clone-repo
---

## 需求概述

将 `pbrt-v4` 源码仓库克隆到 `/Users/laiwugong/Desktop/Git/pbrt-v4`，完成依赖 submodule 拉取，并通过 CMake 编译出可执行文件。macOS CPU only，暂不配置 CUDA/OptiX。

## 目标

- 克隆完成后，仓库位于 `/Users/laiwugong/Desktop/Git/pbrt-v4`
- submodule 完整初始化
- CMake 构建成功，生成 `pbrt` 可执行文件（CPU 版本 Release）
- GPU 支持（CUDA/OptiX）标注为"待将来在 NVIDIA 机器上配置"

## 关键约束

- 必须在克隆时使用 `--recursive` 拉取 submodule，否则编译失败
- CMake 通过 Homebrew 安装（系统无 NVIDIA GPU，无需配置 OptiX/CUDA 路径）
- 编译默认 Release 版本，不额外指定 `-DCMAKE_BUILD_TYPE`

# Agent Extensions

本任务为纯命令行操作，无代码编写，不涉及可用扩展。