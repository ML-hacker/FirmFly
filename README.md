# 基于微控制器虚拟化技术的高可信无人机操作系统设计

## 项目背景

   无人机飞控由于功耗、体积、价格、重量等限制，常常基于微控制器打造。它们往往运行简单结构的实时操作系统（FreeRTOS、RT-Thread等），其内部的各个软件模块之间没有隔离边界，一旦其中任何一个模块崩溃，都会影响到其它模块，使整个无人机重启，因此整个无人机的软件栈都需要经过同等水平的认证测试以保证其可靠性。在传统开发流程中这没有问题，因为几乎整个软件栈都是由同一个开发团队编写维护的，第三方软件的总量整体可控。

## 核心挑战

   随着无人机软件规模的增长，无人机系统中的第三方库与日俱增。这些库可以提供5G通信、避障导航、雷达探测甚至是AI推理等丰富的功能，但它们的可靠性和安全级别都较低，一旦任何库故障或被攻击，无人机都有坠机或被劫持的风险。而对它们进行固件级别的安全认证非常困难：  
1. **知识产权限制**：第三方库常以二进制形式交付，源码审查不可行。
2. **代码审查难题**：某些移植自桌面系统的库代码量庞大，人工审计成本过高。

## 技术方案

   因此，我们需要在飞控微控制器上引入**分区操作系统**和**保护域抽象**，使原本作为一个整体的固件分隔到不同的保护域；任何一个保护域的崩溃都不影响其它保护域的正常运转，只需要重启崩溃的保护域即可。比如：  
- **故障隔离示例**：涉及图像识别的保护域崩溃只会暂时致盲无人机  
- **可靠性提升**：视觉模块异常不会导致控制系统故障重启  

   为了兼容原有的使用了FreeRTOS或RT-Thread的无人机技术栈，每个保护域都要能运行自己的FreeRTOS或RT-Thread副本，相当于将一个微控制器分割成多个子控制器，其中每个子控制器有自己的虚拟硬件，各负责一个无人机子系统的功能。这种概念和云计算等领域的虚拟化技术非常相似：一方面，它能够隔离故障和入侵，另一方面又能兼容已有代码。为此，我们需要利用Cortex-M微控制器（或RISC-V微控制器）中的**内存保护单元（MPU）**来进行这一分割。

## 实现目标

  综上所述，本项目意图在无人机微控制器上建立起虚拟化环境，在不显著增加无人机成本的前提下将传统无人机的功能组件分割到不同的RTOS实例中，实现高水平的容错性和抗攻击性。
  
## 要求：
- 根据任意开源无人机项目（可自选；只要基于微控制器即可）设计并组装一架四轴飞行器，并成功地在其上运行自带飞控程序。

- 重新设计四轴飞行器的电路并引入具备安全隔离功能（MPU）的微控制器，并将其原有飞控代码移植过来。推荐的选项是STM32F767VGT6，也可选STM32F405RGT6等。

- 分析原始飞控程序的组成，并将其进行合理的软件模块划分。利用现有的虚拟化基础设施（参见参考材料）以建立多个预装常见RTOS（优选RT-Thread，可选FreeRTOS等）的虚拟机。

- 在各个虚拟机中添加各个软件功能模块，并创建各个功能的标准镜像；如果无人机需要某种功能，则添加此镜像到虚拟机集群中即可。在此基础上，要求对镜像之间的功能枚举、跨虚拟机功能调用等提出一套通用API标准，以提高系统的整体可用性。

- 对某些模块施加干扰或破坏，观察并评价保护域隔离的对抗软件错误和恶意攻击的优势。无人机在任意虚拟机（除飞控虚拟机外）故障时，应当能够正确地重启相应虚拟机，不致使整个系统失控。

**项目注重架构设计本身，不要求编写飞控算法和功能，也不要求功能模块的数量；如选择复杂的飞控如ArduPilot，则仅移植数个必要模块即可，不要求能运行所有的模块；各飞控功能的源码可以引用任何开源代码，只要注明来源即可。**

## License
- CC0（Unlicense）；第三方代码则依第三方License

## 微控制器虚拟化基础设施
https://github.com/EDI-Systems/M7M01_Eukaron
https://github.com/EDI-Systems/M7M02_Ammonite

ArduPilot（复杂；更有挑战性，能运行它的硬件造价稍高）
https://github.com/ArduPilot/ardupilot

蓝鸟四轴（相对简单；硬件廉价，方便网购，需要自行更换微控制器为带MPU的版本）
https://github.com/wangdy-code/Quadcopter/

## 联系方式

20191081@gdufe.edu.cn
