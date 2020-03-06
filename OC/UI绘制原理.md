## UI绘制原理

### 宏观视图展示流程
```
CPU 视图的创建与配置,生成bitMap
GPU 拿到位图,对位图图层进行渲染,包括纹理的合成
将GPU合成的结果放置到帧缓冲区当中,然后视频控制器按照VSync信号逐行读取缓冲区的数据，经过可能的数模转换传递给显示器,最终显示
```
###  绘制原理
```

UIView.setNeedDisplay
View.layer.setNeedsDisplay
CALayer.display
layer.delegate.displayLayer (可选实现)
若实现 -> 异步绘制入口
未实现 -> 系统绘制
```

#### 系统绘制流程
```
CALayer create backing store (绘图上下文)
判断delegate是否存在
存在调用 
delegate.drawLayer:inContext:
UIView.DrawRect
CALayer Update Backing Store to GPU
```
### layoutSubviews调用情况
```
1 init时不触发, initWithFrame: frame不是zero时调用
2 addSubView会触发layoutSubviews
3 设置view.frame会触发
4 滚动视图时触发
5 Screen旋转
6 手动调用setNeedsLayout时
```
### 异步绘制
```
核心： 在子线程中布局加绘制bitmap，之后在主线程中回调设置layer.content,提供给GPU进行处理
两个方式：1，绘制完之后直接调用主线程进行layer的重新赋值,并执行display方法
        2. 将开启异步绘制的layer记录到临时表中,鉴定runloop的状态,在runloop进入核心态或者退出的时候调用临时表中的视图进行展示
```
