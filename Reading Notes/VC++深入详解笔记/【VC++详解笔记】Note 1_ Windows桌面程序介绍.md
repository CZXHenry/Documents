# 【VC++详解笔记】Note 1: Windows桌面程序介绍
## 1. 相关概念解释
- VC++
	VC++是微软公司旗下的一款IDE，集成了windows桌面应用程序的开发接口（Windows API），目前VC++已经被集成到visual studio里面去了。
- 窗口
	窗口是电脑上能够与用户进行交互的一个矩形区域，win32 SDK中实现了多种样式的窗口。
- 句柄
	句柄相当于每个窗口的标识符，windows系统可以通过句柄来唯一识别电脑中的任意一个窗口，并通过句柄来管理每个窗口。
- 消息
	当用户与应用程序窗口之间发生交互事件时，操作系统会感知到这一事件，然后将这个事件包装成为一条消息，并将这条消息放到应用程序的消息队列之中等待应用程序取出。
- 消息队列
	消息队列是每个应用程序都有的，相当于消息缓冲区，里面都是等待应用取出的消息。
- 窗口过程函数
	用来处理应用接收到的消息的处理函数。
- WinMain函数
	WinMain函数是windows桌面程序的入口，相当于控制台程序中的main函数。
## 2. Windows程序内部运行机制
一个完整的windows桌面应用程序，主要由创建并显示窗口和消息处理两大部分组成的。
### 2.1 创建并显示窗口
创建并显示窗口，有四个主要的步骤，分别是设计窗口类、注册窗口类、创建窗口和显示及更新窗口
#### 2.1.1 设计窗口类
windows.h 头文件中已经定义了一个窗口结构体，里面包含了所有用于表示一个窗口所需要的数据成员（如窗口句柄、窗口长和宽、窗口名称等等）。这里所谓的设计窗口类，其实是通过设定这些数据成员来设定某种具体的窗口样式，而不是重新设计一个表示窗口的结构体。
```cpp
//WNDCLASS结构体定义
typedef struct tagWNDCLASSW {
    UINT        style;
    WNDPROC     lpfnWndProc;
    int         cbClsExtra;
    int         cbWndExtra;
    HINSTANCE   hInstance;
    HICON       hIcon;
    HCURSOR     hCursor;
    HBRUSH      hbrBackground;
    LPCWSTR     lpszMenuName;
    LPCWSTR     lpszClassName;
} WNDCLASSW, *PWNDCLASSW, NEAR *NPWNDCLASSW, FAR *LPWNDCLASSW;
```
## 3. 写一个简单的Windows桌面程序