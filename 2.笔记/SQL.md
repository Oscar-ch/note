#1-学习中 

# SQL

## 第一天 8月24日

### 一、

#### 数据库的好处

 - 实现数据持久化
 -  使用完整的管理系统统一管理，便于查询
 

#### 数据库概念：

DB（database）：存储数据的“仓库”。保存了一系列有组织的数据。

DBMS（database management system）：数据库管理系统；数据库通过DBMS创建和操作的容器。

SQL（structure query language）:结构化查询语句：专门用来与数据库通信的语言。
   
#### 数据库的特点：
   
1. 将数据放到表中，表在放到库中；
2. 一个DB有多张表，每张表都有个名字，用来标识自己，表名具有唯一性；
3. 表由列组成，成为字段。所有表都是由一个或多个列组成，每一列似Java的“属性”；
    4、表有一些特性，他们定义了数据在表中如何存储，似Java类的设计；
    5、表中数据按行存储。，每一行似“对象”。

    
#### MySQL优点：

1. 开源、免费、成本低
2. 性能高、移植性好
3. 体积小便于安装

#### MySQL启动：

1. 服务
2. 管理员身份启动
   1. net start 服务名
   2. net stop 服务名

#### MySQL运行：
1. 客户端
2. 命令行：mysql -h localhost -P 3306 -u root -p root
   1. 退出：exit  Ctrl+C

#### sql常见命令

```mysql
    show databases;
    show tables;
    use 数据库名；
    create table 表名(
    列名 列类型，
    列名 列类型，
    );
    desc 表名；查看表结构
    select * from 表名；
    insert into 表名；
    select version();mysql --version;mysql --V;查看版本信息

```

#### mysql规范

   1. 不区分大小写，建议关键字大写，表名，列名小写
   2 . 每条命令;结尾
   3. 命令很长可以缩进或者换行
   4. 注释
      1. 单行注释：#注释
      2. 单行注释：-- 注释
      3. 多行注释：/*注释*/



### 二、DQL语言学习（数据查询语言）

#### 基础查询

```mysql
/*
语法：select 查询列表 from 表名;
查询列表：表中的字段、常量值、表达式、函数；
查询的结果是一个虚拟的表格
*/
SELECT first_name from employees;
SELECT first_name,last_name,salary from employees;
SELECT * FROM employees;

#查询常量
SELECT 100;
SELECT 'dengfei';

#查询表达式
SELECT 50/2;

#查询方法
SELECT VERSION();
```
   
##### 起别名

好处：
1. 便于理解
2. 如果查询的字段有重名，使用别名区分

```mysql
# 起别名第一种方法：as
SELECT 
   first_name AS xing,
   last_name AS ming 
from 
   employees;

# 起别名第二种方法：空格
SELECT 
   first_name xing1,
   last_name ming1 
from 
   employees; 
 ```
 
##### 去重 DISTINCT

```mysql
SELECT DISTINCT department_id FROM employees;
```

##### 拼接字符 CONCAT(str1,str2,...)

```mysql
SELECT   
   CONCAT(
      first_name, ',',
      last_name,',',
      job_id,',',
      IFNULL(commission_pct,0))as output 
from 
   employees;
```

##### ifnull函数

判断某字段或者表达式是否为null，如果为null 返回指定值，否则返回原本的值。

```mysql
SELECT IFNULL(commission_pct,0) from employees;
```

##### isnull函数

判断某个字段或者表达式是否为null，是null就返回1，否则返回0。

```mysql
SELECT ISNULL(salary) from employees;
```

#### 条件查询

[[条件查询举例]]

分类：

1. 按条件表达式筛选   条件运算符：< > = ！= <>不等于 <=>安全等于 >= <=
2. 按逻辑运算符 and or not 
3. 模糊查询 like between-and in is-null
   1. like的特点
      1. 通常与通配符搭配使用  %任意多个字符 包含0个字符；任意单个字符
   2. between-and
      1. 包含边界值 
      2. 提高语句简洁度 
      3. 两个临界值不要调换顺序
   3. in的含义：判断某个字段的值是否属于in列表中的某一项
       in特点：使用in提高语句简洁度；in列表的值类型必须一致或者兼容。



## 第二天 8月25日

### 排序查询

```mysql
/*
select 查询列表 
from 表名
（where 筛选条件）
order by 排序列表 （asc/desc 升序、降序）
*/

SELECT * from employees ORDER BY salary ASC;
```

特点：

1. asc升序，desc降序；如果不写默认是升序 
2. order by子句可以支持单个字段、多个字段、表达式、函数、别名
3. order by子句放在查询语句的最后，limit语句除外

