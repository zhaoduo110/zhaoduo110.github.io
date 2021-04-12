---
title: MySQL知识梳理-5.SQL语言-DCL
abbrlink: 9e341d9c
date: 2021-03-20 17:07:38
categories:
  - - SQL
tags:
  - MySQL
  - SQL
top_img: 'https://could-res-1252778021.file.myqcloud.com/pic/wallpaper/5ecce72a5977e.jpg'
cover: 'https://could-res-1252778021.file.myqcloud.com/pic/wallpaper/5ecce72a5977e.jpg'
---





# SQL语句

一种描述性语言

## 作用

对存储在RDBMS中的数据进行增删改查等操作



## 分类

**数据控制语言**：DCL（Data Control Language）`GRANT,REVOKE,COMMIT,ROLLBACK` 对数据库安全性完整性等有操作的，可以简单的理解为权限控制或者事务操作等

**数据定义语言**：DDL（Data Ddefinition Language) `CREATE,DROP,ALTER` 主要为以上操作 即对逻辑结构等有操作的，其中包括表结构，视图和索引。

**数据查询语言**：DQL（Data Query Language）`SELECT` 查询操作，以select关键字。各种简单查询，连接查询等 都属于DQL。

**数据操纵语言**：DML（Data Manipulation Language）`INSERT,UPDATE,DELETE` 对数据进行操作的，对应上面所说的查询操作 DQL与DML共同构建了多数初级程序员常用的增删改查操作。而查询是较为特殊的一种 被划分到DQL中。



## 官方文档

https://dev.mysql.com/doc/refman/8.0/en/sql-statements.html



# DCL

系统相关命令官方文档：https://dev.mysql.com/doc/refman/8.0/en/sql-server-administration-statements.html

## 创建数据库账号

create user

语法格式：

```sql
CREATE USER [IF NOT EXISTS]
	user [auth_option] [, user [auth_option]] ...
	[DEFAULT ROLE role [,role]] ...
	[REQUIRE {NONE | tls_option [[AND] tls_option]...}]
	[WITH resource_option [resource_option] ...]
	[password_option | lock_option] ...	
```

[] 括起来的可有可无，下面一行一行来解读

### `CREATE USER [IF NOT EXISTS]`

基本语法，必须

### `user [auth_option] [, user [auth_option]] ... `

**user**

由两部分组成 `用户名@访问控制列表`

访问控制列表决定用户可以从哪些客户端进行访问

比如：

```sql
CREATE USER zhaod@'127.0.0.1' 
CREATE USER zhaod@'127.0.0.%'
CREATE USER zhaod@'%'
```

**auth_option**

有以下四种写法

```
IDENTIFIED BY 'auth_string'
IDENTIFIED WITH auth_plugin
IDENTIFIED WITH auth_plugin BY 'auth_string'
IDENTIFIED WITH auth_plugin AS 'hash_string'
```

- 如果要设置， IDENTFIED就必须有

- BY 关键字后面跟明文密码

- WITH 后面表示使用哪种认证插件(`show plugins;` 语句可以查看所有的插件)
  - mysql_native_password
  - sha256_password
  - caching_sha2_password（MySQL8.0默认认证插件）

查看当前用户使用的密码验证插件

```sql
show variables like '%auth%';
```

`修改用户的密码验证插件`

```sql
alter user root@'localhost' identified with mysql_native_password by 'root';
```

`修改系统默认的密码验证插件`

配置参数： default-authentication-plugin

`启动mysql服务时修改密码验证插件`

参数： --default-authentication-plugin



### `[DEFAULT ROLE role]`

给用户指定默认角色，可多个。并且要设置的角色在 CREATE USER 执行时不需要存在

比如 `DEFAULT ROLE role_read_only,role_write_only`



###  `[REQUIRE {NONE | tls_option [[AND] tls_option]...}]`

