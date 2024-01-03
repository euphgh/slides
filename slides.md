---
theme: seriph
class: text-center
highlighter: shikiji
colorSchema: 'light'
lineNumbers: false
drawings:
  enable: true
  persist: false
transition: slide-left
mdc: true
selectable: true
background: ""
---

##### 2023 32nd International Conference on Parallel Architectures and Compilation Techniques (PACT)

<br>

## MBAPIS: Multi-Level Behavior Analysis Guided Program Interval Selection for Microarchitecture Studies

<br>

### Hongwei Cui, Yujie Cu, Honglan Zhan, Shuhao Liang, Xianhua Liu, Chun Yang and Xu Cheng


Engineering Reserach Center of Microprocessor & System, Ministry of Education, <br>
School of Computer Science, Peking University

National Key Laboratory for Multimedia Information Processing, <br>
School of Computer Science, Peking University


---
transition: fade-out
---

## 作者简介

以北京大学中国学生和教师为主

- **崔宏伟， 顾玉杰， 詹红岚** - 北大微处理器及系统教育部工程研究中心直博生
- **ShuHao Liang（梁淑昊）** - 北大微处理器及系统教育部工程研究中心（不确定是学生还是教职工）
- **刘先华** - 北大系统结构研究所副教授
  - 2000年获得清华大学学士学位，2007年获得北京大学博士学位
- **杨春** - 北大系统结构研究所助理研究员
  - 2001年获得清华大学学士学位，2008年获得北京大学博士学位
- **程旭** - 北大计算机体系结构研究所教授
  - 1988年获哈尔滨工业大学学士学位，1991年获硕士学位，1994年获博士学位
  - 2010年起担任北京大学校长助理，
  - 2011年起担任先进技术研究院院长
  - 自2002年起担任微处理器研发中心（MPRC）主任和计算机体系结构研究所所长

---
layout: backgroud-motivation
transition: slide-up
level: 3
---
## 背景与动机

处理器模拟大型banchmark花费时间长，通过选择**代表区间**减少模拟量

- **SimPoint**：与处理器微架构无关的程序行为描述法
  - 无法为特定的微架构研究选择有**针对性的区间**
- 使用硬件相关信息来选择区间，例如**硬件性能计数器**
  - 仅依靠硬件计数器选择区间不能完全分析程序行为
  - 选用的硬件计数器类型有待研究
  - 需要手动识别行为不同的区间，**硬件事件突出不代表对是性能优化的瓶颈**

---
layout: two-three
transition: slide-up
---

## 背景介绍——Simulation Points

一种硬件无关的，自动选择**能够描述大规模程序行为的程序段**的技术

- 按照指令数将程序分为不同阶段（Phases）
- 计算各个阶段的**基本块向量（Basic Block Vectors）**
- 根据各基本块向量的距离进行*K-means*聚类
- 根据聚类结果挑选具有代表性的阶段，**可估算IPC、硬件计数器数值**
![simpoint est](/simpoint/est.png)

::right::

<br>

![simpoint est](/bbv.png)

<br>

![simpoint est](/simpoint/bbsm.png)




---
layout: two-cols
---

## 背景介绍——TopDown性能分析

一种新颖的性能计数器架构，用于确定一般乱序处理器的真实瓶颈

- 把所有**发射的指令**分为4个大类
  - Frontend Bound - 前端供应不足
  - Retiring - 指令已退休
  - Bad Speculation - 指令未退休
  - Backend Bound - 后端消耗不足
- 每个大类具有不同的层次结构，可向下划分
- 根据划分的结果指导优化

<img src="/mbapis/topdown.png" class="mb-4 mr-4" />

::right::

<img src="/topdown/ooo-cpu.png" class="ml-24 size-1/2" />
<img src="/topdown/top-break.png" class="ml-6" />

---
transition: slide-up
---


## 论文要点

<br>

#### 对于**给定的微架构研究**，如何选择与其相关的、具有**明显硬件行为特征和性能瓶颈**的区间 ？ 

<br>

- 提出一种**面向多层次行为分析**的**程序段选择方案**——定制区间（tailored intervals）


