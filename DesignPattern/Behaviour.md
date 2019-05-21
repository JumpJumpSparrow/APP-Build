行为型模式，共11种：策略模式、模板方法模式、观察者模式、迭代子模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模式、解释器模式。

### 这11中模式的关系：
第一类：通过父类与子类的关系进行实现。第二类：两个类之间。第三类：类的状态。第四类：通过中间类.

**13、策略模式（strategy）**

策略模式定义了一系列算法，并将每个算法封装起来，使他们可以相互替换，且算法的变化不会影响到使用算法的客户。需要设计一个接口，为一系列实现类提供统一的方法，多个实现类实现该接口，设计一个抽象类（可有可无，属于辅助类），提供辅助函数.

ICalculator提供同意的方法，
AbstractCalculator是辅助类，提供辅助方法，接下来，依次实现下每个类：

首先统一接口：

```java
public interface ICalculator {
	public int calculate(String exp);
}
```

辅助类：
```java
public abstract class AbstractCalculator {
	
	public int[] split(String exp,String opt){
		String array[] = exp.split(opt);
		int arrayInt[] = new int[2];
		arrayInt[0] = Integer.parseInt(array[0]);
		arrayInt[1] = Integer.parseInt(array[1]);
		return arrayInt;
	}
}
```

三个实现类：

```java
public class Plus extends AbstractCalculator implements ICalculator {
 
	@Override
	public int calculate(String exp) {
		int arrayInt[] = split(exp,"\\+");
		return arrayInt[0]+arrayInt[1];
	}
}
public class Minus extends AbstractCalculator implements ICalculator {
 
	@Override
	public int calculate(String exp) {
		int arrayInt[] = split(exp,"-");
		return arrayInt[0]-arrayInt[1];
	}
 
}
public class Multiply extends AbstractCalculator implements ICalculator {
 
	@Override
	public int calculate(String exp) {
		int arrayInt[] = split(exp,"\\*");
		return arrayInt[0]*arrayInt[1];
	}
}
```

简单的测试类：

```java
public class StrategyTest {
 
	public static void main(String[] args) {
		String exp = "2+8";
		ICalculator cal = new Plus();
		int result = cal.calculate(exp);
		System.out.println(result);
	}
}
```

输出：10

策略模式的决定权在用户，系统本身提供不同算法的实现，新增或者删除算法，对各种算法做封装。因此，策略模式多用在算法决策系统中，外部用户只需要决定用哪个算法即可。

**14、模板方法模式（Template Method）**
解释一下模板方法模式，就是指：一个抽象类中，有一个主方法，再定义1...n个方法，可以是抽象的，也可以是实际的方法，定义一个类，继承该抽象类，重写抽象方法，通过调用抽象类，实现对子类的调用.

就是在AbstractCalculator类中定义一个主方法calculate，calculate()调用spilt()等，Plus和Minus分别继承AbstractCalculator类，通过对AbstractCalculator的调用实现对子类的调用，看下面的例子：
```java
public abstract class AbstractCalculator {
	
	/*主方法，实现对本类其它方法的调用*/
	public final int calculate(String exp,String opt){
		int array[] = split(exp,opt);
		return calculate(array[0],array[1]);
	}
	
	/*被子类重写的方法*/
	abstract public int calculate(int num1,int num2);
	
	public int[] split(String exp,String opt){
		String array[] = exp.split(opt);
		int arrayInt[] = new int[2];
		arrayInt[0] = Integer.parseInt(array[0]);
		arrayInt[1] = Integer.parseInt(array[1]);
		return arrayInt;
	}
}
public class Plus extends AbstractCalculator {
 
	@Override
	public int calculate(int num1,int num2) {
		return num1 + num2;
	}
}
```

测试类：

```java
public class StrategyTest {
 
	public static void main(String[] args) {
		String exp = "8+8";
		AbstractCalculator cal = new Plus();
		int result = cal.calculate(exp, "\\+");
		System.out.println(result);
	}
}
```

我跟踪下这个小程序的执行过程：首先将exp和"\\+"做参数，调用AbstractCalculator类里的calculate(String,String)方法，在calculate(String,String)里调用同类的split()，之后再调用calculate(int ,int)方法，从这个方法进入到子类中，执行完return num1 + num2后，将值返回到AbstractCalculator类，赋给result，打印出来。正好验证了我们开头的思路。

**15、观察者模式（Observer）**

