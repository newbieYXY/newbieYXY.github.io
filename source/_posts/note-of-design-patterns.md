---
title: 《Head First设计模式》读书笔记
date: 2019-07-11 21:29:35
tags: 设计模式
description: 经典设计模式的要点记录
---

## 简介

该书通过具体情境，结合想要达到的效果，给出编码方案，整理出解决问题的模式。本篇笔记主要记录书中着重介绍的集中设计模式的要点，涉及到跟Java内置相关的会省略，更注重的是其中的编码思想。



###  · 策略模式

Has-A方式；封装可互换的行为，然后使用委托来决定要采用哪一个行为



### · 状态模式

通过改变对象内部的状态来帮助对象控制自己的行为；状态机，将状态对象封装在各自的类中（i.e.，将每个状态直接映射到一个类，类中的方法映射所有可能发生的动作），然后在动作发生时委托给当前状态（i.e.，将动作委托给代表当前状态的对象）。-- （这样子不就是需要所有的类中都包含了所有的动作，即使在该状态下是不会有该动作的发生/该动作并不恰当？另外，状态接口通过构造器得到类的引用，在某些情况下需要调用类的方法去改变当前状态为其他状态，这个会存在“低层组件调用高层组件/低层和高层相互依赖存在的问题”吗？

context（拥有内部状态）和state/concreteState（具体状态）：当状态转换是固定的，可以放在context中；当转换是动态的，通常放在状态类中，虽然这样会产生状态类之间的依赖。



ps：状态模式和策略模式的区别

（1）在状态模式中，当前状态会在状态对象集合中游走改变，以反映出context内部的状态；context的客户对于状态对象了解不多，不需要在context中放置许多判断条件，改变context内的状态对象就可以改变context的行为（任何的状态改变都是定义好的）。



（2）策略模式是客户通常主动指定context所要组合的策略对象是哪一个（控制context使用什么策略），是除了继承之外的一种弹性替代方案，可以通过组合不同的对象来改变行为。



### · 观察者模式

出版者 （主题） + 订阅者 （观察者），定义了对象之间的一对多依赖，主题和观察者对象之间是松耦合的。



### · 装饰者模式

decorate（装饰）+ delegate（委托），装饰者可以再所委托被装饰者的行为之前与/或之后，加上自己的行为，以达到特定的目的。

动态地将责任附加到对象上（如饮料是超类，各种装饰如摩卡、奶泡等继承了饮料这个类，并且每种装饰中都有各自的cost()，在获得总的cost时，就会从最外圈到最内圈调用/委托各装饰者的cost）

装饰者和被装饰者是一样的类型（有共同的超类），用继承达到“类型匹配”，而不是用继承获得”行为“（所以还是遵守多用组合、少用继承的原则）；将装饰者与逐渐（被装饰者）组合时，即加入新的行为。

但是如果代码依赖具体的类型/针对具体组件编程，那么导入装饰者就会造成问题。



### · 工厂模式

问题1：使用new时，是在实例化一个具体类（创建对象）；当代码发生改变时，因为现在用的是“实现”而不是“接口”，就需要重新对该段代码进行检查和修改。Recap设计原则：“将变化的方面从不变的部分分离出来”。

解决方案：

（1）简单工厂：封装创建对象的代码，处理创建对象的细节，即为“工厂”。看起来没有什么区别，但是可能会有很多类（客户，如点单、查询价格）中需要创建对象（需要获得对象实例），如果创建的方式发生改变，那么所有涉及到创建对象的类中的代码都需要进行修改；将创建对象封装起来，多个用户只需要使用这个封装好的类，创建发生改变时也只需要找到这个封装类来修改。

（2）工厂方法：另外，还可以通过一个抽象子类（也是一个“工厂”）来负责实例化，那么在扩展整个大类（如pizza店/store）时，不同的扩展子类（不一样的加盟店）能够自己实现不一样的实例化（不同类型pizza的配料） -- 定义了一个创建对象的接口，让子类决定该创建的对象是什么，将产品的“实现”从“使用”中解耦。