以下摘自：[官方文档](https://dev.mysql.com/doc/refman/8.0/en/create-user.html#create-user-tls)

除基于用户名和凭据的常规身份验证外，MySQL还可以检查X.509证书属性。

有关在MySQL上使用SSL / TLS的背景信息，请参见[第6.3节“使用加密的连接”](https://dev.mysql.com/doc/refman/8.0/en/encrypted-connections.html)。

要为MySQL帐户指定与SSL / TLS相关的选项，请使用 REQUIRE 指定一个或多个 tls_option 值的 子句 。

 REQUIRE 选项的 顺序无关紧要，但是不能两次指定选项。该  AND 关键字是可选之间  REQUIRE 选择

tls_option允许的值

- NONE：
  - 表示该语句命名的所有帐户都没有SSL或X.509要求。如果用户名和密码有效，则允许未加密的连接。如果客户端具有正确的证书和密钥文件，则可以选择使用加密连接
  - 客户端默认尝试建立安全连接。对于具有的客户端，REQUIRE NONE 如果无法建立安全连接，则连接尝试将退回到未加密的连接。要要求加密连接，客户端只需要指定 [`--ssl-mode=REQUIRED`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-mode) 选项即可；如果无法建立安全连接，则连接尝试将失败。
  - 如果没有 REQUIRE 指定与SSL相关的选项，则NONE为默认值
  - `CREATE USER 'jeffrey'@'localhost' REQUIRE NONE;`
- SSL：
  - 告诉服务器仅允许该语句命名的所有帐户的加密连接
  - 客户端默认尝试建立安全连接。对于具有的帐户，`REQUIRE SSL`如果无法建立安全连接，则连接尝试将失败。
  - `CREATE USER 'jeffrey'@'localhost' REQUIRE SSL;`
- X509：
  - 对于该语句命名的所有帐户，要求客户提供有效的证书，但是确切的证书，颁发者和主题无关紧要。唯一的要求是，应该可以使用其中一个CA证书来验证其签名。X.509证书的使用始终暗含加密，因此 `SSL`在这种情况下，此选项是不必要的
  - 对于具有的帐户 REQUIRE X509 ，客户必须指定[`--ssl-key`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-key) 和[`--ssl-cert`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-cert)选项进行连接。（建议但不要求 [`--ssl-ca`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-ca)也进行指定，以便可以验证服务器提供的公共证书。）这对于`ISSUER` 以及都是正确的， SUBJECT 因为这些  REQUIRE 选项暗示了的要求 X509 。
  - `CREATE USER 'jeffrey'@'localhost' REQUIRE X509;`
- CIPHER 'cipher'
  - 对于该语句命名的所有帐户，都需要使用特定的加密方法来加密连接。需要使用此选项以确保使用足够强度的密码和密钥长度。如果使用使用短加密密钥的旧算法，则加密可能会很弱
  - `CREATE USER 'jeffrey'@'localhost'  REQUIRE CIPHER 'EDH-RSA-DES-CBC3-SHA';`
- ISSUER 'issuer'
  - 对于该语句命名的所有帐户，要求客户端出示由CA颁发的有效X.509证书 。如果客户端出示的证书有效但具有不同的颁发者，则服务器将拒绝连接。X.509证书的使用始终暗含加密，因此 在这种情况下，此选项是不必要的。
  - 因为 ISSUER 暗示的要求 X509 ，客户端必须指定 [`--ssl-key`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-key)和 [`--ssl-cert`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-cert)选项进行连接。（建议但不要求 [`--ssl-ca`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-ca)也进行指定，以便可以验证服务器提供的公共证书。）
  - `CREATE USER 'jeffrey'@'localhost'  REQUIRE ISSUER '/C=SE/ST=Stockholm/L=Stockholm/ O=MySQL/CN=CA/emailAddress=ca@example.com';`
- SUBJECT 'subject'
  - 对于该语句命名的所有帐户，要求客户端出示包含主题的有效X.509证书 subject 。如果客户端出示的证书有效但主题不同，则服务器拒绝连接。X.509证书的使用始终暗含加密，因此  SSL 在这种情况下，此选项是不必要的。
  - MySQL 对值与证书中的值进行了简单的字符串比较 ，因此字母和组件的顺序必须完全按照证书中的顺序给出。
  - 因为 SUBJECT 暗示的要求 X509 ，客户端必须指定[`--ssl-key`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-key)和 [`--ssl-cert`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-cert)选项进行连接。（建议但不要求 [`--ssl-ca`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-ca)也进行指定，以便可以验证服务器提供的公共证书。）
  - `CREATE USER 'jeffrey'@'localhost'  REQUIRE SUBJECT '/C=SE/ST=Stockholm/L=Stockholm/    O=MySQL demo client certificate/    CN=client/emailAddress=client@example.com';`

`SUBJECT`，`ISSUER`和 `CIPHER`选项可以组合

```sql
CREATE USER 'jeffrey'@'localhost'
  REQUIRE SUBJECT '/C=SE/ST=Stockholm/L=Stockholm/
    O=MySQL demo client certificate/
    CN=client/emailAddress=client@example.com'
  AND ISSUER '/C=SE/ST=Stockholm/L=Stockholm/
    O=MySQL/CN=CA/emailAddress=ca@example.com'
  AND CIPHER 'EDH-RSA-DES-CBC3-SHA';
```



### `[WITH resource_option [resource_option] ...]`

创建用户资源限制选项。使用WITH指定一个或多个*resource_option*值的子句。

WITH 选项的 顺序无关紧要，除非多次指定给定的资源限制，则以最后一个实例为准

resource_option 允许的值

- MAX_QUERIES_PER_HOUR count：
- MAX_UPDATES_PER_HOUR count ：
- MAX_CONNECTIONS_PER_HOUR count ：同上面两个一起，对于由语句命名的所有帐户，这些选项限制在给定的一小时内允许每个帐户进行多少次查询、更新和连接到服务器。如果count为0(默认值)，这意味着对该帐户没有限制。
- MAX_USER_CONNECTIONS count：对于由语句命名的所有帐户，限制每个帐户同时连接到服务器的最大数量。非零计数显式指定帐户的限制。如果count为0(默认值)，服务器根据max_user_connections系统变量的全局值确定该帐户同时连接的数量。如果max_user_connections也是零，则对该帐户没有限制

```sql
CREATE USER 'jeffrey'@'localhost'
  WITH MAX_QUERIES_PER_HOUR 500 MAX_UPDATES_PER_HOUR 100;
```



### `[password_option]`

[`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/create-user.html)支持多个 *`password_option`*密码管理值，如果指定了给定类型的多个密码管理选项则最后一个优先。例如， `PASSWORD EXPIRE DEFAULT PASSWORD EXPIRE NEVER`与`PASSWORD EXPIRE NEVER`相同。

**密码到期选项**

可以手动使帐户密码到期并建立其密码到期策略。策略选项不会使密码失效。取而代之的是，他们根据密码有效期来确定服务器如何将自动过期应用于帐户，该有效期是根据最近的帐户密码更改的日期和时间来评估的。

支持以下选项

- PASSWORD EXPIRE：立即将该语句命名的所有帐户的密码标记为过期

  ```sql
  CREATE USER 'jeffrey'@'localhost' PASSWORD EXPIRE;
  ```

- PASSWORD EXPIRE DEFAULT：设置该语句命名的所有帐户，以便应用[default_password_lifetime](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_password_lifetime) 系统策略指定的全局到期策略 。

  ```sql
  CREATE USER 'jeffrey'@'localhost' PASSWORD EXPIRE DEFAULT;
  ```

- PASSWORD EXPIRE NEVER：该到期选项将覆盖该语句命名的所有帐户的全局策略。对于每个密码，它都会禁用密码过期，以使密码永不过期。

  ```sql
  CREATE USER 'jeffrey'@'localhost' PASSWORD EXPIRE NEVER;
  ```

- PASSWORD EXPIRE INTERVAL N DAY：该到期选项将覆盖该语句命名的所有帐户的全局策略。对于每个密码，它将密码有效期设置为N几天。以下声明要求每180天更改一次密码：

  ```sql
  CREATE USER 'jeffrey'@'localhost' PASSWORD EXPIRE INTERVAL 180 DAY;
  ```

  

**密码重用选项**

可以根据密码更改次数，经过时间或两者兼而有之来限制密码重用。

支持以下选项

- PASSWORD REUSE INTERVAL DEFAULT：设置该帐户命名的所有语句，以便应用有关经过时间的全局策略，以禁止重复使用比password_reuse_interval 系统变量指定的天数新的密码 。

  ```sql
  CREATE USER 'jeffrey'@'localhost' PASSWORD REUSE INTERVAL DEFAULT;
  ```

- PASSWORD REUSE INTERVAL N DAY：这个经过时间的选项会覆盖该语句命名的所有帐户的全局策略。对于每个密码，它将密码重用间隔设置为N 几天，以禁止重用比该天数新的密码。以下语句禁止360天重复使用密码：

  ```sql
  CREATE USER 'jeffrey'@'localhost' PASSWORD REUSE INTERVAL 360 DAY
  ```



**密码验证所需的选项**

可以指示更改帐户密码的尝试是否必须指定当前密码，以验证尝试进行更改的用户实际上知道当前密码。

支持以下选项

- PASSWORD REQUIRE CURRENT：该验证选项将覆盖该语句命名的所有帐户的全局策略。对于每个密码，要求更改密码指定当前密码。

  ```sql
  CREATE USER 'jeffrey'@'localhost' PASSWORD REQUIRE CURRENT;
  ```

- PASSWORD REQUIRE CURRENT OPTIONAL：该验证选项将覆盖该语句命名的所有帐户的全局策略。对于每个密码，不需要更改密码即可指定当前密码。（可以但不必提供当前密码。）

  ```sql
  CREATE USER 'jeffrey'@'localhost' PASSWORD REQUIRE CURRENT OPTIONAL;
  ```

- PASSWORD REQUIRE CURRENT DEFAULT：设置该帐户命名的所有语句，以便应用password_require_current 系统密码指定的有关密码验证的全局策略 

  ```sql
  CREATE USER 'jeffrey'@'localhost' PASSWORD REQUIRE CURRENT DEFAULT;
  ```



**错误密码失败登录跟踪选项**

可以使服务器跟踪失败的登录尝试并临时锁定为其提供过多连续错误密码的帐户。所需的故障数量和锁定时间是可配置的。

支持以下选项

- FAILED_LOGIN_ATTEMPTS N

  是否跟踪指定错误密码的帐户登录尝试。*`N`*必须是0到32767之间的数字。0值将禁用登录失败跟踪。大于0的值表示有多少连续的密码失败导致临时帐户锁定（如果`PASSWORD_LOCK_TIME`也是非零的话）。

- PASSWORD_LOCK_TIME N

  连续多次登录尝试后锁定帐户多长时间提供了错误的密码。 *`N`*必须是0到32767之间的数字，或者`UNBOUNDED`。值为0将禁用临时帐户锁定。大于0的值表示锁定帐户的天数。值 `UNBOUNDED`导致帐户锁定期限不受限制；一旦锁定，该帐户将保持锁定状态，直到被解锁。有关发生解锁条件的信息，请参阅“ [失败登录跟踪和临时帐户锁定”](https://dev.mysql.com/doc/refman/8.0/en/password-management.html#failed-login-tracking)。



为了进行失败的登录跟踪和临时锁定，帐户`FAILED_LOGIN_ATTEMPTS`和 `PASSWORD_LOCK_TIME`选项都必须为非零。

以下语句创建一个帐户，该帐户在连续四次密码失败后仍保持锁定状态两天：

```sql
CREATE USER 'jeffrey'@'localhost'
  FAILED_LOGIN_ATTEMPTS 4 PASSWORD_LOCK_TIME 2;
```



##### `[lock_option]`

创建用户帐户锁定选项

MySQL支持使用`ACCOUNT LOCK`和`ACCOUNT UNLOCK`选项锁定和解锁帐户 ，这些选项指定帐户的锁定状态。有关更多讨论，请参见 [第6.2.19节“帐户锁定”](https://dev.mysql.com/doc/refman/8.0/en/account-locking.html)。

如果指定了多个帐户锁定选项，则最后一个优先。







## 创建角色

CREATE ROLE 语句

语法格式：

```sql
CREATE ROLE [IF NOT EXISTS] role [, role ] ...
```

[`CREATE ROLE`](https://dev.mysql.com/doc/refman/8.0/en/create-role.html)创建一个或多个角色，这些角色被称为特权集合。要使用此语句，您必须具有全局[`CREATE ROLE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-role)或[`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-user) 特权。当[`read_only`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_read_only) 系统变量启用时，[`CREATE ROLE`](https://dev.mysql.com/doc/refman/8.0/en/create-role.html)另外需要 [`CONNECTION_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_connection-admin)特权（或弃用[`SUPER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_super)特权）。

角色创建时是锁定的，没有密码并被分配默认身份验证插件。（[`ALTER USER`](https://dev.mysql.com/doc/refman/8.0/en/alter-user.html) 具有全局[`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-user)特权的用户可以在稍后使用该语句 更改这些角色属性。）

[`CREATE ROLE`](https://dev.mysql.com/doc/refman/8.0/en/create-role.html)要么对所有命名角色都成功，要么回滚，并且在发生任何错误时不起作用。默认情况下，如果您尝试创建一个已经存在的角色，则会发生错误。如果`IF NOT EXISTS` 给出了该子句，则该语句将为每个已经存在的命名角色生成一个警告，而不是一个错误。

如果成功，则将语句写入二进制日志；如果失败，则不写入语句。在这种情况下，将发生回滚并且不进行任何更改。写入二进制日志的语句包括所有命名角色。如果提供了该`IF NOT EXISTS`子句，则甚至包括已经存在但尚未创建的角色。

每个角色名称都使用[第6.2.5节“指定角色名称”中](https://dev.mysql.com/doc/refman/8.0/en/role-names.html)所述的格式 。例如：

```sql
CREATE ROLE 'administrator', 'developer';
CREATE ROLE 'webapp'@'localhost';
```

角色名称的主机名部分（如果省略）默认为 `'%'`。

有关角色用法的示例，请参见[第6.2.10节“使用角色”](https://dev.mysql.com/doc/refman/8.0/en/roles.html)



## 用户/角色授权

grant

语法格式:

```sql
GRANT
    priv_type [(column_list)]
      [, priv_type [(column_list)]] ...
    ON [object_type] priv_level
    TO user_or_role [, user_or_role] ...
    [WITH GRANT OPTION]
    [AS user
        [WITH ROLE
            DEFAULT
          | NONE
          | ALL
          | ALL EXCEPT role [, role ] ...
          | role [, role ] ...
        ]
    ]
}

GRANT PROXY ON user_or_role
    TO user_or_role [, user_or_role] ...
    [WITH GRANT OPTION]

GRANT role [, role] ...
    TO user_or_role [, user_or_role] ...
    [WITH ADMIN OPTION]
```

部分选项

```
object_type: {
    TABLE
  | FUNCTION
  | PROCEDURE
}

priv_level: {
    *
  | *.*
  | db_name.*
  | db_name.tbl_name
  | tbl_name
  | db_name.routine_name
}

user_or_role: {
    user (see Section 6.2.4, “Specifying Account Names”)
  | role (see Section 6.2.5, “Specifying Role Names”)
}
```



`SHOW PRIVILEGES;` 可查看所有的权限列表

`SHOW GRANTS;`可查看所有的WITH GRANT OPTION



例句

```sql
-- 给mc_class@192.168.1.%账号授予mysql库user表的user和host两列的查询权限
GRANT select(user, host) ON mysql.user to zhaod@'192.168.1.%';

-- 给mc_class@192.168.1.%账号授予mysql库user表的查询权限
GRANT select ON mysql.iser TO zhaod@'192.168.1.%';

-- 给mc_class@192.168.1.%账号授予mysql库所有表的查询和插入权限
GRANT select,insert on mysql.* TO zhaod@'192.168.1.%';
```



`注意事项`

1. MySQL8.0之后不允许使用GRANT命令创建账号（之前是可以的），8.0之后使用grant授权的数据库账号必须存在
2. 使用grant命令的用户必须具有grant option的对应权限，即只能把自己有权限的权限进行授权



下表总结了*`priv_type`*可以为[`GRANT`](https://dev.mysql.com/doc/refman/8.0/en/grant.html)and [`REVOKE`](https://dev.mysql.com/doc/refman/8.0/en/revoke.html)语句指定的允许的静态和动态特权类型 ，以及可以授予每个特权的级别。有关每个特权的更多信息，请参见 [第6.2.2节“ MySQL提供的特权”](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html)。有关静态特权和动态特权之间的区别的信息，请参阅 [静态特权与动态特权](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#static-dynamic-privileges)。



GRANT和REVOKE的允许静态特权

| 选项                                                         | 权限描述                                                     |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`ALL [PRIVILEGES]`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_all) | 除了[`GRANT OPTION`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_grant-option)和以外， 以指定的访问级别授予所有特权 [`PROXY`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_proxy)。 |
| [`ALTER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_alter) | 启用[`ALTER TABLE`](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html)。级别：全局，数据库，表。 |
| [`ALTER ROUTINE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_alter-routine) | 使存储的例程能够被更改或删除。级别：全局，数据库，常规。     |
| [`CREATE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create) | 启用数据库和表创建。级别：全局，数据库，表。                 |
| [`CREATE ROLE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-role) | 启用角色创建。级别：全球。                                   |
| [`CREATE ROUTINE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-routine) | 启用存储的例程创建。级别：全局，数据库。                     |
| [`CREATE TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-tablespace) | 启用要创建，更改或删除的表空间和日志文件组。级别：全球。     |
| [`CREATE TEMPORARY TABLES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-temporary-tables) | 启用[`CREATE TEMPORARY TABLE`](https://dev.mysql.com/doc/refman/8.0/en/create-table.html)。级别：全局，数据库。 |
| [`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-user) | 允许使用[`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/create-user.html)， [`DROP USER`](https://dev.mysql.com/doc/refman/8.0/en/drop-user.html)， [`RENAME USER`](https://dev.mysql.com/doc/refman/8.0/en/rename-user.html)，和 [`REVOKE ALL PRIVILEGES`](https://dev.mysql.com/doc/refman/8.0/en/revoke.html)。级别：全球。 |
| [`CREATE VIEW`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-view) | 启用视图的创建或更改。级别：全局，数据库，表。               |
| [`DELETE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_delete) | 启用[`DELETE`](https://dev.mysql.com/doc/refman/8.0/en/delete.html)。级别：全局，数据库，表。 |
| [`DROP`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_drop) | 启用要删除的数据库，表和视图。级别：全局，数据库，表。       |
| [`DROP ROLE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_drop-role) | 允许删除角色。级别：全球。                                   |
| [`EVENT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_event) | 为事件计划程序启用事件的使用。级别：全局，数据库。           |
| [`EXECUTE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_execute) | 使用户能够执行存储的例程。级别：全局，数据库，常规。         |
| [`FILE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_file) | 使用户能够使服务器读取或写入文件。级别：全球。               |
| [`GRANT OPTION`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_grant-option) | 允许授予其他帐户特权或从其他帐户中删除特权。级别：全局，数据库，表，例程，代理。 |
| [`INDEX`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_index) | 启用要创建或删除的索引。级别：全局，数据库，表。             |
| [`INSERT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_insert) | 启用[`INSERT`](https://dev.mysql.com/doc/refman/8.0/en/insert.html)。级别：全局，数据库，表，列。 |
| [`LOCK TABLES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_lock-tables) | 启用[`LOCK TABLES`](https://dev.mysql.com/doc/refman/8.0/en/lock-tables.html)对您拥有[`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html) 特权的on表的使用。级别：全局，数据库。 |
| [`PROCESS`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_process) | 使用户可以使用查看所有进程[`SHOW PROCESSLIST`](https://dev.mysql.com/doc/refman/8.0/en/show-processlist.html)。级别：全球。 |
| [`PROXY`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_proxy) | 启用用户代理。级别：从一个用户到另一个用户。                 |
| [`REFERENCES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_references) | 启用外键创建。级别：全局，数据库，表，列。                   |
| [`RELOAD`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_reload) | 启用[`FLUSH`](https://dev.mysql.com/doc/refman/8.0/en/flush.html)操作的使用。级别：全球。 |
| [`REPLICATION CLIENT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_replication-client) | 使用户能够询问源服务器或副本服务器在哪里。级别：全球。       |
| [`REPLICATION SLAVE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_replication-slave) | 使副本能够从源读取二进制日志事件。级别：全球。               |
| [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_select) | 启用[`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html)。级别：全局，数据库，表，列。 |
| [`SHOW DATABASES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_show-databases) | 启用[`SHOW DATABASES`](https://dev.mysql.com/doc/refman/8.0/en/show-databases.html)以显示所有数据库。级别：全球。 |
| [`SHOW VIEW`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_show-view) | 启用[`SHOW CREATE VIEW`](https://dev.mysql.com/doc/refman/8.0/en/show-create-view.html)。级别：全局，数据库，表。 |
| [`SHUTDOWN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_shutdown) | 启用使用[**mysqladmin shutdown的功能**](https://dev.mysql.com/doc/refman/8.0/en/mysqladmin.html)。级别：全球。 |
| [`SUPER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_super) | 能够使用如其他行政业务 [`CHANGE REPLICATION SOURCE TO`](https://dev.mysql.com/doc/refman/8.0/en/change-replication-source-to.html)，[`CHANGE MASTER TO`](https://dev.mysql.com/doc/refman/8.0/en/change-master-to.html)，[`KILL`](https://dev.mysql.com/doc/refman/8.0/en/kill.html)， [`PURGE BINARY LOGS`](https://dev.mysql.com/doc/refman/8.0/en/purge-binary-logs.html)， [`SET GLOBAL`](https://dev.mysql.com/doc/refman/8.0/en/set-variable.html)，和[**中mysqladmin调试**](https://dev.mysql.com/doc/refman/8.0/en/mysqladmin.html)命令。级别：全球。 |
| [`TRIGGER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_trigger) | 启用触发器操作。级别：全局，数据库，表。                     |
| [`UPDATE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_update) | 启用[`UPDATE`](https://dev.mysql.com/doc/refman/8.0/en/update.html)。级别：全局，数据库，表，列。 |
| [`USAGE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_usage) | “无特权”的同义词                                             |





## 回收权限

REVOKE

`使用方式大致与 GRANT 命令相同`

语法格式：

```sql
REVOKE
    priv_type [(column_list)]
      [, priv_type [(column_list)]] ...
    ON [object_type] priv_level
    FROM user_or_role [, user_or_role] ...

REVOKE ALL [PRIVILEGES], GRANT OPTION
    FROM user_or_role [, user_or_role] ...

REVOKE PROXY ON user_or_role
    FROM user_or_role [, user_or_role] ...

REVOKE role [, role ] ...
    FROM user_or_role [, user_or_role ] ...
```

部分选项

```
user_or_role: {
    user (see Section 6.2.4, “Specifying Account Names”)
  | role (see Section 6.2.5, “Specifying Role Names”.
}
```







