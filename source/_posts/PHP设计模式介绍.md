---
title: PHP设计模式介绍
date: 2020-05-27 16:54:33
categories:
- [PHP]
tags:
- PHP
- 设计模式
---



# DesignPattern

设计模式

#### 创建型

在软件工程中，创建型设计模式是处理对象创建机制的设计模式，试图以适当的方式来创建对象。对象创建的基本形式可能会带来设计问题，亦或增加了设计的复杂度。创建型设计模式通过控制这个对象的创建方式来解决此问题
- [X] 单例模式（Singleton）
- [X] 多例模式（Multiton）
- [X] 原型模式（Prototype）
- [ ] 工厂方法模式（Factory Method）
- [ ] 简单工厂模式（Simple Factory）
- [ ] 抽象工厂模式（Abstract Factory）
- [ ] 静态工厂模式（Static Factory）
- [ ] 建造者模式（Builder）
- [ ] 对象池模式（Pool）



#### 结构型

在软件工程中，结构型设计模式是通过识别实体之间关系来简化设计的设计模式
- [ ] 适配器模式（Adapter）
- [ ] 桥梁模式（Bridge）
- [ ] 组合模式（Composite）
- [ ] 数据映射模式（Data Mapper）
- [ ] 装饰模式（Decorator）
- [ ] 依赖注入模式（Dependency Injection）
- [ ] 门面模式（Facade）
- [ ] 流接口模式（Fluent Interface）
- [ ] 享元模式（Flyweight）
- [ ] 代理模式（Proxy）
- [ ] 注册模式（Registry）



#### 行为型
在软件工程中，行为设计模式是识别对象之间的通用通信模式并实现这些模式的设计模式。通过这样做，这些模式增加了执行此通信的灵活性
- [ ] 责任链模式（Chain Of Responsibilities）
- [ ] 命令行模式（Command）
- [ ] 迭代器模式（Iterator）
- [ ] 中介者模式（Mediator）
- [ ] 备忘录模式（Memento）
- [ ] 空对象模式（Null Object）
- [ ] 观察者模式（Observer）
- [ ] 规格模式（Specification）
- [ ] 状态模式（State）
- [ ] 策略模式（Strategy）
- [ ] 模板方法模式（Template Method）
- [ ] 访问者模式（Visitor）



#### 其他

- [ ] 委托模式（Delegation）
- [ ] 服务定位器模式（Service Locator）
- [ ] 资源库模式（Repository）
- [ ] 实体属性值模式（EAV 模式）



##### 反面模式（Anti-pattern）

在软件工程中，一个反面模式（Anti-pattern 或 Antipattern）指的是在实践中明显出现但又低效或是有待优化的设计模式，是用来解决问题的带有共同性的不良方法。它们已经经过研究并分类，以防止日后重蹈覆辙，并能在研发尚未投产的系统时辨认出来。

Andrew Koenig 在 1995 年造了 Anti-pattern 这个词，灵感来自于 GoF 的《设计模式》一书。而这本书则在软件领域引入了 “设计模式”（Design Pattern）的概念。三年后 Antipattern 因《AntiPatterns》这本书而获得普及，而它的使用也从软件设计领域扩展到了日常的社会互动中。按《AntiPatterns》作者的说法，可以用至少两个关键因素来把反面模式和不良习惯、错误的实践或糟糕的想法区分开来：

- 行动、过程和结构中的一些重复出现的乍一看是有益的，但最终得不偿失的模式
- 在实践中证明且可重复的清晰记录的重构方案

很多反面模式只相当于是错误、咆哮、不可解的问题、或是可能可以避免的糟糕的实践，它们的名字通常都是一些用反话构成的词语。有些时候陷阱（Pitfalls）或黑色模式（Dark Patterns）这些不正式的说法会被用来指代各类反复出现的糟糕的解决方法。因此，一些有争议的候选的反面模式不会被正式承认