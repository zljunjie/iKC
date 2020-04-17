## Clang&LLVM
### Clang
```
OC/C/C++的预编译工具,将代码编译成中间代码IR,也可以叫前端编译
前端编译过程
1 pre_process 进行宏展开
2 对代码进行词法与语法分析
3 对代码进行中间层编译
```
### Swift
```
Swift通过 swift-clang自己的前端编译器进行编译
```

### LLVM
```
LLVM又叫后端编译,将中间IR标准的代码编译成机器语言
编译过程
1 对代码进行系统级优化
2 针对不同的CPU架构输出不同的机器码
```


#### Clang调用
预编译
xcrun clang -E main.c 查看预编译之后的内容
词法分析 输出token
xcrun clang -fmodule -fsyntax-only -Xclang -dump-token main.c
语法分析 token流会被解析成一颗抽象语法树
xcrun clang -ast-dump- main.c
CodeGen 遍历语法树,生成中间的代码LLVM IR
xcrun clang -emit-llvm main.c -o main.ll
#### LLVM调用
1 LLVM对LR进行优化后,针对不同架构生成不同的目标代码
xcrun clang -S main.c -o main.o	生成arm 64汇编
2 汇编器 生成机器
xcrun nm -nm main.o

#### 链接
连接器







