# 并行计算  
## 一、概述
* [介绍](notes/introduce.md)   
## 二、MPI
* [MPI基础概念](notes/mpiconcept.md)
* [MPI通信](notes/communication.md)
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
* [底层通信原语学习](notes/CollectiveCommunication.md)   
* [NCCL介绍](notes/nccl.md)
* [Gloo介绍](notes/gloo.md)
* [底层通信方案对比（NCCL，Gloo，MPI……）](notes/compare.md) 
### 3.框架层次
* [模型并行和数据并行](notes/dataandmodel.md)
* [PS架构和Ring架构、同步和异步的几种组合](notes/synasy.md)
#### 各种框架的具体实现
* [Pytorch](https://github.com/fusimeng/PyTorch)
* [Horovod](https://github.com/fusimeng/Horovod)
* [Tensorflow](https://github.com/fusimeng/TensorFlow)
* [Mxnet](https://github.com/fusimeng/mxnet_)
   
   
**--------------------------------------------------------------------------------------------------**
## 资源
* [OpenMPI官网](https://www.open-mpi.org/)  |  [MPI Tutorial-1](https://riptutorial.com/zh-CN/mpi/topic/1943/mpi)  |  [MPI Tutorial-2](http://mpitutorial.com/tutorials/)   
* [Intel MPI](https://software.intel.com/en-us/mpi-library) | [MVAPICH2](http://mvapich.cse.ohio-state.edu/) 
* [NCCL GitHub](https://github.com/NVIDIA/nccl)| [API](https://docs.nvidia.com/deeplearning/sdk/index.html) | [官网](https://developer.nvidia.com/nccl)
* [Gloo](https://github.com/facebookincubator/gloo)


