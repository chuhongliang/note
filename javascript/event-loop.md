# 浏览器的事件轮询

### 任务队列
- 所有的任务可以分为同步任务和异步任务，同步任务，顾名思义，就是立即执行的任务，同步任务一般会直接进入到主线程中执行；而异步任务，就是异步执行的任务，比如ajax网络请求，setTimeout 定时函数等都属于异步任务，异步任务会通过任务队列( Event Queue )的机制来进行协调

- 同步和异步任务分别进入不同的执行环境，同步的进入主线程，即主执行栈，异步的进入 Event Queue 。主线程内的任务执行完毕为空，会去 Event Queue 读取对应的任务，推入主线程执行。 上述过程的不断重复就是我们说的 Event Loop (事件循环)。

> 在事件循环中，每进行一次循环操作称为tick，通过阅读规范可知，每一次 tick 的任务处理模型是比较复杂的，其关键的步骤可以总结如下：

1. 在此次 tick 中选择最先进入队列的任务( oldest task )，如果有则执行(一次)
2. 检查是否存在 Microtasks ，如果存在则不停地执行，直至清空Microtask Queue
3. 更新 render
4. 主线程重复执行上述步骤
> 可以用一张图来说明下流程：

![](./img/event-loop.png)

这里相信有人会想问，什么是 microtasks ?规范中规定，task分为两大类, 分别是 Macro Task （宏任务）和 Micro Task（微任务）, 并且每个宏任务结束后, 都要清空所有的微任务,这里的 Macro Task也是我们常说的 task ，有些文章并没有对其做区分，后面文章中所提及的task皆看做宏任务( macro task)。

macrotask 主要包含：script( 整体代码)、setTimeout、setInterval、I/O、UI 交互事件、setImmediate(Node.js 环境)

microtask主要包含：Promise、MutaionObserver、process.nextTick(Node.js 环境)

setTimeout/Promise 等API便是任务源，而进入任务队列的是由他们指定的具体执行任务。来自不同任务源的任务会进入到不同的任务队列。其中 setTimeout 与 setInterval 是同源的

> 下面看一段代码
``` javascript
function test() {
			console.log('script start');
			
			setTimeout(function () {
				console.log('timeout1');
				new Promise(function(reslove){
					console.log('promise2');
					reslove();
				}).then(function(){
					console.log('then2')
				});
				new Promise(function(reslove){
					console.log('promise3');
					reslove();
				}).then(function(){
					console.log('then3')
				});
			});

			setTimeout(function(){
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
then2
then3
timeout2
timeout3
```

### 参考指南
[浏览器与Node的事件循环(Event Loop)有何区别?](https://blog.fundebug.com/2019/01/15/diffrences-of-browser-and-node-in-event-loop/)                     