# CMD


## ***一、存储数据的演变过程***

1.  *随意的存到一个文件中、数据格式也是千差万别的完全取决于我们自己*
    
    ```python
    # 小李
    jason|123|NB
    # 小王
    egon-123-DBJ
    # 小红
    tank~123~hecha
    ```
2.  *软件开发目录规范*
    *限制了存储数据的具体位置*
    
    ```python
    bin
    conf
    core
    db
    lib
    readme
    ```
3.  *如何将单机变成连网*
    
    *假设上述是一个单机游戏，那么每个人的游戏记录只会在自己的计算机上面保存，注册登录的账号也只能在自己的计算机上有效 这样是不行的*
    
    *所以我们可以， 将数据库保存部分全部统一起来，所有人操作数据都来一个地方操作*

## ***二、数据库的本质***

### ***数据库的本质与分类***

*数据库本质上就是一款基于网络通信的应用程序。*

*这也意味着数据库软件其实有很多很多，主要可以分为两大类：*

*   ***关系型数据库 (Relational Database)***
    *   *`MySQL`、`Oracle`、`DB2`、`Access`、`SQL Server`*
*   ***非关系型数据库 (Non-Relational Database / NoSQL)***
    *   *`Redis`、`MongoDB`、`Memcached`*

---

### ***关系型数据库的特点***

1.  ***数据之间彼此有关系或约束***
    
    *   *例如：`男生表` 和 `前女友表` 之间可以通过某个字段建立关联。*
    
2.  ***以表格（Table）的形式存储数据***
    
    *   *数据以行和列的结构进行组织。*
    
    ***示例表格:***
    
    | *name*  | *password* | *hobby*  |
    | :------ | :--------- | :------- |
    | *jason* | *123*      | *学习*   |
    | *egon*  | *123321*   | *女教练* |
    | *tank*  | *12323*    | *吃生蚝* |
    
3.  ***字段有严格的类型限制***
    
    *   *例如，姓名字段（`name`）只能存储字符串类型的数据，年龄字段只能存储数字等。*

---

### ***非关系型数据库的特点***

*   ***以键值对（Key-Value）的形式存储数据***

## ***三、MYSQL***

### ***管理系统 (Management System)***

*MySQL 不仅仅是一个数据仓库，它更是一个功能强大的**“图书管理员”**。这个“管理员”负责：*

*   ***定义数据结构**: 规定每张表有哪些列，每个列应该存什么类型的数据（数字、文字、日期等）。*
*   ***数据操作 (CRUD)***
    *   ***增 (Create)**: 往表里添加新的记录*
    *   ***查 (Read/Retrieve)**: 按照各种条件快速查询想要的数据。*
    *   ***改 (Update)**: 修改表里已有的数据。*
    *   ***删 (Delete)**: 删除表里的数据。*
*   ***用户和权限管理**: 控制谁可以访问数据库，谁有权限进行读写操作。*
*   ***数据备份与恢复**: 防止数据丢失。*
*   ***保证数据一致性和完整性**: 确保存入的数据是有效、不矛盾的。*

---

### ***为什么 MySQL 如此流行？***

1.  ***开源免费**: 个人和大部分企业可以免费使用，大大降低了成本。*
2.  ***性能卓越**: 读写速度快，能够处理大量并发请求。*
3.  ***稳定可靠**: 经过了长时间和大规模应用的考验，非常成熟稳定。*
4.  ***跨平台**: 可以在 Windows, Linux, macOS 等多种操作系统上运行。*
5.  ***社区强大**: 拥有庞大的开发者社区，遇到问题很容易找到解决方案和文档。*
6.  ***生态系统丰富**: 有大量的第三方工具、库和框架支持，开发非常方便。*

### ***总结***

*简单来说，**MySQL 就是一个功能强大、免费开源的“数据管家”，它使用表格来整理数据，我们通过 SQL 语言来指挥它完成数据的存储、查询和管理工作。** 它是当今世界上最受欢迎的数据库之一，是构建网站、App 和各种软件系统的基石。*

## ***四、重要概念介绍***

*任何基于网络通信的应用程序底层用的都是socket。*

*   ***服务端***
    *   *基于socket通信*
    *   *收发消息*
    *   *SQL语句*
*   ***客户端***
    *   *基于socket通信*
    *   *收发消息*
    *   *SQL语句*

---

*MySQL不单单支持MySQL自己的客户端app，还支持其他编程语言来充当客户端操作。*

*如何解决语言沟通的障碍？*

1.  *让服务端兼容所有的语言（一个人精通多国语言）。*
2.  *采用统一的语言（SQL语句）。*

---

*   *库            >>>             文件夹*
*   *表            >>>             文件*
*   *记录         >>>            文件内一行行的数据*

| *name*  | *password* | *hobby*  |
| :------ | :--------- | :------- |
| *jason* | *123*      | *学习*   |
| *egon*  | *123*      | *女教练* |
| *tank*  | *123*      | *吃生蚝* |

*   ***表头***
    *   *表格的第一行*
*   ***字段***
    *   *`name`、`password`、`hobby`*

