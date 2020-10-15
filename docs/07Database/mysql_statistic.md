**MySQL Statistics**

___



```mysql
/*查询2小时前的数据*/
SELECT  * FROM table_name WHERE 
		 create_time < DATE_SUB(NOW(), INTERVAL 2 HOUR);
```



```mysql
/*查询7天前的数据*/
SELECT count(*) FROM table_name WHERE
				DATE(create_time) < DATE_SUB(CURDATE(), INTERVAL 7 DAY);
```



```mysql
/*查询等于7天前的数据*/
SELECT count(*) FROM table_name WHERE
			    DATE(create_time) = DATE_SUB(CURDATE(), INTERVAL 7 DAY);
```



```mysql
/*查询近7天的数据*/
SELECT count(*) FROM table_name WHERE 
				DATE(create_time) > DATE_SUB(CURDATE(), INTERVAL 7 DAY)
```



> *By Day*

```mysql
/*按天统计*/
SELECT 
	count（id) countNum,
	DATE(create_time) createTime
FROM
	table_name
GROUP BY
	DATE(create_time)
ORDER BY
	DATE(create_time) DESC
```



> *By Week*

```mysql
/*按周统计*/
SELECT
	count(id) countNum,
	WEEK(create_time) createTime
FROM
	table_name
GROUP BY
	WEEK(create_time)
ORDER BY
	WEEK(create_time) DESC
```



> *By Month*

```mysql
/*按月统计*/
SELECT
	count(id) countNum,
	MONTH(create_time) createTime
FROM
	table_name
GROUP BY
	MONTH(create_time)
ORDER BY
	MONTH(create_time) DESC
```



> *By Quarter*

```mysql
/*按季度统计*/
SELECT
	count(id) countNum,
	QUARTER(create_time) createTime
FROM
	table_name
GROUP BY
	QUARTER(create_time)
ORDER BY
	QUARTER(create_time) DESC
```



> *By Year*



```mysql
/*按年统计*/
SELECT
	count(id) countNum,
	YEAR(create_time) createTime
FROM
	table_name
GROUP BY
	YEAR(create_time)
ORDER BY
	YEAR(create_time) DESC
```











## MySQL sql_mode=only_full_group_by错误



