# Mysql-面试经常考察的sql知识

## 零、基本操作篇
创建表：
```
CREATE TABLE employees (

    id INT AUTO_INCREMENT PRIMARY KEY,
    
    first_name VARCHAR(50),
    
    last_name VARCHAR(50),
    
    email VARCHAR(100)
    
);
```

删除表:
```
drop table 表名
```

删除行：
```
delete from table name

where...
```
注意：sql里面比较不是==，而是=

删除列： 
```
alter table tablename

drop column column_name
```

要把列名重命名：
```
select xxx as 想要的名字

from xxx...
```

in / not in


例子：选出value是1，2或3的quantity
```
select *

from 。。。

where value in （1，2，3）
```

union：链接多个select from where，进行多行筛选。

group by：按id分别求和等。

having：接在group by后面，如having sales＞500 and invoices ＜5。having必须跟着group by，跟where的区别在于一个筛选符合条件的聚合函数，一个非聚合

## 一、正则（regexp）
| 符号  | 意义 |
| ----- | --- |
| ^ | 开头 |
| $ | 结尾 |
| [abc] | 含abc |
| [a-c] | 含a到c |
| | | 或 |	    

举例：

分别选择满足如下条件的顾客：

1. first names 是 ELKA 或 AMBUR

2. last names 以 EY 或 ON 结束

3. last names 以 MY 开头 或包含 SE

4. last names 包含 BR 或 BU
```
select * 
from customers

where first_name regexp 'elka|ambur'

/where last_name regexp 'ey$|on$'

/where last_name regexp '^my|se'

/where last_name regexp 'b[ru]'/'br|bu'
```


## 二、order
练习

订单2的商品按总价降序排列:

法1. 可以以总价的数学表达式为排序依据
```
select * from order_items 

where order_id = 2

order by quantity * unit_price desc
```
-- 列间数学表达式

法2. 或先定义总价别名，在以别名为排序依据
```
select *, quantity * unit_price as total_price 

from order_items 

where order_id = 2

order by total_price desc
```
-- 列别名

asc升序，desc降序


## 三、limit

找出积分排名前三的死忠粉
```
USE sql_store;

select *

from customers

order by points desc 

limit 3
```
如果要找第8-10名呢？用limit 7，3 忽略前七名，从第八名开始取三个


## 四、计算优先级
```
select from (inner join) where + order by +limit
```

## 五、inner join

订单表连接到顾客信息表（根据顾客id连接）
```
select *

from order

(inner) join customer

on order.id=customer.id
```
如果只要顾客信息表中的id，firstname和lastname这几列呢？

把第一行的*改成id，firstname，lastname即可吗？

错！因为两个表都有id这一列

应该改成customer.id或者order.id

最好每句之前都加一个表名

如果需要将order简称为o，只需要from order o就行，注意，这么改了以后，上面的select语句也一定要把order改成o，跟c++的变量必须提前定义不同。

同样，inner join customer c就可以把customer简称c

**inner join只保留两个表的交集，不像下文的left join 和 right join，会给没有的数据填充null**

## 六、自连接

例子：输出员工id，员工姓名和他们管理员
```
USE sql_hr;


select 

    e.employee_id,
    
    e.first_name,
    
    m.first_name as manager
    
from employees e

join employees m

    on e.reports_to = m.employee_id
``` 

## 七、外连接
```
select...

from customer

left join order

on customer.id=orderid
```
等效于
```
select...

from order 

right join customer

on customer.id=orderid
```
选出所有顾客的订单


a LEFT JOIN b 显示全部的a


## 八、复合链接

查询所有顾客的所有订单详情：
```
USE sql_store;


SELECT 

    c.customer_id,
    
    c.first_name,
    
    o.order_id,
    
    sh.name AS shipper
    
FROM customers c

LEFT JOIN orders o

    ON c.customer_id = o.customer_id
    
LEFT JOIN shippers sh

    ON o.shipper_id = sh.shipper_id
    
ORDER BY customer_id
```

## 九、using

当join两个列名相同时可以用来简化（内外链接都可以这么干）
```
SELECT

    o.order_id,
    
    c.first_name,
    
    sh.name AS shipper
    
FROM orders o

JOIN customers c

    USING (customer_id)
    
LEFT JOIN shippers sh

    USING (shipper_id)
    
ORDER BY order_id
```

## 十、聚合函数

聚合函数：输入一系列值并聚合为一个结果的函数

Select Function(变量)...

AVG(表达式) 返回表达式中所有的平均值。仅用于数字列并自动忽略NULL值。

COUNT(表达式) 返回表达式中非NULL值的数量。可用于数字和字符列。

COUNT(*) 返回表中的行数(包括有NULL值的列)。

MAX(表达式) 返回表达式中的最大值,忽略NULL值。可用于数字、字符和日期时间列。

MIN(表达式) 返回表达式中的最小值,忽略NULL值。可用于数字、字符和日期时间列。

SUM(表达式) 返回表达式中所有的总和,忽略NULL值。仅用于数字列。

实例
```
USE sql_invoicing;

SELECT 

    MAX(invoice_date) AS latest_date,  
    
-- SELECT选择的不仅可以是列，也可以是数字、列间表达式、列的聚合函数   

MIN(invoice_total) lowest,

    AVG(invoice_total) average,
    
    SUM(invoice_total * 1.1) total,
    
    COUNT(*) total_records,
    
    COUNT(invoice_total) number_of_invoices, 
    
-- 和上一个相等    

COUNT(payment_date) number_of_payments,  

-- 【聚合函数会忽略空值】，得到的支付数少于发票数    

COUNT(DISTINCT client_id) number_of_distinct_clients

-- DISTINCT client_id 筛掉了该列的重复值，再COUNT计数，会得到不同顾客数

FROM invoicesWHERE invoice_date > '2019-07-01'  -- 想只统计下半年的结果
```


## 十一、视图（类似C++的类）

就是创建虚拟表，模块化一些重复性的查询模块，简化各种复杂操作（包括复杂的子查询和连接等）

注意视图虽然可以像一张表一样进行各种操作，但并没有真正储存数据，数据仍然储存在原始表中，视图只是储存起来的模块化的查询结果，是为了方便和简化后续进一步操作而储存起来的虚拟表。

例子:
```
USE sql_invoicing;

CREATE VIEW sales_by_client AS

    SELECT 
    
        client_id,
        
        name,
        
        SUM(invoice_total) AS total_sales
        
    FROM clients c
    
    JOIN invoices i USING(client_id)
```    

要用的时候:
```
Select * 

From sales_by_client
```
即可查到上面的结果。


删除:
```
Drop View sales_by_client
```

创建or更新视图:
```
Create or Replace View xxx
```

更新的方法:

放到sql文件里面，用源码控制改变。, 通常放在 git repository（仓库）里与其它人共享，团队其他人因此能在自己的电脑上重建这个数据库


