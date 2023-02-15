## 1.1 分布式系统

一台电脑或者服务器构成一个节点，以前只是在一台电脑上运行程序。现在将多个电脑连接起来，把他们组成一个分布式系统，把分散的物理和逻辑资源、运算资源通过计算机网络实现信息交换。通过统一调度运算、存储，将一个大的任务分配到多台计算机上执行, 系统中存在一个以全局的方式管理计算机资源的分布式操作系统，负责任务分配、资源调度。不同节点之间通过消息传递的形式进行通信和协作以完成特定的功能。节点不仅仅是计算机，也可以是其他的设备。分布式系统分为分布式计算、分布式存储、分布式控制、分布式服务。

## 1.2 远程过程调用RPC（remote procedure call）

分布式系统中，各个节点之间通过消息传递的方式去调用各个服务的接口函数。返回调用结果。例如本地需要调用远程服务上的加法函数add（int x, int y）;这时需要方法远程调用callid（远程服务上函数的调用id，唯一标识）、x,y的值用一定的协议格式，序列化之后为字节流，通过网络传输到服务器，服服务器反序列化数据，再解析协议，根据callid去查找函数，然后将参数传入，按照协议去执行加法，再返回结果。

**RPC**三要素：

（1）服务端的CAllID映射

（2）序列化和反序列化，以用protobuf

（3）网络传输。socket，zeromq，netty

## 1.3 进程间通讯IPC（Inter-process communication）

分布式系统中，节点和节点之间，进程之间的通讯方式叫IPC。面向数据（data-centric） vs 面向消息（Message passing）。基于 Socket 的消息传输是分布式系统中最主要的通信方式，分为 RPC 和 Web Services 两大类。RPC 是面向函数的消息传递方式，Web Services 是面向服务的信息传递方式。

<img src="https://img2020.cnblogs.com/blog/1076976/202008/1076976-20200816084205437-1714992442.png" alt="img" style="zoom:125%;" />



# 2    RPC详解

## 2.1 RPC的结构

RPC主要由客户端向服务端发起请求，服务端返回结果，其中包含了负载均衡，数据序列化，网络传输，数据反序列化，协议编码解码等。还有个注册中心，服务端注册服务和函数到注册中心，客户端去注册中心查找服务函数进行调用。

