###  什么是 MVVM 

MVVM 架构模式是微软在 2005 年诞生的，从诞生一开始就与 WPF 框架（Windows Presentation Foundation 是微软推出的基于Windows 的用户界面框架，属于.NET Framework 3.0的一部分）的联系非常紧密。 2005 年，John Gossman 在他的博客上公布了 [Introduction to Model/View/ViewModel pattern for building WPF apps](https://blogs.msdn.microsoft.com/johngossman/2005/10/08/introduction-to-modelviewviewmodel-pattern-for-building-wpf-apps/) 一文, 

从 Model-View-ViewModel 这个名字来看，它由三个部分组成，也就是 Model、View 和 ViewModel；其中视图模型（ViewModel）在 MVVM 中叫做视图模型。  
![MVVM](Model-View-ViewModel.jpg)  

在 MVVM 的实现中，还引入了隐式的一个 Binder 层，而声明式的数据和命令的绑定在 MVVM 模式中就是通过它完成的。
![binder](/Architecture/Binder-View-ViewModel.jpg)  
