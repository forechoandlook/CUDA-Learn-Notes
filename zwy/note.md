

## 感觉还是很难啊  我靠  哎




## programming model

cluster 的概念？

是一种逻辑概念，会用来划分线程块中的线程组。是用来说线程分组的。

线程块的线程共享同一个共享内存和寄存器空间，而 cluster 则允许进一步在块内部划分线程组，从而形成更小的工作单元。这在处理某些算法（如递归分解或多级问题）时可能会带来性能提升。

应用场景：
1. 线程间通信
2. 分层递归算法
3. 共享内存划分

需要看是否支持cluster？ Ampere 或 更高架构 提供的warp级或block级的特殊操作

cluster 是一组warp的逻辑组合，并且可以在硬件上实现更高效的调度和共享内存管理

### 内存层次结构

texture memory : data filtering and addressing modes

### heterogeneous Programming

uniform memory access 

### 异步simt编程模型

用于cuda线程之间同步的内存屏障的行为 cuda::memcpy_async 在 GPU 中计算时从全局内存异步移动数据。

异步操作：由cuda线程发起并像由另一个线程一样异步执行的操作。

一个或多个 CUDA 线程与异步操作同步。启动异步操作的 CUDA 线程不需要位于同步线程中。

`cuda::memcpy_async` `cooperative_groups::memcpy_async`

对于基于NVIDIA Hopper GPU架构的设备，主要修订号为 9；对于基于NVIDIA Ampere GPU架构的设备，主要修订号为 8；对于基于Volta架构的设备，主要修订号为 7；对于基于Pascal架构的设备，主要修订号为 6；对于基于 NVIDIA Ampere GPU 架构的设备，主要修订号为 5。 Maxwell架构，3 表示基于开普勒架构的设备


### 编译工作流程

offline compilation : nvcc 

1. device code -> assembly form (PTX code) and/or binary form ( cubin object )

just in time compilation

当设备驱动程序即时编译某些应用程序的某些PTX代码时，它会自动缓存生成的二进制代码的副本，以避免在应用程序的后续调用中重复编译。当设备驱动程序升级时，缓存（称为计算缓存）会自动失效，因此应用程序可以从设备驱动程序中内置的新即时编译器的改进中受益。

ptx compatibility

`warp shuffle functions` 仅在 计算能力 5.0 及更高版本的设备上可用

内存api相关内容：

线性内存
设备内存L2访问管理：当CUDA内核重复访问全局内存中的数据区域时，这种数据访问可以被认为是持久的。另一方面，如果数据仅被访问一次，则这种数据访问可以被认为是流式的。

streaming and persisting 

L2 高速缓存的一部分可以留出用于对全局内存进行持久数据访问。持久访问优先使用 L2 缓存的这部分预留部分，而对全局内存的正常或流式访问只能在持久访问未使用时才利用 L2 的这部分。

用于持久访问的 L2 缓存预留大小可以在限制范围内进行调整

当 GPU 配置为多实例 GPU (MIG) 模式时，L2 缓存预留功能将被禁用。

L2 policy for presisting accesses 访问策略窗口指定全局内存的连续区域以及 L2 缓存中用于该区域内访问的持久性属性。