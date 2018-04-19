---
title: '[引擎笔记]之一：windows游戏运行框架'
toc: true
copyright: true
tags: ['引擎笔记','笔记']
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


##### 1.创建WNDCLASSEX结构体

首先需要创建一个窗口的模板，如下所示

```
	WNDCLASSEX  wClass;                         // 使用WNDCLASSEX结构体
	wClass.cbSize = sizeof(WNDCLASSEX);         
	wClass.style = CS_HREDRAW | CS_VREDRAW | CS_DBLCLKS | CS_OWNDC;
	wClass.lpfnWndProc = WinProc;
	wClass.cbClsExtra = 0;
	wClass.cbWndExtra = 0;
	wClass.hInstance = hInstance;
	wClass.hIcon = LoadIcon(NULL, IDI_APPLICATION);                    
	wClass.hCursor = LoadCursor(NULL, IDC_ARROW);
	wClass.hbrBackground = (HBRUSH)GetStockObject(BLACK_BRUSH);
	wClass.lpszMenuName = NULL;
	wClass.lpszClassName = "CLASSNAME_1";
	wClass.hIconSm = LoadIcon(NULL, IDI_APPLICATION);

```
1. 使用WNDCLASSEX结构体
2. cbSize时整个结构体的长度。为了便于指向结构体中的地址
3. style是指窗口风格，这里使用的
    CS_HREDRAW :    若移动或改变了窗口的宽度，则刷新整个窗口
    CS_VREDRAW :    若移动或改变了窗口的高度，则刷新整个窗口
    CS_DBLCLKS :    当用户双击鼠标时向窗口程序发送一个双击的信息，同事，光标位于属于该类的窗口中
    CS_OWNDC   :    为该类中每一个窗口分配一个单值的设备描述表
4. lpfnWndProc 是用来处理windows事件的
5. hInstance代表应用本身，用于追踪应用本身的资源

##### 2.注册WNDCLASSEX

创建完成WNDCLASSEX之后，要将这个模板注册到系统中：

```
	RegisterClassEx(&wClass);
```

这里也使用的是RegisterClassEx(注意有Ex)。

##### 3.创建Windows窗口

将模板注册之后，就可以创建这个模板的窗口了。
使用CreateWindowEx方法。该方法返回一个HWND窗口的句柄，用于标记创建的窗口。

```
	HWND hWnd;
	if (!(hWnd = CreateWindowEx(
		NULL,
		WNDCLASS_NAME,                          
		WND_NAME, WS_OVERLAPPEDWINDOW | WS_VISIBLE,                 // 窗口的样式
		0, 0,                                                       // 窗口左上角的位置
		400, 800,                                                   // 窗口的宽和高的像素
		NULL,
		NULL,
		hInstance,                                                  // 应用的实例
		NULL)))
	{
    // 如果创建失败，弹出一个messagebox
		MessageBox(NULL, "create window error!!", "Create Window msg", MB_OK);
		return 0;
	}

    // 如果创建成功，则刷新显示当前窗口
	ShowWindow(hWnd, nShowCmd);
	UpdateWindow(hWnd);
```


#### 3.窗口事件和游戏主循环

##### 1.处理事件

Windows是一个事件驱动的操作系统，意思是说，Widnows系统会根据用户的操作产生事件，不用开发者自己去写什么操作产生了什么事件。
操作系统发现有事件了之后，会通知监听事件的应用。开发者只要监听系统给发事件，并且处理事件消息就可以了。
举个例子，如果我们点击了某个窗口的关闭按钮，系统就会发送一个关闭了某某窗口的事件。
WNDCLASSEX中的lpfnWndProc就是用来处理系统发送的事件的。

处理事件的回调函数是:

```
LRESULT CALLBACK WinProc(HWND hWnd, UINT msg, WPARAM wParam, LPARAM lParam)
{
	PAINTSTRUCT ps;
	HDC hdc;
	switch (msg)
	{
	case WM_CREATE:
	{
		return 0;
	}
	break;
	case WM_PAINT:
	{
		hdc = BeginPaint(hWnd, &ps);
		EndPaint(hWnd, &ps);
		return 0;

	}
	break;
	case WM_DESTROY:
	{
		PostQuitMessage(0);
		return 0;
	}
	break;
	default:
		break;
	}
	return (DefWindowProc(hWnd, msg, wParam, lParam));
}

```

如果是我们监听的需要处理的事件，那么我们在这个函数中，就做相应的处理，并且return 0,消息不在向后传递;
如果不是我们感兴趣的事件，那么，就交给系统默认的事件处理函数去处理。

这里我们只对窗口的创建、窗口的重绘、窗口的销毁事件感兴趣:
WM_CREATE: 窗口创建时，我们应该初始化自己的资源
WM_PAINT: 窗口需要绘制的时候，我们就重绘一下。
    windows的窗口绘制是只绘制需要重画地方，在PAINTSTRUCT的RECT参数代表重绘区域
    HDC返回重绘的是哪个设备
WM_DESTROY: 当窗口销毁的时候，我们调用PostQuitMessage来告诉系统，应用要停止了。
其他的消息我们不处理，而是交给系统默认处理DefWindowProc(hWnd, msg, wParam, lParam).


