UIView + Category (URL)

调用了 sd_internalSetImageWithURL方法  位于UIView+WebCache中

1 SDWebImageContext 的copy
2 取消当前下载的task从队列里 NSMapTable的构建线程安全,
3 根据options参数配置默认图
4 重置NSProgress
5 检测是否支持动画,若支持,开启动画
6 获取SDWebImageManager(核心)
7 初始化进度block回调
8 构建下载任务 SDWebImageOperation = loadImageWithURL(...) 并设置完成回调block
	FINISH回调中
		进度条100%检测,关闭进度动画
		回调internal完成回调
		设置图片,如果有过度动画,配置动画


loadImageWithURL 方法 位于SDWebImageManager
1 初始化SDWebImageCombinedOperation
2 检测url是否无法下载 failUrls是一个Set，判断时保证线程安全(信号量)
3 有效url 将构建的Operation加入队列中, runningOperations,是一个Set(保证线程安全)
4 构建SDWebImageOptionsResult
5 开始加载过程 callCacheProcessForOperation

callCacheProcessForOperation 方法 位于SDWebImageManager
1 获取SDImageCache 默认或者手动创建SDWebImageContext中
2 获取CacheKey,默认使用url.absoluteString,如果配置中设置缩略图,key中增加缩略图信息
3 构建 operation.cacheOperation block
	缓存未命中时 
	调用 callDownloadProcessForOperation
		构建loaderOperation