## ***五、MYSQL的安装***

在IT界，一般都不会轻易地使用最新版本的软件，因为新版本可能会出现各种问题（你原本项目跑得好好的，非要画蛇添足更新版本，然后项目崩溃）。

> ***小段子：***
>
> *更新完没事，那么你还是一个普通员工。*
> *更新完出事，那么你就是一名“烈士”。*

---

*截止到本博客 MySQL有很多版本（5.6、5.7、8.0），目前企业里面用得比较多的还是5.6左右。*

*参考网站：https://www.mysql.com/*

*下载5.6版本即可，如果你下载了其他版本问题也不大，因为SQL语句是一样的。*

*按照教学方式下载，会将服务端和客户端一并下载到本地。*

*为了学习方便，我们将服务端和客户端都在本地启动，后期到了公司，服务端会专门跑在一台服务器上，所有人基于网络连接服务端操作。*

## ***六、MYSQL服务端与客户端***

### ***exe 文件名***

*   ***服务端**: `mysqld.exe`*
*   ***客户端**: `mysql.exe`*

---

### ***注意***

> *在前期配置MySQL的时候，cmd终端尽量以管理员的身份运行。*
> *`windows+r` 输入`cmd` 进入的是普通用户终端，有一些命令是无法执行的。*
> *应该搜索`cmd`，然后右键以管理员身份运行。*

---

### ***启动***

*先切换到`mysqld`所在的`bin`目录下，然后输入`mysqld`即可。*

*保留原来的cmd窗口，重新打开一个。*

---

### ***常见软件的默认端口号***

*   *`MySQL`: 3306*
*   *`redis`: 6379*
*   *`mongodb`: 27017*
*   *`django`: 8000*
*   *`flask`: 5000*

*MySQL第一次以管理员身份进入是没有密码的，直接回车即可。*

***客户端连接服务端完整命令***

```bash
mysql -h 127.0.0.1 -P 3306 -uroot -p
```

## ***七、MYSQL语句初识***

1.  *MySQL结束是以分号(`;`)为标志。*

2.  *基本命令*
    
    ```sql
    show databases;  -- 查看全部的库名
    ```
    *(注：原文为`#`，在SQL标准中`--`是更通用的注释符)*
    
3.  *MySQL连接精简版命令*
    
    ```bash
    mysql -uroot -p
    ```
    *省去了默认ip和端口*
    
4.  *当你输入的命令不对的时候，不想让他继续执行和报错，输入`\c`即可(不用分号)。*

5.  *客户端退出*
    
    *   `quit`
    *   `exit`
    
    *不用加分号*
    
6.  *当你在连接服务端的时候，直接输入`mysql`，就可以登录进去，但是是游客模式，只会给你展示一部分内容。*

## ***八、环境变量&系统服务制作***

### ***Windowes 设置环境变量***

1.  *找到MySQL的`bin`文件夹绝对路径。*
2.  *右键“我的电脑”，点击“属性”，再点击“高级系统设置”，然后点击“环境变量”。*
3.  *在系统变量中找到`path`，双击它，新建一个条目，把`bin`文件夹的绝对路径输入进去。*
4.  *设置完成后，就可以直接在cmd中输入`mysqld`了，不用再切换路径。*

### ***安装为Windows服务***

1.  *环境变量弄好后，以管理员身份运行cmd，输入以下命令：*
    ```bash
    mysqld --install
    ```
2.  *如果显示 `Service successfully installed` 就表示成功了。*
3.  *这时候再打开任务管理器，点击“服务”，找到`mysql`，右键点击“开始”。*
4.  *完成以上步骤后，MySQL服务端就被设置成了开机自动启动的服务。*
5.  *之后，直接打开cmd连接客户端就好了。*

#### ***小知识点补充—如何查看当前进程&如何杀死当前进程***

```python
1.如何查看当前进程    
	tasklist |findstr mysql
2.如何杀死进程    
	taskkill /F /PID PID号
```

## ***九、设置密码***

```
"""mysqladmin -uroot -p 原密码 password 新密码直接在cmd输入 不用打开mysql例如 mysqladmin -uroot -p 123 password 123456"""
```

## ***十、重置密码***

```
"""你可以将mysql获取用户名和密码校验的功能看成是一个装饰器装饰在了客户端请求访问的功能上我们如果将该装饰器移除 那么mysql服务端就不会校验用户名和密码了"""# 1 先关闭当前mysql服务端 命令行的方式启动(让mysql跳过用户名密码验证功能) mysqld --skip-grant-tables  # 2 直接以无密码的方式连接    mysql -uroot -p   直接回车# 3 修改当前用户的密码    update mysql.user set password=password(123456) where         user='root' and host='localhost';"""真正存储用户表的密码字段 存储的肯定是密文 只有用户自己知道明文是什么 其他人都不知道 这样更加的安全密码比对也只能比对密文"""# 4 立刻将修改数据刷到硬盘    flush privileges;# 5 关闭当前服务端 然后以正常校验授权表的形式启动
```

