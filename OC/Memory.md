
### OC对象内存的占用

```

首先一个对象是一个结构体即空对象内也肯定存在一个isa的指针

64位中 isa占用8个字节也即是8*8 64位
int 占用4个字节

如果一个对象中存储了一个int类型
这个对象实际占用空间为 8+4 12个字节

但是系统分配对象的内存大小是按照16个字节的倍数来申请内存空间的,不足16字节的倍数会多分配

bool       1个字节
char       1个字节
short int  2个字节
int        4个字节
uint       4个字节
float      4个字节
double     8个字节
long/ulong 8个字节
longlong   8个字节
char*      8个字节 指针类型 根据cpu的寻址空间决定(32位就是32bit4字节, 64位就是8字节)


class teacher {
    int age
    bool sex
    char* name
}
teacher的对象实例占用多少内存空间
int 占用4个字节 bool占用1个字节 char* 占用8个字节, isa占用8个
4 + 1 + 8 + 8 = 21个字节
对象占用32个字节



```



