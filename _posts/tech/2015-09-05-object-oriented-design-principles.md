---
layout: post
title: 面向对象五大基本原则
category: 技术
tags: 面向对象
keywords: 面向对象
---


面向对象五大基本原则

以前一直认为程序中的类有使用到封装继承多态就是面向对象设计,其实不然
封装,继承,多态只是面向对象的三大特性,但是在设计程序的时候并不是说类的结构使用到了(或是体现出了)这三个特性就是面向对象,
其实真正的面向对象设计是要符合下面的五大原则,

面向对象的五大基本原则

1 单一职责原则（SRP）

2 开放封闭原则（OCP） 

3 里氏替换原则（LSP） 

4 依赖倒置原则（DIP）

5 接口隔离原则（ISP）

单一职责原则（SRP）

• 一个类应该仅有一个引起它变化的原因(最简单，最容易理解却最不容易做到的一个设计原则)
职员类例子：
  比如在职员类里，将工程师、销售人员、销售经理这些情况都放在职员类里考虑，其结果将会非常混乱，在这个假设下，职员类里的每个方法都要if else判断是哪种情况，从类结构上来说将会十分臃肿，并且上述三种的职员类型，不论哪一种发生需求变化，都会改变职员类！这个是大家所不愿意看到的！


开放封闭原则（OCP）

• 既开放又封闭，对扩展是开放的，对更改是封闭的！

• 扩展即扩展现行的模块，当我们软件的实际应用发生改变时，出现新的需求，就需要我们对模块进行扩展，使其能够满足新的需求！

更改封闭即是在我们对模块进行扩展时，勿需对源有程序代码和DLL进行修改或重新编译文件！
这个原则对我们在设计类的时候很有帮助，坚持这个原则就必须尽量考虑接口封装，抽象机制和多态技术！

里氏替换原则（LSP）

• 子类可以替换父类并且出现在父类能够出现的任何地方

• 这个原则也是在贯彻GOF倡导的面向接口编程！

在这个原则中父类应尽可能使用接口或者抽象类来实现！

子类通过实现了父类接口，能够替父类的使用地方！
通过这个原则，我们客户端在使用父类接口的时候，通过子类实现！
意思就是说我们依赖父类接口，在客户端声明一个父类接口，通过其子类来实现
这个时候就要求子类必须能够替换父类所出现的任何地方，这样做的好处就是，在根据新要求扩展父类接口的新子类的时候而不影响当前客户端的使用！


依赖倒置原则（DIP）

• 传统的结构化编程中，最上层的模块通常都要依赖下面的子模块来实现，也
称为高层依赖低层！
所以DIP原则就是要逆转这种依赖关系，让高层模块不要依赖低层模块，所以称之为依赖倒置原则！


ISP 接口隔离原则

• 这个原则的意思是：使用多个专门的接口比使用单个接口要好的多！

这个我有体会，在我实际编程中，为了减少接口的定义，将许多类似的方法都放在一个接口中，最后发现，维护和实现接口的时候花了太多精力，而接口所定义的操作相当于对客户端的一种承诺，这种承诺当然是越少越好，越精练越好，过多的承诺带来的就是你的大量精力和时间去维护！
