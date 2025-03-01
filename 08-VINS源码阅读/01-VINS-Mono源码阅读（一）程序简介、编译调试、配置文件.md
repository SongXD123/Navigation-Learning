[TOC]

## 一、VINS-Mono 简介

### 1、程序概述

与双目相机和 RGB-D 相机相比，单目相机具有结构简单、成本低和处理速度快的优点。然而，单目 VSLAM 存在尺度不确定性、无法对齐位姿和重力方向的自身缺点和快速运动导致的运动模糊的环境下容易跟踪丢失等不足。为弥补此问题，可将单目相机和 IMU 相结合的传感器融合，这种融合方案被称为单目视觉惯性里程计（Visual Inertial Odometry，VIO）或单目视觉惯性 SLAM（Visual-inertial SLAM，VINS）。

* IMU 也可以弥补视觉 SLAM 在短时间、快速运动上的不足，另外由于 IMU 不依赖外界环境信

  息，对环境变化不敏感，也可以在少纹理、明暗变化较大或者光线较弱场景内提供短期的定位方案以及位姿估计方案。

* 较之惯性信息，视觉里程计可以提供丰富的外界信息，在低速平稳的运动中位姿估计稳定，而且视觉里程计在长时间运行后的漂移较小，并且可以通过回环检测修正自身位置以减小累积误差。

因此，在 SLAM 的众多分支中，无论在理论还是实践上，视觉-惯性融合为导航定位的研究工作提供了一个十分有前景的、小型化和低成本化的改进方案。

香港科技大学沈劭劼团队开发的 VINS 系统，用了一种紧耦合的非线性优化方法。该团队在 2017 年发布的 VINS-Mono 通过在四元数上进行 IMU 的预积分，并且采用滑动窗口法融合 IMU 信息和相机观测到的特征数据，实现了数据的紧耦合。并且采用四自由度的图优化方法实现了回环检测模块，来得到全局约束。在 2019 年，该团队又发布了 VINS-Fusion，在 VINS-Mono 的基础上又加入了双目、双目+IMU 等更多的传感器类型，以及支持了 VINS 和 GPS 的融合。它支持在线标定相机及 IMU 参数及鱼眼相机模型，并且支持保存当前地图和加载过往地图。在与 IMU 的结合上，它采用了四元数积分方案，与视觉信息进行紧耦合，具有很强的鲁棒性和定位精度。

### 2、资源获取

* GitHub 上开源了 VINS-mono，VINS-Fusion 和 VINS-mobile



### 3、代码分析





### 4、程序功能

程序主要分为五部分：

* 传感器数据处理：Camera 特征提取追踪、IMU 预积分
* 初始化：
* 基于滑动窗口的非线性优化：
* 闭环检测：
* 全局位姿图优化：











### 5、程序执行流程图





ROS 节点图如下：

![image-20230815190307979](https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/image-20230815190307979.png)



## 二、编译调试









## 三、配置文件













