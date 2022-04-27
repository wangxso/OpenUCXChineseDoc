# UCX 主要功能

## 高层API功能

- 选择服务端/客户端简历连接(类似TCP)，或者通过远程地址blob直接连接。
- 支持在线程之间共享资源或者给每个线程分配专有资源。
- 事件驱动或轮询驱动过程。
- 与Java和Python绑定。

## 主要API

- 面向流的发送/接收操作
- 标签匹配的发送/接收
- 远程内存访问
- 远程原子操作

## 基础架构支持

- RoCE
- InfiniBand
- TCP Socket
- 共享内存(CMA, knem, xpmem, SysV, mmap)
- Cary Gemini/Aries(ugni)


## 平台支持
- 支持的指令集有: X86_64, Arm V8, Power。
- 可以在虚拟机(SRIOV)或容器(docker, singularity)内运行。
- 可以使用MLNX_OFED或Inbox RDMA的驱动。
- 在主流的Linux分支(RedHat/Ubuntu/SLES)上进行了测试。


## GPU支持
- CUDA(Nvidia GPUs)
- ROCm(AMD GPUs)

## 协议、优化和高级功能
- 自动选择最佳的传输路径和设备
- 带有寄存器缓存的零拷贝
- 可拓展的流量控制算法
- 自适应的内存池
- 对Mellanox设备直接传输的加速
- GPU内存管道协议
- RDMA传输与QoS隔离
- 平台(微指令)特定优化(比如memcpy、内存屏障等)
- Multi-rail和RoCE连接聚合组的支持
- 裸机、容器和云环境的支持
- 用于传输不同大小消息的高级协议