# CPU调度
为了充分利用CPU资源, 需要合理设计CPU调度程序. 
## CPU-I/O区间周期
CPU的成功调度依赖于进程的如下属性: 进程执行由CPU执行和I/O等待周期组成. 进程在这两个状态之间切换. 进程执行从CPU区间(CPU burst)开始, 在这之后是I/O区间(I/O burst), 接着是另一个CPU区间, 然后是另一个I/O区间, 如此进行下去, 最终, 最后的CPU区间通过系统请求终止执行. 

## CPU调度程序
每当CPU空闲时, 操作系统就必须从就绪队列中选择一个进程来执行. 进程选择由短期调度程序(short-term scheduler)或CPU调度程序执行. 调度程序从内存中选择一个能够执行的进程, 并为之分配CPU. 
CPU调度决策可在如下4中环境下发生:
1. 当一个进程从运行状态切换到等待状态 (例如, I/O请求, 或调用wait等待一个子进程的终止)
2. 当一个进程从运行状态切换到就绪状态(例如, 当出现中断时)
3. 当一个进程从等待状态切换到就绪状态(例如, I/O完成)
4. 当一个进程终止时
当调度只能发生在1和4两种情况下时, 称调度方案为`非抢占的(nonpreemptive)`或`协作的(cooperative)`, 否则, 称调度方案是`抢占的(preemptive)`. 

## 分派程序
与CPU调度功能有关的另一个部分是`分派程序(dispatcher)`. 分派程序是一个模块, 用来将CPU的控制交给由短期调度程序选择的进程, 其功能包括:
+ 切换上下文
+ 切换到用户模式
+ 跳转到用户程序的合适位置, 以重新启动程序
分派程序应尽可能的快, 因为在每次进程切换时都要使用. 分派程序停止一个进程而启动另外一个进程所要花费的时间称为`分派延迟(dispatch latency)`. 

## 调度准则
不同的CPU调度算法具有不同的属性, 为了比较CPU调度算法, 分析员提出了许多准则,用于比较不同的CPU调度算法:
+ CPU使用率 需要使CPU尽可能的忙 
+ 吞吐量 一个时间单元内, CPU所完成进程的数量
+ 周转时间 从一个特定进程的角度看, 从进程提交到进程完成的时间称为`周转时间`. 周转时间为所有时间段之和, 包括等待进入内存, 在就绪队列中等待, 在CPU上执行和I/O执行.
+ 等待时间 CPU调度算法并不影响进程运行和执行I/O的时间, 它只影响进程在就绪队列中等待所花费的时间. 等待时间为进程在就绪队列中等待所花费的时间之和.
+ 响应时间 在交互程序中, 从提交请求到产生第一响应的时间, 这种时间称为响应时间, 是开始响应所需要的时间, 而不是输出响应所需要的时间. 周转时间通常受输出设备速度的限制. 
一般来说, 需要使CPU使用率和吞吐量最大化, 而使周转时间, 等待时间和响应时间最小化. 
## 调度算法
### 先到先服务调度
先到先服务调度算法(fist-come, first-served FCFS), 先请求CPU的进程先分配到CPU. FCFS策略可以用FIFO队列来容易地实现. 不过, 采用FCFS策略的平均等待时间通常比较长. FCFS算法是非抢占的. 
`护航效果(convoy effect)`: 所有其他进程都等待一个大进程释放CPU，相比让较短进程最先执行的情况，CPU和设备的使用率更低。

### 最短作业优先调度
最短作业优先调度(shortest-job-first, SJF), 将每个进程与下一个CPU区间段相关联. 当CPU空闲时, 它会赋给具有最短CPU区间的进程. 如果两个进程具有同样长度, 那么可以使用FCFS调度来处理. 注意, 一个更为恰当的表示是最短下一个CPU区间的算法, 这是因为调度检查进程的下一个CPU区间的长度, 而非总长度. SJF算法的真正困难是如何知道下一个CPU区间的长度. SJF调度经常用于长期调度(可以将用户提交作业时所指定的进程时间极限作为长度). 用于短期CPU调度的一个近似方法是, 认为下一个CPU区间的长度与以前的相似, 以此来预测进程的下一个CPU区间长度. 
抢占SJF调度有时称为最短剩余时间优先调度(shortest-remaining-time-first scheduling). 