- 提出一种**通用、可扩展的区间重放设计**，能够在各种执行环境中精确重放


- 使用SonicBOOM，以SPEC 2006和SPEC 2017为benchmark进行评估。

  - 定制区间对硬件事件的评估能力明显优于SimPoint
  
  - 重放设计能够准确估计定制区间的硬件事件

---
transition: slide-up
layout: two-cols-header
---

## 方法介绍——面向多层次行为分析的程序区间选择方案

::left::
使用软件和硬件行为信息，进行分层分析，<br>
逐步选择具有代表性的定制区间

1. 将程序按指令数划分区间
2. 选择的硬件事件最高的$sRatiosL1\%$区间
3. 选择TopDown方法指出的对性能影响最大的$sRatiosL2\%$区间
4. 以上两步结果相与，再用*K-means*聚类分析

<img src="/mbapis/lv1.png" />

::right::
<img src="/mbapis/lv2.png" />
<img src="/mbapis/lv3.png" />

---
layout: two-cols-header
transition: slide-up
---

## 方法介绍——程序区间重放

以还原**用户态程序**的性能计数器特征为目标的**非确定型重放**

```mermaid {theme: 'neutral'}
flowchart LR
    程序区间 -- gem5仿真 --> 检查点文件 -- 检查点加载程序 --> 可重放程序
```

::left::
使用 gem5 的 atomic CPU 生成检查点

- 架构寄存器的区间初始值
- 区间起始、结束指令的地址
- 堆栈指针范围，被加载器使用
- 重放**系统调用**的必要信息
  - 输入输出
  - **内存变化，仅记录首次加载（FLL）**

First-Load Log： 仅记录重放区间内第一次 <br>
对该地址的load数据，其他load数据可以<br>
在重放期间生成。

::right::

![alt](/bugnet.png)

05年在ISCA发表的**用户级软件确定性重放**架构：BugNet

--- 
layout: two-cols
---


## 方法介绍——通用检查点加载器

**不依赖操作系统和外部库**的，用于解析、加载检查点的**程序**

- 处理加载器的堆栈指针和区间程序的堆栈指针
  - 类似OS和用户程序的堆栈指针关系
- 接管并重构系统调用的效果
  - 替换所有系统调用指令为跳转指令
  - 跳转接管函数，读取并恢复检查点记录的系统调用
- 恢复必要的内存状态后重定向首指令
  - 使用读写执行权限加载代码段
  - 数据段仅还原所有First-Load Log
- 终止区间重放
  - 区间尾指令替换为特殊系统调用
-  指令预热和硬件计数器精确采样用于优化重放效果

:: right :: 

![stack point and sys](/mbapis/point-sys.png)
![exit interval](/mbapis/exit.png)

<!-- 没有必要将跳转指令换掉，为什么不直接写异常处理函数 -->

---
layout: image-right
image: /mbapis/est.png
backgroundSize: contain
---

## 效果评估

使用SonicBOOM，以SPEC 2006和SPEC 2017为benchmark进行评估

- 在FPGA平台上运行完整benchmark，得到<br>**每个区间**的硬件计数器采样值
- 以**分支预测失误次数、DCacheMiss次数、IPC**为指标衡量评估精度
- 用SimPoint和MBAPIS选取的**代表区间的<br>采样值估计平局值**
- 仅在加载器上运行MBAPIS选取的代表区间，采样并计算平均值


---

## 总结

<br>

- 提出一种面向多层次行为分析的程序段选择方案——定制区间（tailored intervals）
  - **需要硬件计数器采样支持**
  - 结合了**TopDown性能分析法**和**SimPoint的*K-means*聚类**
- 提出一种通用、可扩展的区间重放设计，能够在各种执行环境中精确重放
  - 仿真生成检查点文件
    - 仅记录架构和系统调用信息
    - 用于用户态程序的非确定性重放
  - 检查点加载器用于解析、加载检查点文件，生成可重放程序
- 使用SonicBOOM，以SPEC 2006和SPEC 2017为benchmark进行评估。
  - 定制区间对硬件事件的评估能力明显优于SimPoint
  - 重放设计能够准确估计定制区间的硬件事件