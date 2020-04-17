
### isa指针 objc_object&objc_class详解
```
arm64架构之前  isa就是一个普通的指针,存储这Class/Meta-Class对象的内存地址
arm64架构之后  isa是一个共同体(union)结构,使用位域来存储更多的值
1. 当调用实例化对象的isa时,isa指向的是class
2. 当调用class的isa指针时,isa指向的是meta-class

对象被定义为  objc_object
类被定义为    objc_class

实例对象
struct objc_object {
    private: 
        isa_t isa;
        
}

union isa_t {
    Class cls;
    uintptr_t bits; (64位)
}



类对象
struct objc_class: objc_object {

    //继承父类的isa
    Override isa_t isa;
    
    Class superclass;
    cache_t cache;
    class_data_bits_t bits;
    
}

cache_t 主要是为了在消息发送的过程中，进行方法的缓存，加快调用效率，其中使用了动态扩容的方法，当容量达到最大值的3/4时，开始2倍扩容，扩容时会完全抹除旧的buckets，并且创建新的buckets代替，之后把最近一次临界的imp和key缓存进来，经典的LRU算法案例~

struct cache_t {
    struct bucket_t * _buckets
    mask_t _mask;
    mask_t _occupied;
}

struct bucket_t {
    cache_key_t _key;
    IMP _imp
}

struct class_data_bits_t {
    uintptr bits;
    //uintptr 是一个复合指针
    
    通过掩码获取
    class_rw_t
}

struct class_rw_t { //可读写 类的相关信息
    const class_ro_t * ro;
    
    // 一下内容可以为runtime进行扩展, 及动态加载类别中的内容
    method_array_t *methods;
    property_array_t properties;
    protocol_array_t protocols;
    
    ...其他内容
    
}

// 类编译时的内容 (无法修改)
struct class_ro_t {
    method_list_t methods;
    property_list_t properties;
    protocol_list_t protocols;

}

Runtime发送一个消息
1.从isa的共同体中获取Class即objc_class
2.从objc_class的 _cache中寻找是否存在本次消息Sel对应的imp实现
3.缓存未命中
4.从cobjc_class的 _bits通过固定掩码获取到 class_rw_t
5.在class_rw_t的methods,protocols,properties中查询Sel对应的Imp实现
6.若找到,执行Imp
7.未找到,通过object_class 的isa共同体,获取到Class或Meta-Class,重复1-6的部分,直到NSObject
8.若均为找到IMP，开启消息转发
9.消息转发一 动态消息转发，回调resolveMethods:或resolveInstanceMethods:进行动态添加MSG
10.执行第二步 备源接受者,回调forwardTargetSelector: 寻找其他的类是否可以实现MSG
11.执行第三部 消息重定向,把消息与接受者封装成NSInvocation执行消息
12.若未处理anInvocation，当前对象会调用 doesRecognizeSelector抛出异常

```
