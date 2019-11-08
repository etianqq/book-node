## 异步I/O

异步I/O四要素：

* **请求对象**：从JavaScript发起调用到内核执行完I/O操作的过渡过程中，存在一种中间产物，就是请求对象。

  > 请求对象是异步 I/O 过程中的重要中间产物，所有状态都保存在这个对象中，包括送入线程池等待执行以及 I/O 操作完后的回调处理

  

* **I/O线程池**：组装好请求、送入I/O线程池等待执行，完成第一步I/O操作，进入第二部分回调通知。（在Windows中，线程池中的I/O操作调用完毕之后，会将获取的结果存在req->result属性上，然后调用PostQueuedCompletionStatus()通知IOCP，告知当前对象操作已经完成）

* **观察者**：每个事件循环中有一个或多个观察者，而判断是否有事件需要处理的过程就是向这些观察者询问是否有要处理的事件。

  在 Node 中，事件主要来源于网络请求、文件 I/O 等，这些事件都有对应的观察者。

* **事件循环**：在进程启动时，Node会创建一个类似于while(true)的循环，每执行一次循环体的过程称为Tick，每个Tick的过程就是查看是否有时间待处理。

  伪代码如下：

  ```javascript
  while(ture) {
    const event = eventQueue.pop()
    if (event && event.handler) {
      event.handler.execute()  // execute the callback in Javascript thread
    } else {
      sleep() // sleep some time to release the CPU do other stuff
    }
  }
  ```

  

![](https://images2015.cnblogs.com/blog/831875/201701/831875-20170117143531755-1196590471.png)



#### 一个完整的 node.js事件循环流程

![](https://pic1.zhimg.com/80/v2-41e98cd87e21c73094006886dc2a511c_hd.jpg)

1. Client 请求到达 node api，该请求被添加到Event Queue（事件队列）。这是因为Node.js 无法同时处理多个请求。
2. Event Loop（事件循环） 始终检查 Event Queue 中是否有待处理事件，如果有就从 Event Queue 中从前到后依次取出，然后提供服务。
3. Event Loop 是单线程非阻塞I/O，它会把请求发送给 C++ Thread Pool(线程池)去处理，底层是基于C++ Libuv 异步I/O模型结构可以支持高并发。
4. 现在 C++ Thread Pool有大量的请求，如数据库请求，文件请求等。
5. 任何线程完成任务时，Callback（回调函数）就会被触发，并将响应发送给 Event Loop。
6. 最终 Event Loop 会将请求返回给 Client。

