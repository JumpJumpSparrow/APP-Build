<p align="center" >
<img width="512" height="550"  src="https://github.com/JumpJumpSparrow/APP-Build/blob/master/Architecture/1024px-MVC-Process.png">
<p>  
## MVC 的概念

1979年，Trygve Reenskaug 在 Smalltalk-76 系统上首次引入了 [MVC](https://en.wikipedia.org/wiki/Model–view–controller) 的概念，最初的时候叫做Model-View-Controller-Editor。 作为最出名并且应用最广泛的架构模式，MVC 并没有一个明确的定义，网上流传的 MVC 架构图也是形态各异，查阅了很多资料也没有办法确定到底什么样的架构图才是标准的 MVC 实现。  

![MVC](/Architecture/MVC-1979.jpg)  
设计 MVC 的重要目的就是在人的`心智模型`与`计算机模型`之间建立一个桥梁，而 MVC 能够解决这一问题并为用户提供`直接看到信息`和`操作信息`的功能。

### MVC 的代码分层
MVC 将整个应用分成 Model、View 和 Controller 三个部分：
- 视图：管理作为位图展示到屏幕上的图形和文字输出；
- 控制器：翻译用户的输入并依照用户的输入操作模型和视图；
- 模型：管理应用的行为和数据，响应数据请求（经常来自视图）和更新状态的指令（经常来自控制器）；

### MVC 对开发人员的影响  
MVC 本身的概念相当简单，同时它也给了开发者很大的自由度。因为这个自由度的存在，开发人员在开发过程中，需要花费更多精力去确定 MVC 的实际划分。不同的开发人员对 MVC的理解和实现往往有不小差异，导致 MVC 在实际应用中效果无法发挥出来。

### `开发人员` VS `MVC` 

完全理解和严格遵守 MVC 的思想，才能发挥 MVC 的优势，这对开发人员来说是一项挑战。如何让开发人员更容易的运用MVC思想呢？目前的答案是MVVM。 MVVM加入额外的规则和限制，提供了相对 MVC 来说更小的自由度。这可以在一定程度上规范开发者的行为，提供更加统一的代码 (当然代价是额外的学习成本)。

在 iOS 中，由于 `UIViewController` 类持有一个根视图 UIView，所以`视图层`与`控制器层`是紧密耦合在一起的，这也是 iOS 项目经常遇到视图控制器非常臃肿的重要原因之一。如何剥离控制器层的非必要部分，是优化控制器臃肿问题的主要方向。 

在 APP 开发中，实际上是 view 负责接收交互事件、呈现数据、处理部分逻辑，model 主要聚集数据、获取数据， controller 主要负责 连接 view 和model，即处理 view 传递过来的事件、更新model和view中的数据，做 view 和 model 连接的中间件。


