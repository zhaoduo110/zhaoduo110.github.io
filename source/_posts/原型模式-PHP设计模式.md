---
title: 原型模式-PHP设计模式
categories:
  - - PHP
tags:
  - PHP
  - 设计模式
abbrlink: 79d489ee
date: 2020-05-27 16:55:47
top_img: https://could-res-1252778021.file.myqcloud.com/pic/wallpaper/5ecce72a5977e.jpg
cover: https://could-res-1252778021.file.myqcloud.com/pic/wallpaper/5ecce72a5977e.jpg
---





# 原型模式

通过创建原型，然后使用克隆方法实现对象创建而不是使用标准的 new 方式



### 作用：

对一些大型对象，每次去new，初始化开销很大，这个时候我们 先new 一个模版对象，然后其他实例都去clone这个模版， 这样可以节约不少性能。这个模版，就是原型（Prototype）



### 使用场景：

- 大数据量 (例如：通过 ORM 模型一次性往数据库插入 1,000,000 条数据)



### UML图：

![UML-prototype](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/UML-prototype.png)



### 对象的浅复制和深复制

#### 浅复制：clone

- 比如 `clone new Db();`
- 当被克隆对象的属性中有对象等引用的时候，浅复制出来的对象的这个属性会指向同一个引用，即两个对象其中任何一个的这个属性发生变化都会发生变化，也有这样的应用场景

#### 深复制：利用序列化和反序列化实现、或者在class的__clone方法中对引用属性进行一次单独的clone

- 比如：`unserialize(serialize(new Db()));`
- 当被克隆对象的属性中有对象等引用的时候，深复制出来的对象的这个属性也是单独复制的，即两个对象完全互不相干



### Demo

Github地址：

#### 实现

Company.php

```php
<?php

namespace App\ProtoType;

class Company
{
    private $name;

    public function setName($name)
    {
        $this->name = $name;
    }
}
```

People.php

```php
<?php

namespace App\ProtoType;

class People implements Prototype
{

    private $name;

    private $company;

    public function __construct($name)
    {
        $this->name = $name;
    }

    public function getCompany()
    {
        return $this->company;
    }

    public function setCompany(Company $company)
    {
        $this->company = $company;
    }

    /**
     * 浅复制
     * @return People
     */
    public function shallowCopy()
    {
        return clone $this;
    }

    /**
     * 深复制实现1
     * @return mixed
     */
    public function deepCopy()
    {
        return unserialize(serialize($this));
    }

    /**
     * 深复制实现2
     */
//    public function __clone()
//    {
//        $this->company = clone $this->company;
//    }
}
```

Prototype.php

```php
<?php

namespace App\ProtoType;

interface Prototype{
    public function shallowCopy();

    public function deepCopy();
}
```



#### 测试

TestPrototypr.php

```php
<?php

namespace Test;

require __DIR__ . '/../vendor/autoload.php';

use App\ProtoType\People;
use App\ProtoType\Company;

/**
 * ======================================
 * ======================================
 * ===============浅复制==================
 * ======================================
 * ======================================
 */
//实例化一个People  设置名字和公司
//$person1 = new People('赵帅帅');
//$company = new Company();
//$company->setName('China');
//$person1->setCompany($company);
//
//$person2 = $person1->shallowCopy();
////看两个是否一样  其实从输入也能看出company的属性是同一个对象
//var_dump($person1,$person2);
//
////当company这个对象修改了名字之后  person1和person2的公司会一起跟着变化
//$company->setName('种花家');
//var_dump($person1,$person2);
//
////使用person对象操作改变属性实验一下   一样两个都会变
//$person1Com = $person1->getCompany();
//$person1Com->setName('大中华');
//var_dump($person1,$person2);

/**
 * ======================================
 * ======================================
 * ===============深复制==================
 * ======================================
 * ======================================
 */
//实例化一个People  设置名字和公司
$person1 = new People('赵帅帅');
$company = new Company();
$company->setName('China');
$person1->setCompany($company);

$person2 = $person1->deepCopy();
//两个对象的company没有指向一个
var_dump($person1, $person2);

//company这个对象修改了名字之后  只有person1的会会变  person2不会被影响
$company->setName('种花家');
var_dump($person1, $person2);

//使用person对象操作改变属性实验一下  操作了person2 只有person2会变  person1不会被影响
$person2Com = $person2->getCompany();
$person2Com->setName('大中华');
var_dump($person1, $person2);
```











