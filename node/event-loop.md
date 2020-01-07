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
- ```timers``` 阶段: 这个阶段执行setTimeout(callback) and setInterval(callback)预定的callback;
- ```I/O callbacks``` 阶段: 执行除了 close事件的callbacks、被timers(定时器，setTimeout、- setInterval等)设定的callbacks、setImmediate()设定的callbacks之外的callbacks;
- ```idle, prepare``` 阶段: 仅node内部使用;
- ```poll``` 阶段: 获取新的I/O事件, 适当的条件下node将阻塞在这里;
- ```check``` 阶段: 执行setImmediate() 设定的callbacks;
- ```close``` callbacks 阶段: 比如socket.on(‘close’, callback)的callback会在这个阶段执行.
> 每一个阶段都有一个装有callbacks的fifo queue(队列)，当event loop运行到一个指定阶段时，
node将执行该阶段的fifo queue(队列)，当队列callback执行完或者执行callbacks数量超过该阶段的上限时，
event loop会转入下一下阶段.

### ```poll``` 阶段
> poll阶段是衔接整个event loop各个阶段比较重要的阶段，为了便于后续例子的理解，本文和原文的介绍顺序不一样，本文先讲这个阶段；

> 在node.js里，任何异步方法（除timer,close,setImmediate之外）完成时，都会将其callback加到poll queue里,并立即执行。

### ```poll``` 阶段有两个主要的功能：
1. 处理poll队列（poll quenue）的事件(callback);
2. 执行timers的callback,当到达timers指定的时间时;

> 如果event loop进入了 poll阶段，且代码未设定timer，将会发生下面情况：

- 如果poll queue不为空，event loop将同步的执行queue里的callback,直至queue为空，或执行的callback到达系统上限;
- 如果poll queue为空，将会发生下面情况：
- - 如果代码已经被setImmediate()设定了callback, event loop将结束poll阶段进入check阶段，并执行check阶段的queue (check阶段的queue是 setImmediate设定的)
- - 如果代码没有设定setImmediate(callback)，event loop将阻塞在该阶段等待callbacks加入poll queue;
> 如果event loop进入了 poll阶段，且代码设定了timer：

- 如果poll queue进入空状态时（即poll 阶段为空闲状态），event loop将检查timers,如果有1个或多个timers时间时间已经到达，event loop将按循环顺序进入 timers 阶段，并执行timer queue.
以上便是整个event loop时间循环的各个阶段运行机制，

---

## ```setTimeout``` 和 ```setImmediate```

> 二者非常相似，但是二者区别取决于他们什么时候被调用.

> setImmediate 设计在poll阶段完成时执行，即check阶段；

> setTimeout 设计在poll阶段为空闲时，且设定时间到达后执行；但其在timer阶段执行
其二者的调用顺序取决于当前event loop的上下文，如果他们在异步i／o callback之外调用，其执行先后顺序是不确定的

--- 

## ```process.nextTick()```
> ```process.nextTick()```不在```event loop```的任何阶段执行，而是在各个阶段切换的中间执行,即从一个阶段切换到下个阶段前执行。

---

> 下面看一段代码
``` javascript
function test() {
	console.log('script start');

	setTimeout(function () {
		console.log('timeout1');
		new Promise(function (reslove) {
			console.log('promise2');
			reslove();
		}).then(function () {
			console.log('then2')
		});
		new Promise(function (reslove) {
			console.log('promise3');
			reslove();
		}).then(function () {
			console.log('then3')
		});
	});

	setTimeout(function () {
		console.log('timeout2');
	});

	new Promise(resolve => {
		console.log('promise1');
		resolve();
		setTimeout(() => console.log('timeout3'), 10);
	}).then(function () {
		console.log('then1')
	})

	console.log('script end');
}
test();
```

> 执行结果：
``` javascript
script start
promise1
script end
then1
timeout1
promise2
promise3
timeout2
then2
then3
timeout3

```


#### 参考指南

* [浏览器与Node的事件循环(Event Loop)有何区别?](https://blog.fundebug.com/2019/01/15/diffrences-of-browser-and-node-in-event-loop/) 
* [The Node.js Event Loop, Timers, and process.nextTick()](https://github.com/nodejs/node/blob/v6.x/doc/topics/event-loop-timers-and-nexttick.md)
* [Node.js Event Loop 的理解 Timers，process.nextTick()](https://cnodejs.org/topic/57d68794cb6f605d360105bf)
* [什么是浏览器的事件循环（Event Loop）](https://segmentfault.com/a/1190000010622146)
* [使用异步 I/O 大大提高应用程序的性能](https://www.ibm.com/developerworks/cn/linux/l-async/)
* [UNIX网络编程](https://book.douban.com/subject/1500149/)
* [Linux网络编程](https://book.douban.com/subject/4189500/)