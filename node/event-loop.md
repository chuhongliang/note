# Event-Loop（事件轮询）

## 单线程模型

> **为什么是单线程?**

javascript是单线程的，此时是否有疑问为什么是单线程呢？多线程处理效率不是更高吗？需要从浏览器说起，在浏览器环境中对于DOM的操作，试想如果多个线程来对同一个DOM操作是不是就乱了呢，那也就意味着对于DOM的操作只能是单线程，避免DOM渲染冲突。另外，在浏览器环境中UI渲染线程和JS执行引擎是互斥的，一方在执行时都会导致另一方被挂起，这是由JS引擎所决定的。

> **单线程模式下为何如此高效？**

上面说了既然javascript是单线程的，那么同一时间只能处理一件事情，对于高并发大量请求不是会造成程序阻塞吗？答案是no，不会造成程序阻塞的解决方案是 **`异步非阻塞`**，异步的具体实现就是今天要讲的重点 **`Event-Loop`** 另外，单线程也避免了线程切换对资源的浪费。

## EventLoop

> ***什么是EventLoop？***

EventLoop是Javascript对异步的具体实现，在程序执行过程中，直接执行同步代码，异步代码／函数会先放在异步队列中挂起，待同步的任务执行完成之后，轮询执行异步队列中的任务。

> ***为什么要进行事件轮询？***

可能经常会听到一个词Node.js是异步非阻塞的，但你真正了解吗？在异步非阻塞I／O调用中，用户发起一个I/O操作后会立即返回（`优处是CPU时间片可以用来处理其它事物，体现了异步非阻塞`），此时返回的只是调用的状态，待完整的I/O完成之后，应用程序通过回调接收通知（`javascript同学熟知的Callback`），在应用程序接收回调通知之前的这段时间里，会有一种判断来确认I/O操作是否完成，这种判断技术就是轮询。

[操作系统的轮询技术演进](system-event-loop.md)

---

# Node.js中的EventLoop

在Node.js启动的时候，它会初始化EventLoop，处理程序代码，可能是调用异步API、定时器或者调用process.nextTick()，然后开始事件轮询。

```txt
   ┌───────────────────────┐
┌─>│        timers         │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     I/O callbacks     │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     idle, prepare     │
│  └──────────┬────────────┘      ┌───────────────┐
│  ┌──────────┴────────────┐      │   incoming:   │
│  │         poll          │<─────┤  connections, │
│  └──────────┬────────────┘      │   data, etc.  │
│  ┌──────────┴────────────┐      └───────────────┘
│  │        check          │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
└──┤    close callbacks    │
   └───────────────────────┘
```

```!
note: 以上每个方框被称为EventLoop的一个"阶段"
```

#### 参考指南

* [The Node.js Event Loop, Timers, and process.nextTick()](https://github.com/nodejs/node/blob/v6.x/doc/topics/event-loop-timers-and-nexttick.md)
* [Node.js Event Loop 的理解 Timers，process.nextTick()](https://cnodejs.org/topic/57d68794cb6f605d360105bf)
* [什么是浏览器的事件循环（Event Loop）](https://segmentfault.com/a/1190000010622146)
* [使用异步 I/O 大大提高应用程序的性能](https://www.ibm.com/developerworks/cn/linux/l-async/)
* [UNIX网络编程](https://book.douban.com/subject/1500149/)
* [Linux网络编程](https://book.douban.com/subject/4189500/)