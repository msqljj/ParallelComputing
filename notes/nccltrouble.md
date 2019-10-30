# NCCL 故障排除 
[官方API](https://docs.nvidia.com/deeplearning/sdk/nccl-developer-guide/docs/index.html)    

## 5. Troubleshooting  NCCL 故障排除
Ensure you are familiar with the following known issues and useful debugging strategies.
### 5.1. Errors
NCCL calls may return a variety of return codes. Ensure that the return codes are always equal to ncclSuccess. If any call fails, and returns a value different from ncclSuccess, setting NCCL_DEBUG to WARN will make NCCL print an explicit warning message before returning the error.   
NCCL调用可能会返回各种返回码。 确保返回码始终等于ncclSuccess。 如果任何调用失败，并返回一个不同于ncclSuccess的值，将NCCL_DEBUG设置为 WARN 将使NCCL在返回错误之前打印一个明确的警告消息。


Errors are grouped into different categories.
* ncclUnhandledCudaError and ncclSystemError indicate that a call to an external library failed.
* ncclInvalidArgument and ncclInvalidUsage indicates there was a programming error in the application using NCCL.

In either case, refer to the NCCL warning message to understand how to resolve the problem.
错误分为不同的类别。
* ncclUnhandledCudaError 和 ncclSystemError 表示对外部库的调用失败。
* ncclInvalidArgument 和 ncclInvalidUsage 指示使用NCCL的应用程序中存在编程错误。

无论哪种情况，请参阅NCCL警告消息以了解如何解决问题。

### 5.2. Networking Issues 网络问题
#### 5.2.1. IP Network Interfaces IP 网络接口
NCCL auto-detects which network interfaces to use for inter-node communication. If some interfaces are in state up, however are not able to communicate between nodes, NCCL may try to use them anyway and therefore fail during the init functions or even hang.
For more information about how to specify which interfaces to use, see NCCL Knobs topic, particularly the NCCL_SOCKET_IFNAME knob.   
NCCL自动检测哪些网络接口用于节点间通信。 如果某些接口处于up状态，但是无法在节点之间进行通信，则NCCL可能会尝试使用它们，从而在init函数期间失败甚至挂起。   
有关如何指定要使用哪个接口的更多信息，请参阅 NCCL Knobs 主题，特别是 NCCL_SOCKET_IFNAME 旋钮。   
#### 5.2.2. InfiniBand
Before running NCCL on InfiniBand, running low-level InfiniBand tests (and in particular the ib_write_bw test) can help verify which nodes are able to communicate properly.   
在InfiniBand上运行NCCL之前，运行低级InfiniBand测试（尤其是ib_write_bw测试）可以帮助验证哪些节点能够正常通信。
### 5.3. Known Issues
Ensure you are familiar with the following known issues:
#### Sharing Data 共享数据
In order to share data between ranks, NCCL may require shared system memory for IPC and pinned (page-locked) system memory resources. The operating system’s limits on these resources may need to be increased accordingly. Please see your system’s documentation for details. In particular, Docker® containers default to limited shared and pinned memory resources. When using NCCL inside a container, it is recommended that you increase these resources by issuing:    
为了在队列之间共享数据，NCCL可能需要IPC的共享系统内存和固定（页面锁定）系统内存资源。操作系统对这些资源的限制可能需要相应的增加。有关详细信息，请参阅您的系统文档。特别是，Docker®容器默认为使用有限的共享和固定内存资源。在容器内使用NCCL时，建议您通过使用以下命令来增加这些资源：
--shm-size=1g --ulimit memlock=-1
in the command line to
nvidia-docker run


Concurrency between NCCL and CUDA calls (NCCL up to 2.0.5 or CUDA 8) NCCL和CUDA调用之间的并发性（NCCL版本不低于2.0.5或CUDA 8）
NCCL uses CUDA kernels to perform inter-GPU communication. The NCCL kernels synchronize with each other, therefore, each kernel requires other kernels on other GPUs to be also executed in order to complete. The application should therefore make sure that nothing prevents the NCCL kernels from being executed concurrently on the different devices of a NCCL communicator.    
NCCL使用CUDA内核来执行GPU间通信。 NCCL内核彼此同步，因此，每个内核都需要其他GPU上的内核也执行才能完成。因此，应用程序应该确保没有阻止在NCCL通信器的不同设备上同时执行NCCL内核。

For example, let's say you have a process managing multiple CUDA devices, and, also features a thread which calls CUDA functions asynchronously. In this case, CUDA calls could be executed between the enqueuing of two NCCL kernels. The CUDA call may wait for the first NCCL kernel to complete and prevent the second one from being launched, causing a deadlock since the first kernel will not complete until the second one is executed. To avoid this issue, one solution is to have a lock around the NCCL launch on multiple devices (around ncclGroupStart and ncclGroupEnd when using a single thread, around the NCCL launch when using multiple threads, using thread synchronization if necessary) and take this lock when calling CUDA from the asynchronous thread.   
例如，假设您有一个管理多个CUDA设备的进程，并且还具有一个异步调用CUDA函数的线程。在这种情况下，可以在排队的两个NCCL内核之间执行CUDA调用。 CUDA调用可能会等待第一个NCCL内核完成，并阻止第二个内核启动，从而导致死锁，因为直到执行第二个内核，第一个内核才会完成。为了避免这个问题，一个解决方案是锁定多个设备上的NCCL启动（当使用单个线程时围绕ncclGroupStart和ncclGroupEnd，在使用多个线程时围绕NCCL launch，必要时使用线程同步），并在调用异步线程中的CUDA时，使用此锁。

Starting with NCCL 2.1.0, this issue is no longer present when using CUDA 9, unless Cooperative Group Launch is disabled in the NCCL_LAUNCH_MODE = PARALLEL setting.    
从NCCL 2.1.0开始，使用CUDA 9时，此问题不再存在，除非在NCCL_LAUNCH_MODE = PARALLEL设置中禁用了“合作组启动”。

### 5.4. NCCL Knobs
A knob isa type of environment variable that can you can turn on or off by settingspecific values. These environment variables should be set in the context ofrunning NCCL. The following table lists all of the available knobs thatcan be modified in NCCL.    
旋钮是一种环境变量，可以通过设置特定的值来打开或关闭。 这些环境变量应该在运行NCCL的环境中进行设置。 下表列出了所有可在NCCL中修改的可用旋钮。   

Table 1. Knobs available for modification in NCCL
|Environment Variable|Description|Values Accepted|
|:-|:-|:-|
|NCCL_SHM_DISABLE|The NCCL_SHM_DISABLE variable disables the Shared Memory (SHM) transports.   SHM is used between devices when peer-to-peer cannot happen, therefore, host memory is used. NCCL uses network (InfiniBand or IP sockets) to communicate between the CPU sockets when SHM is disabled.NCCL_SHM_DISABLE变量禁用共享内存（SHM）传输。在对等不可能发生的情况下在设备之间使用SHM，因此使用主机内存。 当SHM禁用时，NCCL使用网络（InfiniBand或IP sockets）在CPU sockets之间进行通信。|Define and set to 1 to disable SHM.定义并设置为1以禁用SHM。|
|NCCL_SOCKET_IFNAME|The NCCL_SOCKET_IFNAME variable specifies which IP interface to use for communication.This variable also defines a prefix for the network interfaces to be filtered. NCCL_SOCKET_IFNAME变量指定用于通信的IP接口。该变量还定义了要过滤的网络接口的前缀。|Define and set to ib or eth. The value searches for all applicable ib* or eth* named interfaces on the system.Another accepted value is ^eth, which searches for interfaces that do not match eth.定义并设置为ib或eth。 该值在系统上搜索所有适用的ib*或eth*命名的接口。另一个可接受的值是^eth，它搜索与eth不匹配的接口。Note: Loopback (lo) is not selected by NCCL unless it is explicitly set in the environment variable.注意：除非在环境变量中明确设置，否则NCCL不会选择Loopback（lo）。|
|NCCL_DEBUG|The NCCL_DEBUG variable controls the debug information that is displayed from NCCL. This variable is commonly used for debugging.NCCL_DEBUG变量控制从NCCL显示的调试信息。 这个变量通常用于调试。|VERSION Prints the NCCL version at the start of the program.在程序开始时打印NCCL版本。  WARN  Prints an explicit error message whenever any NCCL call errors out.每当出现任何NCCL调用错误时，打印一个明确的错误消息。|
|NCCL_IB_DISABLE|The NCCL_IB_DISABLE variable disables the IB transport that is to be used by NCCL. Instead, NCCL will fallback to using IP sockets. NCCL_IB_DISABLE变量将禁用NCCL要使用的IB传输。NCCL将回退到使用IP sockets 。|Define and set to 1 to force IP sockets usage.定义并设置为1以强制使用IP sockets 。|
|NCCL_BUFFSIZE|The NCCL_BUFFSIZE variable controls the amount of buffer to share data between two GPUs.Use this variable if you encounter memory constraint issues when using NCCL or you think that a different buffer size would improve performance.NCCL_BUFFSIZE变量控制两个GPU之间共享数据的缓冲区大小。如果在使用NCCL时遇到内存限制问题，或者您认为不同的缓冲区大小会提高性能，请使用此变量。|Default is 4194304 (4 MB).Values are integers, in bytes. The recommendation is to use powers of 2. For example, 1024 will give a 1K buffer.默认是4194304（4 MB）。值是整数，以字节为单位。 建议使用2的证书幂。例如，1024会给1K缓冲区。|
|NCCL_NTHREADS|The NCCL_NTHREADS variable sets the number of CUDA threads per CUDA block. NCCL will launch one block per communication ring.NCCL_NTHREADS变量设置每个CUDA块的CUDA线程数。 NCCL将为每个通讯环路启动一个模块。Use this variable if you think your GPU clocks are low and you want to increase the number of threads.You can also use this variable to reduce the number of threads to decrease the GPU workload.如果您认为您的GPU时钟不足，并且想要增加线程数，请使用此变量。您也可以使用此变量来减少线程数量，以减少GPU工作负载。|Default is 512 for Kepler.Default is 256 for Maxwell and Pascal.The values allowed are 128, 256 and 512.Kepler的默认值是512。Maxwell和Pascal的默认值是256。允许的值是128,256和512。|
|NCCL_RINGS|The NCCL_RINGS variable overrides the rings that NCCL forms by default. Rings are sequences of ranks. They can be any permutations of ranks.NCCL filters out any rings that do not contain the number of ranks in the NCCL communicator. In general, the ring formation is dependent on the hardware topology connecting the GPUs in your system.NCCL_RINGS变量覆盖默认情况下NCCL形成的环。环是ranks的序列。 他们可以是ranks的任何排列。NCCL过滤掉任何NCCL通信器中不包含的秩数的环。 一般来说，环的形成取决于系统中连接GPU的硬件拓扑结构。|Ranks from 0 to n-1, where n is the number of GPUs in your communicator.The ranks can be separated by any non-digit character, for example, " ", "-", except "|".Multiple rings can be specified separated by the pipe character "|".For example, if you have 4 GPUs in a communicator, you can form communication rings as such:0 1 2 3 | 3 2 1 0.This will form two rings, one in each direction.ranks从0到n-1，其中n是通信器中的GPU数量。ranks可以用任何非数字字符分隔，例如" ", "-", "|"除外。可以用管道字符“|”指定多个环。例如，如果在通信器中有4个GPU，则可以如下形成通信环：0 1 2 3 | 3 2 1 0。这将形成两个环，每个方向一个。|
|NCCL_MAX_NRINGS(since 2.0.5)|The NCCL_MAX_NRINGS variable limits the number of rings NCCL can use. Reducing the number of rings also reduces the number of CUDA blocks used for communication, hence the impact on GPU computing resources.NCCL_MAX_NRINGS变量限制了NCCL可以使用的环的个数。 减少环的数量也减少了用于通信的CUDA块的数量，从而影响GPU计算资源。|Any value above or equal to 1.任何大于或等于1的值。|
|NCCL_CHECKS_DISABLE(since 2.0.5)|Disable argument checks. Checks are useful during development but can increase the latency. They can be disabled to improve performance in production.禁用参数检查。 检查在开发过程中很有用，但会增加延迟。 他们可以被禁用，以提高生产性能。|Default is 0. Set the value to 1 to disable checks.默认值为0.将值设置为1以禁用检查。|
|NCCL_LAUNCH_MODE(since 2.1.0)|Controls how NCCL launches CUDA kernels.控制NCCL如何启动CUDA内核。|The default value is to use cooperative groups (CUDA 9).Setting it to PARALLEL uses the previous launch system which can be faster but is prone to deadlocks.默认值是使用合作组（CUDA 9）。将其设置为PARALLEL将使用先前的启动系统，该启动系统可能更快，但容易出现死锁。|
|NCCL_IB_TIMEOUT|The NCCL_IB_TIMEOUT variable controls the InfiniBand Verbs Timeout. Refer to the InfiniBand documentation for more information.NCCL_IB_TIMEOUT变量控制InfiniBand Verbs超时。 有关更多信息，请参阅InfiniBand文档。|The default value used by NCCL is 14.The value depends on the size of your InfiniBand network.NCCL使用的默认值是14。该值取决于您的InfiniBand网络的大小。|
|NCCL_IB_CUDA_SUPPORT|The NCCL_IB_CUDA_SUPPORT variable is used to disable GPU Direct RDMA.NCCL_IB_CUDA_SUPPORT变量用于禁用GPU Direct RDMA。|By default, NCCL enables GPU Direct RDMA, if the topology permits it. This variable can disable this behavior.Define and set to 0 to disable GPU Direct RDMA.默认情况下，如果拓扑结构允许，NCCL启用GPU Direct RDMA。 此变量可以禁用此行为。定义并设置为0以禁用GPU Direct RDMA。|
|NCCL_NET_GDR_READ|The NCCL_NET_GDR_READ variable enables GPU Direct RDMA when sending data. By default, NCCL uses GPU Direct RDMA to receive data directly in GPU memory. However, when sending data, the data is first stored in CPU memory, then goes to the InfiniBand card.发送数据时，NCCL_NET_GDR_READ变量启用GPU Direct RDMA。 默认情况下，NCCL使用GPU Direct RDMA直接在GPU内存中接收数据。 但是，在发送数据时，首先将数据存储在CPU内存中，然后进入InfiniBand卡。Note: Reading directly GPU memory when sending data is known to be slightly slower than reading from CPU memory.注意：发送数据时直接读取GPU内存比CPU内存读取要慢一些。Default value is 0.Define and set to 1 to use GPU Direct RDMA to send data to the NIC directly (bypassing CPU).默认值是0。定义并设置为1，以使用GPU Direct RDMA将数据直接发送到NIC（绕过CPU）。|
|NCCL_SINGLE_RING_THRESHOLD(since 2.1.0)|Set the limit under which NCCL will only use one ring. This will limit bandwidth but improve latency.设置NCCL只使用一个环的限制。 这会限制带宽，但会提高延迟。|Default value is 256kB on GPUs with compute capability 7 and above. Otherwise, the default value is 128kB on others.Values are integers, in bytes.在计算能力为7以上的GPU上，默认值为256kB。 否则，其他的默认值是128kB。值是整数，以字节为单位。|
|NCCL_LL_THRESHOLD(since 2.1.0)|Set the size limit under which NCCL uses low-latency algorithms.设置NCCL使用低延迟算法的大小限制。|Default is 16kB.Values are integers, in bytes.默认值是16kB。值是整数，以字节为单位。|
### 5.5. Support
Register for the NVIDIA Developer Program to report bugs,issues and make requests for feature enhancements. For more information, see:https://developer.nvidia.com/developer-program.

## Reference
[1] https://blog.csdn.net/s_sunnyy/article/details/79023532    
[2] https://blog.csdn.net/s_sunnyy/article/details/79025262   

