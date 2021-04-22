# nextTick 实现原理

在日常开发中，想必我们已经对 `nextTick` 非常熟悉了，如果我们想要正确操作数据状态变化后的 `DOM`，那么我们一定与其打过交道。通过上面文章派发更新我们可以知道，最后一般都会调用 `nextTick(flushSchedulerQueue)`方法，从而达到异步更新。同时这也是比较核心的一个 `API`，加下来我们来详细介绍其具体是如何实现的。

## 运行机制

由于 `nextTick` 内部实现涉及 `JavaScript` 运行机制（Event Loop）相关知识，因此这里简单提及几个知识点，详细知识可以查看本文底部的相关文档。

### 单线程

首先我们要明确 `JS` 是单线程的，同一个时间点只能处理一件事。其原因与其用途相关，因为作为浏览器的脚本语言，与用户互动及操作 `DOM` 是其主要功能。如果不是单线程，会造成很复杂的同步问题。假如同时有两个线程，一个在 `DOM` 中添加内容，而另一个线程删除了这个节点，那么具体以哪个线程为准则？因此，其单线程已经成为了其核心特征。

即使为了利用多核 CPU 的计算能力，HTML5 提出Web Worker标准，允许 JavaScript 脚本创建多个线程，但是子线程完全受主线程控制，且不得操作 `DOM`。其核心本质是没有变化的。

### 同步和异步

`JS` 中任务分为