```mysql

SELECT * from employees ORDER BY salary ASC;

#查询部门编号大于90的员工信息，按照入职先后顺序排序（单个字段）
SELECT *
from employees
where department_id >=100
ORDER BY hiredate ASC;

#按照年薪的高低查询员工的信息和年薪（按表达式排序）
SELECT *,
   salary*12*(1+IFNULL(commission_pct,0)) as nianxin 
from 
   employees
ORDER BY
   salary*12*(1+IFNULL(commission_pct,0)) DESC;

#按照年薪的高低查询员工的信息和年薪（按别名排序）
SELECT *,
   salary*12*(1+IFNULL(commission_pct,0))as nianxin
from employees
ORDER BY nianxin DESC;

SELECT * from employees WHERE commission_pct is null;

#按姓名的长度显示员工的姓名和工资（按函数排序）
SELECT
   LENGTH(last_name) as mingzichangdu,
   last_name,
   salary
from employees
ORDER BY mingzichangdu DESC;

#查询员工信息，先按工资升序，在按员工编号降序（按多个字段排序）
SELECT salary,employee_id
from employees
ORDER BY salary DESC,employee_id ASC;
```

### 常用函数

#### 1. 概念

将一组逻辑语句封装在方法体中，对外暴露方法名

调用：select 函数名（实参列表）（from 表名）；

#### 2. 好处

1. 隐藏实现的细节
2. 提高代码的复用性

#### 3. 分类

1. 单行函数 concat、length、ifnull
2. 分组函数 功能：做统计使用，又叫统计函数、聚合函数、组函数

##### 1. 单行函数：

1. 字符函数
   1. length() 获取参数值的字节个数
   2. cancat() 拼接字符串
   3. upper(),lower() 大小写
   4. substr,substring
   5. instr 返回子串第一次出现的索引，如果找不到就返回0
   6. trim 去除两边空白
   7. lpad 用指定字符实现左填充指定长度
   8. Rpad 用指定字符实现右填充指定长度
   9. replace 替换

```mysql
# 4.substr
# 截取从指定索引后的所有字符
SELECT SUBSTR('李晨喜欢范冰冰',5) as lichen ;

# 截取指定索引处指定字符长度的字符
SELECT SUBSTR('李晨喜欢范冰冰',3,5)as lic ;

# 5.instr
SELECT INSTR('乱世营销活动好好说说','活') as 说的;

# 6.trim
SELECT TRIM('  lichen '); 
SELECT TRIM('a' FROM 'aaaaaa  lichenaaasb aaaaaaaaaaaaaaaaaaa');

# 7.lpad
SELECT LPAD('asdw',9,'1') lichen;

# 8.Rpad
SELECT RPAD('asdw',9,'2') lichen;

# 9.replace 替换
SELECT REPLACE('lichenshigeshabi','shi','buhsi');

```


2. 数学函数
   1. round 四舍五入
   2. ceil() 向上取整，返回>=该参数的最小整数
   3. floor() 向下取整，返回<=该参数的最大整数
   4. truncate() 截断
   5. mod() 取余
   6. truncate() 截断
   7. rand() 获取随机数0~1之间，无限接近1

```mysql
# 1.round
SELECT ROUND(2.66);SELECT ROUND(3.6574,3);

# 2.ceil
SELECT CEIL(-2.11);SELECT CEIL(2.11);

# 3.floor
SELECT FLOOR(9.99);SELECT FLOOR(-9.99);

# 4.truncate
SELECT TRUNCATE(2.22222,3);

# 5.mod
SELECT MOD(7,3);

```

3. 日期函数
   1. now() 返回当前日期+时间
   2. curdate() 返回当前系统日期，不包含时间
   3. curtime() 返回当前系统时间，不包含日期
   4. 获取指定的部分，年、月、日、时、分、秒
   5. str_to_date 将字符通过指定的格式转换成日期
   6. date_format 将日期转换成字符
   7. datediff() 返回两个日期相差天数

```mysql
# 1.now
SELECT NOW();

# 2.curdate
SELECT CURDATE();

# 3.curtime
SELECT CURTIME();

# 4.获取指定时间
SELECT YEAR(NOW());
SELECT MONTH(NOW());
SELECT MONTHNAME(NOW());
SELECT SECOND(NOW());

# 5.str_to_date
SELECT STR_TO_DATE('2000-4-16','%Y-%m-%d');

# 6.date_format
SELECT DATE_FORMAT(NOW(),'%Y年%m月%d日');

SELECT 
   last_name,
   DATE_FORMAT(hiredate,'%m月/%d日 %Y年')
from employees
WHERE commission_pct is not null;
```

4. 其他函数
   1. SELECT VERSION();
   2. SELECT DATABASE();
   3. SELECT USER();
   4. password('字符')返回该字符的密码形式
   5. md5('字符')返回该字符的md5形式

5. 流程控制函数
   1. if函数：if else的效果
   2. case函数使用一：Switch case的效果

