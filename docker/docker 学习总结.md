# docker 学习总结

## docker的技术实现

>docker的实现主要归结于三大技术
>
>1. NAMESPACE 命名空间
>2. CGroups(control groups)资源控制组
>3. UFS (union file system) 联合文件系统

- NAMESPACE 命名空间

  命名空间是Linux内核2.4之后引入的用于运行隔离的模块，能够将计算机的资源进行切割划分，形成各自独立的空间。

  主要包括有PID NAMESPACE,NET NAMESPACE,MOUNT NAMESPACE,USER NAMESPACE

  比如pid namespace，就可以创造一个独立的运行空间，在其中进程的pid又将从1开始。在这个空间中的进程完全感知不到外界的进程。

- CGroups 控制资源组

  cgroups主要的作用是对硬件资源的隔离和分配，通过cgroups我们可以指定任意的隔离环境对资源的占用值和占用率

- UFS联合文件系统的改进版 AUFS( AdvanceUFS)

  AUFS将文件的更新挂载到旧的文件之上而不去修改未更新的文件内容，这就保证了即使对虚拟的文件系统的反复修改，也能保证在真实的文件占用保持在一个较低的水平

## docker的四大组成对象

- 镜像
- 容器
- 网络
- 数据卷

