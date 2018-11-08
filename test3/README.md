# 实验3：创建分区表

## 实验目的：

掌握分区表的创建方法，掌握各种分区方式的使用场景。

## 实验内容：
- 本实验使用3个表空间：USERS,USERS02,USERS03。在表空间中创建两张表：订单表(orders)与订单详表(order_details)。
- 使用**你自己的账号创建本实验的表**，表创建在上述3个分区，自定义分区策略。
- 你需要使用system用户给你自己的账号分配上述分区的使用权限。你需要使用system用户给你的用户分配可以查询执行计划的权限。
- 表创建成功后，插入数据，数据能并平均分布到各个分区。每个表的数据都应该大于1万行，对表进行联合查询。
- 写出插入数据的语句和查询数据的语句，并分析语句的执行计划。
- 进行分区与不分区的对比实验。

## 实验步骤
## 1.创建主表和从表
![运行结果](https://github.com/Scarpes/Oracle/blob/master/test3/1.png)
![运行结果](https://github.com/Scarpes/Oracle/blob/master/test3/2.png)
## 2.分配使用权限
![运行结果](https://github.com/Scarpes/Oracle/blob/master/test3/3.png)
![运行结果](https://github.com/Scarpes/Oracle/blob/master/test3/4.png)
## 3.在刚才创建的两张表中插入万条以上数据
主表sql语句：
```sql
//插入单条数据的sql语句

INSERT INTO orders(customer_name, customer_tel, order_date, employee_id, trade_receivable, discount) VALUES('Li', '777', to_date ( '2015-12-20 12:40:32' , 'YYYY-MM-DD HH24:MI:SS' ), 3, 7, 80);
INSERT INTO orders(customer_name, customer_tel, order_date, employee_id, trade_receivable, discount) VALUES('Hong', '888', to_date ( '2016-12-20 12:40:32' , 'YYYY-MM-DD HH24:MI:SS' ),4, 8, 90);
INSERT INTO orders(customer_name, customer_tel, order_date, employee_id, trade_receivable, discount) VALUES('Fei', '999', to_date ( '2017-12-20 12:40:32' , 'YYYY-MM-DD HH24:MI:SS' ),5,9, 100);

//重复插入达到万条数据

insert into orders
select *
from orders;
```

- 主表数据概览：
![运行结果](https://github.com/lihongfei666/oracle/blob/master/test3/主表数据.png )

从表sql语句：
```sql

//插入单条数据的sql语句

insert into order_details(id, PRODUCT_ID, PRODUCT_NUM, PRODUCT_PRICE) VALUES(1, 2, 3, 4);
insert into order_details(id, PRODUCT_ID, PRODUCT_NUM, PRODUCT_PRICE) VALUES(2, 3, 4, 5);
insert into order_details(id, PRODUCT_ID, PRODUCT_NUM, PRODUCT_PRICE) VALUES(3, 4, 5, 6);
//重复插入达到万条数据

insert into order_details
select *
from order_details;
```

- 从表数据概览：
![运行结果](https://github.com/lihongfei666/oracle/blob/master/test3/从表数据.png )

## 4.联合查询主表和从表（分区）
```sql

SET AUTOTRACE ON;
SELECT
    *
FROM orders partition (PARTITION_BEFORE_2017) LEFT JOIN order_details partition (PARTITION_BEFORE_2017)
ON (orders.order_id = order_details.order_id);

```

- 查询结果概览：
![运行结果](https://github.com/lihongfei666/oracle/blob/master/test3/联合查询.png)

- 查询执行计划：
![运行结果](https://github.com/lihongfei666/oracle/blob/master/test3/计划1.png )

## 5.联合查询两张表（不分区）对比实验分析
```sql

select * from orders_nopartition, order_details_nopartition where orders_nopartition.order_id = order_details_nopartition.order_id(+);

```
- 查询结果概览：
![运行结果](https://github.com/lihongfei666/oracle/blob/master/test3/不分区查询.png)

- 查询执行计划：
![运行结果](https://github.com/lihongfei666/oracle/blob/master/test3/计划2.png)

- 对比分析：

两张表中数据均为12288条，但是相对于不分区查询，分区查询所需要的时间更短，查询速度更快。所以可以看出分区查询的好处在于查询时候不会扫描完整张表，而是分区查询，从而提高了查询速度。
