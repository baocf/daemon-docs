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



