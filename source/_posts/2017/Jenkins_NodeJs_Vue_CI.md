---
title: Jenkins使用NodeJS插件完成Vue的可持续集成
date: 2018/01/16 19:00:39
tags: 
- 前端
- 可持续集成
categories: 
- 开发
---
![jenkins](https://v.moring.pw/mchen/img/2018/jenkins/jenkins_top.jpg)

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
这里有个小坑，我的服务器上的8080端口被占用了，换了一个...

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
Jenkins Dashboard -> 系统管理 -> 管理插件 -> 可选插件
过滤里选择NodeJs，勾选插件直接安装。
![nodejs_plugin](https://v.moring.pw/mchen/img/2018/jenkins/jenkins_nodejs_plugin.jpg)

#### 配置NodeJs插件
Jenkins Dashboard -> 系统管理 -> 全局工具配置
找到NodeJs节点，目前最新的LTS版本是8.9.4，选择对应版本安装即可。
![nodejs_config](https://v.moring.pw/mchen/img/2018/jenkins/jenkins_nodejs_config.png)

#### 新建jenkins任务
Jenkins Dashboard -> 新建任务，构建一个自由风格的软件项目。
![new_job_setp1](https://v.moring.pw/mchen/img/2018/jenkins/jenkins_new_job_step1.png)

选择github项目，vue的初始化项目，https://github.com/chen-c/test.git/。
如果找不到GitBucket选项卡，去管理插件中安装下GitBucket插件。
![new_job_setp2](https://v.moring.pw/mchen/img/2018/jenkins/jenkins_new_job_step2.png)

源码管理中选择Git
![new_job_setp3](https://v.moring.pw/mchen/img/2018/jenkins/jenkins_new_job_step3.png)

构建环境中选择Provide Node&npm bin/ folder to PATH，Installation中选择之前安装的NodeJs版本。
新增一个构建步骤，选择Execute shell，简单的就先放两个命令。
``` bash
npm install
npm run build
```
![new_job_setp4](https://v.moring.pw/mchen/img/2018/jenkins/jenkins_new_job_step4.png)
全部完成后保存应用一下。

#### 立即构建
点击立即构建，biu~
![new_job_setp5](https://v.moring.pw/mchen/img/2018/jenkins/jenkins_new_job_step5.png)

编译完成后的vue就在服务器工作空间的dist文件夹里，后续可以在任务的构建后操作里增加一些复制打包等shell命令，就能完成自动部署等相关功能了。
比如本hexo就由Jenkins自动打包并部署到DO上。

参考链接:
> https://segmentfault.com/a/1190000007086764
> https://www.cnblogs.com/vipzhou/p/7890016.html