```mysql
# 1.if函数
SELECT 
   salary,
   last_name,
   commission_pct,
IF(commission_pct is NULL,'yes','no')
FROM employees;

2.case函数

/*
格式一
case 要判断的字段或表达式
when 常量1 then 要显示的值1或语句1;
when 常量2 then 要显示的值2或语句2;
···
else 要显示的值n或语句n;
end

格式二
case
when 条件一 then 要显示的值或语句1
when 条件二 then 要显示的值或语句2
···
else 要显示的值n或语句n
end
*/
```

##### 2. 分组查询

###### 语法：

```mysql
/*
select 分组函数，列（要求出现在group by的后面）
from 表名
【where 筛选条件】
group by 分组的列表
【order by 子句】
*/
```

注意：查询列表必须特殊，要求是分组函数和group by后出现的字段

###### 特点：

| 类型       | 数据源         | 位置               | 关键字 |
| ---------- | -------------- | ------------------ | ------ |
| 分组前筛选 | 原始表         | group by子句的前面 | where  |
| 分组后筛选 | 分组后的结果集 | group by子句的后面 | having |

1. group by 子句支持单个字段分组，多个字段分组（用逗号连接，没有顺序要求），表达式或函数
2. 也可以添加排序（排序放在分组查询的最后）
3. 分组函数做条件肯定是放在having子句中；
4. 能用分组前筛选的，就优先考虑分组前筛选

###### 简单的分组查询

```mysql
# 每个工种的最高薪资
SELECT
   MAX(salary) zuigaoxinzi,
   job_id
from employees
GROUP BY job_id;

# 查询每个位置上的部门个数
SELECT COUNT(*) ,location_id from departments GROUP BY location_id;
```

###### 添加简单的筛选条件/添加分组前筛选
```mysql
# 查询邮箱中包含a字符的，每个部门的平均工资
SELECT
   AVG(salary),
   department_id
from employees
WHERE email LIKE '%a%'
GROUP BY department_id;

# 查询有奖金的每个领导手下员工的最高工资
SELECT
   MAX(salary),
   manager_id
FROM employees
WHERE commission_pct is not NULL
GROUP BY manager_id;
```
###### 添加复杂的筛选条件/添加分组后筛选
```#查询哪个部门的员工个数>2SELECT COUNT(*),department_id from employees GROUP BY department_id HAVING COUNT(*)>2;#查询每个工种有奖金的员工的最高薪资>12000的工种编号和最高薪资SELECT MAX(salary),job_id from employees WHERE commission_pct is not null GROUP BY job_id HAVING MAX(salary)>12000;```
2.按表达式或函数分组
```#按员工姓名长度分组，查询每一组的员工个数，筛选员工个数大于5的有哪些SELECT COUNT(*),LENGTH(last_name) from employees GROUP BY LENGTH(last_name) HAVING COUNT(*)>5;```
3.按多个字段分组
```#查询每个部门每个工种的员工的平均工资SELECT AVG(salary),department_id,job_id FROM employees GROUP BY department_id,job_id;```
4.添加排序
```#查询每个部门每个工种的员工的平均工资,并按照平均工资的高低排序SELECT AVG(salary),department_id,job_id FROM employees WHERE department_id IS NOT NULL GROUP BY department_id,job_id HAVING AVG(salary)>10000ORDER BY AVG(salary) DESC;```

### 第三天 8月28日

分组函数：用作统计使用，又称为聚合函数或统计函数或组函数
1.分类：sum求和，avg平均值，max最大值，min最小值，count求个数
2.参数支持哪些类型（特点）：
1、sum，avg用于处理数值型；max，min，count用于处理任何类型
2、以上分组函数都忽略null值
3、可以和distinct搭配使用实现去重
```SELECT SUM(salary),SUM(DISTINCT salary) FROM employees;SELECT COUNT(salary),COUNT(DISTINCT salary) from employees;```
4、count函数详细介绍
```SELECT COUNT(salary) from employees;SELECT COUNT(*) from employees;SELECT COUNT(1) FROM employees;```都是求行数
一般使用count(*)查询行数
效率：MYISAM存储引擎下，count(*)的效率最高；
INNODB存储引擎下，count(*)和count(1)的效率差不多，比count(字段)要高一点。
5、和分组函数一同查询的字段要求是group by 后的字段


### 连接查询
含义：也叫多表查询，当查询的字段来自多个表时，就会用到连接查询。
分类：按年代分：sql92标准（仅支持内连接）和sql99标准（支持内连接、外连接除全外连接、交叉连接）
按功能分：1、内连接：等值连接、非等值连接、自连接 2、外连接：左外连接、右外连接、全外连接 3、交叉连接

