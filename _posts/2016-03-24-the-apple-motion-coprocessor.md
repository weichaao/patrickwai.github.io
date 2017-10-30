---
layout: post
title: "苹果运动协处理器"
description: ""
category: iPhone
tags: []
---
##Apple motion coprocessors 苹果运动协处理器

The Apple M7 (codename Oscar), M8, and M9 are motion coprocessors used by Apple Inc. in their mobile devices. 
Their function is to collect sensor data from integrated accelerometers, gyroscopes and compasses and offload 
the collecting and processing of sensor data from the main central processing unit (CPU). The M7 was introduced 
in September 2013 with the iPhone 5S and the updated version, M8 was introduced in September 2014 with the 
iPhone 6 and also processes data from the barometer that is included in the iPhone 6 and iPad Air 2. September 
2015 brought the M9 motion coprocessor embedded within the A9 chip found in the iPhone 6S, iPhone 6S Plus, and within 
the A9X chip found in the iPad Pro.

M7, M8, M9是苹果公司的运动协处理器(motion coprocessors)，被应用在移动设备上。功能是收集传感器数据(来自集成的加速度计，陀螺仪，指南针)。
中央处理器不用再参与收集和处理这些数据。M7在2013年被介绍，和iPhone5s一起。新版本的M8在2014年9月被介绍，和iphone 6一起。它也处理
来自气压计的数据。气压计被包含在iphone 6 和iPad Air 2。2015年9月出了M9协处理器，内嵌在A9芯片和A9X芯片中。A9被发现在iPhone 6s和
iPhone 6s plus。A9X被发现在iPad pro。

Chipworks found that the M7 most likely is a NXP LPC1800 based microcontroller called LPC18A1. It uses an ARM Cortex-M3 
core with a customised packaging and naming scheme indicating that it is for an Apple customized part. iFixit have 
identified the M8 in the iPhone 6 to be an NXP device with a very similar name, the LPC18B1.

Chipworks发现，M7很可能是一个`LPC1800`(以叫做`LPC18A1`的微控制器为基础)，它用ARM Cortex-M3核心(定制的包装和命名标识)，作为苹果
定制的部分。iFixit已经指出，iPhone 6的M8是一个NXP设备，拥有一个相似的名字，LPC18B1。

Chipworks found that the M7 most likely is a NXP LPC1800 based microcontroller called LPC18A1.</br>
Chipworks发现M7很可能是一个`LPC1800`(以叫做`LPC18A1`的微控制器为基础)。</br>
Chipworks发现M7很可能是一个基于叫做`LPC18A1`的微控制器的`NXP LPC1800`。

##Usage 应用

The Apple M7, M8, and M9 coprocessors collect, process, and store sensor data even if the device is asleep, 
and applications can retrieve data when the device is powered up again. This reduces power draw of the device and 
saves battery life. In addition to servicing the accelerometer, gyroscope, compass, and barometer, the M9 coprocessor 
can recognize Siri voice commands.

运动协处理器收集、处理、存储传感器数据，即使设备在睡眠状态。应用程序可以取得数据，当设备再次打开时。这样降低了电耗，延长了电池寿命。
除了服务于加速记、陀螺仪、指南针和气压计之外，M9协处理器也可以识别Siri语音命令。

The M-series motion coprocessors are accessible to applications through the Core Motion API introduced in iOS 7, 
so they do, for example, allow fitness apps that track physical activity and access data from the M processors without 
constantly engaging the main application processor. They enable applications to be aware of what type of movement the user 
is experiencing, such as driving, walking, running, or sleeping. Another application could be the ability to 
do indoor tracking and mapping.

M系列的运动协处理器对app是开放的，通过`Core Motion API`(出现在IOS 7)。因此他们确实，例如，允许fitness app追踪身体活动，获取数据，
不必使用CPU。他们使应用知道用户正处于什么类型的运动，比如驾车，步行，跑步，或者睡觉。其他应用可以做内部的追踪和映射。

英文部分文出自 [维基百科 - Apple motion coprocessors](https://en.wikipedia.org/wiki/Apple_motion_coprocessors)