![img](https://img2020.cnblogs.com/blog/1076976/202008/1076976-20200816084240326-767130982.png)

 

 

RPC的主要核心作用是

（1）RPC的服务函数寻址

服务端向注册中心注册服务信息和调用函数的CALLID，客户端向注册中心查询服务器信息和函数的CALLID等信息。实现注册和查找调用的功能。

（2）数据的序列化和反序列化

数据需要通过网络传输，所以要将结构体数据类型转化为字节流，然后通过网络传输，在反序列化转为结构体数据。序列化将对象转换成二进制流的过程，反序列化将二进制流转换成对象的过程。

（3）网路传输和协议

传输方式可以是 Socket，或者用 Asio，ZeroMQ，activemq，rabbitmq，Netty 。传输协议可以TCP，UDP，HTTP。

 

## 2.2 主流的RPC服务框架

目前流行的开源 RPC 框架还是比较多的，有阿里巴巴的 Dubbo、Facebook 的 Thrift、Google 的 gRPC、Twitter 的 Finagle 等。

下面重点介绍三种：

- gRPC：是 Google 公布的开源软件，基于udp的 HTTP 2.0 协议，并支持常见的众多编程语言。RPC 框架是基于 HTTP 协议实现的，底层使用到了 Netty 框架的支持。
- Thrift：是 Facebook 的开源 RPC 框架，主要是一个跨语言的服务开发框架。

用户只要在其之上进行二次开发就行，应用对于底层的 RPC 通讯等都是透明的。不过这个对于用户来说需要学习特定领域语言这个特性，还是有一定成本的。

- Dubbo：是阿里集团开源的一个极为出名的 RPC 框架，在很多互联网公司和企业应用中广泛使用。协议和序列化框架都可以插拔是极其鲜明的特色。



# 3    IPC 进程间通讯

## 3.1 同计算机进程间通讯方式

https://www.cnblogs.com/zgq0/p/8780893.html

**（1）管道：速度慢，容量有限，只有父子进程能通讯。pipe函数方式打开，read和write函数进行读写。**

**（2）命名管道FIFO：任何进程间都能通讯，但速度慢。mkfifo函数创建管道，open打开一个字符串表示的命名管道，进行读写，以设备文件的形式存在文件系统中。**  

**（3）系统消息队列：容量受到系统限制，且要注意第一次读的时候，要考虑上一次没有读完数据的问题。**

\#include <sys/msg.h>

// 创建或打开消息队列：成功返回队列ID，失败返回-1

int msgget(key_t key, int flag);

// 添加消息：成功返回0，失败返回-1

int msgsnd(int msqid, const void *ptr, size_t size, int flag);

// 读取消息：成功返回消息数据的长度，失败返回-1

int msgrcv(int msqid, void *ptr, size_t size, long type,int flag);

// 控制消息队列：成功返回0，失败返回-1

int msgctl(int msqid, int cmd, struct msqid_ds *buf);  

**（4）进程间同步信号量：不能传递复杂消息，只能用来同步。需要配合共享内存实现进程间数据传输。**

\#include <sys/sem.h>

// 创建或获取一个信号量组：若成功返回信号量集ID，失败返回-1

int semget(key_t key, int num_sems, int sem_flags);

// 对信号量组进行操作，改变信号量的值：成功返回0，失败返回-1

int semop(int semid, struct sembuf semoparray[], size_t numops);

struct sembuf

{

  short sem_num; // 信号量组中对应的序号，0～sem_nums-1

  short sem_op; // 信号量值在一次操作中的改变量

  short sem_flg; // IPC_NOWAIT, SEM_UNDO

}

// 控制信号量的相关信息cmd参数控制初始化，删除

int semctl(int semid, int sem_num, int cmd, ...);

semctl函数中的cmd参数有多种命令。

SETVAL：用于初始化信号量为一个已知的值。所需要的值作为联合semun的val成员来传递。在信号量第一次使用之前需要设置信号量。

IPC_RMID：删除一个信号量集合。如果不删除信号量，它将继续在系统中存在，即使程序已经退出，它可能在你下次运行此程序时引发问题，而且信号量是一种有限的资源。

**（5）共享内存区：能够很容易控制容量，速度快，但要保持同步，比如一个进程在写的时候，另一个进程要注意读写的问题，相当于线程中的线程安全.**

\#include <sys/shm.h>

// 创建或获取一个共享内存：成功返回共享内存ID，失败返回-1

int shmget(key_t key, size_t size, int flag);

// 连接共享内存到当前进程的地址空间：成功返回指向共享内存的指针，失败返回-1

void *shmat(int shm_id, const void *addr, int flag);

// 断开与共享内存的连接：成功返回0，失败返回-1

int shmdt(void *addr);

// 控制共享内存的相关信息：例如cmd为IPC_RMID删除共享内存。成功返回0，失败返回-1

int shmctl(int shm_id, int cmd, struct shmid_ds *buf);

## 3.2 远程进程间通讯

（1）  socket TCP UDP。

（2）  activemq，rabbitmq，zeromq，kafka等消息通讯方式。

（3）  restful接口 HTTP ，HTTPS。



gRPC是什么呢？

- 一个高性能RPC框架，一个跨语言平台的RPC框架。
- 使用Protocol Buffers作为二进制序列化
- 使用HTTP/2进行数据传输

通过定义 *proto* 文件，来定义 **`service`** 和 **`message`** 。 service负责提供服务， message提供结构化数据的序列化。

gRPC与Protocol Buffers的关系:

**Protocol Buffers 负责gRPC的结构化数据的序列化**

**gRPC与HTTP2**

gRPC引入了三个新概念:channel、RPC和Message。三者之间的关系很简单:每个Channel可能有许多RPC，而每个RPC可能有许多Message。

<img src="https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/840f84b13ab84ab1a5043a9f99f62c27~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp" alt="image.png" style="zoom: 50%;" />

























