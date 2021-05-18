---
title: Redis知识梳理-2-数据类型及操作
abbrlink: 6b080b4
date: 2021-05-18 21:06:22
categories:
  - - NoSQL
tags:
  - Redis
  - NoSQL
top_img: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218907854.jpg'
cover: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218907854.jpg'
---



# 五种基本数据类型

| 数据类型 |                        存储的值                         |                   读写能力                    |
| :------: | :-----------------------------------------------------: | :-------------------------------------------: |
|  String  |         可以使用字符串、整数或浮点数,统称为元素         |          对字符串操作,对整数类型加减          |
|   List   |         一个序列集合且每个节点都包好了一个元素          | 序列两端推入、或弹出元素,修剪、查找或移除元素 |
|   Set    |                     各不相同的元素                      |           从集合中插入或者删除元素            |
|   Hash   |     有Key-value的散列祖,其中key是字符串,value是元素     |              按照key进行增加删除              |
| Sort Set | 带分数的score-value有序集合,其中score为浮点,value为元素 |           集合插入,按照分数范围查找           |





## String类型

1. 设置值   `set key value`

    string1 为 key,zhaoduo为value

    ```
    > set string1 zhaoduo
    ```

2. 取出值     `get key`

    ```
    > get string1
    ```

3. 设置整数   `set key value`

    ```
    > set string2 2
    ```

4. 整数自增 执行完之后string会自增1

    `incr value`

    ```
    > incr string2
    # z再取出值得时候会自增1
    > get string2
    ```

5. 整数减 后面的数代表减几,自增的时候不能跟这个数

    `decrby key value`

    ```
    > decrby string2 3
    #集合第四步的操作，这时候取出值会减3 就是0
    > get string2
    ```





## List类型

###### List类型是有序的列表,允许重复,List不要求里面的元素是唯一的

说明：可以结合栈和队列的含义理解,一直lpush(rpush),取值的时候一直lpop(rpop)是栈(后进先出)。如果一直lpush(rpush),取值的时候一直rpop(lpop)就是队列(先进先出)

1. 从左边push进一个值     (设置一个值)    `lpush key value`

    ```
    > lpush List1 value1
    ```

2. 从右边push进一个值     (设置一个值)     `rpush key value`

    ```
    > rpush List1 value2
    ```

3. 从左边pop一个值      (取出一个值)  `lpop key`

    ```
    > lpop List1
    #这里获得的值是value1
    ```

4. 从右边pop一个值     (取出一个值)   `rpop key`

    ```
    > rpop List1
    #这里获取的值是value2
    ```

5. 获取list的长度, `llen key`

    ```
    > llen List1
    ```





## Set类型

###### Set类型是无序的列表且不允许重复

1. 添加一个值,`sadd key value`

    ==set1为key 12为value==

    ```
    > sadd set1 12
    ```

2. 获取set长度, `scard key`

    ```
    > scard set1
    ```

3. 查看set里面是否存在某个value, `sismember key value`

    ==set1为key,13为value==

    ```
    > sismember set1 13
    ```

4. 删除一个值 `srem key value`

    ```
    > srem set1 13
    ```





## Hash类型(又称：散列)

###### 	Hash只要求键不一样，就是不同的条数

	数据结构图:
	
			key->key1  ->   value(string/int/float)     
	
				  key2  ->  value(string/int/float)     
	
				  key3  ->  value(string/int/float)     
	
				  key4  ->   value(string/int/float)

1. 插入       `hset key key value`

    ==如果键重复,值会被覆盖==

    ```
    > hset hash1 key1 12
    ```

2. 获取      `hget key key`

    ```
    > hget hash1 key1
    ```

    

3. 查看长度    `hlen key`

    ```
    > hlen key
    ```

4. 一次性获取多个值     `hmget key key1 key2`

    ```
    > hmget hash1 key1 key2
    ```





## Sort set类型(又称:有序分数集)

==存储和hash一样，都是一个映射。存储的是分数与元素的映射。可以看出是排行榜。value必须是全局唯一==

	数据结构：score 是浮点型
	
		key->score(10.1)   value(string/int/float)  rank:1     
	
	         score(9.1)    value(string/int/float)  rank:0    
	
	         score(11.2)   value(string/int/float)  rank:2

==如果两个元素的score一样，按照字节顺序排列先后==

1. 增加    `zadd key score value`

    ```
    > zadd zset1 10.1 val1
    ```

2. 查看个数      `zcard key`

    ```
    > zcard zset1
    ```

3. 查看排名       `zrange key sort widthscores`

    ```
    # 表示按照从o到2查看zset包括scores值
    > zrange zset1 0 2 widthscores
    ```

4. 查看某个value排名   `zrand key value`

    ```
    > zrank zset1 val2
    ```