包括这个模式在内的接下来的四个模式，都是类和类之间的关系，不涉及到继承，学的时候应该 记得归纳。观察者模式很好理解，类似于邮件订阅和RSS订阅，当我们浏览一些博客或wiki时，经常会看到RSS图标，就这的意思是，当你订阅了该文章，如果后续有更新，会及时通知你。其实，简单来讲就一句话：当一个对象变化时，其它依赖该对象的对象都会收到通知，并且随着变化！对象之间是一种一对多的关系。

解释下这些类的作用：MySubject类就是我们的主对象，Observer1和Observer2是依赖于MySubject的对象，当MySubject变化时，Observer1和Observer2必然变化。AbstractSubject类中定义着需要监控的对象列表，可以对其进行修改：增加或删除被监控对象，且当MySubject变化时，负责通知在列表内存在的对象。我们看实现代码：

一个Observer接口：
```java
public interface Observer {
	public void update();
}
```

两个实现类：
```java
public class Observer1 implements Observer {
 
	@Override
	public void update() {
		System.out.println("observer1 has received!");
	}
}
public class Observer2 implements Observer {
 
	@Override
	public void update() {
		System.out.println("observer2 has received!");
	}
}
```

Subject接口及实现类：
```java
public interface Subject {
	
	/*增加观察者*/
	public void add(Observer observer);
	
	/*删除观察者*/
	public void del(Observer observer);
	
	/*通知所有的观察者*/
	public void notifyObservers();
	
	/*自身的操作*/
	public void operation();
}
public abstract class AbstractSubject implements Subject {
 
	private Vector<Observer> vector = new Vector<Observer>();
	@Override
	public void add(Observer observer) {
		vector.add(observer);
	}
 
	@Override
	public void del(Observer observer) {
		vector.remove(observer);
	}
 
	@Override
	public void notifyObservers() {
		Enumeration<Observer> enumo = vector.elements();
		while(enumo.hasMoreElements()){
			enumo.nextElement().update();
		}
	}
}
public class MySubject extends AbstractSubject {
 
	@Override
	public void operation() {
		System.out.println("update self!");
		notifyObservers();
	}
 
}
```
测试类：

```java
public class ObserverTest {
 
	public static void main(String[] args) {
		Subject sub = new MySubject();
		sub.add(new Observer1());
		sub.add(new Observer2());
		
		sub.operation();
	}
}
```


输出：

update self!
observer1 has received!
observer2 has received!

**16、迭代子模式（Iterator）**

顾名思义，迭代器模式就是顺序访问聚集中的对象，一般来说，集合中非常常见，如果对集合类比较熟悉的话，理解本模式会十分轻松。这句话包含两层意思：一是需要遍历的对象，即聚集对象，二是迭代器对象，用于对聚集对象进行遍历访问。
 
这个思路和我们常用的一模一样，MyCollection中定义了集合的一些操作，MyIterator中定义了一系列迭代操作，且持有Collection实例，我们来看看实现代码：

两个接口：

```java
public interface Collection {
	
	public Iterator iterator();
	
	/*取得集合元素*/
	public Object get(int i);
	
	/*取得集合大小*/
	public int size();
}
public interface Iterator {
	//前移
	public Object previous();
	
	//后移
	public Object next();
	public boolean hasNext();
	
	//取得第一个元素
	public Object first();
}
```

两个实现：

```java
public class MyCollection implements Collection {
 
	public String string[] = {"A","B","C","D","E"};
	@Override
	public Iterator iterator() {
		return new MyIterator(this);
	}
 
	@Override
	public Object get(int i) {
		return string[i];
	}
 
	@Override
	public int size() {
		return string.length;
	}
}
public class MyIterator implements Iterator {
 
	private Collection collection;
	private int pos = -1;
	
	public MyIterator(Collection collection){
		this.collection = collection;
	}
	
	@Override
	public Object previous() {
		if(pos > 0){
			pos--;
		}
		return collection.get(pos);
	}
 
	@Override
	public Object next() {
		if(pos<collection.size()-1){
			pos++;
		}
		return collection.get(pos);
	}
 
	@Override
	public boolean hasNext() {
		if(pos<collection.size()-1){
			return true;
		}else{
			return false;
		}
	}
 
	@Override
	public Object first() {
		pos = 0;
		return collection.get(pos);
	}
 
}
```

测试类：

```java
public class Test {
 
	public static void main(String[] args) {
		Collection collection = new MyCollection();
		Iterator it = collection.iterator();
		
		while(it.hasNext()){
			System.out.println(it.next());
		}
	}
}
```

输出：A B C D E