一、92标准
1、等值连接
特点：
1.多表等值连接的结果为各表的交集
2.n表连接，至少需要n-1个连接条件
3.多标的顺序没要求
4.一般要为表起别名
5.可以搭配筛选、分组、排序
```#查询员工名和对应的部门名SELECT last_name,department_name FROM employees,departments WHERE employees.department_id = departments.department_id;```
2、为表起别名
好处：1.提高语句的简洁度 2.区分多个重名的字段名
注意：如果取了别名，前面查询字段就不能用表名来限定，要用别名来限定
```#查询员工名、工种号、工种名SELECT em.last_name,em.job_id,jo.job_title FROM employees as em,jobs as jo WHERE em.job_id = jo.job_id;```
3、两个表的顺序可以调换
4、可以加筛选
```#查询有奖金的员工名和部门名SELECT last_name,department_name ,commission_pct FROM employees e ,departments d WHERE e.department_id = d.department_id and commission_pct is not null ;#查询城市名第二字字母有o的部门名和城市名SELECT department_name,city FROM departments d,locations l WHERE d.location_id = l.location_id and city LIKE '_o%';```
5、可以加分组
```#查询每个城市的部门个数SELECT COUNT(*) 个数,city from departments d ,locations l WHERE d.location_id = l.location_id GROUP BY city;```
6、可以加排序
```#查询每个工种的工种名和员工的个数，并且按照员工个数降序SELECT job_title,COUNT(*) FROM employees e ,jobs j WHERE e.job_id = j.job_id GROUP BY job_title ORDER BY COUNT(*) DESC;```
7、可以三表连接
```#查询员工名、部门名和所在城市SELECT last_name,department_name,city FROM employees e,departments d,locations l WHERE e.department_id = d.department_id AND d.location_id = l.location_id AND city LIKE 's%' ORDER BY department_name DESC; ```

2、非等值连接
3、自连接
```#查询员工名和上级的名称SELECT e.last_name,e.employee_id,m.employee_id,m.last_name FROM employees e,employees m WHERE e.employee_id = m.manager_id;```


二、sql99语法
语法：select 查询列表 from 表1 别名 【连接类型】
join 表2 别名 on 连接条件
【where 筛选条件】
【group by 分组】
【having 筛选条件】
【order by 排序列表】

分类：
内连接：inner
外连接：左外（left【outer】），右外（right【outer】），全外（full）
交叉连接：cross

1、内连接：语法：
select 查询列表 from 表1 别名 inner join 表2 别名 on 连接条件;
分类：等值连接，非等值连接，自连接

特点：
1、添加排序、分组、筛选
2、inner可以省略
3、筛选条件放在where后，连接条件放在on后面，提高分离性，便于阅读
4、inner join连接和sql92语法中的等值连接效果一样，都是查询多表的交集

1.等值连接 
```#查询名字中包含e的员工名和工种名（添加筛选）SELECT last_name,job_title from employees e INNER JOIN jobs j ON e.job_id = j.job_id WHERE e.last_name LIKE '%e%';```
```#查询部门个数>3城市名和部门个数（添加分组和筛选）SELECT city,COUNT(*) from departments d INNER JOIN locations j ON d.location_id = j.location_id GROUP BY city HAVING COUNT(*)>3;```
```#查询哪个部门的员工个数>3的部门名和员工个数，并按这个降序（添加排序）SELECT COUNT(*),department_name from employees e INNER JOIN departments d ON e.department_id = d.department_id GROUP BY department_name HAVINGCOUNT(*)>3 ORDER BY COUNT(*) DESC;```
```#查询员工名、部门名、工种名，并按部门名降序(多表查询,要与前一个表有连接关系)SELECT last_name,department_name,job_title from employees e INNER JOIN departments d ON e.department_id = d.department_idINNER JOIN jobs j ON e.job_id = j.job_id ORDER BY department_name DESC;```
2.非等值连接
```#查询员工的工资级别SELECT salary,grade_level FROM employees e INNER JOIN job_grades j ON e.salary BETWEEN j.lowest_sal AND j.highest_sal;#BETWEEN 起始位置 AND 结束位置```
```#查询工资级别的个数>20的个数，并按照工资级别降序SELECT COUNT(*),grade_level FROM employees e INNER JOIN job_grades j ON e.salary BETWEEN j.lowest_sal AND j.highest_sal GROUP BY grade_level HAVING COUNT(*)>20 ORDER BY grade_level desc;```
3.自连接
```#查询员工的名字和上级的名字（自连接）SELECT e.last_name,m.last_name FROM employees e INNER JOIN employees m ON m.employee_id = e.manager_id;```

二、外连接
应用场景：用于查询一个表中有，另一个表没有的记录