## ***十一、MYSQL配置文件修改—–统一编码***

```
"""我们来打开my-default.ini的文件，ini为结尾的文件一般都是配置文件，程序启动会先加载配置文件在真正启动"""[mysqld]  # 一旦服务端启动立刻加载下面的配置sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES [mysql]  # 一旦客户端启动立刻加载下面的配置...[client]  # 其他客户端...# 我们来新建一个ini文件------my.ini# 我们在这里来写个同意编码的文件，然后我们还可以偷个懒，把用户名和密码也写进去，实现自动登录，代码如下    [mysqld]    character-set-server=utf8    collation-server=utf8_general_ci    [client]    default-character-set=utf8    [mysql]    user="root"    password=123456    default-character-set=utf8# 我们修改后 要去重启服务端，然后再去登陆
```

## ***十二、MYSQL的基本语句***

### ***12.1 库的增删改查(文件夹)***

```python
# 增    create database ame;    create database yuki charset='gbk';# 查    show database;    show create database yuki;# 改    alter database ame charset='gbk';# 删    drop database ame;
```

### ***12.2 表的增删改查(文件)***

```
# 查看当前库所在    select database();# 切换库    use ame;# 增    create table t1(id int, name char(4));  # 里面4的意思就是最大数据4个# 查    show tables  # 查看所有表    show create table t1  # 查看t1表    describe t1;  # 查看t1表 简写 desc t1# 改     alter table t1 modify name char(16);# 删    drop table t1;"""create table ame.t1(id int, name char(16));  使用绝对路径也是可以增加的，用来操做不同的库"""
```

### ***12.3 数据的增删改查***

```
# 增    insert into t1 values(1, 'Ame');    insert into t1 values(2, 'yuki'),(3, 'ori');# 查    select * from t1;  # 查看所有 数据特别大不建议用    select name from t1;  # 查看name# 改    update t1 set name='DSB' where id > 1;# 删    delete from t1 where id > 1;    delete from t1 where name='Ame';# 将表所有的数据清空    delete from t1;
```

## ***十三、存储引擎***

```
"""MySQL 主要的存储引擎    1.Innodb        是MySQL5.5版本机制后默认的存储引擎        存储数据更加的安全    2. myisam        是MySQL5.5版本之前默认的存储引擎        速度要比Innodb更快 但是我们更加注重的是数据的安全    3.memory        内存引擎(数据全部存放在内存中)断电数据丢失    4.blackhole        无论存什么，都立刻消失(黑洞)""""""# 查看所有的存储引擎    show engines;# 不同的存储引擎在存储表的时候 异同点    create table t1(id int) engine=innodb;    create table t2(id int) engine=myisam;    create table t3(id int) engine=blackhole;    create table t4(id int) engine=memory;# 存数据    insert into t1 values(1);    insert into t2 values(1);    insert into t3 values(1);    insert into t4 values(1);"""
```

## ***十四、创建表的完整语法***

```
# 语法create table 表名(    字段名1 类型(宽度) 约束条件,    字段名2 类型(宽度) 约束条件,    字段名3 类型(宽度) 约束条件)# 注意1 在同一张表中字段名不能重复2 宽度和约束条件是可选的(可写可不写) 而字段名和字段类型是必须的    约束条件写的话 也支持写多个    字段名1 类型(宽度) 约束条件1 约束条件2...,    create table t5(id);  报错3 最后一行不能有逗号    create table t6(        id int,        name char,    );   报错"""补充"""# 宽度    一般情况下指的是对存储数据的限制    create table t7(name char);  默认宽度是1    insert into t7 values('jason');    insert into t7 values(null);  关键字NULL        针对不同的版本会出现不同的效果            5.6版本默认没有开启严格模式 规定只能存一个字符你给了多个字符，那么我会自动帮你截取            5.7版本及以上或者开启了严格模式 那么规定只能存几个 就不能超，一旦超出范围立刻报错 Data too long for ...."""严格模式到底开不开呢？"""MySQL5.7之后的版本默认都是开启严格模式的使用数据库的准则:    能尽量少的让数据库干活就尽量少 不要给数据库增加额外的压力# 约束条件 null  not null不能插入nullcreate table t8(id int,name char not null);"""宽度和约束条件到底是什么关系    宽度是用来限制数据的存储    约束条件是在宽度的基础之上增加的额外的约束"""
```

## ***十五、基本数据类型***

### ***15.1 整形***

```
1.分类     tinyint smallint meduimint int bigint2.作用    存储年龄 等级 id 号码..."""以tinyint    是否有符号        默认情况下是带符号的    超出会如何        超出限制只存最大可接受值"""create table t9(id tinyint);insert into t9 values(-129),(256);# 约束条件之unsigned 无符号create table t10(id tinyint unsigned);# int默认也是带符号的# 整形默认情况下都是带有符号的create table t11(id int);# 针对整形 括号内的宽度到底是干嘛的create table t12(id int(8));insert into t12 values(123456789)"""特例：只有整形括号里面的数字不是表示限制位数id int(8)    如果数字没有超出8位 那么默认用空格填充至8位    如果数字超出了8位 那么有几位就存几位(但是还要是遵守最大范围)"""create table t13(id int(8) unsigned zerofill);# 用0填充到8位# 总结    针对整形字段 括号内无需指定宽度 因为他默认的宽度以及足够显示所有的数据了
```

