### Optimization One

```
1. 不合适的API
	UIImage- name会再内存中保留一份
	Assert文件下的图片也会被直接copy到内存中
	UIColor创建图片
	修改方案 - 使用UIButton设置背景图
	谨慎使用CoreGraphic的drawRect方法 会额外增加内存占用
	修改方案 - 使用CAShapeLayer 
	CAShapeLayer优势: 使用硬件加速,绘制相同图片比CoreGraphic快
	不会生成寄宿图形，占用内存更小
	不会被边界裁剪掉
	不会像素化
	
	UILabel设置背景色，除了clear和white 
	
2. 
```