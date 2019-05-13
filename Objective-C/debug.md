## LLDB调试器

### 1. Command 命令
  - help 查看命令文档，例如 help print
  - print 打印变量值
  - expression 改变调试器中的值，实际上它改变了程序中的值。这时候继续执行程序，会以改变后的值继续执行
  - po  (**p**rint **o**bject 的缩写)
  - p/x 打印十六进制，p/t 打印二进制, [更多格式](https://sourceware.org/gdb/onlinedocs/gdb/Output-Formats.html)  
  
### 2. 变量
  
  以 **$**开头声明变量
```language
(lldb) e int $a = 2
(lldb) p $a * 19
38
(lldb) e NSArray *$array = @[ @"Saturday", @"Sunday", @"Monday" ]
(lldb) p [$array count]
2
```
### 不用断点调试

程序运行时，Xcode 的调试条上会出现暂停按钮，而不是继续按钮：

![](https://objccn.io/images/issues/issue-19/Screen_Shot_2014_11_22_at_1_50_56_PM.png)

点击按钮会暂停 app (这会运行 `process interrupt` 命令，因为 LLDB 总是在背后运行)。这会让你可以访问调试器，但看起来可以做的事情不多，因为在当前作用域没有变量，也没有特定的代码让你看。

这就是有意思的地方。如果你正在运行 iOS app，你可以试试这个： (因为全局变量是可访问的)

```
    (lldb) po [[[UIApplication sharedApplication] keyWindow] recursiveDescription]
<UIWindow: 0x7f82b1fa8140; frame = (0 0; 320 568); gestureRecognizers = <NSArray: 0x7f82b1fa92d0>; layer = <UIWindowLayer: 0x7f82b1fa8400>>
   | <UIView: 0x7f82b1d01fd0; frame = (0 0; 320 568); autoresize = W+H; layer = <CALayer: 0x7f82b1e2e0a0>>

```

你可以看到整个层次。[Chisel](https://github.com/facebook/chisel) 中 `pviews` 就是这么实现的。

### 更新UI

有了上面的输出，我们可以获取这个 view：

```
(lldb) e id $myView = (id)0x7f82b1d01fd0
```
然后在调试器中改变它的背景色：
```
(lldb) e (void)[$myView setBackgroundColor:[UIColor blueColor]]
```

但是只有程序继续运行之后才会看到界面的变化。因为改变的内容必须被发送到渲染服务中，然后显示才会被更新。

渲染服务实际上是一个另外的进程 (被称作 `backboardd`)。这就是说即使我们正在调试的内容所在的进程被打断了，`backboardd` 也还是继续运行着的。

这意味着你可以运行下面的命令，而不用继续运行程序：

```
(lldb) e (void)[CATransaction flush]
```

即使你仍然在调试器中，UI 也会在模拟器或者真机上实时更新。[Chisel](https://github.com/facebook/chisel) 为此提供了一个别名叫做 `caflush`，这个命令被用来实现其他的快捷命令，例如 `hide <view>`，`show <view>` 以及其他很多命令。所有 [Chisel](https://github.com/facebook/chisel)的命令都有文档，所以安装后随意运行 `help show` 来看更多信息。

### Push 一个 View Controller

想象一个以 `UINavigationController` 为 root ViewController 的应用。你可以通过下面的命令，轻松地获取它：

```
(lldb) e id $nvc = [[[UIApplication sharedApplication] keyWindow] rootViewController]
```

然后 push 一个 child view controller:

```
(lldb) e id $vc = [UIViewController new]
(lldb) e (void)[[$vc view] setBackgroundColor:[UIColor yellowColor]]
(lldb) e (void)[$vc setTitle:@"Yay!"]
(lldb) e (void)[$nvc pushViewContoller:$vc animated:YES]
```
最后运行下面的命令：
```
(lldb) caflush // e (void)[CATransaction flush]
```
navigation Controller 就会立刻就被 push 到你眼前。

参考资料：
[objccn](https://objccn.io/issue-19-2/)  
[LLDB](https://lldb.llvm.org/use/map.html)  




