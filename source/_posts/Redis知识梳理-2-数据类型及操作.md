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





# 基本命令

| 命令     | 用法 | 作用 |      |
| -------- | ---- | ---- | ---- |
| SELECT   |      |      |      |
| KEYS     |      |      |      |
| EXISTS   |      |      |      |
| FLUSHALL |      |      |      |
| FLUSHDB  |      |      |      |
|          |      |      |      |

#### TYPE

用法：TYPE key

作用：返回 key 所存储的 value 的数据结构类型，它可以返回`string、list、set、zset、hash`等不同的类型。

返回：返回当前 key 的数据类型，如果 key 不存在时返回 none

举例：

```
redis> SET key1 "value"
OK
redis> LPUSH key2 "value"
(integer) 1
redis> SADD key3 "value"
(integer) 1
redis> TYPE key1
string
redis> TYPE key2
list
redis> TYPE key3
set
```







# 五种基本数据类型

| 数据类型 |                        存储的值                         |                   读写能力                    |
| :------: | :-----------------------------------------------------: | :-------------------------------------------: |
|  String  |         可以使用字符串、整数或浮点数,统称为元素         |          对字符串操作,对整数类型加减          |
|   List   |         一个序列集合且每个节点都包好了一个元素          | 序列两端推入、或弹出元素,修剪、查找或移除元素 |
|   Set    |                     各不相同的元素                      |           从集合中插入或者删除元素            |
|   Hash   |     有Key-value的散列祖,其中key是字符串,value是元素     |              按照key进行增加删除              |
| Sort Set | 带分数的score-value有序集合,其中score为浮点,value为元素 |           集合插入,按照分数范围查找           |



## String类型

value可以是字符串、数字（当做字符串处理的）、对象(:的用法)

相关命令：http://redis.cn/commands.html#string

#### SET

用法：SET key value [EX PX NX XX ]

- EX：设置键key的过期时间，单位是秒
- PX：设置键key的过期时间，单位是毫秒
- NX：只有键key不存在的时候才会设置key的值
- XX：只有键key存在的时候才会设置key的值

作用：将键`key`设定为指定的 value，如果key已经存在则会被覆盖为新的value

返回：如果`SET`命令正常执行那么回返回`OK`，否则如果加了`NX` 或者 `XX`选项，但是没有设置条件。那么会返回nil

