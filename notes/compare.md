# MPI、Gloo、NCCL
使用PyTorch附带的后端为例来讲解三种方式   
  
目前PyTorch分发版仅支持Linux。默认情况下，Gloo和NCCL后端构建并包含在PyTorch的分布之中（仅在使用CUDA构建时为NCCL）。MPI是一个可选的后端，只有从源代码构建PyTorch时才能包含它。（例如，在安装了MPI的主机上构建PyTorch）

## 哪个后端使用？
在过去，我们经常被问到：“我应该使用哪个后端？”。

### 经验法则
* 使用NCCL后端进行分布式 GPU 训练。
* 使用Gloo后端进行分布式 CPU 训练。
### 具有InfiniBand互连的GPU主机
* 使用NCCL，因为它是目前唯一支持InfiniBand和GPUDirect的后端。
### GPU主机与以太网互连
* 使用NCCL，因为它目前提供最佳的分布式GPU训练性能，特别是对于多进程单节点或多节点分布式训练。如果您遇到NCCL的任何问题，请使用Gloo作为后备选项。（请注意，Gloo目前运行速度比GPU的NCCL慢。）
### 具有InfiniBand互连的CPU主机
* 如果您的InfiniBand在IB上已启用IP，请使用Gloo，否则请使用MPI。我们计划在即将发布的版本中为Gloo添加InfiniBand支持。
### 具有以太网互连的CPU主机
* 除非您有特殊原因要使用MPI，否则请使用Gloo。