区别：简单工厂不能变更正在创建产品，因为创建产品的工厂是在store之外定义的，不同的store扩展并不能对于内部引用的factory做出改变，只能是factory内部进行改变。



问题2：高层组件（store）中实际仍然创建了具体的低层对象（pizza），需要将实例化对象的代码独立出来，构造抽象类/接口。

解决方案：

转换从顶层开始思考的模式，从底层开始，将能抽象的都抽象成一个接口。

抽象工厂模式：提供一个接口，用于创建相关或依赖对象的家族，而不需要明确指定具体类



### · 单例模式

确保只有一个实例会被创建（有全局变量的方便，也避免了全局变量的一些缺点：全局变量不能确保只要一个实例，也可能会造成许多全局变量指向许多小对象、造成名命名空间污染的情况），提供一个全局访问点，可以在需要时才创建对象。

类的构造器为私有的，并且还有一个私有的静态变量，被赋值实例；如果不需要该实例，那么它永远不会产生（即实例化对象的方法不会被调用），这就是“延迟实例化”。确保实例化一次的原理在于：类外取得实例的方式，并不是直接实例化，而是通过“请求”得到实例，该“请求”内部会判断对象是否已经被实例化。 -- 私有构造器+静态方法+静态变量

多线程中使用单例：需要设置线程进入和离开“请求”方法的条件，即不可以有两个线程同时进入该方法，否则会造成混乱。 -- 1）设置同步，但只有在第一次调用的时候有用，在之后就不需要同步了，有点累赘；2）直接在静态初始化器中创建单例，即直接给私有静态变量赋值对象实例；3）双重检查加锁*

问题：多个类加载器与单例模式的结合，可能会产生混乱，因为使用多个类加载器，有可能会加载同一个类多次



### · 命令模式 -- 封装调用（把方法调用封装起来）

客户，调用者对象，命令对象，接收者：客户创建调用者和**命令**（其中接收者会传给命令）；然后将**命令**传给调用者（这里及前面的“传给”指的是以参数形式，命令即为对象）。

将“请求”封装成对象，以便使用不同的请求、队列或者是日志来参数化其他对象（i.e.，通过在特定接收者上绑定一组动作来封装一个请求；从外面看来，其他对象不知道究竟哪个接收者进行了哪些动作，只知道调用execute()方法，就能达到请求目的；这个execute相当于给了统一的接口，至于每种命令中的动作具体是怎样的，就要看内部的实现），并支持撤销操作（用局部变量记录状态，或者使用栈记录历史）。

调用者和接收者之间的解耦；空对象的使用（处理null）；使用宏命令，用数组存储一大堆命令，可以一次性执行数组里的每个命令。

队列请求：在一端添加命令，在另一端是线程进行取命令、调用exectute方法、调用完成后即丢弃对象并继续取下一个命令等动作。

日志请求：将历史记录储存在磁盘中，一旦系统死机，可以重新加载命令对象并以此调用execute。



### · 适配器模式与外观模式

#### （一）适配器模式

区别于装饰者模式，包装某些对象，是为了让它们的接口看起来不像是自己而像是别的东西，这样就可以**将类的接口转换成想要的接口**，以便实现不同的接口。（相当于在现有接口和期望接口之间增加一个过渡）

适配器实现了目标接口，并持有被适配者的实例（需要将被适配者的接口转换为期望/客户知道的接口），客户和被适配者是解耦的（因为中间隔着适配器）

（1）对象适配器：利用**组合**的方式将请求传送给被适配者 -- **委托**调用方法；可以适配某个类，也可以适配该类的任何子类

（2）类适配器：**继承**了被适配者和目标类 -- 扩展类**转接**调用方法（？？？）；不需要重新实现整个被适配者。



#### （二）外观模式

**让接口更简单**（将一个或多个类的复杂接口包装起来）；但并没有“封装”子系统的类，依然将系统完整的功能暴露出来，以供需要的人使用。它将客户实现从子系统中解耦。