此处我们貌似模拟了一个集合类的过程，感觉是不是很爽？其实JDK中各个类也都是这些基本的东西，加一些设计模式，再加一些优化放到一起的，只要我们把这些东西学会了，掌握好了，我们也可以写出自己的集合类，甚至框架！

**17、责任链模式（Chain of Responsibility）**

接下来我们将要谈谈责任链模式，有多个对象，每个对象持有对下一个对象的引用，这样就会形成一条链，请求在这条链上传递，直到某一对象决定处理该请求。但是发出者并不清楚到底最终那个对象会处理该请求，所以，责任链模式可以实现，在隐瞒客户端的情况下，对系统进行动态的调整。

Abstracthandler类提供了get和set方法，方便MyHandle类设置和修改引用对象，MyHandle类是核心，实例化后生成一系列相互持有的对象，构成一条链。

```java
public interface Handler {
	public void operator();
}
public abstract class AbstractHandler {
	
	private Handler handler;
 
	public Handler getHandler() {
		return handler;
	}
 
	public void setHandler(Handler handler) {
		this.handler = handler;
	}
	
}
public class MyHandler extends AbstractHandler implements Handler {
 
	private String name;
 
	public MyHandler(String name) {
		this.name = name;
	}
 
	@Override
	public void operator() {
		System.out.println(name+"deal!");
		if(getHandler()!=null){
			getHandler().operator();
		}
	}
}
public class Test {
 
	public static void main(String[] args) {
		MyHandler h1 = new MyHandler("h1");
		MyHandler h2 = new MyHandler("h2");
		MyHandler h3 = new MyHandler("h3");
 
		h1.setHandler(h2);
		h2.setHandler(h3);
 
		h1.operator();
	}
}
```

输出：

h1deal!
h2deal!
h3deal!

此处强调一点就是，链接上的请求可以是一条链，可以是一个树，还可以是一个环，模式本身不约束这个，需要我们自己去实现，同时，在一个时刻，命令只允许由一个对象传给另一个对象，而不允许传给多个对象。

**18、命令模式（Command）**

命令模式很好理解，举个例子，司令员下令让士兵去干件事情，从整个事情的角度来考虑，司令员的作用是，发出口令，口令经过传递，传到了士兵耳朵里，士兵去执行。这个过程好在，三者相互解耦，任何一方都不用去依赖其他人，只需要做好自己的事儿就行，司令员要的是结果，不会去关注到底士兵是怎么实现的。

Invoker是调用者（司令员），Receiver是被调用者（士兵），MyCommand是命令，实现了Command接口，持有接收对象，看实现代码：

```java
public interface Command {
	public void exe();
}
public class MyCommand implements Command {
 
	private Receiver receiver;
	
	public MyCommand(Receiver receiver) {
		this.receiver = receiver;
	}
 
	@Override
	public void exe() {
		receiver.action();
	}
}
public class Receiver {
	public void action(){
		System.out.println("command received!");
	}
}
public class Invoker {
	
	private Command command;
	
	public Invoker(Command command) {
		this.command = command;
	}
 
	public void action(){
		command.exe();
	}
}
```

```java
public class Test {
 
	public static void main(String[] args) {
		Receiver receiver = new Receiver();
		Command cmd = new MyCommand(receiver);
		Invoker invoker = new Invoker(cmd);
		invoker.action();
	}
}
```

输出：command received!

这个很好理解，命令模式的目的就是达到命令的发出者和执行者之间解耦，实现请求和执行分开，熟悉Struts的同学应该知道，Struts其实就是一种将请求和呈现分离的技术，其中必然涉及命令模式的思想！

**19、备忘录模式（Memento）**
主要目的是保存一个对象的某个状态，以便在适当的时候恢复对象，个人觉得叫备份模式更形象些，通俗的讲下：假设有原始类A ，A中有各种属性，A可以决定需要备份的属性，备忘录类B是用来存储A的一些内部状态，类C呢，就是一个用来存储备忘录的，且只能存储，不能修改等操作。

Original类是原始类，里面有需要保存的属性value及创建一个备忘录类，用来保存value值。Memento类是备忘录类，Storage类是存储备忘录的类，持有Memento类的实例，该模式很好理解。直接看