特点：
1、外连接的查询结果为主表的所有记录
如果从表中有和它匹配的，则显示匹配的值
如果从表中没有和它匹配的，则显示null
外连接查询结果=内连接结果+主表中而从表没有的记录
2、左外连接，left join左边是主表
右外连接，right join右边是主表
3、左外和右外交换顺序可以实现相同功能
4、全外连接=内连接的结果+表1中有但表2没有的+表2中有的但表1中没有的
```#查询哪个部门没有员工#左外连接SELECT d.*,e.employee_id FROM departments d LEFT OUTER JOIN employees e ON d.department_id = e.department_id WHERE e.employee_id is NULL;```
```#右外连接SELECT d.*,e.employee_id FROM employees e RIGHT OUTER JOIN departments d ON d.department_id = e.department_id WHERE e.employee_id is NULL;```
全外连接
```#全外SELECT b.*,bo.* FROM beauty b FULL OUTER JOIN boys bo ON b.boyfriend_id = bo.id;#不支持全外```
交叉连接
```#交叉连接SELECT b.*,bo.* FROM beauty b CROSS JOIN boys bo;```


### 2023年9月4日
### 子查询
含义：出现在其他语句中的select语句，称为子查询或内查询
外部的查询语句，称为主查询或外查询
分类：
按子查询出现的位置：
select后面：
    仅支持标量子查询
from后面：
    支持表子查询
重点 where后面或having后面：
    重点（支持标量子查询（单行），列子查询（多行）），行子查询
exists后面（相关子查询）：
    支持表子查询
按结果集的行列数不同：
标量子查询（结果集只有一行一列）
列子查询（只有一列多行）
行子查询（只有一行多列）
表子查询（一般为多行多列）

##### 一、where或having后面
1、标量子查询（单行子查询）
2、列子查询（多行子查询）
3、行子查询（多列多行）
特点：
1.子查询放在小括号内
2.子查询一般放在条件的右侧
3.标量子查询，一般搭配着单行操作符使用（> < >= <= = <>）
列子查询，一般搭配多行操作符使用（in、any/some、all）
4.子查询的执行优先级先于主查询执行，主查询的条件用到了子查询的结果
1、标量子查询
```#谁的工资比Abel高SELECT salary FROM employees WHERE last_name = 'Abel';SELECT last_name FROM employees WHERE salary > (SELECT salary FROM employees WHERE last_name = 'Abel');SELECT * FROM employees WHERE salary > (SELECT salary FROM employees WHERE last_name = 'Abel');```
```#返回job_id与141号员工相同，salary比143号员工多的员工姓名，job_id和工资SELECT job_id FROM employees WHERE employee_id = 141;SELECT salary FROM employees WHERE employee_id = 143;SELECT last_name,job_id,salary FROM employees WHERE job_id = (SELECT job_id FROM employees WHERE employee_id = 141)and salary > (SELECT salary FROM employees WHERE employee_id = 143);```
```#返回公司工资最少的员工的last_name，job_id,salarySELECT MIN(salary) FROM employees;#查询最低工资 大于 50号部门最低工资的 部门ID 和 其最低工资SELECT MIN(salary) FROM employees WHERE department_id = 50SELECT department_id,MIN(salary) FROM employees GROUP BY department_idSELECT MIN(salary),department_id FROM employees GROUP BY department_id HAVING MIN(salary) > (SELECT MIN(salary) FROM employees WHERE department_id = 50)```
2、列子查询
使用in操作符
```#返回location_id是1400或1700的部门中的所有员工姓名（in）SELECT department_id FROM departments WHERE location_id IN(1400,1700)SELECT last_name FROM employees WHERE department_id IN (SELECT department_id FROM departments WHERE location_id IN(1400,1700));```
使用any操作符
```#返回其他工种中比job_id为`IT_PROG`部门任一工资低的员工的员工号、姓名、job_id以及salary（any）SELECT salary FROM employees WHERE job_id = 'IT_PROG'SELECT employee_id,last_name,job_id,salary FROM employees WHERE salary < ANY(SELECT salary FROM employees WHERE job_id = 'IT_PROG')and job_id <>'IT_PROG';SELECT employee_id,last_name,job_id,salary FROM employees WHERE salary < ALL(SELECT salary FROM employees WHERE job_id = 'IT_PROG')and job_id <>'IT_PROG';SELECT employee_id,last_name,job_id,salary FROM employees WHERE salary < (SELECT MIN(salary) FROM employees WHERE job_id = 'IT_PROG')and job_id <>'IT_PROG';```

### 2023年9月5日
3、行子查询（结果集一行多列或多行多列）
```#查询员工编号最小并且工资最高的员工信息SELECT * FROM employees WHERE (employees_id,salary) = (SELECT min(employees_id),max(salary)FROM employees);SELECT MIN(employee_id) FROM employeesSELECT MAX(salary) FROM employeesSELECT * FROM employees WHERE employee_id = (SELECT MIN(employee_id) FROM employees) AND salary =(SELECT MAX(salary) FROM employees)```

