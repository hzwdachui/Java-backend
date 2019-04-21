安卓

基础知识 – 四大组件（生命周期，使用场景，如何启动）

java基础 – 数据结构，线程，mvc框架

通信 – 网络连接（HttpClient，HttpUrlConnetion），Socket

数据持久化 – SQLite，SharedPreferences，ContentProvider

性能优化 – 布局优化，内存优化，电量优化

安全 – 数据加密，代码混淆，WebView/Js调用，https

UI– 动画

其他 – JNI，AIDL，Handler，Intent等

开源框架 – Volley，Gilde，RxJava等（简历上写你会的，用过的）

拓展 – Android6.0/7.0/8.0特性，kotlin语言，I/O大会
## Android系统架构
- 应用程序(Applications)
- 应用程序框架(Application Frameworks)
	- 提供各种API进行开发
- 系统运行库与Android运行环境(Libraris & Android Runtime)
	- Lib: 例如SQLite
	- 运行环境：Dalvik虚拟机
- Linux内核(Linux Kernel)
	- Android的核心系统服务依赖于Linux 2.6 内核，如安全性，内存管理，进程管理， 网络协议栈和驱动模型。 Linux 内核也同时作为硬件和软件栈之间的抽象层。

## Activity生命周期
- 四种状态
	- 活动（Active/Running）状态
	- 暂停(Paused)状态
	- 停止(Stopped)状态
	- 非活动（Dead）状态
- 七个重要方法
	- onCreate()
	- onStart() 
	- onResume()
	- onPause()
	- onStop() 
	- onRestart() **or** onDestroy()
- 三个嵌套循环 (???)

## Android Activity的Launch Mode(启动模式)
配制方法实在Mainifest.xml中进行：  
对安卓而言，Activity有四种启动模式，它们是：  

- standard 标准模式，每次都新建一个实例对象
- singleTop 如果在任务栈顶发现了相同的实例则重用，否则新建并压入栈顶
- singleTask 如果在任务栈中发现了相同的实例，将其上面的任务终止并移除，重用该实例。否则新建实例并入栈
- singleInstance 允许不同应用，**进程线程等共用一个实例，无论从何应用调用该实例都重用**

## 四大组件
参考： [四大组件](https://www.jianshu.com/p/930dadb7a3cf)

- activity
- service 
- content provider
- broadcast receiver
## Android 多线程
参考：[Android多线程](https://www.jianshu.com/p/56163a3beb4a)

## ANR
参考：[ANR](https://www.jianshu.com/p/388166988cef)

### 定义
ANR全称：Application Not Responding，也就是应用程序无响应。

### 原因
Android系统中，**ActivityManagerService**(简称AMS)和**WindowManagerService**(简称WMS)会检测App的**响应时间**，如果App在特定时间无法相应屏幕触摸或键盘输入时间，或者特定事件没有处理完毕，就会出现ANR。

以下四个条件都可以造成ANR发生：

- InputDispatching Timeout：5秒内无法响应屏幕触摸事件或键盘输入事件
- BroadcastQueue Timeout ：在执行前台广播（BroadcastReceiver）的onReceive()函数时10秒没有处理完成，后台为60秒。
- Service Timeout ：前台服务20秒内，后台服务在200秒内没有执行完毕。
- ContentProvider Timeout ：ContentProvider的publish在10s内没进行完。

### 避免
- 尽量避免在主线程（UI线程）中作耗时操作。
- 耗时操作放在子线程中