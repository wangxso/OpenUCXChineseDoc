# OpenUCX

Unified Communication X (UCX) 是一个[屡获殊荣](https://losalamosreporter.com/2019/11/07/nine-los-alamos-national-laboratory-projects-win-rd-100-awards/)，经过生产验证的优化通信框架，适用于现代的、高带宽的、低延迟的网络。

UCX采用了一系列的通信原语来达到最好的硬件资源利用率和负载。这些包括了RDMA (InfiniBand 和 RoCE), TCP, GPUs, 共享内存 和 network atomic operations。

UCX隐藏了底层的细节，可以通过高层的API在保持高性能和可拓展性下面快速开发。

UCX实现了传送各种大小的消息的最佳实践，这来自于世界上最大的数据中心和超级计算机上运行的程序锁获得的经验。你点击[这里](https://openucx.readthedocs.io/en/master/ucx_features.html#ucx-features)可以找到完整的功能列表。

UCX项目是[UCF](https://ucfconsortium.org/)的成员之一。

![UCX layer diagram](https://openucx.readthedocs.io/en/master/_images/UCX_Layers.png)

## 快速开始

以下命令将下载最新的UCX v1.12.1 release 版本，编译代码，并且运行一个简单的客户端/服务端实例:

```bash
wget https://github.com/openucx/ucx/releases/download/v1.12.1/ucx-1.12.1.tar.gz

tar xzf ucx-1.12.1.tar.gz

cd ucx-1.12.1

./contrib/configure-release --prefix=$PWD/install

make -j8 install

gcc examples/ucp_client_server.c -lucp -lucs -o ucp_client_server \
      -Iinstall/include -Linstall/lib

export LD_LIBRARY_PATH=$PWD/install/lib
./ucp_client_server &
./ucp_client_server -a <ip-addr>   # <ip-addr>: IP address of a local RoCE or IPoIB interface

...
----- UCP TEST SUCCESS -------

UCX Client-Server Hello World

------------------------------
```