### ***15.2 严格模式(小插曲)***

```
# 如何查看严格模式show variables like "%mode";模糊匹配/查询    关键字 like        %:匹配任意多个字符        _:匹配任意单个字符# 修改严格模式    set session  只在当前窗口有效    set global   全局有效        set global sql_mode = 'STRICT_TRANS_TABLES';        修改完之后 重新进入服务端即可
```

### ***15.3 浮点型***

```
1.分类    FLOAT、DOUBLE、DECIMAL2.作用    身高、体重、薪资# 存储限制float(255,30)  # 总共255位 小数部分占30位double(255,30)  # 总共255位 小数部分占30位decimal(65,30)  # 总共65位 小数部分占30位# 精确度验证create table t15(id float(255,30));create table t16(id double(255,30));create table t17(id decimal(65,30));"""你们在前期不要给我用反向键 所有的命令全部手敲！！！增加熟练度"""insert into t15 values(1.111111111111111111111111111111);insert into t16 values(1.111111111111111111111111111111);insert into t17 values(1.111111111111111111111111111111);float < double < decimal# 要结合实际应用场景 三者都能使用
```

### ***15.4 字符类型***

```
1.分类   """    char        定长        char(4)     数据超过四个字符直接报错 不够四个字符空格补全    varchar        变长        varchar(4)  数据超过四个字符直接报错 不够有几个存几个    """    create table t18(name char(4));    create table t19(name varchar(4));    insert into t18 values('a');    insert into t19 values('a');# 介绍一个小方法 char_length统计字段长度    select char_length(name) from t18;    select char_length(name) from t19;    """    首先可以肯定的是 char硬盘上存的绝对是真正的数据 带有空格的    但是在显示的时候MySQL会自动将多余的空格剔除    """# 再次修改sql_mode 让MySQL不要做自动剔除操作    set global sql_mode = 'STRICT_TRANS_TABLES,PAD_CHAR_TO_FULL_LENGTH';    # char 与 varchar对比    """    char        缺点:浪费空间        优点:存取都很简单            直接按照固定的字符存取数据即可            jason egon alex wusir tank             存按照五个字符存 取也直接按照五个字符取    varchar        优点:节省空间        缺点:存取较为麻烦            1bytes+jason 1bytes+egon 1bytes+alex 1bytes+tank             存的时候需要制作报头            取的时候也需要先读取报头 之后才能读取真实数据    以前基本上都是用的char 其实现在用varchar的也挺多    """补充:    进来公司之后你完全不需要考虑字段类型和字段名    因为产品经理给你发的邮件上已经全部指明了
```

### ***15.5 时间类型***

```
1.分类    date:年月日 2020-5-4    datetime:年月日时分秒  2020-5-4 11:11:11    time:时分秒 11:11:11    Year:年 2020    2.具体使用    create table student(        id int,        name varchar(16),        born_year year,        birth date,        study_time time,        reg_time datetime    );    insert into student values(1,'egon','1880','1880-11-11','11:11:11','2020-11-11 11:11:11');
```

### ***15.6 枚举与集合类型***

```
1.分类    """    枚举(enum)  多选一    集合(set)   多选多    """    2.具体使用    create table user(        id int,        name char(16),        gender enum('male','female','others')    );    insert into user values(1,'jason','male');  正常    insert into user values(2,'egon','xxxxooo');  报错# 枚举字段 后期在存数据的时候只能从枚举里面选择一个存储     create table teacher(        id int,        name char(16),        gender enum('male','female','others'),        hobby set('read','DBJ','hecha')    );    insert into teacher values(1,'jason','male','read');  正常    insert into teacher values(2,'egon','female','DBJ,hecha');  正常    insert into teacher values(3,'tank','others','生蚝'); 报错# 集合可以只写一个  但是不能写没有列举的
```

## ***十六、约束条件***

### ***16.1 DEFAULT 默认值***

```
default 默认值    补充知识点 插入数据的时候可以指定字段    create table t1(        id int,        name char(16)    );    insert into t1(name,id) values('ame',1)        create table t2(        id int,        name char(16),        gender enum('male','female','others')default 'male'    );    insert into t2(id, name) values(1,'jason');    insert into t2 values(2, 'egon', 'female');    
```

### ***16.2 UNIQUE唯一***

```
unique唯一    # 单列唯一    create table t3(        id int unique,        name char(16)    );    insert into t3 values(1,'jason'),(1,'egon');    insert into t3 values(1,'jason'),(2,'egon');        # 联合唯一    """    ip和port    单个都可以重复 但是加在一起必须是唯一的    """    create table t4(        id int,        ip char(16),        port int,        unique(ip,port)    );    insert into t4 values(1,'127.0.0.1',8080);    insert into t4 values(2,'127.0.0.1',8081);    insert into t4 values(3,'127.0.0.2',8080);    insert into t4 values(4,'127.0.0.1',8080);  报错
```

