# 操作系统启动过程

1. PC机启动时，ROM BIOS中程序负责把默认启动驱动器上的引导扇区代码、操作系统加载程序和数据读入内存
2. 引导扇区代码负责把操作系统加载程序和内核执行代码读入内存，然后把控制权交给操作系统加载程序
3. 操作系统加载程序准备内核的初始化操作，最终把控制权交给内核代码

bootimage是引导启动的image文件，其中主要包括上述的磁盘引导扇区代码、操作系统加载程序和内核执行代码。
内核代码若要正常运行就需要文件系统的支持，rootimage就是用于向内核提供最基本支持的根文件系统，其中包括操作系统最起码的一些配置文件和命令执行程序。对于Linux系统中使用的Unix类文件系统，其中主要包括一些规定的目录、配置文件、设备驱动程序、开发程序以及所有其他用户数据或文本文件等。

# Linux内核模式
Linux内核的结构模式可分为整体式的单内核模式和层次式的微内核模式. 单内核模式的主要优点是内核代码紧凑、执行速度快，不足之处主要是层次结构性不强.

## 单内核下操作系统提供服务的流程
应用主程序使用指定的参数值执行系统调用指令(int x80)，使CPU从用户态(User Mode)切换到核心态(Kernel Mode)，然后操作系统根据具体的参数值调用特定的系统调用服务程序，而这些服务程序则根据需要再调用底层的一些支持函数以完成特定功能. 在完成应用程序所要求的服务后，操作系统又使CPU从核心态切换回用户态, 从而返回到应用程序中继续执行后面的指令. 

P.s. 为什么会有用户态，核心态？用户态和核心态又分别指什么模式?


## Linux 内核系统体系结构
Linux内核主要由5个模块构成: 进程调度模块, 内存管理模块, 文件系统模块, 进程间通信模块和网络接口模块. 

### 进程调度模块
进程调用模块负责控制进程对CPU资源的使用. 


### 内存管理模块
内存管理模块用于确保所有进程能够安全地共享机器主内存区, 同时, 内存管理模块还支持虚拟内存管理方式, 使得Linux支持进程使用比实际内存空间更多的内存容量, 并可以利用文件系统把暂时不用的内存数据块交换到外部存储设备上, 当需要时再交换回主内存区. 


### 文件系统模块
文件系统模块用于支持对外部设备的驱动和存储, 虚拟文件系统模块通过向所有的外部存储设备提供一个通用的文件接口, 隐藏了各种硬件设备的不同细节. 从而提供并支持与其他操作系统兼容的多种文件系统格式. 


### 进程间通信模块
进程间通信模块用于支持多种进程间的信息交换方式.

### 网络接口模块
网络接口模块提供对多种网络通信标准的访问并支持许多网络硬件.

 