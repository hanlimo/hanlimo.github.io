---
layout:     post
title:      "CI "
subtitle:   "Hello World, Hello Blog"
date:       2019-02-14 12:00:00
author:     "Hanlimo"
header-img: "img/post-bg-2015.jpg"
tags:
	- Tech
---

# CI流程搭建
1. 加载必要的docker镜像：gogs、mysql、habor、jenkins；
2. 启动mysql：
```
docker run --name mysql -p 3306:3306 -v /docker-data/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql
```
3. 可能根据需要修改数据编码和引擎，在文件/etc/mysql/conf.d/，[mysqld]标签下添加：
```
 default-character-set = utf8
 character_set_server = utf8
 default-storage-engine=INNODB
```
4. 启动gogs：
```
docker run -d -p 10022:22 -p 3000:3000
--name=gogs -v /docker-data/gogs/data:/opt/docker/gogs/ -v /docker-data/gogs/app:/app gogs/gogs
```
5. 连接mysql数据，密码为启动mysql容器时设置的密码，其他默认即可：
![](CI%E6%B5%81%E7%A8%8B%E6%90%AD%E5%BB%BA/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-12-16%20%E4%B8%8B%E5%8D%8812.04.56.png)
6. 数据库的地址，实验中采用的docker部署，默认使用bridge模式，所以mysql的数据库地址应为docker虚拟地址，进入容器，执行 `cat /etc/hosts`
 最后一行即为容器地址，后期采用K8S部署时可忽略此步骤；
7. 安装之后自动跳转到欢迎页面，注册用户并登陆 ，到此为止就可以拥有和github.com一样功能的本地git了：
![](CI%E6%B5%81%E7%A8%8B%E6%90%AD%E5%BB%BA/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-12-16%20%E4%B8%8B%E5%8D%8812.47.00.png)
7. harbor离线安装，也可以helm安装harbor[官方地址](https://github.com/goharbor/harbor-helm)
```
第一步：配置harbor.cfg
第二部：tar解压
第三部：运行install.sh
```
9. 启动jenkins
 `docker run -v /data/jenkins/plugins:/var/jenkins_home/plugins -itd -p 8080:8080 -p 50000:50000 --name=jenkins jenkins/jenkins`
9. 访问http://localhost:8080，登陆jenkins，很吃配置要等待很久
![](CI%E6%B5%81%E7%A8%8B%E6%90%AD%E5%BB%BA/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-12-16%20%E4%B8%8B%E5%8D%885.31.02.png)
11. 在Jenkins界面配置Git+Jenkins持续集成作业（Job）
12. 【优化】设置软件包缓存加快构建速度，bup优化docker镜像打包的网络资源消耗
13. 【优化】容器化CI