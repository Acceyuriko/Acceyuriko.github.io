---
title: 'QtScrcpy 增加压枪'
date: 2022-02-21 19:26:00
description: '给 QtScrcpy 增加压枪功能'
tags:
  - QtScrcpy
  - 和平精英
---

手搓和平精英太伤眼睛，在淘宝买了北通E1接外设打，体验并不好，视角乱跳，投屏卡顿。 一顿研究发现了这个神器，[QtScrcpy](https://github.com/barry-ran/QtScrcpy)，流畅，好用。不过相比淘宝的外设缺少了自动压枪功能，因此想着给 QtScrcpy 加上压枪功能。写篇博客记录一下制作流程。

基本思路是
- 截取足够数量的游戏里的按钮图像，用 OpenCV 的 SVM 做训练，以识别人物蹲、趴、站状态，枪的类型，倍镜，以及是否开镜等状态
- 观察代码发现，QtScrcpy 使用 OpenGL 渲染 yuv 图像。因此在 render 过程中，每隔一段时间，取一帧图像出来，截取按钮位置的图像，交给 OpenCV 识别，按钮位置应该记录在配置文件中，方便修改
- 维护一个 map，根据不同的游戏状态，设置相对应的压枪参数，这个 map 也应该记录在配置文件中
- 添加一种按键类型 KMT_RECOIL，当按下该键时，发出 touchdown 事件，并根据压枪参数每帧或定时进行 touchmove， 当按钮抬起时，发出 touchup

### 实现过程（待续）


