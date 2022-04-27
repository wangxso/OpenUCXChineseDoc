# 运行UCX
## UCX 编译与安装
### 获取源码
- 从github中获取最新的源码
  ```bash
  git clone https://github.com/openucx/ucx.git ucx
  cd ucx
  ./autogen.sh
  ```
- 或者下载并解压UCX预先配置中的一个UCX [releases:](https://openucx.readthedocs.io/en/master/download.html)
  ```bash
   wget https://github.com/openucx/ucx/releases/download/v1.12.1/ucx-1.12.1.tar.gz
   tar xzf ucx-1.12.1.tar.gz
   cd ucx-1.12.1
  ```
- (这一步仅对OpenPOWER平台需要)在Ubuntu平台下面```config.guess```文件有点过时了并且没有很好的支持power平台。为了解决这个问题，你应该下载并且更新你的```config.guess```。从项目的根中:
  ```bash
  wget https://github.com/shamisp/ucx/raw/topic/power8-config/config.guess
  ```

### 编译
1. Configure：
   ```bash
   mkdir build
   cd build
   ../configure --prefix=<ucx-install-path>
   ```
2. 编译安装
   ```bash
   make -j4
   make install
   ```


## OpenMPI和UCX结合
[OpenMPI](https://www.open-mpi.org/)从3.0版本开始支持UCX，但是为了稳定和性能我们推荐您使用4.0以上版本的OpenMPI。

### 编译安装
1. 获取最新和最好的OpenMPI版本
   ```bash
   git clone https://github.com/open-mpi/ompi.git
   cd ompi
   ./autogen.pl
   ```
2. 配置UCX
   ```bash
   mkdir build-ucx
   cd build-ucx
   ../configure --prefix=<ompi-install-path> --with-ucx=<ucx-install-path>
   ```
   **NOTE:**在OpenMPI 4.0及以上版本``` btl_uct```组件可能会出现汇编错误。该组件对于UCX说并不重要，所以你可以这样禁用他:
   ```bash
   ./configure ... --enable-mca-no-build=btl-uct ...
   ```
3. 编译并安装
   ```bash
    make
    make install
   ```

### 运行MPI
命令行示例(使用可选标志来选择IB设备mlx5.0的端口1)
```shell
mpirun -np 2 -mca pml ucx -x UCX_NET_DEVICES=mlx5_0:1 ./app
```
**重要信息**：最近的OpenMPI包含一个BTL的组件叫做```uct```，由于```OPAL```与```UCM```之间Malloc钩子发生冲突，该版本可能会在启用时导致数据损坏。为了工作进行，请使用一下替代方式之一:
- 方式一：在OpenMPI构建的时候禁用btl/uct
    ```bash
    ./configure ... --enable-mca-no-build=btl-uct ...
    ```
- 方式二：在运行时禁用btl/uct
    ```bash
    mpirun -np 2 -mca pml ucx -mca btl ^uct -x UCX_NET_DEVICES=mlx5_0:1 ./app
    ```


### 运行时优化
默认情况下，OpenMPI启用了build-in transport(BTLs), 这可能会导致OpenMPI Progress功能中的其他软件开销。为了解决这个问题，您可以尝试禁用指定的 BTLs
```bash
mpirun -np 2 -mca pml ucx --mca btl ^vader,tcp,openib,uct -x UCX_NET_DEVICES=mlx5_0:1 ./app
```


## MPICH和UCX结合

UCX支持MPICH 3.3及以上版本。MPICH包中就包含了UCX，因此你不需要单独下载UCX。

### 安装
1. 下载mpich-3.3或以上版本 [here](https://www.mpich.org)
2. 配置UCX：
   ```bash
   mkdir build
   cd build
   ../configure --prefix=<mpich-install-path> --with-device=ch4:ucx
   ```
3. 编译安装
   ```bash
   make -j4
   make install
   ```
4. 运行MPI
   ```bash
    mpirun -np 2 -env UCX_NET_DEVICES=mlx5_0:1 ./executable
   ```


## 在Docker中运行
UCX能够在docker中运行，但是需要一些调整。
- 根据运行时配置，有些传输方式可能不支持。你可以通过```ucx_info -d```来查看哪些是支持的。
- 为了启用共享内存传输，需要ptrace capability。推荐共享内存大小为8GB。在```docker run```后面添加如下：
  ```--cap-add CAP_SYS_PTRACE --shm-size="8g"```
- 在多个容器之间使用共享内存，使用一下命令代替：
  ```--cap-add CAP_SYS_PTRACE --ipc host```
- 启用RDMA
  - ```rdma-core```、```libibverbs```或```MLNX_OFED```其中一个已经安装。
  - 必须允许相应的设备
    
    ```--device=/dev/infiniband/rdma_cm --device=/dev/infiniband/uverbsX```替换其中的X为设备号

  - ```ulimit -l```应该一个足够大的值，以容纳传输的资源和发送/接收的缓存区。推荐使用128MB，如果应用的工作集很大则可能需要更大的值。

