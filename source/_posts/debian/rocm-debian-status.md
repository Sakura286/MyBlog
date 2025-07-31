---
title: ROCm Debian 支持现状
date: 2025-07-31 15:15:50
tags:
---

本调查于 2025 年 7 月 31 日

今天在 RISC-V 平台上基于 ROCm 6.4.2 后端，把 Deepseek 70B Q4 跑通了，而且很流畅，有点小高兴。

可行性验证了，是时候考虑上游化的事情了。

想从下面几个方面着手调查

1. Debian ROCm 的版本
2. Debian ROCm 组的活跃情况
3. Debian ROCm 对其他架构的支持情况

## 基础信息

ROCm Team 介绍 https://wiki.debian.org/Teams/ROCm
源码仓库 https://salsa.debian.org/rocm-team
邮件列表 https://lists.debian.org/debian-ai/

## 1. 版本

以 llama.cpp 所依赖的最高层级的包 hipblas 开始捋，根据[Packages overview for Debian ROCm Team](https://qa.debian.org/developer.php?email=debian-ai@lists.debian.org)

hipblas     5.5.1
rocsolver   5.5.1
rocblas     5.5.1 (experimental: 6.4.1)
rocsparse   5.7,1 (experimental: 6.4.1)
rocprim     5.7.1 (experimental: 6.4.1)
roctracer   5.7.1 (experimental: 6.4.1)

smi-lib     5.7.1 (experimental: 6.4.1)
rocm-hipamd 5.7.1 (experimental: 6.1.5)
rocminfo    6.1.2 (experimental: 6.4.1)
rocm-llvm   n/a   (experimental: 6.1.2)

附：上游的
5.5.1 在 2023.05 发布
5.7.1 在 2023.10 发布
6.1.2 在 2024.07 发布
6.4.1 在 2025.05 发布

可以看出，除了工具链/HIP库略老（6.1.2）之外，重要的数学库已经在 experimental 里更新到了最新的 6.4.1

或许可以看看 rocm-llvm 与 rocm-hipamd 的更新情况，以及一些不关键的包的升级情况

## 2. ROCm 社区活跃情况

目前有两位活跃的维护者[Cordell Bloor](https://salsa.debian.org/cgmb)与[Christian Kastner](https://salsa.debian.org/ckk)

最近（三个月内）有其他的代码贡献者，例如[Kentaro Hayashi](https://salsa.debian.org/kenhys)，[Andrew Lee (李健秋) ](https://salsa.debian.org/ajqlee)，[Christian Bayle](https://salsa.debian.org/bayle)，[Jérémy Lal](https://salsa.debian.org/kapouer)‘。邮件列表里活跃的还有 M. Zhou，

最近两个月，Cordell Bloor 开始[搞双周会](https://lists.debian.org/debian-ai/2025/05/msg00113.html)，但是最近因为他[休假](https://lists.debian.org/debian-ai/2025/07/msg00052.html)，所以跳过了一次。目前双周会的链接并未公开，需要给 Bloor 发邮件去要，地址`cgmb@slerp.xyz`

## 3. 对其他架构的支持

AMD 上游并未提到对特定 CPU 架构的需求，只需该 CPU 支持 PCIe atomic 即可

Debian 这边支持 amd64 arm64 ppc64el

2024.12，Cordell Bloor 看到 [POWER 系统支持 ROCm](https://www.phoronix.com/news/AMDKFD-Compute-POWERPC)，打算[搞 arm64 和 ppc64](https://lists.debian.org/debian-ai/2024/12/msg00026.html)

2025.02，Raptor Computing 给 Debian ROCm 组[提供了一台 ppc64 机器]((https://lists.debian.org/debian-ai/2025/02/msg00250.html)
)，但是驱动有bug；有人建议[用树莓派5来做 arm64 的验证](https://lists.debian.org/debian-ai/2025/02/msg00251.html)

2025.04，

2025.05

2025.06

2025.07


## 结论