#### 二、select后面（一行一列）
仅支持标量子查询（一行一列）
```#查询每个部门的员工个数SELECT d.*,(SELECT COUNT(*) FROM employees e WHERE e.department_id = d.department_id) FROM departments d;```

#### 三、from后面
将子查询结果充当一张表，要求必须取别名
```#查询每个部门的平均工资的工资等级SELECT avg(salary) ag,department_id FROM employees GROUP BY department_idSELECT ag_dep.*,g.grade_level FROM(SELECT avg(salary) ag,department_id FROM employees GROUP BY department_id) ag_dep INNER JOIN job_grades g ON ag_dep.ag BETWEEN lowest_sal AND highest_sal;```
#### 四、exists（相关子查询）
exists（完整的查询语句）
结果：1或0
```#查询有员工的部门名SELECT department_name FROM departments d WHERE EXISTS(SELECT * FROM employees e WHERE d.department_id = e.department_id);```

### 分页查询
应用场景：当要显示的数据，一页显示不全，需要分页提交sql请求
语法：select 查询列表 from 表 【join type join 表2 on 连接条件 where 筛选条件 group by 分组字段 having 分组后的筛选 order by 排序的字段】limit 【offset,】size;

offset：要显示条目的起始索引（索引从0开始）
size：要显示的条目个数
特点：1.limit语句放在查询语句的最后
2.公式
要显示的页数page，每页的条目数size

select 查询列表 from 表 limit（page-1）*size，size;
```#查询前五条员工信息SELECT * FROM employees LIMIT 0,5;SELECT * FROM employees LIMIT 5;#查询10-25的内容SELECT * FROM employees LIMIT 9,16;#查询有奖金的员工信息，并且工资较高的前十名显示出来SELECT * FROM employees WHERE commission_pct is NOT NULL ORDER BY salary DESC LIMIT 10;```

### 联合查询
union 联合 合并：将多条查询语句的结果合并成一个结果
语法：查询语句1
union
查询语句2
union
...
应用场景：要查询的结果来自于多个表，且多个表没有直接的连接关系，但查询的信息一致时


特点☆：1.要求多条查询语句的查询列数是一致的
2.要求多条查询语句的查询的每一列的类型和顺序最好一致
3.union关键字默认去重，如果使用union all可以包含重复项


#### 三、DML语言（数据操作语言）
插入：insert
修改：update
删除：delete

### 一、插入语句
#### 2023年9月6日
1、插入方式一：
语法： insert into 表名(列名,列名,...) value (值1,值2,...);
```INSERT INTO beauty (id,`name`,sex,borndate,phone,photo,boyfriend_id) VALUES (13,'李晨','女','2000-05-12','1234',NULL,3);```

特点：
1.插入的值的类型要与列的类型一致或兼容
2.不可以为null的列必须插入值。可以为null的列如何插入值？
方法一：直接插入时，写null
```INSERT INTO beauty (id,`name`,sex,borndate,phone,photo,boyfriend_id) VALUES (13,'李晨','女','2000-05-12','1234',NULL,3);```
方法二：可以为空的字段不写
```INSERT INTO beauty (id,`name`,sex,phone) VALUES (13,'李晨','女','1234');```
3.列的顺序可以调换
4.列数和值的个数必须一致
5.可以省略列名，默认所有列，并且列的顺序和表中列的顺序一致

2、插入方式二：
语法：insert into 表名 set 列名=值,列名=值,...
```INSERT into beauty SET id =14,`name`='limazi',phone='120';```

两种方式的比较：
1.方式一支持多行插入，方式二不支持
2.方式一支持子查询，方式二不支持
通常使用方式一，常规插入
```INSERT INTO beauty(id,`name`,phone) SELECT 26,'马航','1111'```

### 修改语句
1、修改单表记录☆
语法：
update 表名 set 列=新值,列=新值,... where 筛选条件;
```UPDATE beauty SET phone = '12222333' WHERE `name` LIKE '李%';```
2、修改多表记录
语法：
92语法：
update 表1 别名，表2 别名 set 列=值,... where 连接条件 and 筛选条件;
99语法：
update 表1 别名 inner/left/right join 表2 别名 on 连接条件 set 列=值,... where 筛选条件;
```#修改张无忌的女朋友的手机号为443UPDATE  boys bo INNER JOIN beauty b ON bo.id = b.boyfriend_id SET b.phone = '443' WHERE bo.boyName = '张无忌'#修改没有男朋友的女神的男朋友编号都为2号UPDATE boys bo RIGHT JOIN beauty b ON bo.id = b.boyfriend_id SET b.boyfriend_id =2 WHERE b.id is NULL;```

