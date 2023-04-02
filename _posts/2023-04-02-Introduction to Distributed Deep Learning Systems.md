---
layout:       post
title:        "Introduction to Distributed Deep Learning Systems"
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - Distributed Compute
    - AI cluster
    
---

# AI 分布式集群服务架构

   大规模分布式训练中主要使用参数服务器架构模式(PS)，参数服务器分布在多个 GPU 是 PS 模式的一种特殊形态;
   PS架构下通过集合通信来实现环同步，从而同步分布在多个 GPU 中的参数， Ring All Reduce 是环同步的经典同步方式;


## AI集群用服务器架构

* 同步
   ![](/img/in-post/post-ai/training/sync_archi.png)
   * 必须等全部工作节点完成了本次通信之后才能继续下一轮本地计算
     * 优点:本地计算和通信同步严格顺序化，能够容易地保证并行的执行逻辑于串行相同
     * 缺点:本地计算更早的工作节点需要等待其它工作节点处理，造成了计算硬件的浪费。

* 异步
   ![](/img/in-post/post-ai/training/asyn_archi.png)
   当前batch迭代完后与其他服务器进行通信传输网络模型参数
   * 优点:执行效率高，中间除了单机通信时间以外没有任何通信和执行之间的阻塞等待
   * 缺点:网络模型训练不收敛，训练时间长，模型参数反复使用导致无法工业化

* 半同步并行
   ![](/img/in-post/post-ai/training/Semi-synchronous.png)
   * 通过动态限制进度推进范围，有限定的宽松同步障的通信协调并行
   * 跟踪各节点进度并维护最慢节点，保证计算最快和最慢节点差距在一个预定的范围内
  


### 分布式架构之参数服务器
参数服务器PS架构已经成为现在AI集群、AI服务器中最常用的组网架构，有了参数服务器PS架构之后，训练大模型就需要解决模型参数同步的问题，于是出现了各种各样的物理网络互联方式，通过集合通信进行网络数据传输。而环是一种较优的网络拓扑，通过Ring All Reduce算法可以有效地解决参数服务器之间的数据同步问题。

* 聚合的NVLink带宽明显高于PCIe带宽，问题是如何有效地使用环。
  
![](/img/in-post/post-ai/training/ring_Synchronization.png)


![](/img/in-post/post-ai/training/ring_sync_2.png)

* how
  1. 首先是scatter-reduce，在scatter-reduce步骤中，GPU将交换数据，使每个GPU可得到最终结果的一个块。

![](/img/in-post/post-ai/training/scatter-reduce.png)

  2. 然后是allgather。在allgather步骤中，gpu将交换这些块，以便所有gpu得到完整的最终结果

![](/img/in-post/post-ai/training/all-gather.png)





## 参考文件

1.  > [ 参数服务器 ]
   (https://openmlsys.github.io/chapter_distributed_training/parameter_servers.html) 

2. > [ DeepLearningSystem]
   (https://github.com/chenzomi12/DeepLearningSystem) 

3. >[ 分布式计算介绍 ]
   (https://zhuanlan.zhihu.com/p/161652132?utm_id=0)



