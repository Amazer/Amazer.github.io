---
title: '[游戏框架]笔记之一：记录框架的优秀模块'
toc: true
copyright: true
date: 2018-04-11 12:27:57
tags: ['笔记','游戏框架']
---

记录一下看别人游戏框架的时候的一些模块。
大型项目中如何使用这些“模块”还是个问题。这些模块太简单了，实际的需求就...
先记录一下，作为借鉴吧。

<!--more-->

## 1.别人的
[QFrame](http://liangxiegame.com/post/1/)
### 0.大模块分类
- Main(入口)
- Event
- GUI/UI
- Audio
- Pool(对象池)
- Level
- Game
- Data


### 1.单例模板(object/Monobehavior/Static)/单例模板管理器
- 反射
- 效率
- GUI查看内部数据


### 2.状态机模板
1. State
2. Transition
注：Unity动画状态机


### 3.消息机制：事件触发


### 4.入口模块(Dev/Debug/Release)


### 5.Log:记录log信息到本地
1. Application.RegisterLogCallback:
2. Application.LogMessageReceived:
    - 在真机上Error和Exception时候，收不到信息
3. **Application.logMessageReceivedThreaded**:
    - 可以接收堆栈信息
    - 需要保证线程安全
4. Log分级：
- LOG
- WARNING
- ASSERT
- ERROR


### 6.GUI Console:在真机屏幕输出log


### 7.AssetBundle打包工具
- 区分平台：iOS/Android
- 资源输出路径
- 打包资源类型


### 8.设计模式
- Chain链式模式：方法都返回自己
- 静态扩展(+链式)
- 泛型静态扩展(+链式)


### 9.引用计数(内存管理)

---

## 2.自己的补充

---

### 1.可视化工具
- 必要！！特别是战斗数据
    - 使用属性


### 2.打包工具链