### 删除语句
方式一：delete
语法：
1.单表的删除☆
delete from 表名 where 筛选条件;
2.多表的删除
92语法：
delete 表1的别名，表2的别名 from 表1 别名，表2 别名 where 连接条件 and 筛选条件;
99语法：
delete 表1别名，表2别名 from 表1 别名 inner/left/right join 表2 别名 on 连接条件 where 筛选条件;
方式二：truncate(清除)
语法：truncate table 表名;

delete:
1.单表的删除
### 2023年9月7日
```#删除手机号为9结尾的女生信息,单表的删除DELETE FROM beauty WHERE phone LIKE '%9';``
2.多标的删除
```#删除张无忌的女朋友的信息，多表的删除DELETE b FROM beauty b INNER JOIN boys bo ON b.boyfriend_id = bo.id WHERE bo.boyName = '张无忌';``

truncate：清除整个表的数据

delete与truncate的比较☆：
1.delete可以加where条件，truncate不能加
2.truncate删除效率要高一些，没有经过where筛选
3.假如要删除的表中有自增长列，
用delete删除后，在插入数据，自增长列从断点开始，
truncate删除后，在插入数据，自增长列的值从1开始。
4.truncate删除没有返回值，delete删除有返回值
5.truncate删除不能回滚，delete删除可以回滚


#### 四、DDL（数据定义语言）
库和表的管理
一、库的管理
创建、修改、删除
二、表的管理
创建、修改、删除

创建：create
修改：alter
删除：drop

一、库的管理
1、库的创建
语法：
create dateabase 【if not exists】 库名;
2、库的修改
rename database 原库名 to 新库名;
文件名的修改
3、库的删除
drop database if exists库名;

二、表的管理
1、表的创建
语法：
create table 表名(
    列名 列的类型 【（长度） 约束】,
    列名 列的类型 【（长度） 约束】,
    列名 列的类型 【（长度） 约束】,
    ...
);
```CREATE TABLE book(id INT,bookName VARCHAR(20),price DOUBLE,authorId INT,publishDate datetime);```
2、表的修改
1.修改列名
```alter table book change column publishdate pubdate datetime```
2.修改列的类型或约束
```alter table book modify column pubdate timestamp```
3.添加新列
```alter table book add column annual DOUBLE```
4.删除列
```alter table book drop column annual```
5.修改表名
```alter table book rename to new_book```

3、表的删除
```drop table if exists new_book```
通用的写法：
drop database if exists 旧库名
create database 新库名

drop table if exists 旧表名
create table 表名（）

4、表的复制
1.仅复制表的结构
```create table copy like book```
2.复制表的结构+数据
```create table copy2 select * from book```

只复制部分数据
```create table copy3 select id,au_name from author where nation='中国';```
仅仅复制某些字段
```create table copy4 select id,au_name from author where 0;```

### 2023年9月8日
### 常见的数据类型
数值型：
    整数：
    小数：定点数和浮点数
字符型：
    较短的文本：char,varchar
    较长的文本：text、blob(较长的二进制数据)
日期型：

一、整数
分类：
tinyint 1字节
smallint 2字节
int integer 4字节
bigint 8字节
特点：
### 2023年9月12日
1.如果不设置无符号还是有符号，默认是有符号，如果设置无符号，需要添加unsigned关键字
2.如果插入的数值超出了整型的范围，会报out of range异常，并且插入临界值
3.如果不设置长度，会有默认的长度

二、小数
分类：
1、浮点型
float(M,D);
double(M,D);
2、定点型
dec(M,D);
decimal(M,D);
特点：
1.M:整数部位+小数部位
D：小数部位   如果超出范围，则插入临界值
2.M和D都可以省略
3.定点型的精确度较高，如果要求插入数值的精确度较高如货币运算等可考虑使用

三、字符型
分类：
较短的文本：
char，varchar

其他:
binary和varbinary用于保存较短的二进制
enum用于保存枚举
set用于保存集合

较长的文本：
text，blob(较长的二进制)
特点：
            写法        M的意思                 特点            空间的耗费  效率
char        char(M)     最大的字符数，可省略     固定长度的字符  比较耗费    高
varchar     varchar(M)  最大的字符数，不可省略   可变长度的字符  比较节省    低

四、日期型
分类：
date：只保存日期
time：只保存时间
year：只保存年

datetime：保存日期加时间
timestamp：保存日期加时间
特点：datetime 8字节，范围：1000-9999，时区的影响：不受
timestamp 4字节，范围：1970-2038，时区的影响：受