```java
public class Original {
	
	private String value;
	
	public String getValue() {
		return value;
	}
 
	public void setValue(String value) {
		this.value = value;
	}
 
	public Original(String value) {
		this.value = value;
	}
 
	public Memento createMemento(){
		return new Memento(value);
	}
	
	public void restoreMemento(Memento memento){
		this.value = memento.getValue();
	}
}
public class Memento {
	
	private String value;
 
	public Memento(String value) {
		this.value = value;
	}
 
	public String getValue() {
		return value;
	}
 
	public void setValue(String value) {
		this.value = value;
	}
}
public class Storage {
	
	private Memento memento;
	
	public Storage(Memento memento) {
		this.memento = memento;
	}
 
	public Memento getMemento() {
		return memento;
	}
 
	public void setMemento(Memento memento) {
		this.memento = memento;
	}
}
```

测试类：

```java
public class Test {
 
	public static void main(String[] args) {
		
		// 创建原始类
		Original origi = new Original("egg");
 
		// 创建备忘录
		Storage storage = new Storage(origi.createMemento());
 
		// 修改原始类的状态
		System.out.println("初始化状态为：" + origi.getValue());
		origi.setValue("niu");
		System.out.println("修改后的状态为：" + origi.getValue());
 
		// 回复原始类的状态
		origi.restoreMemento(storage.getMemento());
		System.out.println("恢复后的状态为：" + origi.getValue());
	}
}
```

输出：

初始化状态为：egg
修改后的状态为：niu
恢复后的状态为：egg

简单描述下：新建原始类时，value被初始化为egg，后经过修改，将value的值置为niu，最后倒数第二行进行恢复状态，结果成功恢复了。其实我觉得这个模式叫“备份-恢复”模式最形象。

**20、状态模式（State）**
核心思想就是：当对象的状态改变时，同时改变其行为，很好理解！就拿QQ来说，有几种状态，在线、隐身、忙碌等，每个状态对应不同的操作，而且你的好友也能看到你的状态，所以，状态模式就两点：1、可以通过改变状态来获得不同的行为。2、你的好友能同时看到你的变化。

State类是个状态类，Context类可以实现切换，我们来看看代码：

```java
package com.xtfggef.dp.state;
 
/**
 * 状态类的核心类
 * 2012-12-1
 * @author erqing
 *
 */
public class State {
	
	private String value;
	
	public String getValue() {
		return value;
	}
 
	public void setValue(String value) {
		this.value = value;
	}
 
	public void method1(){
		System.out.println("execute the first opt!");
	}
	
	public void method2(){
		System.out.println("execute the second opt!");
	}
}
package com.xtfggef.dp.state;
 
/**
 * 状态模式的切换类   2012-12-1
 * @author erqing
 * 
 */
public class Context {
 
	private State state;
 
	public Context(State state) {
		this.state = state;
	}
 
	public State getState() {
		return state;
	}
 
	public void setState(State state) {
		this.state = state;
	}
 
	public void method() {
		if (state.getValue().equals("state1")) {
			state.method1();
		} else if (state.getValue().equals("state2")) {
			state.method2();
		}
	}
}
```

测试类：
```java
public class Test {
 
	public static void main(String[] args) {
		
		State state = new State();
		Context context = new Context(state);
		
		//设置第一种状态
		state.setValue("state1");
		context.method();
		
		//设置第二种状态
		state.setValue("state2");
		context.method();
	}
}
```

输出：
execute the first opt!
execute the second opt!

根据这个特性，状态模式在日常开发中用的挺多的，尤其是做网站的时候，我们有时希望根据对象的某一属性，区别开他们的一些功能，比如说简单的权限控制等。

**21、访问者模式（Visitor）**

访问者模式把数据结构和作用于结构上的操作解耦合，使得操作集合可相对自由地演化。访问者模式适用于数据结构相对稳定算法又易变化的系统。因为访问者模式使得算法操作增加变得容易。若系统数据结构对象易于变化，经常有新的数据对象增加进来，则不适合使用访问者模式。访问者模式的优点是增加操作很容易，因为增加操作意味着增加新的访问者。访问者模式将有关行为集中到一个访问者对象中，其改变不影响系统数据结构。其缺点就是增加新的数据结构很困难。—— From 百科

简单来说，访问者模式就是一种分离对象数据结构与行为的方法，通过这种分离，可达到为一个被访问者动态添加新的操作而无需做其它的修改的效果。
来看看原码：一个Visitor类，存放要访问的对象，

