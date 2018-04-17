---
title: '[引擎笔记]之二：创建资源以及菜单'
toc: true
copyright: true
date: 2018-04-13 13:18:34
tags: ['引擎笔记','笔记']
---
#### 1.资源概括
- 资源最终将会被打包成.res文件。

<!--more-->
- 自定义资源:
    - 创建.rc文件和.h文件
        myresource.h
        myres.rc
    - .h文件中定义资源的const id
        eg. myresource.h中内容：
        ```
        #define IDX_XXX    1000
        ```
    - .rc文件中,引入.h，并且定义资源的类型和位置
        eg.myres.rc文件中的内容
        IDX_XXX &ensp; &ensp;   资源类型    &ensp; &ensp;   资源名称/资源全路径
    - 或者.rc文件中，使用名称定义资源
        eg.myres.rc文件中的内容
        RES_NAME  &ensp; &ensp;  资源类型   &ensp; &ensp;    资源名称/资源全路径
- 加载资源
    - 用字符串名字定义的
    - 用ID定义的，使用**MAKEINTRESOURCE(ID)**

#### 2.资源类型和资源加载
##### 1 .ico图标资源(**ICON**)
1.定义图标资源
    icon_name/ICON_ID &ensp; &ensp; **ICON** &ensp; &ensp; icoName.ico
2.加载图标资源
```
    #include "myresource.h"
    LoadICON(hInstance,icon_name);                      // 资源定义的名称是字符串
    LoadICON(hInstance,MAKEINTRESOURCE(ICON_ID));       // 资源用ID定义的
```
##### 2 .cur光标资源(**CURSOR**)
1.定义光标资源资源
    cur_name/CUR_ID &ensp; &ensp; **CURSOR** &ensp; &ensp; curName.ico
2.加载图标资源
```
    #include "myresource.h"
    LoadCursor(hInstance,cur_name);                      // 资源定义的名称是字符串
    LoadCursor(hInstance,MAKEINTRESOURCE(CUR_ID));       // 资源用ID定义的
```
##### 3 字符串资源
1.定义字符串资源：在.rc文件中，格式为：
```
STRINGTABLE
{
    ID_STRING_1, "string 1"
    ID_STRING_2, "string 2"
}
```
每条字符串资源长度不能超过255个字符。

2.加载字符串资源
```
    #include "myresource.h"
    int bufferCount=80;
    char load_str[bufferCount]
    LoadString(hInstance,
        ID_STRING_1,                // 字符串的id
        load_str,                   // 加载字符串的指针
        bufferCount                 // buffer的长度
        )

```

##### 4 .WAV声音资源(**WAVE**)
1.定义声音资源
wave_name/WAVE_ID **WAVE** file_name.wav

2.加载/播放声音资源
```
    #include "myresource.h"
    PlaySound("wave_name",hInstance,
        SND_XXX | SND_YYY);

    PlaySound(MAKEINTRESOURCE(WAVE_ID),hInstance,
        SND_XXX | SND_YYY);
    // 停止所有声音
    PlaySound(NULL,hInstance,
        SND_PURGE);

```
#### 3.菜单资源(**MENU**)
##### 1.创建菜单资源
在.rc文件中,创建一个常见的菜单，包含File和Help
```
MainMenu  MENU  DISCRADABLE
{
    POPUP  "&File"
    {
        MENUITEM "Open",    MENU_FILE_ID_OPEN
        // ......

        MENUITEM "E&xit",    MENU_FILE_ID_EXIT
    }
    POPUP "Help"
    {
        MENUITEM "About",    MENU_FILE_ID_EXIT
    }
}
```
- POPUP：弹出菜单
- MENUITEM: 菜单项
- 使用&符号，定义&后的字母为Alt快捷键
在myres.h中定义使用到的id:
```
#define  MENU_FILE_ID_OPEN    20000
#define  MENU_FILE_ID_XXXX    20000
// ......

```

##### 2.加载菜单资源
1. 方法1：Windows类winClass定义中：
    winClass.lpszMenuName = "MainMenu"
    winClass.lpszMenuName = MAKEINTRESOURCE(MAIN_MENU_ID)
2. 方法2:先加载菜单资源，再赋值

```
    HMENU hMenu = LoadMenu(hInstance, "MainMenu");
```

或者:

```
    HMENU hMenu = LoadMenu(hInstance, MAKEINTRESOURCE(MAIN_MENU_ID));

```
赋值：
给hWnd赋值hMenu菜单

```
    SetMenu(hWnd,hMenu);
```

##### 3.响应菜单事件(page 82)
- 1.WM_COMMAND消息
    - 响应系统按键
    - 消息结构：msg、lparam、wparam
        对于响应菜单点击事件:
        - msg:WM_COMMAND
        - lparam:发出消息的窗口句柄
        - wparam:选中的菜单项的ID(LOWORD(wparam))
- 2.实例代码
在WinProc处理程序中：

```
case  WM_COMMAND:
{
    switch(LOWORD(wparam)
    {
        case MENU_FILE_ID_OPEN:
        {
            // clicked open
        }
        break;
        case MENU_FILE_ID_EXIT:
        {
            // clicked exit
        }
        break;

        // ......
        
    }
}
break;
```



