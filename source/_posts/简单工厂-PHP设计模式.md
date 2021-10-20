---
title: 简单工厂-PHP设计模式
categories:
  - - PHP
tags:
  - PHP
  - 设计模式
abbrlink: 3e71befb
date: 2020-05-27 16:59:31
top_img: https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218943853.jpg
cover: https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218943853.jpg
---





# 简单工厂模式

由一个工厂对象决定创建出哪一种产品类的实例



### 作用：

简单工厂的作用是实例化对象，而不需要客户了解这个对象属于哪个具体的子类



### 使用场景：

- 在子类比较固定并不需要扩展时
- 工厂类负责创建的对象比较少



### 3个对象

- 抽象产品类（Vehicle\Vehicle.php）：提供抽象方法供具体产品类实现
- 具体产品类（Vehicle\Car.php、Vehicle\Bicycle.php）：提供具体的产品
- 工厂类（Factory.php）：根据内部逻辑返回相应的产品



### UML图：

![UML-Simple-Factory](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/UML-Simple-Factory.png)



### Demo

Github地址：

#### 实现

Vehicle\Bicycle.php

```php
<?php

namespace App\SimpleFactory\Vehicle;

class Bicycle extends Vehicle
{

    public function run()
    {
        return '两个轮子跑的贼慢';
    }
}
```

Vehicle\Car.php

```php
<?php

namespace App\SimpleFactory\Vehicle;

class Car extends Vehicle
{

    public function run()
    {
        return '四个轮子跑的飞起';
    }
}
```

Vehicle\Vehicle.php

```php
<?php

namespace App\SimpleFactory\Vehicle;

abstract class Vehicle
{
    abstract public function run();
}
```

Factory.php

```php
<?php

namespace App\SimpleFactory;

use App\SimpleFactory\Vehicle\Bicycle;
use App\SimpleFactory\Vehicle\Car;
use mysql_xdevapi\Exception;

class Factory
{

    const CAR = 1;
    const BICYCLE = 2;

    public static function create($carType)
    {
        switch ($carType) {
            case 1:
                $obj = new Bicycle();
                break;
            case 2:
                $obj = new Car();
                break;
            default:
                throw new Exception('Unsupport');
                break;
        }
        return $obj;
    }
}
```



#### 测试

TestSimpleFactory.php

```php
<?php

namespace Test;

require __DIR__ . '/../vendor/autoload.php';

use App\SimpleFactory\Factory;

$obj = Factory::create(Factory::BICYCLE);
var_dump($obj->run());
```













