 VIPER架构，最初是2013年在MutualMobile的技术博客上，由Jeff Gilbert 和 Conrad Stoll 提出的。
 VIPER 是一个创建 iOS 应用简明构架， 简明架构将一个应用程序的逻辑结构划分为不同的责任层。这使得它更容易隔离依赖项 (如数据库)，也更容易测试各层间的边界处的交互。
VIPER 分别代表了：
 - 视图 (View)
 - 交互器 (Interactor)
 - 展示器 (Presenter)
 - 实体 (Entity) 
 - 路由 (Routing) 

代码拆分后各部分的分工职责：

### View
- 提供完整的视图，负责视图的组合、布局、更新
- 向Presenter提供更新视图的接口
- 将View相关的事件发送给Presenter

### Interactor
- 维护主要的业务逻辑功能，向Presenter提供现有的业务用例
- 维护、获取、更新Entity
- 当有业务相关的事件发生时，处理事件，并通知Presenter

### Presenter
- 接收并处理来自View的事件
- 向Interactor请求调用业务逻辑
- 向Interactor提供View中的数据
- 接收并处理来自Interactor的数据回调事件
- 通知View进行更新操作
- 通过Router跳转到其他View

### Entity
- 和Model一样的数据模型

### Router

- 提供View之间的跳转功能，减少了模块间的耦合
- 初始化VIPER的各个模块

### VIPER 架构图  
![VIPER 架构](/Architecture/viperArc1.png)  
其中 wireframe 就是 router的原型，更详细的结构图如下：

![VIPER 架构](/Architecture/viperArc2.png)


### VIPER的特色就是职责明确，粒度细，隔离关系明确，这样能带来很多优点：
- 可测试性好。UI测试和业务逻辑测试可以各自单独进行。
- 易于迭代。各部分遵循单一职责，可以很明确地知道新的代码应该放在哪里。
- 隔离程度高，耦合程度低。一个模块的代码不容易影响到另一个模块。
- 易于团队合作。各部分分工明确，团队合作时易于统一代码风格，可以快速接手别人的代码。

### 缺点
- 一个模块内的类数量增大，代码量增大，在层与层之间需要花更多时间设计接口。
- 模块的初始化较为复杂，打开一个新的界面需要生成View、Presenter、Interactor，并且设置互相之间的依赖关系。而iOS中缺少这种设置复杂初始化的原生方式。
### 解决
- 使用代码模板来自动生成文件和模板代码可以减少很多重复劳动，而花费时间设计和编写接口是减少耦合的路上不可避免的，你也可以使用数据绑定这样的技术来减少一些传递的层次。
- 可以选用一些第三方工具来实现，也可以在Router的界面跳转方法里，对模块进行初始化。

个人感觉 将 router 的概念引入，是VIPER 特色鲜明的地方，router 起了解耦的主要作用。



参考资料
- [VIPER](https://objccn.io/issue-13-5/)
- [iOS VIPER架构实践](https://zuikyo.github.io/2017/07/21/iOS%20VIPER架构实践(一)：从MVC到MVVM到VIPER/)
- [Router](https://github.com/joeldev/JLRoutes)
