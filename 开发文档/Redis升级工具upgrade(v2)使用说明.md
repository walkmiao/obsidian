## 参数说明
输入`./upgrade redis --help`可以获得详细的参数说明，以下是说明图片
![[Pasted image 20240130160058.png]]


参数详细说明：
- --authPassword              远程仓库的密码,默认无需指定

- --authUser                       远程仓库的用户，默认无需指定

- --host                               需要升级的服务器ip，必须要指定

- --old                                  原redis容器的名称，需要指定。通过命令`docker ps -a` 可以查看当前的redis的容器名称

- --oldPassword                 原redis的密码(如果原来redis有密码需要指定，没有的话不需要指定)

- --oldPort                          原redis的端口默认为6379，如果分行有特殊的不是6379的则需要指定

- --image                            需要拉取的新的redis镜像名称，请联系`设备二部`获取。需要指定

- --newPassword              升级后的redis密码，按照安全要求升级后必须要指定密码

- --newPort                        升级后的redis端口，默认为6379。如分行有特殊需求，可以改成其他

- --sync                              主从同步的时间，默认为5m（即5分钟，可以根据实际分行的数据修改）


## 适用于大部分分行的命令

一般分行redis未升级前不具备密码，端口一般都为6379。下面的命令即可满足需求
`./upgrade redis --host="服务器IP" --image="76.125.33.125:9005/dcim/bitnami/redis:latest" --old="redis" --newPassword="指定密码"`



