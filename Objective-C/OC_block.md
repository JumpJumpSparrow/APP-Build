# OC block 原理和机制

从 4 个问题深入了解 block：  

1. block的原理是怎样的？本质是什么？
2. `__block`的作用是什么？有什么使用注意点？
3. block的属性修饰词为什么是 copy ？使用 block 有哪些使用注意？
4. block在修改 NSMutableArray，需不需要添加 `__block`？

一句话概括就是：**了解的 block 的内存管理方式，就了解了 block 的关键**

## 1.block的原理是怎样的？本质是什么？

**block本质上也是一个oc对象，内部也有一个 isa 指针。block 是封装了函数调用以及函数调用环境的OC对象。**

使用命令行将代码转化为c++查看其内部结构，与OC代码进行比较
```language
xcrun -sdk iphoneos clang -arch arm64 -rewrite-objc main.m
```

## block的变量捕获

为了保证block内部能够正常访问外部的变量，block有一个变量捕获机制。

### 局部变量  

#### auto变量  
auto自动变量，离开作用域就销毁自动变量会捕获到block内部，也就是说block内部会专门新增加一个参数来存储变量的值。 auto只存在于局部变量中，访问方式为`值传递`。因此 block 内部无法改变 block 外的自动变量。

#### static变量
static 修饰的变量为指针传递，同样会被block捕获。

因为自动变量可能会销毁，block在执行的时候有可能自动变量已经被销毁了，那么此时再去访问被销毁的地址肯定会发生坏内存访问，**因此对于自动变量一定是值传递而不可能是指针传递了**。而静态变量不会被销毁，所以完全可以传递地址。因为传递的是值得地址，所以在block调用之前修改地址中保存的值，block中的地址是不会变的，所以值会随之改变。

#### 全局变量
`局部变量因为跨函数访问所以需要捕获，全局变量在哪里都可以访问 ，所以不用捕获。`
`局部变量都会被block捕获，自动变量是值捕获，静态变量为地址捕获。全局变量则不会被block捕获`

## block的类型

### block的3种类型
block有3中类型
```
__NSGlobalBlock__ （ _NSConcreteGlobalBlock ）
__NSStackBlock__ （ _NSConcreteStackBlock ）
__NSMallocBlock__ （ _NSConcreteMallocBlock ）
```

根据block的类型不同，block存放在不同的区域中。 数据段中的`__NSGlobalBlock__`直到程序结束才会被回收，不过我们很少使用到`__NSGlobalBlock__`类型的block，因为这样使用block并没有什么意义。

`__NSStackBlock__`类型的block存放在栈中，我们知道栈中的内存由系统自动分配和释放，作用域执行完毕之后就会被立即释放，而在相同的作用域中定义block并且调用block似乎也多此一举。

`__NSMallocBlock__`是在平时编码过程中最常使用到的。存放在堆中需要我们自己进行内存管理。

**没有访问auto变量的block是`__NSGlobalBlock__`类型的，存放在数据段中。 访问了auto变量的block是`__NSStackBlock__`类型的，存放在栈中。`__NSStackBlock__`类型的block调用copy成为`__NSMallocBlock__`类型并被复制存放在堆中。**

|block类型| 内存区域    |copy作用   |
| ------- | :--------- | ---------: |
|`__NSGlobalBlock__`| 数据段 |没有影响，不改变类型 |
|`__NSStackBlock__`|栈 |从栈覆值到堆，类型改变为`__NSMallocBlock__`|
|`__NSMallocBlock__`|堆|引用计数增加，不改变类型|



[参考链接](https://juejin.im/post/5b0d026bf265da090e3decb7)