```java
public interface Visitor {
	public void visit(Subject sub);
}
public class MyVisitor implements Visitor {
 
	@Override
	public void visit(Subject sub) {
		System.out.println("visit the subject："+sub.getSubject());
	}
}
Subject类，accept方法，接受将要访问它的对象，getSubject()获取将要被访问的属性，
public interface Subject {
	public void accept(Visitor visitor);
	public String getSubject();
}
public class MySubject implements Subject {
 
	@Override
	public void accept(Visitor visitor) {
		visitor.visit(this);
	}
 
	@Override
	public String getSubject() {
		return "love";
	}
}
```

测试：
```java
public class Test {
 
	public static void main(String[] args) {
		
		Visitor visitor = new MyVisitor();
		Subject sub = new MySubject();
		sub.accept(visitor);	
	}
}
```

输出：visit the subject：love
该模式适用场景：如果我们想为一个现有的类增加新功能，不得不考虑几个事情：1、新功能会不会与现有功能出现兼容性问题？2、以后会不会再需要添加？3、如果类不允许修改代码怎么办？面对这些问题，最好的解决方法就是使用访问者模式，访问者模式适用于数据结构相对稳定的系统，把数据结构和算法解耦，

**22、中介者模式（Mediator）**
中介者模式也是用来降低类类之间的耦合的，因为如果类类之间有依赖关系的话，不利于功能的拓展和维护，因为只要修改一个对象，其它关联的对象都得进行修改。如果使用中介者模式，只需关心和Mediator类的关系，具体类类之间的关系及调度交给Mediator就行，这有点像spring容器的作用。

User类统一接口，User1和User2分别是不同的对象，二者之间有关联，如果不采用中介者模式，则需要二者相互持有引用，这样二者的耦合度很高，为了解耦，引入了Mediator类，提供统一接口，MyMediator为其实现类，里面持有User1和User2的实例，用来实现对User1和User2的控制。这样User1和User2两个对象相互独立，他们只需要保持好和Mediator之间的关系就行，剩下的全由MyMediator类来维护！基本实现：

```java
public interface Mediator {
	public void createMediator();
	public void workAll();
}
public class MyMediator implements Mediator {
 
	private User user1;
	private User user2;
	
	public User getUser1() {
		return user1;
	}
 
	public User getUser2() {
		return user2;
	}
 
	@Override
	public void createMediator() {
		user1 = new User1(this);
		user2 = new User2(this);
	}
 
	@Override
	public void workAll() {
		user1.work();
		user2.work();
	}
}
public abstract class User {
	
	private Mediator mediator;
	
	public Mediator getMediator(){
		return mediator;
	}
	
	public User(Mediator mediator) {
		this.mediator = mediator;
	}
 
	public abstract void work();
}
public class User1 extends User {
 
	public User1(Mediator mediator){
		super(mediator);
	}
	
	@Override
	public void work() {
		System.out.println("user1 exe!");
	}
}
public class User2 extends User {
 
	public User2(Mediator mediator){
		super(mediator);
	}
	
	@Override
	public void work() {
		System.out.println("user2 exe!");
	}
}
```

测试类：
```java
public class Test {
 
	public static void main(String[] args) {
		Mediator mediator = new MyMediator();
		mediator.createMediator();
		mediator.workAll();
	}
}
```

输出：
user1 exe!
user2 exe!
23、解释器模式（Interpreter）
解释器模式是我们暂时的最后一讲，一般主要应用在OOP开发中的编译器的开发中，所以适用面比较窄。
Context类是一个上下文环境类，Plus和Minus分别是用来计算的实现，代码如下：

```java
public interface Expression {
	public int interpret(Context context);
}
public class Plus implements Expression {
 
	@Override
	public int interpret(Context context) {
		return context.getNum1()+context.getNum2();
	}
}
public class Minus implements Expression {
 
	@Override
	public int interpret(Context context) {
		return context.getNum1()-context.getNum2();
	}
}
public class Context {
	
	private int num1;
	private int num2;
	
	public Context(int num1, int num2) {
		this.num1 = num1;
		this.num2 = num2;
	}
	
	public int getNum1() {
		return num1;
	}
	public void setNum1(int num1) {
		this.num1 = num1;
	}
	public int getNum2() {
		return num2;
	}
	public void setNum2(int num2) {
		this.num2 = num2;
	}
	
	
}
public class Test {
 
	public static void main(String[] args) {
 
		// 计算9+2-8的值
		int result = new Minus().interpret((new Context(new Plus()
				.interpret(new Context(9, 2)), 8)));
		System.out.println(result);
	}
}
```

最后输出正确的结果：3。
基本就这样，解释器模式用来做各种各样的解释器，如正则表达式等的解释器等等！

原文：https://blog.csdn.net/zhangerqing/article/details/8245537 


