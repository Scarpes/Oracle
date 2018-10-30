# 实验2：用户及权限管理

## 实验内容：
Oracle有一个开发者角色resource，可以创建表、过程、触发器等对象，但是不能创建视图。本训练要求：
- 在pdborcl插接式数据中创建一个新的本地角色con_res_view，该角色包含connect和resource角色，同时也包含CREATE VIEW权限，这样任何拥有con_res_view的用户就同时拥有这三种权限。
- 创建角色之后，再创建用户new_user，给用户分配表空间，设置限额为50M，授予con_res_view角色。
- 最后测试：用新用户new_user连接数据库、创建表，插入数据，创建视图，查询表和视图的数据。

## 实验步骤

我创建的对象名称:1. 角色--CON_RES_VIEW_ZLH； 2. 用户名：NEW_USER_ZLH。

- 第1步：以system登录到pdborcl，创建角色CON_RES_VIEW_ZLH和用户NEW_USER_ZLH，并授权和分配空间：

```sql
CREATE ROLE CON_RES_VIEW_ZLH;
GRANT CONNECT, RESOURCE, CREATE VIEW TO CON_RES_VIEW_ZLH;
CREATE USER NEW_USER_ZLH IDENTIFIED BY 123 DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
ALTER USER NEW_USER_ZLH QUOTA 50M ON USERS;	
GRANT CON_RES_VIEW_ZLH TO NEW_USER_ZLH;	
```

运行结果：
![代码](https://github.com/lihongfei666/oracle/blob/master/test2/步骤1-代码.png )
![运行结果](https://github.com/lihongfei666/oracle/blob/master/test2/步骤1-运行结果.png )


- 第2步：新用户NEW_USER_ZLH连接到pdborcl，创建表MYTABLE和视图MYVIEW，插入数据，最后将MYVIEW的SELECT对象权限授予HR用户。

```sql
GRANT SELECT ON MYVIEW TO HR;
```

运行结果：
![代码](https://github.com/lihongfei666/oracle/blob/master/test2/步骤2-代码.png )
![运行结果](https://github.com/lihongfei666/oracle/blob/master/test2/步骤2-运行结果.png )

 

- 第3步：用户HR连接到pdborcl，查询NEW_USER_ZLH授予它的视图MYVIEW

```sql
SELECT * FROM NEW_USER_ZLH.MYVIEW;
```

运行结果：
![运行结果](https://github.com/lihongfei666/oracle/blob/master/test2/步骤3-运行结果.png )


## 实验分析
????