### ***16.3 PRIMARY KEY 主键***

```
"""1.单单从约束效果上来看primary key等价于not null + unique非空且唯一!!!"""    create table t5(id int primary key);    insert into t5 values(null); 报错    insert into t5 values(1),(1); 报错    insert into t5 values(1),(2);"""2.它除了有约束效果之外 他还是Innodb存储引擎组织数据的依据Innodb存储引擎在创建表的时候必须要有primary key因为它类似于书的目录 能够帮助提示查询效率并且也是建表的依据"""    2.1 一张表中有且只有一个主键 如果你没有设置主键 那么会从上往下搜索直到遇到一个非空且唯一的字段将他自动升级为主键        create table t6(            id int,            name char(16),            age int not null unique,            addr char(32) not null unique        );    2.2 如果表中没有主键也没有其他任何和的非空且唯一的字段 那么Innodb会采用自己内部提供的一个隐藏字段作为主键，隐藏意味着你无法使用到它 就无法提示查询速度        3.3 一张表中通常都应该有一个主键字段 并且通常将id/uid/sid字段作为主键        单个字段主键            create table t5(                id int primary key                name char(16)            );        联合主键(多个字段联合起来作为表的主键 本质还是一个主键)             create table t7(                    ip char(16),                    port int,                    primary key(ip, port)                );    """    也意味着 以后我们在创建表的时候id字段一定要加primary key    """
```

### ***16.4 AUTO_INCREMENT 自增***

```
# 当编号特别多的时候 人为的去维护太麻烦    create table t8(        id int primary key auto_increment,        name char(16)    );    insert into t8(name) values('jason'),('egon'),('kevin');# 注意auto_increment通常都是加在主键上的 不能给普通字段加    create table t9(        id int primary key auto_increment,        name char(16),        cid int auto_increment    );    ERROR 1075 (42000): Incorrect table definition; there can be only one auto column and it must be defined as a     key
```

## ***十七、表与表之间建关系***

### ***17.1 问题引出***

```
"""定义一张员工表 表中有很多字段id name gender dep_name dep_desc"""# 1 该表的组织结构不是很清晰(可忽视)# 2 浪费硬盘空间(可忽视)# 3 数据的扩展性极差(无法忽视的)# 如何优化？"""上述问题就类似于你将所有的代码都写在了一个py文件中"""将员工表拆分  员工表和部门表
```

### ***17.2 外键***

```
"""外键就是用来帮助我们建立表与表之间关系的foreign key"""
```

### ***17.3 表关系***

```
"""表与表之间最多只有四种关系    一对多关系        在MySQL的关系中没有多对一一说        一对多 多对一 都叫一对多！！！    多对多关系    一对一关系    没有关系"""
```

### ***17.4 一对多关系***

```
"""判断表与表之间关系的时候 前期不熟悉的情况下 一定要按照我给你的建议换位思考  分别站在两张表的角度考虑员工表与部门表为例    先站在员工表        思考一个员工能否对应多个部门(一条员工数据能否对应多条部门数据)            不能！！！            (不能直接得出结论 一定要两张表都考虑完全)    再站在部门表        思考一个部门能否对应多个员工(一个部门数据能否对应多条员工数据)            能！！！    得出结论        员工表与部门表示单向的一对多        所以表关系就是一对多"""foreign key    1 一对多表关系   外键字段建在多的一方    2 在创建表的时候 一定要先建被关联表     3 在录入数据的时候 也必须先录入被关联表# SQL语句建立表关系create table dep(    id int primary key auto_increment,    dep_name char(16),    dep_desc char(32));create table emp(    id int primary key auto_increment,    name char(16),    gender enum('male','female','others') default 'male',    dep_id int,    foreign key(dep_id) references dep(id));insert into dep(dep_name,dep_desc) values('教学部','教书育人'),('外交部','多人外交'),('nb技术部','技术能力有限部门');insert into emp(name,dep_id) values('jason',2),('egon',1),('tank',1),('kevin',3);# 修改dep表里面的id字段update dep set id=200 where id=2;  不行# 删除dep表里面的数据delete from dep;  不行# 1 先删除教学部对应的员工数据 之后再删除部门    操作太过繁琐    # 2 真正做到数据之间有关系    更新就同步更新    删除就同步删除"""级联更新   >>>   同步更新级联删除   >>>   同步删除"""create table dep(    id int primary key auto_increment,    dep_name char(16),    dep_desc char(32));create table emp(    id int primary key auto_increment,    name char(16),    gender enum('male','female','others') default 'male',    dep_id int,    foreign key(dep_id) references dep(id)     on update cascade  # 同步更新    on delete cascade  # 同步删除);insert into dep(dep_name,dep_desc) values('sb教学部','教书育人'),('外交部','多人外交'),('nb技术部','技术能力有限部门');insert into emp(name,dep_id) values('jason',2),('egon',1),('tank',1),('kevin',3);
```

### ***17.5 多对多表关系***

