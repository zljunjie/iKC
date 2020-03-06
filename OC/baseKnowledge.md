## 1.KVO的实现原理
```
在对象增加监听之后
1 runtime生成所监听对象的子类 NSKVONotifying_xxx

2 在子类中重写所监听属性的set方法, 然后将对象的isa指向该子类

// KVC会触发
// willChangeValueForKey与didChangeValueForKey同时调用会触发
```
## 2.消息调用的过程
```
Runtime
objc_msgSend()
1 通过isa指针找到Class
2 从Class的Cache中寻找Selector
3 如果3未命中,在Class的MethodList中寻找Selector
4 如果在Class中没有找到,继续往SuperClass中寻找 (重复2,3)
5 如果找到Selector，就去执行方法的IMP
6 NSObject也未找到时开始执行消息转发(动态方法解析,备源接受者,消息重定向)
7 runtime调用resolveInstanceMethod 或者 resolveClassMethod方法,开发者可以在这两个方法中动态添加Method
8 如果未重写7中的方法,runtime开始询问是否有其他的对象可以处理当前Selector,此时调用forwardingTargetForSelector 此方法返回可调用该方法的对象
9 若8未找到,runtime则进行消息重定向,runtime调用methodSignatureForSelector获取函数签名，如果返回, runtime将执行forwardInvocation方法消息分装成NSInvocation对象,分发出去
```
```
struct objc_class {
	Class _Nonenull isa OBJC_ISA_AVALIABILITY
#if !__OBJC2__
	Class _Nullable super_class
	const char * _Nonenull name
	long version
	long info
	long instance_size
	struct objc_var_list * _Nullable ivars
	struct objc_method_list * _Nullable methodLists
	struct objc_cache * _Nonenull cache
	struct objc_protocol_list * _Nullable protocols
#end
} 
```

## 响应链

继承自UIResponder的类都可以响应事件

```
1. 用户触摸屏幕
2. 系统将事件传递给应用
3. 应用唤起Runloop并响应Source1回调
4. 回调函数处理之后会将事件包装后传给UIApplication
5. UIApplication传递给KeyWindow
6. window通过对subView开始进行遍历,寻找合适的响应对象,通过调用subViewhitText:withEvent:->View方法 (一层一层的调用下去,直至找到合适的响应者或者响应被废弃)

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

## 三种Block

```

```