# 系统重启后Mongo服务启动失败

## 环境
- 硬件：树莓派4B（RaspberryPi 4B）
- OS：Ubuntu19.10
- Mongod：db version v4.2.3

## 问题
照官方手册安装好Mongo后，启动并检查状态，没有问题。

重启系统后，调试项目时发现 Mongo 无法连接。检查Mongod 服务状态：

` sudo systemctl status mongod `

显示未启动：

> Active: Failed...

查日志：

`sudo vi /var/log/mongodb/mongod.log`

发现

> Failed to unlink socket file /tmp/mongodb-27017.sock Operation not permitted

应是 mongodb-27017.sock 文件问题。bing一下，找到解决办法：

https://mkyong.com/mongodb/mongodb-failed-to-unlink-socket-file-tmpmongodb-27017/

大意是，安装Mongo 后启动服务时创建 mongodb-27017.sock归属于当前用户而不是 Mongo，重启后，Mongo 因为无此文件权限故无法启动服务。

查看文件权限：

` ls -ls /tmp/mongodb-27017.sock`

返回：

> 0 srwx------ 1 ubuntu ubuntu 0 Aug 24 03:56 /tmp/mongodb-27017.sock

## 解决步骤

删除 mongodb-27017.sock 文件：

` $ sudo rm -rf /tmp/mongodb-27017.sock`

启动服务：

` $ sudo service mongod start`

再查一下状态：

> Active: active (running) since Wed 2019-12-04 00:42:41 CST; 3 months 22 days ago

重启后再查，正常启动。

Over