```
"""图书表和作者表"""create table book(    id int primary key auto_increment,    title varchar(32),    price int,    author_id int,    foreign key(author_id) references author(id)     on update cascade  # 同步更新    on delete cascade  # 同步删除);create table author(    id int primary key auto_increment,    name varchar(32),    age int,    book_id int,    foreign key(book_id) references book(id)     on update cascade  # 同步更新    on delete cascade  # 同步删除);"""按照上述的方式创建 一个都别想成功！！！其实我们只是想记录书籍和作者的关系针对多对多字段表关系 不能在两张原有的表中创建外键需要你单独再开设一张 专门用来存储两张表数据之间的关系"""create table book(    id int primary key auto_increment,    title varchar(32),    price int);create table author(    id int primary key auto_increment,    name varchar(32),    age int);create table book2author(    id int primary key auto_increment,    author_id int,    book_id int,    foreign key(author_id) references author(id)     on update cascade  # 同步更新    on delete cascade,  # 同步删除    foreign key(book_id) references book(id)     on update cascade  # 同步更新    on delete cascade  # 同步删除);
```

### ***17.6 一对一***

```
"""id name age addr phone hobby email........如果一个表的字段特别多 每次查询又不是所有的字段都能用得到将表一分为二      用户表        用户表            id name age        用户详情表            id addr phone hobby email........        站在用户表        一个用户能否对应多个用户详情   不能！！！    站在详情表        一个详情能否属于多个用户      不能！！！    结论:单向的一对多都不成立 那么这个时候两者之间的表关系        就是一对一        或者没有关系(好判断)客户表和学生表    在你们报名之前你们是客户端    报名之后是学生(期间有一些客户不会报名)"""一对一 外键字段建在任意一方都可以 但是推荐你建在查询频率比较高的表中create table authordetail(    id int primary key auto_increment,    phone int,    addr varchar(64));create table author(    id int primary key auto_increment,    name varchar(32),    age int,    authordetail_id int unique,    foreign key(authordetail_id) references authordetail(id)     on update cascade  # 同步更新    on delete cascade  # 同步删除)
```

### ***17.7 总结***

```
"""表关系的建立需要用到foreign key    一对多        外键字段建在多的一方    多对多        自己开设第三张存储    一对一        建在任意一方都可以 但是推荐你建在查询频率较高的表中判断表之间关系的方式    换位思考！！！        员工与部门            图书与作者            作者与作者详情"""
```

### ***17.8 修改表***

```
# MySQL对大小写是不敏感的"""1 修改表名    alter table 表名 rename 新表名;2 增加字段    alter table 表名 add 字段名 字段类型(宽度)  约束条件;    alter table 表名 add 字段名 字段类型(宽度)  约束条件 first;    alter table 表名 add 字段名 字段类型(宽度)  约束条件 after 字段名;3 删除字段    alter table 表名 drop 字段名;4 修改字段    alter table 表名 modify 字段名 字段类型(宽度) 约束条件;        alter table 表名 change 旧字段名 新字段名 字段类型(宽度) 约束条件;    """
```

### ***17.9 复制表***

```
"""我们sql语句查询的结果其实也是一张虚拟表"""create table 表名 select * from 旧表;  不能复制主键 外键 ...create table new_dep2 select * from dep where id>3;
```

## ***十八、前期表准备***

```
create table emp(  id int not null unique auto_increment,  name varchar(20) not null,  sex enum('male','female') not null default 'male', #大部分是男的  age int(3) unsigned not null default 28,  hire_date date not null,  post varchar(50),  post_comment varchar(100),  salary double(15,2),  office int, #一个部门一个屋子  depart_id int);#插入记录#三个部门：教学，销售，运营    insert into emp(name,sex,age,hire_date,post,salary,office,depart_id) values    ('jason','male',18,'20170301','张江第一帅形象代言',7300.33,401,1), #以下是教学部    ('tom','male',78,'20150302','teacher',1000000.31,401,1),    ('kevin','male',81,'20130305','teacher',8300,401,1),    ('tony','male',73,'20140701','teacher',3500,401,1),    ('owen','male',28,'20121101','teacher',2100,401,1),    ('jack','female',18,'20110211','teacher',9000,401,1),    ('jenny','male',18,'19000301','teacher',30000,401,1),    ('sank','male',48,'20101111','teacher',10000,401,1),    ('哈哈','female',48,'20150311','sale',3000.13,402,2),#以下是销售部门    ('呵呵','female',38,'20101101','sale',2000.35,402,2),    ('西西','female',18,'20110312','sale',1000.37,402,2),    ('乐乐','female',18,'20160513','sale',3000.29,402,2),    ('拉拉','female',28,'20170127','sale',4000.33,402,2),    ('僧龙','male',28,'20160311','operation',10000.13,403,3), #以下是运营部门    ('程咬金','male',18,'19970312','operation',20000,403,3),    ('程咬银','female',18,'20130311','operation',19000,403,3),    ('程咬铜','male',18,'20150411','operation',18000,403,3),    ('程咬铁','female',18,'20140512','operation',17000,403,3);# 当表字段特别多 展示的时候错乱 可以使用\G分行展示    select * from emp\G;# 个别同学的电脑在插入中文的时候还是会出现乱码或者空白的现象 你可以将字符编码统一设置成GBK
```