### · 模板方法模式 -- 封装算法

在超类中，对相同的方法直接定义（由超类处理），对不同的方法先声明抽象（由子类处理）；在扩展子类的时候再给抽象的方法以具体的定义。这能使得抽象类专注于算法（流程）本身，而由子类提供完整的实现。

在一个方法中**定义一个算法的骨架**，而将一些步骤延迟到子类中（相当于用了继承来封装算法），使得子类可以在**不改变算法结构**的的情况下，重新定义算法中的某些步骤的具体实现。

对模板方法进行挂钩：可以有“默认不做事”的方法，子类选择情况来进行覆盖。ps：必须执行的步骤就是用抽象方法，可选部分就用“钩子”。

与策略模式的区别：模板方法定义了算法的大纲，虽然允许其中某些步骤的内容在子类中定义，但算法结构维持不变，对算法有更多的控制权（由超类主控一切）。但必须依赖超类中的方法的实现；而策略模式使用对象组合，使得客户可以按照需求改变算法/选择算法的实现。



### · 迭代器与组合模式

#### （一）迭代器模式

情景：

集合内的对象可能会有不一样的实现/存储方式（如列表、数组、散列表等），遍历对象需要不同的循环实现，这样就是针对对象的具体实现编码，而不是针对接口编码；因此创建“迭代器”，来封装“遍历集合内的每个对象的过程”。

迭代器模式依赖的是一个迭代器的接口（该接口内的方法包括如next(),hasNext()等），可以为各种对象集合实现迭代器，顺序访问一个聚合对象中的各个元素，而又不暴露器内部表示（存储方式）。

还需要给不同对象提供一个相同的接口（否则编码面对的还是具体类），这个接口是由对象实现创建跌代器。

外部迭代器和内部迭代器：外部迭代器是指客户通过调用next()取得下一个元素；内部的迭代器是由迭代器自己控制，需要告诉迭代器在元素之间游走的过程中要做的事情，即将操作传给迭代器。

使用迭代器作为方法的参数时，是在使用多态的迭代，因为相当于该方法可以在不同的集合内遍历元素。

#### （二）组合模式

情景：在某一类菜单中添加子菜单

集合内对象的内部结构已经比较复杂了，仅依靠迭代器不能解决问题。组合模式允许将对象组合成**树形结构**来表现“整体/部分”层次结构，能把相同的操作应用在组合和个别对象上。组合以树形结构表示，包含了组件，而组件有组合跟叶子节点元素两种。

创建组件接口，作为菜单（组合/节点）和菜单项（叶子节点）的共同接口，在菜单和菜单项中实现这些接口（此时就会涉及到有一些方法可能在一些节点中并不适用，就需要空迭代器）。这样对客户而言，组合和叶子节点都**一视同仁**，组合模式以单一责任（需要管理层次结构+执行方法）换区透明性。



### · 代理模式 -- 控制对象访问

情景：为糖果机输出一份库存及状态报告

直接创建一个监视器的类，并在构造器汇总传入糖果机，这样的监视器和糖果机是在同一个JVM上执行；如果需要**远程控制**，就需要代理 -- 代表某个真实的对象，幕后是利用网络和一个远程的真正对象沟通（远程对象：在不同的地址空间运行的对象）。

*Recap：Java RMI*

提供了客户辅助对象（stub）和服务辅助对象（skeleton）；在客户对象由客户辅助对象接收客户堆的请求，在服务端由服务辅助对象接收可会对的请求，并将请求解包、调用服务对象的方法；客户辅助对象和服务辅助对象之间是Socket连接；返回值的返回经过相同的路径（在客户辅助对象处解包）。

将对象编程可以接受远程调用的服务：制作远程接口，实现远程接口；其中客户对象通过RMI registry获得代理的位置，而真正的服务也在registry中注册、可被调用。

上面提到的是远程代理，还有其他的代理模式：

