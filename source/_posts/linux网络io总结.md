---
title: Linux网络IO总结
date: 2019-01-13 18:35:15
tags: 
	- linux
	- io
categories: 计算机
copyright:
top: 3
typora-root-url: ../
typora-copy-images-to: ../images
---

## 1. Linux

​	1. 基本构成：内核，shell和文件系统一起构成基本的操作系统结构，使得用户能运行程序，管理文件并使用	系统。如下图层次结构：

​	![linux-zc](/images/linux-zc.png) 

 2. 内核和内存

    ![linux-zc2](/images/linux-zc2.png)

    Linux系统将内存划分为：内核空间（提供内核程序使用）用户空间（提供用户应用程序使用）

    换句话说，就是内核程序与用户应用是隔离的。

    内核的作用是什么？从上图看，内核用于管理内存，硬盘接口，网路接口等。

    内核相当于管理员，所有的用户应用程序要使用计算机（如：读取文件）都要经过内核，因此内核也提供了相应的应用编程接口给应用程序调用。

	3. shell

    shell 是系统的用户界面，提供了用户与内核进行交互操作的一种接口。它接收用户输入的命令并把它送入内核去执行。

## 2. IO技术

1. 前言

   Linux将所有外部设备都看作一个文件来进行操作，每个文件操作用文件描述符（file descriptor）来标示描述。文件的操作本质就是IO的操作。

   

2. Unix/linux 下的IO模型

   - 阻塞IO模型（blocking io）

   - 非阻塞IO模型（nonblocking io）

   - IO复用模型（io multiplexing）

   - 信号驱动式IO模型（sigio）

   - 异步IO式模型（aio）

     IO操作两阶段：

     1. 等待数据就绪阶段。等待数据到达网络。当数据包到达时，它将被复制到内核中的缓冲区中。该阶段分IO阻塞和IO非阻塞方式。
     2. 数据拷贝阶段。将数据从内核的缓冲区复制到我们的应用程序缓冲区中。该阶段分同步IO和异步IO方式。

3. **阻塞式IO模型(blocking IO model)**

   ![](/images/io-3.png)

   ​	应用进程进行系统调用 recvfrom ，在数据准备和拷贝两个过程，IO一直处于阻塞。所以该模型属于**阻塞同步IO**。

4. **非阻塞式IO模型(noblocking IO model)**

   ![](/images/io-02.png)
   ​	应用进程轮询系统调用 recvfrom，无数据时候系统返回 EWOULDBLOCK，在数据准备就绪后同步拷贝，该模型属于**非阻塞同步IO**。

5. **IO复用式IO模型(IO multiplexing model)**

   ![](/images/io-03.png)

   ​	应用进程改为阻塞调用select，当数据就绪也就是select返回可读时，调用同步调用recvfrom进行数据拷贝。该模型属于**阻塞同步IO**。

   ​	但与上面同步IO阻塞模型相比：

   ​		缺点：进行两次系统调用（注：系统调用会进行用户内核态切换，耗费性能资源）。

   ​		优点：它可以对多个文件描述符进行阻塞监听，所以它的效率比阻塞IO模型高效。

6. **信号驱动式IO模型(signal-driven IO model)**

   ![](/images/io-04.png)

   ​	应用进程进行非阻塞系统调用sigaction，调用sigaction后立即返回，当数据准备就绪后，系统会通知处理程序，进行同步revfrom数据拷贝。该模型属于**非阻塞同步IO**。

   ​	这种方式与IO复用模型相比优势在于等待数据就绪阶段未被阻塞。​		

7. **异步IO式IO模型(asynchronous IO model)**

   ![](/images/io-05.png)

   应用进程调用`aio_read`（POSIX异步I / O函数以`aio_`或开头`lio_`）并传递以下内核：

   ​	描述符，缓冲区指针，缓冲区大小（相同的三个参数`read`），

   ​	文件偏移量（类似于`lseek`），

   ​      以及如何在整个操作完成时通知我们。

   此系统调用立即返回，并且在等待I / O完成时（也就是数据拷贝阶段）不会阻塞应用进程。该模型属于**异步非阻塞IO**。

8. 总结上述IO模型比较

   ![](/images/io-1-7388414.png)

   |      | 阻塞                         | 非阻塞                             |
   | ---- | ---------------------------- | ---------------------------------- |
   | 同步 | 阻塞式IO模型，IO复用式模型， | 非阻塞式IO模型，信号驱动式IO模型， |
   | 异步 | 异步阻塞IO模型               | 异步非阻塞式IO模型                 |

## 3. Socket IO

1. 一个socket IO读取流程：

![](/images/s-01.png)	

```
1. 应用程序调用read，通知内核需要做读取数据操作
2. 内核创建一个文件描述符
3. 内核从物理层收到读数据的命令，从网络中获取数据包
4. 数据包传递到TCP/IP层，解析数据包的头
5. 内核将数据包缓存在文件描述符的读缓存区(内核缓存区)中
6. 当文件描述符读缓存区数据字节数大于应用程序定义的低水位(阈值)的时候(read的一个参数)，此时文件描述符处于读就绪的状态
7. 将读缓存区中的数据复制到应用程序(用户区)返回
```