### 优先级调度
优先级调度算法(priority scheduling algorithm), 每个进程都有一个优先级与其关联, 具有最高优先级的进程会分配到CPU, 具有相同优先级的进程按FCFS顺序调度. SJF可以看做是优先级调度的一个特例. 
优先级调度算法的一个主要问题是无穷阻塞(indefinite blocking)或饥饿(starvation). 可以运行但缺乏CPU的进程可认为是阻塞的, 它在等待CPU. 优先级调度算法会使某个低优先级进程无穷等待CPU. 低优先级进程无穷等待问题的解决方案之一是老化(aging), 即逐渐增加在系统中等待很长时间的进程的优先级.
### 轮转法调度
轮转法(round-robin, RR)调度算法是专门为分时系统设计的, 它类似于FCFS调度, 但是增加了抢占以切换进程. 采用RR策略的平均等待时间通常较长. 

### 多级队列调度
在进程可以容易地分成不同组的情况下,可以建立另一类调度算法. 例如, 一个常用的划分方法是前台(交互)进程和后台(批处理)进程. 多级队列调度算法(multilevel queue scheduling algorithm)将就绪队列分成多个独立队列. 根据进程的属性, 如内存大小, 进程优先级, 进程类型, 一个进程被永久地分配到一个队列. 每个队列有自己的调度算法. 队列之间也必须有调度, 通常采用固定优先级抢占调度, 比如说前台队列可以比后台队列拥有绝对的优先级. 

### 多级反馈队列调度
多级反馈队列调度算法(multilevel feedback queue scheduling algorithm)允许进程在队列之间移动. 如果进程使用过多CPU时间, 那么它会被移动到更低优先级队列. 这种方案将I/O约束和交互进程留在更高优先级队列. 此外, 在较低优先级队列中等待时间过长的进程会被转移到更高优先级队列. 这种形式的老化阻止了饥饿的发生. 
通常多级反馈队列调度程序可由下列参数来定义:
+ 队列数量
+ 每个队列的调度算法
+ 用以确定何时升级到更高优先级队列的方法
+ 用以确定何时降级到更低优先级队列的方法
+ 用以确定进程在需要服务时应进入哪个队列的方法 

======
多级反馈队列调度程序的定义使它成为最通用的CPU调度算法, 它可被配置以适应特定系统设计, 不过它也是最复杂的算法. 
FCFS算法是非抢占的, 而RR算法是抢占的. SJF和优先级算法可以是抢占的, 也可以是非抢占的. 如果时间片太大, RR调度就成了FCFS调度, 如果时间片太小, 那么因为上下文切换而引起的调度开销就过大. 

## 多处理器调度
如果有多个CPU, 则`负载分配(load sharing)`成为可能, 但调度问题也变得更为复杂. 
`非对称多处理(asymmetric multiprocessing)`指, 一个处理器(主服务器)处理所有的调度决定, I/O处理以及其他系统活动, 其他的处理器只执行用户代码. 
`对称处理器(symmetric multiprocessing, SMP)`即每个处理器自我调度, 所有进程可能处于同一个共同的就绪队列中, 或每个处理器都有它私有的就绪队列. 无论如何, 调度通过每个处理器检查共同就绪队列并选择一个进程来执行. 

### 处理器亲和性
当一个进程迁移到其他处理器上时, 被迁移的第一个处理器的缓存中的内容必须为无效, 而将要迁移到的第二个处理器的缓存需要重新构建. 由于使缓存无效或重新构建的代价高, 绝大多数SMP系统试图避免将进程从一个处理器移至另一个处理器, 而是努力使一个进程在同一个处理器上运行, 这被称为处理器亲和性, 即一个进程需有一种对其运行所在处理器的亲和性(affinity). 

当一个操作系统具有设法让一个进程保持在同一个处理器上运行的策略, 但并不保证进程一定在同一个处理器上运行, 则称其支持软亲和性(soft affinity), 此时, 进程可能在处理器之间移动. 有些系统, 如Linux, 还提供一个支持硬亲和性(hard affinity)的系统调用, 从而允许进程指定它不允许移至其他处理器上. 

### 负载平衡
在SMP系统中, 保持所有处理器的工作负载平衡, 以完全利用多处理器的优点, 这是很重要的. 负载平衡(load balancing)设法将工作负载平均地分配到SMP系统中的所有处理器上. 负载平衡通常只是对那些拥有自己私有的可执行进程的处理器而言是必要的. 在具有共同队列的系统中, 通常不需要负载平衡.

## 总结
如果操作系统在内核级支持线程, 那么必须调度线程而不是进程来执行.  
另外, 未详细讲述负载平衡算法以及如何处理处理器亲和性. 
