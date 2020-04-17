## 1.KVO的实现原理
```
在对象增加监听之后
1 runtime生成所监听对象的子类 NSKVONotifying_xxx

2 在子类中重写所监听属性的set方法, 然后将对象的isa指向该子类

// KVC会触发
// willChangeValueForKey与didChangeValueForKey同时调用会触发
```

## 响应链

继承自UIResponder的类都可以响应事件

```
1. 用户触摸屏幕
2. 系统将事件传递给应用
3. 应用唤起Runloop并响应Source1回调
4. 回调函数处理之后会将事件包装后传给UIApplication
5. UIApplication传递给KeyWindow
6. window通过对subView开始进行遍历,寻找合适的响应对象,通过调用subView hitTest:withEvent:->View方法 (一层一层的调用下去,直至找到合适的响应者或者响应被废弃)

扩大响应范围
继承自UIResponder的类可以实现 -pointInside:CGPoint withEvent:UIEvent方法，
调整可选区域, 返回命中情况

```
## 如何通过一个view查找它所在的viewController
```
遍历当前View的next(遍历响应链) 
white(view.next != nil) {
	view = view.next
}
```

## 进程间通信
```
1 URL Scheme 
2 粘贴板
3 Keychain
4 local Socket
5 UIDocumentInteractionController
6 App Group 
```

## 程序执行的过程
```
pre-main执行过程
1. 初始化内存空间，fork/exec创建进程
2. 读取程序的执行文件 Mach-O
3. 加载dyld 动态链接器
4. 加载程序相关依赖库
5. 对相关依赖库进行链接,runtime初始化,调用每一个依赖库的初始化方法
6. 对程序可执行文件进行初始化,(runtime对项目中的所有类进行类结构初始化，然后调用所有的load方法)
7. dyld返回main函数地址，main函数被调用
main执行过程
1. main函数执行
2. UIApplicationMain执行
3. 创建UIApplication
3. 创建ApplicationDelegate
4. 加载plist文件
5. 构建Runloop及各类监听
6. 回调ApplicationDidFinishLaunchingWithOptions
7. 构建Window
8. 构建RootViewController
```

## 自动释放池

```
结构: Page结构组成的双向链表
autoreleasePoolPage结构可以存储4096个字节,其中56个字节用于存储Page自身成员变量,其他的字节用于存储加入到自动释放池中的对象
结合RunLoop运行
监听runloop的Entry、beforewaiting两个状态 优先级分别为最高与最低
在Entry回调时创建自动释放池(Push)
在BeforeWaiting回调时 释放自动释放池并构建新的自动释放池(Pop之后在Push)

```