## ***十九、几个重要关键字的执行顺序***

```
# 书写顺序    select id,name from emp where id > 3;# 执行顺序    from    where    select    """    虽然执行顺序和书写顺序不一致 你在写sql语句的时候可能不知道怎么写    你就按照书写顺序的方式写sql        select * 先用*号占位        之后去补全后面的sql语句        最后将*号替换后你想要的具体字段         这里先理解    """
```

## ***二十、WHERE筛选条件***

```
# 作用:是对整体数据的一个筛选操作# 1.查询id大于等于3小于等于6的数据    select id,name,age from emp where id>=3 and id<=6;    select id,name from emp where id between 3 and 6;  两者等价# 2.查询薪资是20000或者18000或者17000的数据    select * from emp where salary=20000 or salary=18000 or salary=17000;    select * from emp where salary in (20000,18000,17000);# 3.查询员工姓名中包含字母o的员工的姓名和薪资    """    模糊查询        like            %  匹配任意多个字符            _  匹配任意单个字符    """    select name,salary from emp where name like '%o%';# 4.查询员工姓名是由四个字符组成的 姓名和薪资  char_length()   _    select name,salary from emp where name like '____';    select name,salary from emp where char_length(name) = 4;# 5.查询id小于3或者id大于6的数据    select * from emp where id not between 3 and 6;# 6.查询薪资不在20000,18000,17000范围的数据    select * from emp where salary not in (20000,18000,17000);# 7.查询岗位描述为空的员工姓名和岗位名  针对null不用等号 用is    select name,post from emp where post_comment = NULL;    select name,post from emp where post_comment is NULL;
```

## ***二十一、GROUP BY分组***

```
# 分组实际应用场景  分组应用场景非常的多    男女比例    部门平均薪资    部门秃头率    国家之间数据统计# 1    按照部门分组    select * from emp group by post;    """    分组之后 最小可操作单位应该是组 还不再是组内的单个数据        上述命令在你没有设置严格模式的时候是可正常执行的 返回的是分组之后 每个组的第一条数据 但是这不符合分组的规范:分组之后不应该考虑单个数据 而应该以组为操作单位(分组之后 没办法直接获取组内单个数据)        如果设置了严格模式 那么上述命令会直接报错     """    set global sql_mode = 'strict_trans_tables,only_full_group_by';    设置严格模式之后  分组 默认只能拿到分组的依据    select post from emp group by post;      按照什么分组就只能拿到分组 其他字段不能直接获取 需要借助于一些方法(聚合函数)"""什么时候需要分组啊？？？    关键字         每个 平均 最高 最低         聚合函数        max        min        sum        count        avg"""# 1.获取每个部门的最高薪资    select post,max(salary) from emp group by post;    select post as '部门',max(salary) as '最高薪资' from emp group by post;    select post '部门',max(salary) '最高薪资' from emp group by post;# as可以给字段起别名 也可以直接省略不写 但是不推荐 因为省略的话语意不明确 容易错乱# 2.获取每个部门的最低薪资    select post,min(salary) from emp group by post;# 3.获取每个部门的平均薪资select post,avg(salary) from emp group by post;# 4.获取每个部门的工资总和    select post,sum(salary) from emp group by post;# 5.获取每个部门的人数    select post,count(id) from emp group by post;  # 常用 符合逻辑    select post,count(salary) from emp group by post;    select post,count(age) from emp group by post;    select post,count(post_comment) from emp group by post;  null不行# 6.查询分组之后的部门名称和每个部门下所有的员工姓名 # group_concat不单单可以支持你获取分组之后的其他字段值 还支持拼接操作    select post,group_concat(name) from emp group by post;    select post,group_concat(name,'_DSB') from emp group by post;    select post,group_concat(name,':',salary) from emp group by post;# concat不分组的时候用     select concat('NAME:',name),concat('SAL:',salary) from emp;# 补充 as语法不单单可以给字段起别名 还可以给表临时起别名    select emp.id,emp.name from emp;      select emp.id,emp.name from emp as t1;   报错    select t1.id,t1.name from emp as t1;# 查询每个人的年薪  12薪    select name,salary*12 from emp;
```

## ***二十二、分组注意事项***

```
# 关键字where和group by同时出现的时候group by必须在where的后面    where先对整体数据进行过滤之后再分组操作    where筛选条件不能使用聚合函数    select id,name,age from emp where max(salary) > 3000;    select max(salary) from emp;  # 不分组 默认整体就是一组# 统计各部门年龄在30岁以上的员工平均薪资    1 先求所有年龄大于30岁的员工        select * from emp where age>30;    2 再对结果进行分组         select * from emp where age>30 group by post;        select post,avg(salary) from emp where age>30 group by post;
```

## ***二十三、HAVING分组之后的筛选条件***

