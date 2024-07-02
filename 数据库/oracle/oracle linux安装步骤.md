[TOC]

# oracle linux安装步骤

##硬件检查

- 内存检查

  **grep MemTotal /proc/meminfo**

- 共享内存检查

  **d f -k /dev/shm/**

- 交换空间检查

  **grep SwapTotal /proc/meminfo**

- 查看有效的内存和交换空间

  **free**

- 临时目录temp检查

  **d f -k /tmp**

##软件检查

- 检查操作系统版本

  **cat /etc/redhat-release**

- 内核检查

  **uname -r**

## 创建用户和组

> Oracle 安装一般需要在操作中创建3个组和1个操作系统用户oracle。
>
> 组的命名可以是自定义的，但是oracle文档中一般建议这么命名
>
> 1. oracle Inventory组 ---- **oinstall**
>
>    _groupadd oinstall_
>
> 2. 数据库管理员组-----       **dba**
>
>    _groupadd dba_
>
> 3. 数据操作员组-----     **oper**
>
>    _groupadd oper_

- 创建操作系统用户oracle

  **useradd -g install -G dba,oper oracle**

  -G 是指定附属组

- 查看用户的组信息

  **id oracle**

- 修改用户密码

  **passwd oracle**

## 创建相应的目录

- 创建oracle 根目录 $ORACLE_BASE 并修改属主和目录的权限

  1. mkdir -p /u01/app

  2. chown -R oracle:install /u01/app
  3. chmod -R 755 /u01/app

- 创建oracle主目录 $ORACLE_HOME并修改属主和目录的权限
  1. mkdir -p /u01/app/product/11.2.0/db_1
  2. chown -R oracle:install /u01/app/product/11.2.0/db_1
  3. chmod -R 755 /u01/app/product/11.2.0/db_1

- 创建oracle Inventory目录并修改属主和目录的权限

  1. mkdir -p /u01/oraInventory

  2. chown -R oracle:install /u01/oraInventory

  3. chmod -R 755 /u01/oraInventory

- 创建数据文件目录oradata并修改属主和目录的权限
  1. mkdir -p /u01/oradata
  2. chown -R oracle:install /u01/oradata
  3. chmod -R 755 /u01/oradata

## 修改用户的shell限制

- 编辑文件limits.conf

  vi /etc/security/limits.conf

  在文件中加入或者修改下面的内容

  > oracle soft nproc 2047
  >
  > oracle hard nproc 16384
  >
  > oracle soft nodule 1024
  >
  > oracle hard nofile 65536

- 编辑文件login

  vi /etc/pam.d/login

  在文件中加入或者修改下面的内容

  > session required /lib/security/pam_limits.so
  >
  > session required pam_limits.so

- 编辑文件profile

  vi /etc/profile

  在文件中加入或者修改下面的内容

  > if [$USER = "oracle"]; then
  >
  > if [$SHELL = "/bin/ksh"]; then
  >
  > ulimit -p 16384
  >
  > ulimit -p 65536
  >
  > else
  >
  > ulimit -u 16384 -n 65536
  >
  > fi
  >
  > fi

## 修改用户oracle的环境变量

- 从root切换至oracle用户

  su - oracle

- 修改.bash_profile配置文件

  vi ~/.bash_profile

  添加一下内容，主要是oracle的环境变量

  >export ORACLE_BASE=/u01/app #oracle根目录变量
  >
  >export ORACLE_HOME=/u01/app/product/11.2.0/db_1 #oracle家目录变量
  >
  >export ORACLE_SID=orcl #实例名称
  >
  >export umask=022 #设置用户创建文件的默认权限 也就是777-022=755
  >
  >export PATH=\$PATH:$ORACLE_HOME/bin #让oracle用户可以直接使用命令sqlplus等等

  完事之后，应用配置 **source ~/.bash_profile** 。使用**env**命令查看现在的环境变量。

## 修改内核变量

vi /etc/sysctl.conf

[详细解释]: https://blog.csdn.net/qq_21271511/article/details/106182276	"配置文件详解"



