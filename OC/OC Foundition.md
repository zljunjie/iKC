##  OC Foundition

### NSArray&NSMutableArray
```
数组中只有一个元素
struct _NSSingleObjectArrayI {
	id _object;
}

不可变数组
struct _NSArrayI {
	UInt _used;
	id _list[0];
}

可变数组
_NSArrayM
使用环形缓冲区结构,
收尾插入时 offset移动和_used数量不涉及原数据位置变化
中间插入时 移动数据个数少的一方
收尾删除时 仅offset移动和_used数量
中间删除时 移动数据个数少的一方
struct _NSArrayM {
	
	UInt _offset;
	UInt _used;
	int _size;
	int _unused;
	uint32_t _mutations; //修改标记
	id *_list;
}

```
