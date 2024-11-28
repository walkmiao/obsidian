---
page-title: 2024-08-20 16:21:22-"odoo 权限管理学习总结 - 授客 - 博客园"
url: https://www.cnblogs.com/shouke/p/17135926.html
web-message:
web-badge-color: ""
web-badge-message:
date: "2024-08-20 16:21:22"
---

> #### **eval语法说明**
> 
> -   `(0, 0, values)` 从提供的`valueS`字典创建新记录，形如`(0, 0, {'author': user_root.id, 'body': 'one'})`。
> -   `(2, ID, values)` 使用`values`字典中的值更新id值=ID的现有记录
> -   `(2, ID)` 删除id=ID这条记录（调用unlink方法，删除数据及整个主从数据链接关系）
> -   `(3, ID)` 删除主从数据的链接关系但是不删除这个记录
> -   `(4, ID)` 为id=ID的数据添加主从链接关系
> -   `(5)` 去除所有的链接关系,也就是循环所有的从数据且调用`(3,ID)`
> -   `(6, 0, [IDs])` 用IDs中的记录替换原来链接的记录（相当于先执行`(5)`再循环执行`(4, ID)`）
