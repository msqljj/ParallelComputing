# Gloo介绍
[GitHub](https://github.com/facebookincubator/gloo)     
## 一、概述
Gloo是一个collective communications library。它附带了许多对机器学习应用程序有用的集合算法。这些包括 barrier, broadcast, and allreduce.

机器之间的数据传输可以使用IP，或者使用InifiniBand（或RoCE）。在后一种情况下，如果使用InfiniBand传输，GPUDirect 可用于加速跨机器GPU到GPU的内存传输。

