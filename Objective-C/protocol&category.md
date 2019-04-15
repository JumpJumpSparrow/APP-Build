## OC Protocol、分类中声明属性的处理方式

OC 中`协议`和`分类`是可以用@property形式声明属性的，只不过在`协议`、`分类`中声明的属性，只有对应的setter／getter方法，并没有生成对应的成员变量。因为**协议中只可以声明方法，分类中只能声明方法和对应的实现**。
```Objective-C
@protocol MyProtocol

@property (nonatomic, strong)NSString *protocolName;
@end

@interface ViewController :UIViewController
@end
```

如果一个类遵守这个协议的话，在该类中即可调用出self.protocolName,但是如果直接调用 `self.protocolName = @" protocol 声明成员变量";`
```Objective-C
self.protocolName = @"protocol 声明成员变量";
NSLog(@"%@",self.protocolName);
```
进程会直接出现崩溃
```Objective-C
Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: '-[ViewController setProtocolName:]: unrecognized selector sent to instance 0x7f9fb350aa20'
```

##### 解决方法1：

由于在协议当中的属性只有只有对应的`setter／getter`，但是没有成员变量，如果直接直接将其get方法写出如下：
```Objective-C
- (NSString*)protocolName{
    return @"protocol 声明成员变量";
}
```
这样调用的话，就能够直接调用

 NSLog(@"%@",self.protocolName);

输出结果为：

`2018-04-10 15:30:36.373180+0800 ProtocolTest[36622:228189] protocol 声明成员变量`

##### 解决方法2：

```Objective-C
@interface ViewController ()
@end
@implementation ViewController

@synthesize protocolName =_protocolName;
```
上面方法中主要用到了`@synthesize`上面声明部分的 `@synthesize protocolName =_protocolName`; 意思是说，protocolName 属性为`_protocolName `成员变量合成访问器方法。 也就是说，protocolName 属性生成存取方法是 setprotocolName，这个setprotocolName方法就是`_protocolName`变量的存取方法，它操作的就是  `_protocolName`这个变量。通过这个看似是赋值的这样一个操作，可以在 @synthesize 中定义与变量名不相同的getter和setter的命名，籍此来保护变量不会被不恰当的访问
```Objective-C
self.protocolName = @"protocol 声明成员变量";
NSLog(@"%@",self.protocolName);
```
输出结果：

`2018-04-10 15:30:36.373180+0800 链式编程[36622:228189] protocol 声明成员变量`
