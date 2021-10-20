---
title: 工厂方法-PHP设计模式
categories:
  - - PHP
tags:
  - PHP
  - 设计模式
abbrlink: b4a8c0f9
date: 2020-05-27 17:01:10
top_img: https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218943853.jpg
cover: https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218943853.jpg
---



# 工厂方法模式

定义一个创建对象的接口，但让实现这个接口的类来决定实例化哪个类，工厂方法让类的实例化推迟到子类中进行



### 作用：



### 使用场景：

- 创建对象需要大量重复的代码
- 客户端不依赖于产品类实例如何被创建、实现的细节
- 一个类通过子类来制定创建哪个对象



### 优点

- 用户只需要关心所需产品的对应工厂，无需关心细节
- 加入新产品符合开闭原则，提高可扩展性



### 缺点

- 类的个数容易过多，增加复杂度
- 增加了系统的抽象性和理解难度



### 4个对象

- 抽象工厂类（App/FactoryMethod/CarFactory.php）：提供抽象方法供具体工厂实现
- 具体工厂类（App/FactoryMethod/BmwCarFactory.php）：提供具体的工厂
- 抽象产品类（App/FactoryMethod/Car.php）：提供抽象方法供具体产品类实现
- 具体产品类（App/FactoryMethod/BmwCar.php）：提供具体的产品



### UML图：

![UML-Factory-Method](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/UML-Factory-Method.png)



### Demo

Github地址：

#### 实现

BmwCar.php

```php
<?php

namespace App\FactoryMethod;

class BmwCar extends Car
{

    public function setAutoLogos()
    {
        return '宝马车标';
    }
}
```

BmwCarFactory.php

```php
<?php

namespace App\FactoryMethod;

class BmwCarFactory extends CarFactory
{

    public function create()
    {
        return new BmwCar();
    }
}
```

Car.php

```php
<?php

namespace App\FactoryMethod;

abstract class Car
{
    abstract public function setAutoLogos();
}
```

CarFactory.php

```php
<?php

namespace App\FactoryMethod;

abstract class CarFactory
{
    abstract public function create();
}
```



#### 测试

TestFactoryMethod.php

```php
<?php
namespace Test;

require __DIR__.'/../vendor/autoload.php';

use App\FactoryMethod\BmwCarFactory;

$bmwCarFactory = new BmwCarFactory();
$bmwCar = $bmwCarFactory->create();
var_dump($bmwCar->setAutoLogos());
```