### 常见的约束
含义：一种限制，用于限制表中的数据，为了保证表中的数据的准确和可靠性。
分类：六大约束：
    not null：非空，用于保证该字段的值不能为空，如姓名，学号；
    default：默认，用于保证该字段有默认值，如性别；
    primary key：主键，用于保证该字段的值具有唯一性，并且非空，如学号，员工编号；
    unique：唯一，保证该字段的值具有唯一性，可以为空，如座位号；
    check：检查约束【MySQL不支持】如年龄，性别；
    foreign key：外键：用于限制两个表的关系，用于保证该字段的值必须来自主表的关联列的值。 在从表添加外键约束，用于引用主表的某列的值，如学生表的专业编号。

添加约束的时机：
    1.创建表时
    2.修改表时

约束的添加分类：
    1.列级约束
    六大约束语法都支持，但外键约束没有效果
    2.表级约束
    除了非空、默认、其他的都支持

主键和唯一的对比：
    主键：保证唯一性，不允许为空，至少有一个，允许组合，但不推荐
    唯一：保证唯一性，允许为空，可以有多个，允许组合，但不推荐
语法：
create table 表名(字段名 字段类型 列级约束，字段名 字段类型，表级约束)

外键：
    1.要求在从表设置外键关系
    2.从表的外键列的类型和主表的关联列的类型要求一致或兼容，名称无限制
    3.主表的关联列必须是一个key（一般是主键或唯一）
    4.插入数据时，先插入主表，在插入从表；删除数据时，先删除从表，在删除主表

一、创建表时添加约束
1、添加列级约束
语法：
直接在字段名和类型后面追加 约束类型即可。只支持：默认、非空、主键、唯一
2、添加表级约束
语法：
【constraint 约束名】 约束类型（字段名）
### 2023年9月19日

二、修改表时添加约束
语法：
1.添加列级约束
alter table 表名 modify column 字段名 字段类型 新约束；
2.添加表级约束
alter table 表名 add【constraint 约束名】约束类型（字段名）【外键的引用】；

1、添加非空约束
```ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20) NOT NULL;```
2、添加默认约束
```ALTER TABLE stuinfo MODIFY COLUMN age INT DEFAULT 18;```
3、添加主键
```ALTER TABLE stuinfo MODIFY COLUMN id INT PRIMARY KEY;#列级约束ALTER TABLE stuinfo ADD PRIMARY KEY(id);#表级约束```
4、添加唯一
```ALTER TABLE stuinfo MODIFY COLUMN seat INT UNIQUE;#列级约束ALTER TABLE stuinfo ADD UNIQUE(seat);#表级约束```
5、添加外键
```ALTER TABLE stuinfo ADD CONSISTENT fk_stuinfo_major FOREIGN KEY;```

三、修改表时删除约束
1、删除非空约束
```ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20) NULL;```
2、删除默认约束
```ALTER TABLE stuinfo MODIFY COLUMN age INT;```
3、删除主键
```ALTER TABLE stuinfo DROP PRIMARY KEY;```
4、删除唯一
```ALTER TABLE stuinfo DROP INDEX seat;```
5、删除外键
```ALTER TABLE stuinfo DROP FOREIGN KEY fk_stuinfo_major;```

### 标识列
含义：
又称为自增长列  含义：可以不用手动的插入值，系统提供默认的序列值

一、创建表时设置标识列
特点：
1.标识列必须和主键搭配吗？不一定，但要求是一个key
2.一个表可以有多个标识列？至多一个！！只有一个标识列
3.标识列的类型只能是数值型
4.标识列可以通过set auto_increment=3；设置步长可以通过 手动插入值，设置起始值
```CREATE TABLE tab_identity(id INT PRIMARY KEY auto_increment,name VARCHAR(20));INSERT INTO tab_identity(id,NAME) VALUES(NULL,'lichen');INSERT INTO tab_identity(NAME)VALUES('lucy');```

二、修改表时设置标识列
```ALTER TABLE tab_identity MODIFY COLUMN id INT PRIMARY KEY auto_increment;```
三、修改表时删除标识列
```ALTER TABLE tab_identity MODIFY COLUMN id INT PRIMARY KEY```


#### TCL语言 事务控制语言
transaction control language 事务控制语言
事务：一个或一组sql语句组成一个执行单元，这个执行单元要么全部执行，要么全部不执行

属性：ACID
1.原子性（Atomicity） 指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。
2.一致性(Consistency) 事务必须使数据库从一个一致性状态变换到另一个一致性的状态。
3.隔离性(Isolation) 一个事务的执行不能被其他事务干扰
4.持久性(Durability) 一个事务一旦被提交，它对数据库中数据的改变就是永久性的

事务的创建：
隐式事务：事务没有明显的开启和结束的标记 如insert、delete、update
```delete from 表 where id =1```
显式事务：事务必须具有明显的开启和结束的标记  前提：必须设置自动提交功能为禁用
步骤一：开启事务  set autocommit=0;start transaction;
步骤二：编写事务中的sql语句
步骤三：结束事务 commit-提交事务 rollback-回滚事务 savepoint-设置节点名



























