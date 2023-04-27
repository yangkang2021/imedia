# js异步开发总结(让所有任务按顺序都异步)
- 做完一件事才后能做下一件事，这叫串行。
- javascript默认单线程异步不可改变，怎么实现串行呢？
- **那就让所有任务按顺序都异步**，4种方法都可以：

### (一)四种方法
1. 回调函数：既然异步，那就在回调函数里去做下一件事情。缺点：回调地域，代码缩进太多不好维护。
2. Generator：用迭代器。缺点：要定义迭代器，和反复使用next把任务串起来。
3. Promise：自动把结果封装成Promise，用then和catch把任务串起来。 缺点：还是要用then和catch。
4. async/await：对Promise使用的进一步封装，自动把任务串起来。完美：需要串行的语句前加await，然后就跟写同步代码一样。

### (二)异步应用场景
1. 定时任务：setTimeout、setInterval。
2. 网络请求：ajax请求、动态创建img标签的加载。
3. 事件监听器：addEventListener。
4. websocket收发数据。
5. nodejs对文件的读写。