![img](https://images2018.cnblogs.com/blog/1413299/201807/1413299-20180711140420456-1688517117.png)

今天在测试服务器上突然出现了这么一个MySQL的问题,同样的代码正式服没有问题,那肯定就是出在了配置上,查了一下原因才明白原来是数据库版本为5.7以上的版本,默认是开启了 `only_full_group_by` 模式的，但开启这个模式后，原先的 `group by` 语句就报错，

![img](https://images2018.cnblogs.com/blog/1413299/201807/1413299-20180711140724447-1433892235.png)

查询MySQL版本可以在命令行模式刚连接上数据库时就能看到,或者在进入mysql后使用命令:status查看版本号,既然是版本的问题,那就查询一下配置看看具体的情况,命令行连接上数据库,再输入命令:

```
select @@sql_mode;
```

你会看到sql_mode的配置情况:

![img](https://images2018.cnblogs.com/blog/1413299/201807/1413299-20180711141319941-1398494589.png)

其中ONLY_FULL_GROUP_BY就是造成这个错误的罪魁祸首了,对于group by聚合操作,如果在select中的列没有在group by中出现,那么这个SQL是不合法的,因为列不在group by从句中,所以设置了sql_mode=only_full_group_by的数据库,在使用group by时就会报错,既然知道了问题,那么修改这个配置就可以了,找到MySQL的配置文件,在linux系统上/etc/my.cnf文件,查询sql_mode字段,我并没有在配置文件中找到这个关键字,所以我手动添加进去:

```
sql_mode = STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

需要注意的一点是一定要添加在[mysqld]配置内,这样添加完后重启mysql才会生效,退出数据库:exit,重启命令:

service mysqld restart;

刷新页面报错信息消失成功解决,再次连接上数据库查看sql_mode配置select @@sql_mode:

![img](https://images2018.cnblogs.com/blog/1413299/201807/1413299-20180711143058389-1231745826.png)

到此已经成功解决了这个问题。

set @@sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER
,NO_ENGINE_SUBSTITUTION';





# [mysql命令gruop by报错this is incompatible with sql_mode=only_full_group_by](https://www.cnblogs.com/jim2016/p/6322703.html)



在mysql 工具 搜索或者插入数据时报下面错误：

```mysql
ERROR 1055 (42000): Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'database_tl.emp.id' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by
```

 

原因：

看一下group by的语法：

select 选取分组中的列+聚合函数 from 表名称 group by 分组的列 

从语法格式来看，是先有分组，再确定检索的列，检索的列只能在参加分组的列中选。

我当前Mysql版本5.7.17，

再看一下ONLY_FULL_GROUP_BY的意思是：对于GROUP BY聚合操作，如果在SELECT中的列，没有在GROUP BY中出现，那么这个SQL是不合法的，因为列不在GROUP BY从句中，也就是说查出来的列必须在group by后面出现否则就会报错，或者这个字段出现在聚合函数里面。

 

查看mysql版本命令：select version();

查看sql_model参数命令：

```mysql
SELECT @@GLOBAL.sql_mode;

SELECT @@SESSION.sql_mode;
```

发现：

ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION

第一项默认开启ONLY_FULL_GROUP_BY，

 

解决方法：

1.只选择出现在group by后面的列，或者给列增加聚合函数；（不推荐）

2.命令行输入：

```mysql
set @@GLOBAL.sql_mode='';

set sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
```

 

默认关掉ONLY_FULL_GROUP_BY！

 

这个时候 在用工具select 一下

```mysql
SELECT @@sql_mode;

SELECT @@GLOBAL.sql_mode;
```

 

发现已经不存在ONLY_FULL_GROUP_BY ，感觉已经OK。但是如果你重启Mysql服务的话，发现ONLY_FULL_GROUP_BY还是会存在的

 

想要彻底解决这个问题 就得去改my.ini 配置（如果你们mysql 没有这个文件，就把my-default.ini 改成my.ini，我这个版本就是没有my.ini配置问题）

 

在 [mysqld]和[mysql]下添加

```mysql
 sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
```



sql_mode=only_full_group_by研读

天台的云 2017-02-06 13:45:49  17424  收藏
展开
MySQL 5.7.5后only_full_group_by成为sql_mode的默认选项之一，这可能导致一些sql语句失效。

比如下表game：

| id   | name | group_id | score |
| ---- | ---- | -------- | ----- |
| 1    | 小刚 | A        | 20    |
| 2    | 小明 | B        | 19    |
| 3    | 小花 | B        | 17    |
| 4    | 小红 | C        | 18    |



执行sql:

```mysql
select name, group_id from game group by group_id
```

（记为sql_make_group）
返回：

```mysql
Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'game.name' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by
1
```


解决方法
把group by字段group_id设成primary key 或者 unique NOT NULL。这个方法在实际操作中没什么意义。

使用函数any_value把报错的字段name包含起来。如，

```mysql
select any_value(name), group_id from game group by group_id。
```

在配置文件my.cnf中关闭sql_mode=ONLY_FULL_GROUP_BY.。msqyl的默认配置是```

```mysql
sql_mode=ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

。可以把ONLY_FULL_GROUP_BY去掉，也可以去掉所有选项设置成sql_mode=，如果你确信其他选项不会造成影响的话。

mysql官方文档有详细解译。

执行成功后，返回结果应该是

| name | group_id |
| ---- | -------- |
| 小刚 | A        |
| 小明 | B        |
| 小红 | C        |


为什么默认设置ONLY_FULL_GROUP_BY限制？
对于上述的报错信息，我的理解是select字段里包含了没有被group by条件唯一确定的字段name。

因为执行sql_make_group语句实际上把两行纪录小明和小花合并成一行，搜索引擎不知道该返回哪一条，所以认为这样的sql是武断的(arbitrary).

解决办法2和3都是禁止检查返回结果的唯一性。

进阶讨论
上述办法可以解决报错的问题，但也说明sql语句本身有逻辑问题。name字段不应该出现在返回结果，因为它是不确定的。

考虑这样的需求：按group_id分组后，找出每组得分最少的人。

执行sql: 

```mysql
select any_value(name) as name, group_id, min(score) as score from game group by group_id order by min(score)
```

返回结果

| name | group_id | score |
| ---- | -------- | ----- |
| 小明 | B        | 17    |
| 小红 | C        | 18    |
| 小刚 | A        | 20    |


B组的name是小明（因为小明的id更小），而期望结果应该是小花。

所以单纯使用group by无法实现这样的需求。可以使用临时表的方法：

```Mysql
select id, name,t.group_id, t.score from (select group_id, min(score) as score from game group by group_id order by min(score)) t inner join game on t.group_id=game.group_id and t.score=game.score
```

如果要实现更为复杂的需求，可以考虑使用group_concat，请参考这里。

#### Laravel的相关配置

把`my.cnf`中的`ONLY_FULL_GROUP_BY`去掉后，laravel的运行结果仍然报错。翻过代码后发现，laravel在每一次请求都会设定sql_mode。实现代码在`Illuminate\Database\Connectors\MySqlConnector`，不同版本稍有不同。

5.2和5.3可以在config/databse.php修改配置。

```php
[
'connections' =>
    'msyql' => [
        'strict' => true,
        'modes' => [
            'option1',
            'option2',
            ...
        ]
    ],
]  
```

 

当`strict=true`(默认)即采用mysql的默认配置，当然也包含`ONLY_FULL_GROUP_BY`。
当`strict=false`即设置`sql_mode=NO_ENGINE_SUBSTITUTION`。
也可以自定义，在modes下添加相应的配置项。modes的优先级比strict高。

当strict=true(默认)即采用mysql的默认配置，当然也包含ONLY_FULL_GROUP_BY。
当strict=false即设置sql_mode=NO_ENGINE_SUBSTITUTION。
也可以自定义，在modes下添加相应的配置项。modes的优先级比strict高。