```
"""having的语法根where是一致的只不过having是在分组之后进行的过滤操作即having是可以直接使用聚合函数的"""# 统计各部门年龄在30岁以上的员工平均工资并且保留平均薪资大于10000的部门    select post,avg(salary) from emp             where age>30             group by post            having avg(salary) > 10000            ;
```

## ***二十四、DISTINCT去重***

```
"""一定要注意 必须是完全一样的数据才可以去重！！！一定不要将逐渐忽视了 有逐渐存在的情况下 是不可能去重的[{'id':1,'name':'jason','age':18},{'id':2,'name':'jason','age':18},{'id':3,'name':'egon','age':18}]ORM  对象关系映射   让不懂SQL语句的人也能够非常牛逼的操作数据库表                                类一条条的数据                        对象字段对应的值                        对象的属性你再写类 就意味着在创建表用类生成对象 就意味着再创建数据对象点属性 就是在获取数据字段对应的值目的就是减轻python程序员的压力 只需要会python面向对象的知识点就可以操作MySQL"""select distinct id,age from emp;select distinct age from emp;
```

## ***二十五、ORDER BY排序***

```
select * from emp order by salary;select * from emp order by salary asc;select * from emp order by salary desc;"""order by默认是升序  asc 该asc可以省略不写也可以修改为降序     desc"""select * from emp order by age desc,salary asc;# 先按照age降序排  如果碰到age相同 则再按照salary升序排# 统计各部门年龄在10岁以上的员工平均工资并且保留平均薪资大于1000的部门,然后对平均工资降序排序    select post,avg(salary) from emp         where age>10         group by post        having avg(salary) > 1000        order by avg(salary) desc        ;
```

## ***二十六、LIMIT限制展示条数***

```
select * from emp;"""针对数据过多的情况 我们通常都是做分页处理"""    select * from emp limit 3;  # 只展示三条数据    select * from emp limit 0,5;    select * from emp limit 5,5;    第一个参数是起始位置    第二个参数是展示条数
```

## ***二十七、正则***

```
select * from emp where name regexp '^j.*(n|y)$';
```

## ***二十八、多表操作***

### ***28.1 前期表准备***

```
#建表    create table dep(    id int,    name varchar(20)     );    create table emp(    id int primary key auto_increment,    name varchar(20),    sex enum('male','female') not null default 'male',    age int,    dep_id int    );#插入数据    insert into dep values    (200,'技术'),    (201,'人力资源'),    (202,'销售'),    (203,'运营');    insert into emp(name,sex,age,dep_id) values    ('jason','male',18,200),    ('egon','female',48,201),    ('kevin','male',18,201),    ('nick','male',28,202),    ('owen','male',18,203),    ('jerry','female',18,204);
```

### ***28.2表查询***

```
select * from dep,emp;  # 结果   笛卡尔积"""了解即可 不知道也没关系"""    select * from emp,dep where emp.dep_id = dep.id;"""MySQL也知道 你在后面查询数据过程中 肯定会经常用到拼表操作 所以特地给你开设了对应的方法    inner join  内连接    left join   左连接    right join  右连接    union        全连接"""# inner join  内连接    select * from emp inner join dep on emp.dep_id = dep.id;# 只拼接两张表中公有的数据部分# left join   左连接    select * from emp left join dep on emp.dep_id = dep.id;# 左表所有的数据都展示出来 没有对应的项就用NULL# right join  右连接    select * from emp right join dep on emp.dep_id = dep.id;# 右表所有的数据都展示出来 没有对应的项就用NULL# union        全连接  左右两表所有的数据都展示出来    select * from emp left join dep on emp.dep_id = dep.id    union    select * from emp right join dep on emp.dep_id = dep.id;
```

### ***28.3 子查询***

```
"""子查询就是我们平时解决问题的思路    分步骤解决问题        第一步        第二步        ...将一个查询语句的结果当做另外一个查询语句的条件去用"""# 查询部门是技术或者人力资源的员工信息    1 先获取部门的id号    2 再去员工表里面筛选出对应的员工    select id from dep where name='技术' or name = '人力资源';        select name from emp where dep_id in (200,201);            select * from emp where dep_id in (select id from dep where name='技术' or name = '人力资源');
```

### ***28.4 总结***

```
表的查询结果可以作为其他表的查询条件也可以通过起别名的方式把它作为一个张虚拟表根其他表关联"""多表查询就两种方式    先拼接表再查询    子查询 一步一步来"""
```

## ***小知识：mysql-arm 安装***

```
安装mysql：brew install mysql@5.7安装brew服务：brew tap homebrew/services加载和启动MySQL服务：brew services start mysql@5.7检查mysql服务是否已加载：brew services list创建软链接: ln -s /opt/homebrew/Cellar/mysql@5.7/5.7.38/bin/mysql /usr/local/bin/mysql验证安装mysql是否成功： mysql -v进入brew安装目录: cd /opt/homebrew/Cellar/mysql@5.7/5.7.38/bin初始化密码，更改你自己的密码： ./mysqladmin -u root password 'yourpassword'
```

