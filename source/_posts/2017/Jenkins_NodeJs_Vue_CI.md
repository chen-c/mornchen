---
title: Jenkins使用NodeJS插件完成Vue的可持续集成
date: 2018/01/16 19:00:39
tags: 
- 前端
- 可持续集成
categories: 
- 开发
---
项目使用Vue做前端开发，故涉及到使用Jenkins做可持续集成，记录一下其中过程。

<!-- more -->
### Jenkins的安装

#### 查看一下系统信息
我使用的是DigitalOcean的服务器，系统版本CentOS 7.4.1708 x64。

#### 安装Java
``` bash
yum install java
```
查看一下java版本，应该安装的是openjdk的版本。gcj版本的java会导致jenkins不工作，[JENKINS-743](https://issues.jenkins-ci.org/browse/JENKINS-743)
``` bash
java -version
openjdk version "1.8.0_151"
OpenJDK Runtime Environment (build 1.8.0_151-b12)
OpenJDK 64-Bit Server VM (build 25.151-b12, mixed mode)
```

#### 拉取Jenkins库配置到本地
``` bash
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
```

#### 导入公钥
``` bash
sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
```

#### 安装Jenkins
``` bash
sudo yum -y install jenkins
```
yum真是好东西。

#### Jenkins配置文件及日志
``` bash
cat /etc/sysconfig/jenkins | more #默认端口8080
cd /var/lib/jenkins/logs
cat /var/log/jenkins/jenkins.log
```

#### 启动服务
``` bash
service jenkins start #停止 stop
```

#### 访问Jenkins
url:8080
这里有个小坑，服务器上的8080端口被占用了，

#### 初始化Jenkins
浏览器打开地址后，基本上按照页面提示就能完成初始化了，比如获取初始密码，安装插件等。
![初始化](https://v.moring.pw/mchen/img/2018/jenkins/20180116212350.jpg)

#### 卸载Jenkins
``` bash
rpm -e jenkins
find / -iname jenkins | xargs -n 1000 rm -rf
```

### NodeJs编译Vue

#### 安装NodeJs插件