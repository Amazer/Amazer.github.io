---
title: '[引擎笔记]之一：windows游戏运行框架'
toc: true
copyright: true
tags: ['引擎','笔记']
---

#### 1.工程配置

- 配置为win32应用程序
    注意，不是console控制台程序。console控制台程序基于dos,没有窗口。win32应用程序是标准的windows事件响应的程序。
<!--more-->

- 安装DX9.0和DX9.0SDK
    DX9.0Sdk是dx的开发工具包，里面提供了调用dx的api。
    使用Dx9.0的原因是，作者使用7.0或者8.0的接口，9.0基本上是兼容的。
    不要用Dx10,Dx10改动比较大。

- 引用Dx SDK

    0. **注意：将引用的dx目录放到所有引用的最前面**，以免引用到工程默认配置的东西
    1. 要include dxsdk的include。路径：dxsdk安装目录/Include
    2. 要依赖dxsdkd lib库。路径: dxsdk安装目录/Lib/x86
        这里使用的是x86的库
    3. 手动包含用到的库,避免找不到库
        《windows编程大师技巧》中:ddraw.lib dinput.lib dinput8.lib dsound.lib d3dim.lib dxguid.lib winmm.lib
        《3D游戏编程大师技巧》中: ddraw.lib dinput.lib dinput8.lib dsound.lib winmm.lib

#### 2.创建Windows窗口

##### 0.基础准备知识
- console应用的入口是main()

    ```
    void main()
    ```
    或者是：

    ```
    int main(int argc char *argv[])

    ```

- win32应用的入口是WinMain()

    ```
    int WINAPI WinMain(HINSTANCE hinstance,
    HINSTANCE hprevinstance,
    LPSTR lpcmdline,
    int ncmdshow)
    ```
- win32应用需要包含头文件
    ```
    #define WIN32_LEAN_AND_MEAN             
    #include <windows.h>
    #include <windowsx.h>

    ```
    - \#defind WIN32_LEAN_AND_MEAN：开发win32应用可以使用MFC和win sdk两种。MFC是比较重量级的api，这里告诉编译器，只是用轻量级的win sdk
    - \#include <windows.h>     包含旧版win api的头文件 
    - \#include <windowsx.h>    包含新版win api的头文件
    - **Windows SDK中带x(Ex)的比较新**


##### 1.创建WindowsClassEx结构体

##### 2.注册WindowsClassEx

##### 3.创建Windows窗口

#### 3.窗口事件和游戏主循环