2. select

   ```c
   #include <sys/select.h>
   #include <sys/time.h>
   
   struct timeval  {
   	long tv_sec;       //秒
   	long tv_usec;      //微秒
   };
   
   struct fd_set{} //文件描述符的集合
   
   /**
   *   maxfdp1   文件描述符中最大值
   *  *readset   监视可读的文件描述符集合
   *  *writeset  监视可写的文件描述符集合
   *  *exceptset 监视文件描述符查看是否发生了异常，或者带外数据是否可用
   *  *timeout	  超时时间
   */
   int select(int maxfdp1, fd_set *readset, fd_set *writeset, fd_set *exceptset,
              const struct timeval *timeout);
   
   /* 返回: 就绪的文件描述符数量, 超时时返回 0, 错误时候返回 –1 */
   ```

    例：

   ```c
   #include <stdio.h>
   #include <sys/types.h>
   #include <sys/socket.h>
   #include <netinet/in.h>
   #include <wait.h>
   #include <signal.h>
   #include <errno.h>
   #include <sys/select.h>
   #include <sys/time.h>
   #include <unistd.h>
    
   #define MAXBUF 256
    
   int main()
   {
     char buffer[MAXBUF];
     int fds[3];
     // 服务端地址
     struct sockaddr_in addr;
     struct sockaddr_in client;
     int addrlen, n,i,max=0;;
     int sockfd;
     fd_set rset;
    
     sockfd = socket(AF_INET, SOCK_STREAM, 0);
   
     for (i=0;i<3;i++) 
     {
       ...
       // 为每个客户端创建不同的文件描述符存入数组
       fds[i] = accept(sockfd,(struct sockaddr*)&client, &addrlen);
   	// 选取最大文件描述符
       if(fds[i] > max)
       	max = fds[i];
     	...
     }
     
     //循环处理
     while(1){
   	FD_ZERO(&rset);
     	for (i = 0; i< 3; i++ ) {
     		FD_SET(fds[i], &rset);
     	}
    	
       // 当有文件描述符被标记需要等待可读时，select会阻塞等待数据到达（select通过&rset位标示状态）
   	int ret = select(max+1, &rset, NULL, NULL, NULL);
   	for(i=0;i<3;i++) {
           if ( ret == -1 )
       	// 处理错误
   		else if ( ret == 0 )
       	// 超时，没有可用的文件描述符处理
   		else
   		{	// 读取数据
   			if (FD_ISSET(fds[i], &rset)){
   				memset(buffer,0,MAXBUF);
   				read(fds[i], buffer, MAXBUF);
   				puts(buffer);
   			}
   		}	
       }
     }
     return 0;
   }
   ```

   总结：每次调用前构建后集合，计算最高位，需要迭代文件描述符以检查它是否存在于select返回的集合中，优点在于移植性很好， 每个像OS这样的unix都有它。

3. poll

   ```c
   #include <poll.h>
   
   struct pollfd {
         int fd; // 文件描述符
         short events; // 输入事件
         short revents; // 输出事件
   };
   
   int poll (struct pollfd *fdarray, unsigned long nfds, int timeout);
   
   /* 返回: 就绪的文件描述符数量, 超时时返回 0, 错误时候返回 –1 */
   
   ```

   例：

   ```c
   for (i=0;i<3;i++) 
     {
       memset(&client, 0, sizeof (client));
       addrlen = sizeof(client);
       pollfds[i].fd = accept(sockfd,(struct sockaddr*)&client, &addrlen);
       pollfds[i].events = POLLIN;
     }
     sleep(1);
     while(1){
   	int ret = poll(pollfds, 3, 50000);
    
   	for(i=0;i<3;i++) {
           if ( ret == -1 )
       	// 处理错误
   		else if ( ret == 0 )
       	// 超时，没有可用的文件描述符处理
   		else
   		{	// 区别于select poll 是判断pollfds事件是否发生
   			if (pollfds[i].revents & POLLIN){
   			pollfds[i].revents = 0;
   			memset(buffer,0,MAXBUF);
   			read(pollfds[i].fd, buffer, MAXBUF);
   			puts(buffer);
   		}
   		}	
   	}
     }
   ```

   与select相比：select 每次返回后，文件描述符集合需要重建，poll返回分为输入，输出事件，允许在不更改的情况下重用数组，而部分unix系统可不能不支持poll。

4. epoll

   select 和 poll 每次调用处理都会涉及到用户内核上下文切换。

   首先创建一个上下文，当客户端连接时，我们创建一个 epoll_event 对象并将其添加到上下文和无限循环中，我们只等待上下文

   ``` c
   // 在内核中创建一个上下文
   int epoll_create(int size); 
   // 向/从上下文添加/移除文件描述符
   int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event); 
   // 等待上下文中的事件
   int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);
   ```

    

   例：

   ```c
    struct epoll_event events[5];
     int epfd = epoll_create(10);
     ...
     ...
     for (i=0;i<3;i++) 
     {
       static struct epoll_event ev;
       memset(&client, 0, sizeof (client));
       addrlen = sizeof(client);
       ev.data.fd = accept(sockfd,(struct sockaddr*)&client, &addrlen);
       ev.events = EPOLLIN;
       epoll_ctl(epfd, EPOLL_CTL_ADD, ev.data.fd, &ev); 
     }
     
     while(1){
     	nfds = epoll_wait(epfd, events, 3, 10000);
   	for(i=0;i<nfds;i++) {
   			memset(buffer,0,MAXBUF);
   			read(events[i].data.fd, buffer, MAXBUF);
   			puts(buffer);
   	}
     }
   ```

5. kqueue

   