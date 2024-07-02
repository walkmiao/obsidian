[TOC]

# Oracle 常用操作

## 1. 是否锁表

- 查看是否锁表

  当使用select语句查询表时，后面跟着for update
  
  当使用select语句查询表时，后面跟着for update ，

  ```sql
  select * from  table  for update
  ```

​		当修改表中数据，但是没有commit就关掉PL/SQL，下次再打开，执行带for update的sql语句，就会卡死

​		查看锁表进程SQL语句： 
   ```sql
  select * from v$session t1, v$locked_object t2 where t1.sid = t2.SESSION_ID; 
   ```

​		如果有记录，可查到如下锁表记录
​		 ![img](https://images2015.cnblogs.com/blog/990022/201609/990022-20160928113045203-896805276.png)	

- 锁表解锁

  杀掉锁表进程： 
  记录下SID和serial# ，分别替换掉下面的1155,39095，即可解除锁表

  ```sql
  alter system kill session '1155,39095'; 
  ```

  ![img](https://images2015.cnblogs.com/blog/990022/201609/990022-20160928114259641-725298881.png)

## 2.执行操作系统命令

sqlplus登陆后 可以执行**host  操作系统的命令（例如ls，dir）**

