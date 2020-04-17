## AppDelegate 如何瘦身
```
Delegate中都有哪些内容
1 设置主windows和window的RootView
2 注册三方SDK的信息
3 注册系统功能
4 各种回调 (推送通知,openURL,app生命周期，后台执行任务,3DTouch，iWatch，访问用户健康数据,siri分析意图)

NSNotification方法, 系统的回调支持NSNotification
注册模块,不同的回调,按顺序调用不同模块中处理回调的方法
Method-Swizzling 交换方法

```

## NSOperation与GCD的区别
```
GCD特点
GCD提供了NSOperation不具备的功能,比如一次性执行(创建单例),延迟执行,调度组

NSOperation特点
1 可设置最大并发数
2 对队列中的任务可进行状态操作(暂停,继续,取消)
3 指定操作之间的依赖关系
4 可以设置任务的优先级

使用:
基于GCD简单高效,更强的执行能力，操作不太复杂时使用GCD,任务之间存在复杂的优先级关系及依赖关系时使用NSOperation

```

## APP常见崩溃
```
1. unrecognized selector crash
2. kvo crash
3. NSNotificaiton crash
4. NSTimer crash
5. Container crash
6. NSString crash
7. Bad Access crash
8. UI not on Main THerad Crash

如何避免
1 使用runtime消息转发中forwardTargetSelector方法中动态创建一个class，根据selector名称创建一个空函数，返回该类
2 kvo 有3个解决方案, 一 使用tryCrash避免多次remove出现crash。二 使用方法交换,在hook的方法中增加数量统计, 保证不会出现多次remove。dealloc之前未remove 三

3 NSNotification 未释放 ios9之后系统保护
4 NSTimer crash 内存泄露
5 Container crash, NSString crash hook相关获取导致越界的方法, 在hook的方法中增加安全性校验
7 僵尸指针(野指针) 在Scheme中选中Zoombie Object
	1 hook NSObject的dealloc方法, 
	2 运行时动态生成新类,将僵尸指针的isa执行新类
	3 给新类添加forwardingTargetForSelector方法，或添加resolveInstanceMethod方法,动态创建method
	4 在forwardingTargetForSelector中打印错误类

```

## .a静态库制作
```
1 使用xcode创建Cocoa Touch Static Library工程
2 实现功能业务的封装,系统动态库添加 Build Phases-Link Binary with libraryes
3 在Build Phases中增加头文件引用 /Add Headers Build Phaese
4 Headers中存在三个区域 public private project(public是公开的,Private可被用户看到的, project是私有的)
5 禁掉无效代码,debug符号 
	Build Settings中设置 Dead Code Stripping 删除被定义未调用的代码
	Strip Debug Symbol During Copy去除符号 Debug也设置为NO
	Strip Style 设置为 Non-Global Symbols 去除符号
6 分别编译生成真机与模拟器 .a静态文件
7 合并.a文件 lipo -create phones.a summer.a -output all.a
```

## 静态库debug时,可以看到内部源码问题
```
Q1 调用的时候可以直接跟到库的源码里
Q2 断点堆栈信息里可以看到库内部的函数调用

Q1原因: 感觉源文件被记录下来了,使用machoview工具发现信息处于Section(__DWARF,__Debug_str)段
解决: 关闭 build Setting中 generate Debug symbols 

Q2原因: symbols表中包含了自定义库里的方法
static Library中设置Strip Style为 non-global symbols，此时match-o文件符号表中无法查看到内部方法
strip stype 继续配合开启Deployment Postprocessing才能生效


但是在 framework库中使用报错

```
## 死锁的出现
```
第一种 ：线程死锁,两个或多个线程互相持有对方所需要的资源，导致这些线程处于等待状态
第二种 ：递归死锁,在递归链中加锁不当,导致死锁

1 系统资源不足
2 运行推进顺序不当
3 资源分配不当

四个必要条件
1 互斥条件: 一个资源只能被一个进程使用
2 请求与保持条件: 一个进程因请求资源而阻塞，对已经持有的资源保持不放
3 不剥夺条件: 进行已获得的资源,再未使用时不能强制剥夺
4 循环等待条件: 若干进程之间行程一种头尾相接的循环等待资源关系 

递归锁
同一个线程可以多次加锁,不会出现死锁,
为保证多线程,需要保证加锁与解锁次数相同


避免死锁

1 加锁顺序, 保证多个线程获得锁的顺序是一致的
2 加锁时限 在一定时间内未获得需要的锁,就释放自己所有的资源
3 死锁检测 


```