##### 2.主动查询事件

1. 阻塞线程方式：使用GetMessage()函数来主动获取事件。

```
	MSG msg;

	while (GetMessage(&msg, NULL, 0, 0))
	{
		TranslateMessage(&msg);     
		DispatchMessage(&msg);
	}

```
注意，GetMessage()会阻塞调用这个函数的线程，直到能够获取到事件
TranslateMessage()函数将获取到的事件信息作了处理，是一个虚拟加速键翻译器
DispatchMessage()函数是分发消息，将调用WinProc来进一步处理事件

2. 实时查询方式，不阻塞线程：使用PeekMessage()函数来查询事件

```
	MSG msg;
	while (true)
	{
		if (PeekMessage(&msg, NULL, 0, 0, PM_REMOVE))
		{
			if (msg.message == WM_QUIT)
			{
				break;
			}
			TranslateMessage(&msg);
			DispatchMessage(&msg);
		}
		Game_Main();        // 游戏主循环
	}

```
在主线程中一直查询事件，如果有事件，我们就处理事件。
并且，当我们收到WM_QUIT消息的时候，就退出while循环，结束主窗口.



- 注意，GetMessage()和PeekMessage()的第二个参数HWND参数都是NULL,这是因为：
函数接收属于调用线程的窗口的消息，线程消息由函数PostThreadMessage寄送给调用线程,
不接收属于其他线程或其他线程的窗口的消息，即使hWnd为NULL。由PostThreadMessage寄送的线程消息，其消息hWnd值为NULL。
我们当前的应用程序只有一个主线程。（每个引用程序都默认会起一个线程，这个线程就是主线程)

##### 3. 游戏主循环流程总结

在创建窗口后，在while(true)中实时查询事件和调用游戏主循环Game_Main();

所有整体的流程是:

```
WinMain()
{
    创建WNDCLASSEX模板
    注册WNDCLASSEX模板
    创建窗口
    while(ture)
    {
        查询事件：如果有事件，就处理事件
        Game_Main(): 游戏主循环
    }
}

WinProc():  处理事件的回调函数
Game_Main(): 游戏主循环函数
```


### 4.完整程序

```
#define WIN32_LEAN_AND_MEAN

#include <windows.h>
#include <windowsx.h>
#include <stdio.h>
#include <math.h>

#define WNDCLASS_NAME "WNDCLASS_1"
#define WND_NAME "延澈左 GameEngine"

LRESULT CALLBACK WinProc(HWND hWnd, UINT msg, WPARAM wParam, LPARAM lParam)
{
	PAINTSTRUCT ps;
	HDC hdc;
	switch (msg)
	{
	case WM_CREATE:
	{
		return 0;
	}
	break;
	case WM_PAINT:
	{
		hdc = BeginPaint(hWnd, &ps);
		EndPaint(hWnd, &ps);
		return 0;

	}
	break;
	case WM_DESTROY:
	{
		PostQuitMessage(0);
		return 0;
	}
	break;
	default:
		break;
	}
	return (DefWindowProc(hWnd, msg, wParam, lParam));
}

void Game_Main()
{

}

int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, LPSTR lpCmdLine, int nShowCmd)
{
	WNDCLASSEX  wClass;
	wClass.cbSize = sizeof(WNDCLASSEX);
	wClass.style = CS_HREDRAW | CS_VREDRAW | CS_DBLCLKS | CS_OWNDC;
	wClass.lpfnWndProc = WinProc;
	wClass.cbClsExtra = 0;
	wClass.cbWndExtra = 0;
	wClass.hInstance = hInstance;
	wClass.hIcon = LoadIcon(NULL, IDI_APPLICATION);
	wClass.hCursor = LoadCursor(NULL, IDC_ARROW);
	wClass.hbrBackground = (HBRUSH)GetStockObject(BLACK_BRUSH);
	wClass.lpszMenuName = NULL;
	wClass.lpszClassName = WNDCLASS_NAME;
	wClass.hIconSm = LoadIcon(NULL, IDI_APPLICATION);

	RegisterClassEx(&wClass);

	HWND hWnd;
	if (!(hWnd = CreateWindowEx(
		NULL,
		WNDCLASS_NAME,
		WND_NAME, WS_OVERLAPPEDWINDOW | WS_VISIBLE,
		0, 0,
		400, 800,
		NULL,
		NULL,
		hInstance,
		NULL)))
	{
		MessageBox(NULL, "create window error!!", "Create Window msg", MB_OK);
		return 0;
	}

	ShowWindow(hWnd, nShowCmd);
	UpdateWindow(hWnd);

	MSG msg;

//	while (GetMessage(&msg, NULL, 0, 0))
//	{
//		TranslateMessage(&msg);
//		DispatchMessage(&msg);
//	}

	while (true)
	{
		if (PeekMessage(&msg, NULL, 0, 0, PM_REMOVE))
		{
			if (msg.message == WM_QUIT)
			{
				break;
			}
			TranslateMessage(&msg);
			DispatchMessage(&msg);
		}
		Game_Main();
	}

	return msg.wParam;
}

```