（1）虚拟代理：作为**创建开销大的对象**的代表，经常直到我们真正需要一个对象的时候才创建。

情景：创建Icon接口从网络上下载图像，加载图像时显示一些其他东西如“等待加载”（imageproxy管理下载过程中的背景显示；图像加载完成后将方法调用委托给imageicon）

（2）动态代理和保护代理：根据权限决定客户可否访问对象（没看懂orz...）



共同点：将客户对主题施加的方法调用拦截下来。与装饰者（增加对象的行为）的区别：重在对对象的访问；与适配器（改变对象适配的接口）的区别：实现相同的接口。



### · 复合模式 

（考验记忆力和理解力的时候到了......）

MVC：结合了观察者模式、策略模式和组合模式



### · 其他模式

（1）桥接模式

情景：让所有遥控器都基于相同的抽象（即所有遥控器调用的是相同的接口），而该抽象有许多**不同的实现**（即不同型号的电视机有不同的遥控器调用方法的实现）；另外，即使用户界面已经抽象出来，之后也可能需要**改变抽象**来满足用户新的要求。

把抽象和实现放在不同的类层次结构，这两个类层次之间是Has-A关系，即为“桥接”（原来的实现已不再是抽象的实现了；抽象的类由它的子类实现，实现的类也由它的子类实现）。这使得抽象和实现可以独立扩展，但同时也增加了复杂度。

（2）生成器模式

情景：为游客制定度假计划，包括每一天的行程，其中一些行程还包括了具体的活动选择（类似于树形结构），而且计划对于不同的游客会有不一样的设计（即需要弹性）。

将计划的创建过程封装到一个对象中（生成器），客户使用抽象的接口建立计划，由具体生成器创建真正的产品，并存储在组合结构中（生成器模式经常被用来创建组合结构，两者之间的逻辑关系是怎样的？？？）

（3）责任链

情景：需要分类处理多种请求

请求的发送者调用抽象类的处理请求的接口，而抽象类具有多个具体对象担任处理器的角色，并且这些具体对象之间是有递推关系的（具有后继者，i.e.，当第一个具体对象无法处理请求时，就会将请求传给后一个对象）

（4）蝇量模式

情景：让某个类的一个实例提供多个“虚拟实例”

（5）解释器模式

（6）中介者模式

中介者让对象之间解耦，不需要认识/接触其他对象，将对象之间的各种复杂关系包含在中介者内，让它来拥有整个系统的控制逻辑。

（7）备忘录模式

记录关键对象的状态，能够在需要时返回对象之前所在的状态

（8）原型模式

情景：游戏角色在不同场景中有不同的表现 —— 在单一区域内封装所有的实例化细节（将负责处理创建对象的细节代码，从实际需要动态创建实例的代码中解耦）

（9）访问者模式

为一个对象的组合增加新的能力，那么构造访问者，通过访问者收集所有对象组合的状态并进行操作，这样就不需要改变原来的对象组合的结构。



###  · 设计模式原则

#### - 单一职责

一个类应该只有一个**引起变化**的原因（尽量让每个类保持单一责任）；“内聚”用来度量一个类或模块紧密地达到单一目的或责任。

#### - 开放-封闭原则

类应该对扩展开放，对修改关闭 （即在不修改现有代码的情况下对功能进行扩展）

#### - 多用组合，少用继承

#### - 依赖倒置原则

要依赖抽象，不要依赖具体类；不能让高层组件依赖低层组件，它们都应该依赖于抽象。

#### - 最少知识原则

不要让太多的类耦合在一起（避免系统中一部分的修改会影响到其他很多部分），即减少对象之间的交互。如果调用从另一个调用中返回的对象的方法，相当向另一个对象的子部分发出请求，直接知道对象更多的细节。

#### - 好莱坞原则

防止“依赖腐败”，允许底层组件将自己挂钩到系统上，高层组件决定何时及如何使用底层组件。（可以理解为单方向的调用，而不是双向的调用 -- 双向调用在什么情况下可能发生？）



（未完......）