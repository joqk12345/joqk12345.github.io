---
layout:       post
title:        "AI Chip Basic"
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - Chip
    - AI
    - CPU
    - GPU
---

# AI Chip


## 通用处理器 CPU
* CPU 发展历史
  * ENIAC:(Electronic Numerical Integrator And Calculator，电子数字积分计算机)世界上 第一台真正意义上的计算机，于1946年在美国宾夕法尼亚大学投入运行，采用十进制进行数据 存储。ENIAC的发明，奠定了电子计算机的发展基础，开辟了信息时代的新纪元，是人类第三 次产业革命开始的标志，具有重要的历史意义。
  * EDVAC:(Electronic Discrete Variable Automatic Computer，电子离散变量自动计算机 )。世界上首次提出的第一台采用二进制的冯·诺依曼计算机，由运算器、控制器、存储器、输 入和输出设备5部分组成。1945年3月由冯·诺伊曼本人与莫奇利、埃克特等提出，1951年最终 完成。
* CPU 主要组成
  * 从彼时至今，无论CPU的具体实现怎么变、晶体管数量翻多少番，它的构成始终由运算器、控
制器和寄存器这三大部分组成。
    ![](/img/in-post/post-ai/hardware/cpu-componts-2.png)
  *  运算器:也叫算术逻辑单元(ALU)，负责算术运算和逻辑运算
  *  寄存器:分为指令寄存器和数据寄存器，负责暂存指令、ALU所需操作数、ALU算出结果等
    ![](/img/in-post/post-ai/hardware/register.png)
  * 控制器:负责调度工作，包括对要执行的指令进行译码、从内存中调取数据给寄存器、向运算
器和寄存器发出具体操作指令等。
    ![](/img/in-post/post-ai/hardware/controller.png)
* CPU 工作流程
    ![](/img/in-post/post-ai/hardware/cpu-work-process.png)
* 冯·诺依曼架构
   * CPU三大组成的各自分工，控制器和寄存器负责的工作最多、要存的数据最多的两部分。
  ![](/img/in-post/post-ai/hardware/CPU09.png)
* 约束与限制
  * 实质上ALU模块(逻辑运算单元)是用来完成数据计算，其他各个模块的存在都是为了保证指令 能够一条接一条的有序执行。这种通用性结构对于传统的编程计算模式非常适合，同时可以通 过提升CPU主频(提升单位时间内执行指令的条数)来提升计算速度。
  * 但是，依照冯·诺依曼架构针对指令的“顺序执行”的原则，CPU 只能执行完一条指令再来下 一条，计算能力进一步受限。

### Parallel Processing 并行处理架构
* 单指令流单数流(SISD)系统。
  * 每个指令部件每次仅译码一条指令，而且在执行时仅为操作部件提供一份数据
  * 串行计算，硬件不支持并行计算;在时钟周期内，CPU只能处理一个数据流。
* 单指令流多数据流(SIMD)系统。 
  * 一个控制器控制多个处理器，同时对一组数据中每一个分别执行相同操作
  * SIMD主要执行向量、矩阵等数组运算，处理单元数目固定，适用于科学计算
  * 特点是处理单元数量很多， 但处理单元速度受计算机通讯带宽传递速率的限制
* 多指令流单数据流(MISD)系统。 
  * 多指令流单数据流机器，采用多个指令流来处理单个数据流
  * 作为理论模型出现，没有投入到实际应用之中
* 多指令流多数据流(MIMD)系统。
  * 在多个数据集上执行多个指令的多处理器机器
  * 共享内存 MIMD 和分布式内存
* 并行计算处理硬件架构(IV): SIMT 系统
  * 单指令多线程，有效地管理和执行多个单线程，允许一条指令的多数据分开寻址 
  * 无需开发者把数据凑成合适的矢量长度，并且SIMT允许每个线程有不同的分支 
  * 条件跳转会根据输入数据不同在不同的线程中有不同表现

### ISA 指令集架构
1. ISA - Instruction Set Architecture
   1. CPU 的全称叫中央处理器单元，通常用来区分 CPU 的标准是指令集架构(Instruction Set Architecture，简称 ISA)。
   2. 开发人员基于指令集架构(ISA)，使用不同的处理器硬件实现方案，来设计不同性能的处理器 ，因此 ISA 又被视作 CPU 的灵魂。
2. ISA - Instruction Set Architecture 基本分类
   1. 运算指令:在 ALU 中执行的计算操作
   2. 数据移动指令:读写存储操作(包括寄存器读写)
   3. 控制指令:更改指令执行顺序，进行程序跳转，实现 if/else，循环等
