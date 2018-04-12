---
title: '[游戏框架]ECS(Entity-Component-System)个人笔记'
toc: true
copyright: true
date: 2018-04-12 13:18:32
tags: '笔记'
---
### 1.[无框架实现ECS](https://zhuanlan.zhihu.com/p/32787878)
1. Entity
    - 是数据(Data)
    - 多个Data的桥梁
    - 唯一标识一个物体

<!--more-->

2. Component
    - 存放Data,不允许有逻辑；也没有对外依赖
    - 依赖于Entity:Entity作为Component的管理者
3. System:逻辑(方法函数)
    (将Component的逻辑全部移动到System中)
    - 是唯一承载逻辑的敌方
    - 直接依赖于Component,对数据进行处理

### 2.Unity3D中的框架和ECS的对比
0. 参考链接：[Unity下的ECS框架 Entitas简介](https://www.cnblogs.com/yangrouchuan/p/7436533.html)
1. Unity3D中是GameObject-Component设计
2. Unity3D不是ECS设计：
    - Unity中的Component都是一个完整的类，缓存不友好
    - Unity中的Component包含逻辑。
    - Unity中无法获取所有"有ComponentA和ComponentB"的所有GameObject,而在ECS中这是非常必要的

### 3.[Entitas-CSharp](https://github.com/sschmid/Entitas-CSharp)
[Unity3D ECS框架 Entitas 基本概念](https://zhuanlan.zhihu.com/p/31393681)
1. Entity
    - 数据容器(IComponent的容器)
2. Context:
    - Factory of Entity
        - 创建销毁Entity
        - 过滤选择感兴趣的Entity
3. Group
    - 为拥有不同Component的Entity分组
    - 从Context中过滤选择Groups
4. Matcher
    - 从Context中选择过滤Groups
5. Collector收集器
    - 收集处理根据Component过滤选择出来的entities
        - Group
        - 根据group获得entities
6. Systems
    - 根据IComponent执行逻辑
    - 有四种类型：
        - IInitializeSystem: 执行一次
        - IExecuteSystem: 每帧执行
        - ICleanupSystem: 在别的systems完成后，每帧执行
        - ReactiveSystem: 当Group有变换时执行


