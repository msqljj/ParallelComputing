# 并行计算  
## 一、概述
* [介绍](notes/introduce.md)   
## 二、MPI
* [MPI的几个概念](notes/mpiconcept.md)
## 三、GPU&CUDA
### 1.硬件层次
**单机多卡**内存和GPU、GPU和GPU之间互联可通过PCIE、NVLink、NVSwitch；    
**多机多卡**GPU之间（不同主机）、CPU与GPU之间互联可通过GPUDirect RDMA、IB/万兆以太网 + TCP/IP；      
* [PCIE、NVlink、NVSwitch和InfiniBand技术介绍](notes/pcie.md)   
* [浅析GPU通信技术](notes/gpus_communication.md)
* [P2P，GPU连接等性能指标测试方法](notes/test.md)
### 2.软件（库）层次
**单机多卡**  
NCCL；Gloo；       
**多机多卡**   
NCCL2.x；MPI；TCP/IP；Gloo；       
* [NCCL介绍](notes/nccl.md)
* [NCCL安装](notes/ncclinstall.md)   
### 3.框架层次
* 模型并行和数据并行
* PS架构和Ring架构
* 底层通信方案对比（NCCL，Gloo，MPI……）    
   
**-------------------------------------------------**
## 资源
* [OpenMPI官网](https://www.open-mpi.org/)  |  [MPI Tutorial-1](https://riptutorial.com/zh-CN/mpi/topic/1943/mpi)  |  [MPI Tutorial-2](http://mpitutorial.com/tutorials/)   
* [Intel MPI](https://software.intel.com/en-us/mpi-library) | [MVAPICH2](http://mvapich.cse.ohio-state.edu/) 
* [NCCL GitHub](https://github.com/NVIDIA/nccl)| [API](https://docs.nvidia.com/deeplearning/sdk/index.html) | [官网](https://developer.nvidia.com/nccl)
* [Gloo](https://github.com/facebookincubator/gloo)


