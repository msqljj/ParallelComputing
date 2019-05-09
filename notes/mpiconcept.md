# MPI 基础概念  
MPI是分布式计算的基础接口架构，他有很多实现，比如intelMPI openMPI等等，而这些具体实现了这些接口里面的内容，比如一些通信协议。   

MPI有几个很重要的概念**rank**, **group**, **communicator**, **type**, **pack**, **spawn**, **window**, 理解了这些概念MPI就算入门了。  
## 1.group  
group是MPI一个很重要的概念，一台电脑可以属于多个group，group的正真强大体现在可以随时随地的组合任意group，然后利用gourp内，和group间的communicator，可以很容易实现复杂科学计算的中间过程，比如奇数rank一个group，偶数另一个group，或者拓扑结构的group，这样可以解决很多复杂问题，另外MPI还有一个默认的全局的group，他就是comm world，一般简单的应用有了这一个group已经足够了。
## 2.rank
rank就是任意group内的一个计算单元，利用rank我们可以很轻松的实现client server的架构，比如rank＝0是server其他就是client。
## 3.communicator
communicator就是各种通信，比如一对一，一对多，多对一，其中多往往代表着一个group, 在传输过程中tag还是很有用的可以用来区别不同的任务类型，一般都是先解析tag，然后再解析具体的数据内容， 这里要有一个信封和信内容的差别的概念，理解了这样的差别，可以很好的扩展程序。
## 4.type
type是MPI的自定义类型，由于通常编程的时候常用struct 数组 和离散的变量，这些东西不能直接进行通信， 然后MPI同样有一套这样的定义，我们可以转化成MPI的格式，这样就可以很自由的通信了。
## 5.pack
Pack，就是把离散的数据打包起来，方便传送，其实这个作用和type很类似，如果你不想很麻烦的定义type直接打包发送。
## 6.spawn
spawn是区分MPI一代和二代的一个重要的标志，有了spawn，就可以在运行过程中自动的改变process的数量，可能复杂的软件才有这样的需求。
## 7.window
window远程的控制同一个文件，只有在网络条件很好的时候用这个才有意义，否则会让软件效率变得很糟糕。、



最后要有一个思想就是同一份代码可能会被很多电脑同时执行到，注意区分个个部分代码的角色。
