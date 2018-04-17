---
title: '[C#笔记]IDisposable接口'
toc: false
copyright: true
date: 2018-04-16 16:46:22
tags: ['c#','笔记']
---
IDisposable接口
- 在System命名空间下

<!--more-->
1. void Disposable()
2. 这个接口的存在原因是，为了可以释放Object做占用的内存资源,可以帮助GC减少计算引用Object的时候，
3. **官方建议**，类都定义一个调用了Disposable()方法的析构函数。（在gc的时候应该会调用析构函数。在析构函数中释放了资源之后，gc计算这个Object引用的其他Object就少了）
- 使用using(){}的时候，会调用Dispose方法
- 对于Dispose,官方给的建议是满足下列条件：
1. 即使调用多次，也是安全的 （多次调用的时候，不要出错）
2. 释放此实例用到的资源
3. 如果需要，调用父类的Dispose方法
4. 减少Gc finalization队列里面的对象数量
5. Dispose方法中一般不要抛出异常。除非是非常严重的异常，比如OutOfMemoryException