注意: 由于`SET`命令加上选项已经可以完全取代[SETNX](http://redis.cn/commands/setnx.html), [SETEX](http://redis.cn/commands/setex.html), [PSETEX](http://redis.cn/commands/psetex.html)的功能，所以在将来的版本中，redis可能会不推荐使用并且最终抛弃这几个命令。

举例：

```shell
# 设置key1的值为value1
SET key1 value1
# 设置key2的值为value2 并在20s后过期
SET key2 value2 EX 20
# 设置key3的值为value3 并在10s后过期
SET key3 value3 PX 10000
# 如果key4不存在则设置为value4 如果存在则不设置
SET key4 value4 NX
# 如果key4存在则设置为value4 如果不存在则不设置
SET key4 value4 XX
```

#### GET

用法：GET key

作用：返回 key 的 value。如果key不存在，返回特殊值 nil 。如果`key`的 value 不是string，就返回错误，因为 GET 只处理string类型的 values

返回：key对应的value，key不存在时返回nil

举例：

```shell
# 获取key1的值
GET key1
```

#### EXPIRE

用法：EXPIRE key seconds

作用：设置key的过期时间为seconds秒

返回：设置成功返回1，设置失败（key不存在等原因）返回0

注意：

举例：

```shell
# 设置key1 20秒后失效
EXPIRE key1 20
```

#### TTL

用法：TTL key

作用：查看key的过期时间

返回：返回key的过期时间，永久有效则返回-1

举例：

```shell
# 首先设置一个key并设置过期时间
SET key1 value1 EX 30
# 获取过期时间
TTL key1
```

#### SETEX

用法：SETEX key seconds value

作用：

- 设置key对应字符串value，并且设置key在给定的seconds时间之后超时过期
- 这个命令等效于 `SET` 和 `EXPIRE` 的事务执行
- SETEX是原子的，也可以通过把上面两个命令放到 [MULTI](http://redis.cn/commands/multi.html)/[EXEC ](http://redis.cn/commands/exec.html)块中执行的方式重现。相比连续执行上面两个命令，它更快，因为当Redis当做缓存使用时，这个操作更加常用

注意: ==由于`SET`命令加上选项已经可以完全取代[SETNX](http://redis.cn/commands/setnx.html), [SETEX](http://redis.cn/commands/setex.html), [PSETEX](http://redis.cn/commands/psetex.html)的功能，所以在将来的版本中，redis可能会不推荐使用并且最终抛弃这几个命令。==

返回：成功返回ok

举例：

```shell
127.0.0.1:6379> SETEX key1 20 value1
OK
127.0.0.1:6379> TTL key1
(integer) 14
```

#### PSETEX

和 SETEX 完全一样，唯一的区别是到期时间以毫秒为单位,而不是秒

#### SETNX

用法：SETNX key value

作用：SET if Not exists，将`key`设置值为`value`，如果`key`不存在，这种情况下等同[SET](http://redis.cn/commands/set.html)命令。 当`key`存在时，什么也不做。

返回： 1 如果key被设置了，0如果key没有被设置

举例：

```shell
127.0.0.1:6379> SET key1 value
OK
127.0.0.1:6379> SETNX key1 value1
(integer) 0
127.0.0.1:6379> SETNX key2 value2
(integer) 1
127.0.0.1:6379> KEYS *
1) "key1"
2) "key2"
127.0.0.1:6379> GET key1
"value"
127.0.0.1:6379> GET key2
"value2"
```

#### MSET

用法：MSET key value [key value ...]

作用：

- 对应给定的keys到他们相应的values上。`MSET`会用新的value替换已经存在的value，就像普通的[SET](http://redis.cn/commands/set.html)命令一样
- `MSET`是原子的，所以所有给定的keys是一次性set的。客户端不可能看到这种一部分keys被更新而另外的没有改变的情况

返回：总是OK，因为MSET不会失败

举例：

```shell
127.0.0.1:6379> MSET key1 value1 key2 value2 key3 value3
OK
127.0.0.1:6379> KEYS *
1) "key1"
2) "key3"
3) "key2"
127.0.0.1:6379> GET key1
"value1"
127.0.0.1:6379> GET key2
"value2"
127.0.0.1:6379> GET key3
"value3"
```

#### MGET

用法：MGET key [key ...]

作用：返回所有指定的key的value。对于每个不对应string或者不存在的key，都返回特殊值`nil`。正因为此，这个操作从来不会失败

返回：指定的key对应的values的list

举例：

```shell
127.0.0.1:6379> MSET key1 value1 key2 value2 key3 value3
OK
127.0.0.1:6379> MGET key1 key2
1) "value1"
2) "value2"
127.0.0.1:6379> MGET key1 key2 key4
1) "value1"
2) "value2"
3) (nil)
```

#### MSETEX

==弃用==

用法：

作用：

返回：

举例：

#### MSETNX

用法：MSETNX key value [key value ...]

作用：

- 对应给定的keys到他们相应的values上。==只要有一个key已经存在，`MSETNX`一个操作都不会执行。 由于这种特性，`MSETNX`可以实现要么所有的操作都成功，要么一个都不执行，这样可以用来设置不同的key，来表示一个唯一的对象的不同字段。==
- `MSETNX`是原子的，所以所有给定的keys是一次性set的。客户端不可能看到这种一部分keys被更新而另外的没有改变的情况。

返回：

- 1 如果所有的key被set
- 0 如果没有key被set(至少其中有一个key是存在的)

举例：

```shell
127.0.0.1:6379> MSETNX key1 value1 key2 value2 key3 value3
(integer) 1
127.0.0.1:6379> SET key4 value4
OK
127.0.0.1:6379> KEYS *
1) "key1"
2) "key4"
3) "key3"
4) "key2"
127.0.0.1:6379> MSETNX key4 value4 key5 value5 key6 value6
(integer) 0
127.0.0.1:6379> KEYS *
1) "key1"
2) "key4"
3) "key3"
4) "key2"
```

#### GETSET

用法：

作用：

返回：

举例：

#### MOVE

用法：MOVE key db

作用：将当前数据库的 key 移动到给定的数据库 db 当中

返回：移动成功返回1，移动失败返回0

注意：如果当前数据库(源数据库)和给定数据库(目标数据库)有相同名字的给定 key ，或者 key 不存在于当前数据库，那么 MOVE 没有任何效果。因此，也可以利用这一特性，将 `MOVE` 当作锁(locking)原语(primitive)

举例：

```shell
# 选择0号库
127.0.0.1:6379> SELECT 0
OK
# SET key1和key2
127.0.0.1:6379> SET key1 value1
OK
127.0.0.1:6379> SET key2 value2
OK
# 查看当前库有key1和key2两条数据
127.0.0.1:6379> KEYS *
1) "key1"
2) "key2"
# 移动key1到1号库
127.0.0.1:6379> MOVE key1 1
(integer) 1
# 查看当前库只剩下key2一条数据
127.0.0.1:6379> KEYS *
1) "key2"
# 选择1号库
127.0.0.1:6379> SELECT 1
OK
# 查看key1已经被移动到1号库
127.0.0.1:6379[1]> KEYS *
1) "key1"
```

#### APPEND

用法：APPEND key value

作用：如果  key  已经存在，并且值为字符串，那么这个命令会把  value 追加到原来值（value）的结尾。 如果 key 不存在，那么它将首先创建一个空字符串的 key ，再执行追加操作，这种情况  APPEND 将类似于 SET 操作

返回：返回append后字符串（value）的长度

举例：

```shell
# 设置key1值为value1
127.0.0.1:6379> SET key1 value1
OK
# 查看当前key1的值
127.0.0.1:6379> GET key1
"value1"
# key1值追加 valueAppend，追加成功返回key1值的总长度
127.0.0.1:6379> APPEND key1 valueAppend
(integer) 17
# 再次查看key1的值 说明追加成功
127.0.0.1:6379> GET key1
"value1valueAppend"
```

#### STRLEN

用法：STRLEN key

作用：查看key值的长度

返回：返回int    key值的长度

举例：

```shell
127.0.0.1:6379> SET key1 value1
OK
127.0.0.1:6379> STRLEN key1
(integer) 6
```

#### INCR

用法：INCR key

作用：对存储在指定`key`的数值执行原子的加1操作。

- 如果指定的key不存在，那么在执行incr操作之前，会先将它的值设定为`0`。

- 如果指定的key中存储的值不是字符串类型（fix：）或者存储的字符串类型不能表示为一个整数，那么执行这个命令时服务器会返回一个错误(eq:(error) ERR value is not an integer or out of range)
- 这个操作仅限于64位的有符号整型数据

注意: 

- 由于redis并没有一个明确的类型来表示整型数据，所以这个操作是一个字符串操作。

- 执行这个操作的时候，key对应存储的字符串被解析为10进制的**64位有符号整型数据**。

- 事实上，Redis 内部采用整数形式（Integer representation）来存储对应的整数值，所以对该类字符串值实际上是用整数保存，也就不存在存储整数的字符串表示（String representation）所带来的额外消耗

返回：执行递增操作后`key`对应的值

举例：

```shell
127.0.0.1:6379> SET key1 5
OK
127.0.0.1:6379> INCR key1
(integer) 6
127.0.0.1:6379> INCR key1
(integer) 7
127.0.0.1:6379> GET key1
"7"
```

#### DECR

用法：DECR key

作用：对key对应的数字做减1操作。如果key不存在，那么在操作之前，这个key对应的值会被置为0。如果key有一个错误类型的value或者是一个不能表示成数字的字符串，就返回错误。这个操作最大支持在64位有符号的整型数字。

返回：减小之后的value

举例：

```shell
127.0.0.1:6379> SET key1 5
OK
127.0.0.1:6379> DECR key1
(integer) 4
127.0.0.1:6379> DECR key1
(integer) 3
127.0.0.1:6379> GET key1
"3"
```

#### INCRBY

用法：INCRBY key increment

作用：将key对应的数字加decrement。如果key不存在，操作之前，key就会被置为0。如果key的value类型错误或者是个不能表示成数字的字符串，就返回错误。这个操作最多支持64位有符号的正型数字

返回：增加之后的value值

举例：

```shell
127.0.0.1:6379> SET key1 1
OK
127.0.0.1:6379> INCRBY key1 2
(integer) 3
127.0.0.1:6379> INCRBY key1 3
(integer) 6
127.0.0.1:6379> INCRBY key1 5
(integer) 11
127.0.0.1:6379> GET key1
"11"
```

#### DECRBY

用法：DECRBY key increment

作用：将key对应的数字减decrement。如果key不存在，操作之前，key就会被置为0。如果key的value类型错误或者是个不能表示成数字的字符串，就返回错误。这个操作最多支持64位有符号的正型数字

返回：减少之后的value值

举例：

```shell
127.0.0.1:6379> SET key1 10
OK
127.0.0.1:6379> DECRBY key1 2
(integer) 8
127.0.0.1:6379> DECRBY key1 4
(integer) 4
127.0.0.1:6379> GET key1
"4"
```

#### GETRANGE

用法：GETRANGE key start end

作用：返回key对应的字符串value的子串

- 这个子串是由start和end位移决定的（两者都在string内）。

- 可以用负的位移来表示从string尾部开始数的下标。所以-1就是最后一个字符，-2就是倒数第二个，以此类推。

- 这个函数处理超出范围的请求时，都把结果限制在string内

注意：这个命令是被改成GETRANGE的，在小于2.0的Redis版本中叫SUBSTR。start end都是闭区间，即返回的子串包含start和end位置的字符

返回：返回符合指定位置的子字符串

举例：

```
127.0.0.1:6379> SET key1 'This is a dog'
OK
127.0.0.1:6379> GET key1
"This is a dog"
127.0.0.1:6379> GETRANGE key1 4 10
" is a d"
127.0.0.1:6379> GETRANGE key1 4 20
" is a dog"
```

#### SETRANGE

用法：SETRANGE key offset value

作用：覆盖key对应的string的一部分，从指定的offset处开始，覆盖value的长度

- 如果offset比当前key对应string还要长，那这个string后面就补0以达到offset。
- 不存在的keys被认为是空字符串，所以这个命令可以确保key有一个足够大的字符串，能在offset处设置value

返回：该命令修改后的字符串长度

注意：

- offset最大可以是229-1(536870911)，因为redis字符串限制在512M大小。如果你需要超过这个大小，你可以用多个keys。

- ==当set最后一个字节并且key还没有一个字符串value或者其value是个比较小的字符串时，Redis需要立即分配所有内存，这有可能会导致服务阻塞一会。在一台2010 MacBook Pro上，set 536870911字节（分配512MB）需要～300ms，set134217728字节(分配128MB)需要～80ms，set 33554432比特位（分配32MB）需要～30ms，set 8388608比特（分配8MB）需要8ms。注意，一旦第一次内存分配完，后面对同一个key调用[SETRANGE](http://redis.cn/commands/setrange.html)就不会预先得到内存分配==

举例：

```shell
127.0.0.1:6379> SET key1 value1234567890
OK
127.0.0.1:6379> SETRANGE key1 5 000000000
(integer) 15
127.0.0.1:6379> GET key1
"value0000000000"
127.0.0.1:6379> SETRANGE key1 5 0000
(integer) 15
127.0.0.1:6379> GET key1
"value0000000000"
127.0.0.1:6379> SETRANGE key1 14 0000
(integer) 18
127.0.0.1:6379> GET key1
"value0000000000000"
```









## List类型

List类型是有序的列表,允许重复,List不要求里面的元素是唯一的

说明：可以结合栈和队列的含义理解，

- 设置和取值的时候同一方向是栈(后进先出)，lpush lpop结合、rpush rpop结合。
- 设置和取值的时候方向相反就是队列(先进先出)，lpush rpop结合、rpush lpop结合

==list头部在左，尾部在右==

相关命令：http://redis.cn/commands.html#list

#### LPUSH

用法：LPUSH key value [value ...]

作用：将所有指定的值插入到列表key的头部。

- 如果 key 不存在，那么在进行 push 操作前会创建一个空列表。
- 如果 key 对应的值不是一个 list 的话，那么会返回一个错误。

- 可以使用一个命令把多个元素 push 进列表，只需在命令末尾加上多个指定的参数。
- 元素是从最左端的到最右端的、一个接一个被插入到 list 的头部。 所以对于这个命令例子 `LPUSH mylist a b c`，返回的列表是 c 为第一个元素， b 为第二个元素， a 为第三个元素。

返回：push 操作后的 list 长度

注意: 2.4版本可以接受多个 value 参数。2.4版本之前的 Redis 只能每条命令 push 一个值

举例：

```shell
127.0.0.1:6379> LPUSH key_list value1
(integer) 1
127.0.0.1:6379> LRANGE key_list 0 -1
1) "value1"
127.0.0.1:6379> LPUSH key_list value1 value2 value3
(integer) 4
127.0.0.1:6379> LRANGE key_list 0 -1
1) "value3"
2) "value2"
3) "value1"
4) "value1"
```

#### RPUSH

用法：RPUSH key value [value ...]

作用：向列表 key 的尾部插入所有指定的值。

- 如果 key 不存在，那么会创建一个空的列表然后再进行 push 操作。 
- 当 key 保存的不是一个列表，那么会返回一个错误。

- 可以使用一个命令把多个元素打入队列，只需要在命令后面指定多个参数。
- 元素是从左到右一个接一个从列表尾部插入。 比如命令 RPUSH mylist a b c 会返回一个列表，其第一个元素是 a ，第二个元素是 b ，第三个元素是 c。

返回：push 操作后的列表长度

注意: 2.4版本可以接受多个 value 参数。2.4版本之前的 Redis 只能每条命令 push 一个值

举例：

```shell
127.0.0.1:6379> RPUSH key_list value1
(integer) 1
127.0.0.1:6379> LRANGE key_list 0 -1
1) "value1"
127.0.0.1:6379> RPUSH key_list value1 value2 value3
(integer) 4
127.0.0.1:6379> LRANGE key_list 0 -1
1) "value1"
2) "value1"
3) "value2"
4) "value3"
```

#### LPUSHX

用法：LPUSHX key value [value ...]

作用：只有当 key 已经存在并且为 list 的时候，就在这个 key 下面的 list 的头部插入 value。 当 key 不存在的时候不会进行任何操作

返回：push 操作后的 list 长度

举例：

```shell
# 当key1不存在时直接用LPUSHX 不行  返回0则说明没有push成功
127.0.0.1:6379> LPUSHX key1 value1 value2
(integer) 0
127.0.0.1:6379> LRANGE key1 0 -1
(empty array)
127.0.0.1:6379> FLUSHALL
OK
127.0.0.1:6379> LPUSHX key1 value1
(integer) 0
127.0.0.1:6379> LRANGE key1 0 -1
(empty array)
127.0.0.1:6379> FLUSHALL
OK
# 首先用LPUSH生成一个列表key1
127.0.0.1:6379> LPUSH key1 value1
(integer) 1
127.0.0.1:6379> LRANGE key1 0 -1
1) "value1"
# 当列表key1已经存在时再使用LPUSHX就可以成功
127.0.0.1:6379> LPUSHX key1 value2
(integer) 2
127.0.0.1:6379> LRANGE key1 0 -1
1) "value2"
2) "value1"
# LPUSHX也支持同时push多个值
127.0.0.1:6379> LPUSHX key1 value3 value3
(integer) 4
127.0.0.1:6379> LRANGE key1 0 -1
1) "value3"
2) "value3"
3) "value2"
4) "value1"
```

#### RPUSHX

操作同RPUSHX

#### LPOP

用法：LPOP key [count]

作用：移除并且返回 key 对应的 list 的前count个元素，count默认为1

返回：返回元素值，或者当 key 不存在时返回 nil

举例：

```shell
# key1 依次头部追加onew two three三个元素
127.0.0.1:6379> LPUSH key1 one
(integer) 1
127.0.0.1:6379> LPUSH key1 two
(integer) 2
127.0.0.1:6379> LPUSH key1 three
(integer) 3
# 当前key1的列表值
127.0.0.1:6379> LRANGE key1 0 -1
1) "three"
2) "two"
3) "one"
# 弹出列表key1中的第一个元素
127.0.0.1:6379> LPOP key1
"three"
127.0.0.1:6379> LRANGE key1 0 -1
1) "two"
2) "one"
# 弹出列表key1中的前2个元素
127.0.0.1:6379> LPOP key1 2
1) "two"
2) "one"
127.0.0.1:6379> LRANGE key1 0 -1
(empty array)
```

#### RPOP

用法：RPOP key [count]

作用：移除并且返回 key 对应的 list 的最后count个元素，count默认为1

返回：返回元素值，或者当 key 不存在时返回 nil

举例：

```shell
# key1 依次头部追加onew two three三个元素
127.0.0.1:6379> LPUSH key1 one
(integer) 1
127.0.0.1:6379> LPUSH key1 two
(integer) 2
127.0.0.1:6379> LPUSH key1 three
(integer) 3
# 当前key1的列表值
127.0.0.1:6379> LRANGE key1 0 -1
1) "three"
2) "two"
3) "one"
# 弹出列表key1中的最后一个元素
127.0.0.1:6379> RPOP key1
"one"
127.0.0.1:6379> LRANGE key1 0 -1
1) "three"
2) "two"
# 弹出列表key1中的最后2个元素
127.0.0.1:6379> RPOP key1 2
1) "two"
2) "three"
127.0.0.1:6379> LRANGE key1 0 -1
(empty array)
```

#### BLPOP

用法：BLPOP key [key ...] timeout

作用：命令 [LPOP](http://redis.cn/commands/lpop.html) 的阻塞版本，这是因为当给定列表内没有任何元素可供弹出的时候， 连接将被 [BLPOP](http://redis.cn/commands/blpop.html) 命令阻塞。 当给定多个 key 参数时，按参数 key 的先后顺序依次检查各个列表，弹出第一个非空列表的头元素

返回：

注意: 

举例：

#### BRPOP

用法：

作用：

返回：

注意: 

举例：

#### LLEN

用法：

作用：

返回：

注意: 

举例：

#### LINDEX

用法：

作用：

返回：

注意: 

举例：

#### LSET

用法：

作用：

返回：

注意: 

举例：

#### LINSERT

用法：

作用：

返回：

注意: 

举例：

#### LREM

用法：

作用：

返回：

注意: 

举例：

#### LRANGE

用法：

作用：

返回：

注意: 

举例：

#### LTRIM

用法：

作用：

返回：

注意: 

举例：

#### RPOPLPUSH

用法：

作用：

返回：

注意: 

举例：

#### BRPOPLPUSH

用法：

作用：

返回：

注意: 

举例：







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





## Zset 类型(Sort set又称:有序分数集)

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









# 特殊数据类型



## Geospatial





## Hyperlog





## bitmaps