![](/img/in-post/post-ai/hardware/ISA03.png)
3. ISA - Instruction Set Architecture 声明周期
   1. FETCH:将Memory中的指令放入Instruction Register ，PC指定指令位置
   2. DECODE:通过指令解码过程，识别指令内容，开启控制信号通路
   3. EVALUATE ADDRESS:比如加载内存之前，通过寄存器内容和偏移量获得真正的内存位置 • FETCH OPERANDS:加载寄存器中的操作数，不同指令实际执行的内容不同
   4. EXECUTE:在ALU中执行计算逻辑
   5. STORE RESULT:存储计算结果
4. ISA - Instruction Set Architecture
   1. 可以将指令集架构理解为一个抽象层，它是处理器底层硬件与运行在硬件上的软件之间桥梁和接口。
    ![](/img/in-post/post-ai/hardware/ISA01.png)
### CISC 架构与 RISC 架构
1. CISC 架构:除常用指令还包含许多不常用特殊指令。随着越来越多的特殊指令被添加到 CISC 架构中，常用程序运算指令仅占指令集 20%，80% 指令则很少用到，而这些很少用到的指令让 CPU 的设计变得极其复杂，大大增加了硬件设计的时间成本和面积开销。
2. RISC 架构:只包含处理器常用指令，对于不常用操作，执行多条常用指令的方式来达到同样 的效果。因而在 RISC 架构诞生后，移动端设备设计的 CPU 都倾向于选择使用 RISC 架构。
![](/img/in-post/post-ai/hardware/ISA02.png)

3. CISC 架构:除常用指令还包含了许多不常用的特殊指令。随着越来越多的特殊指令被添加到 CISC 架构中，常用的典型程序运算过程中用到的指令仅占指令集的20%，80%的指令则很少用 到，而这些很少用到的指令让 CPU 的设计变得极其复杂，大大增加了硬件设计的时间成本和面 积开销。

4. RISC 架构:只包含处理器常用的指令，对于不常用的操作，通过执行多条常用指令的方式来 达到同样的效果。因而在 RISC 架构诞生后，移动端设备设计的 CPU 都倾向于选择使用 RISC 架构。
![](/img/in-post/post-ai/hardware/risc_cisc01.png)

5. CISC vs RISC

6. ISA 种类
CPU 于上世纪 60 年代问世，已发展几十年，有几十种不同的指令集架构相继诞生或消亡。
![](/img/in-post/post-ai/hardware/isa_categray.png)

7. 总结

* CPU 具备图灵完备，可自运行的处理器。其主动从指 令内存读取指令流，然后译码后执行;指令执行会涉 及到数据的载入(Load)、计算和存储(Store)。
* ISA 是计算机体系结构与编程相关的部分，定义了: 指令集、数据类型、寄存器、寻址模式、内存管理、 I/O模型等。
* 可以把 CPU 简单地分为控制平面和计算平面两部分 ，CPU 作为指令流驱动计算的处理引擎。
![](/img/in-post/post-ai/hardware/cpu_abstract.png)

### 延时

" CUDA的架构师Stephen Jones发表了关于GPU计算以及它如何受到硬件和物理定律限制的演讲。他认为，人们在购买机器时不应该关心flops，而应该关注数据。他声称，理解这些原则对于让机器做想要的事情至关重要。

1. 从本质上讲，现代处理器的计算强度是由处理器的内存带宽和它每秒可计算的千兆运算数量之间的比例决定的。为了达到良好的性能，算法必须有很高的计算强度--也就是说，它们必须对它们收到的每一个数据做大量的工作。唯一符合这一标准的算法是矩阵乘法，它是唯一一种每负载需要80次运算的算法。虽然翻转率很重要，但大多数算法都无法跟上处理器的需求，更多的翻转数可能不等于性能的提高。

2. 计算的翻转率越来越高，这使得程序员需要使用算法来保持现代芯片的忙碌。内存带宽有限的程序比flop密集型程序更常见，延迟是编程时需要考虑的一个重要因素，因为加载数据所需的时间需要与flop功率相平衡。

3. 加载y并不取决于x，因为有一种叫做流水线的技术，额外的内存操作被有用的工作所隐藏。流水线是大多数程序优化的核心，因为由于物理学的限制，内存的延迟与计算机的延迟相比是巨大的。"




## 参考文件

1. >[ Processor_Architecture_Design_Practices_Survey ]
   (https://www.researchgate.net/publication/50281783_Processor_Architecture_Design_Practices_Survey_Issues/fulltext/57a7b60108ae3f45293919aa/Processor-Architecture-Design-Practices-Survey-Issues.pdf) 

2. > [ How GPU Computing Works ]
   (https://www.youtube.com/watch?v=3l10o0DYJXg)