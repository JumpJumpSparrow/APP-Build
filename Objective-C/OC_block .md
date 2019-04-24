# OC block 原理和机制

从 4 个问题深入了解 block：  

1. block的原理是怎样的？本质是什么？
2. `__block`的作用是什么？有什么使用注意点？
3. block的属性修饰词为什么是 copy ？使用 block 有哪些使用注意？
4. block在修改 NSMutableArray，需不需要添加 `__block`？

## 1.block的原理是怎样的？本质是什么？

**block本质上也是一个oc对象，内部也有一个 isa 指针。block 是封装了函数调用以及函数调用环境的OC对象。**

使用命令行将代码转化为c++查看其内部结构，与OC代码进行比较
`xcrun -sdk iphoneos clang -arch arm64 -rewrite-objc main.m`

当在定义block之后修改局部变量的值，在block调用的时候无法生效。因为block在定义的之后已经将局部变量的值传入存储在`__main_block_imp_0`结构体中并在调用的时候将局部变量从block中取出来使用，因此在 block 定义之后对局部变量进行改变是无法被 block 捕获的。

[参考链接](https://juejin.im/post/5b0d026bf265da090e3decb7)