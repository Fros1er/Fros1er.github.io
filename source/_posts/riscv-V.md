---
title: riscv V扩展编程笔记
date: 2023-06-11 23:04:27
tags: coding
---

基于`riscv_vector.h`，抄的<https://www.bilibili.com/video/BV1VT411E7iQ>。

<!-- more -->

Vector寄存器长度是厂商可选的。可以是64, 128, ..., 2048bits。

寄存器数据类型：`v<type><len><group>_t`. e.g. `vfloat32m1_t`。group代表几个寄存器拼成一组，最大八个。下文用`reg_type`指代。

`uint vsetvlmax_e<len><group>()`: 返回寄存器最大能操作多少个len bit的元素。如`vsetvlmax_e32m1()`。

`reg_type vfmv_v_f_f32m1(f, vl)`：返回一个每个元素初始化为f的reg_type。vl代表寄存器操作几个数，`0 <= vl <= vlmax`。f32m1对应`vfloat32m1_t`，`_v`代表向量寄存器，`_f`代表浮点数。

`reg_type vle32_v_f32m1(addr, vl)`：从addr开始读取vl个数据。f32m1同上。这条是连续访存，还有固定间隔跳跃访存和index间接访存。

`reg_type vfadd_vv_f32m1(op1, op2, vl)`：op1和op2相加。还有标量形式的`reg_type vfadd_vf_f32m1(reg_type op1, float op2, vl)`

`reg_type vfredusum_vs_f32m1_f32m1(vd, vv, vs, vl)`：等同于`vd[0] = (((vs[0] + vv[0]) + vv[1]) + ...) + vv[vl-1]`。返回vd。

有一个mask类型，可以指定向量寄存器哪几位不更新。分支的时候用。





