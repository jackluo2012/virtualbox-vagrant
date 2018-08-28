# ubuntu下virtualbox与vagrant的安装、配置以及日常使用
本项目主要介绍在ubuntu下进行单个、集群的虚拟机环境的快速搭建，以解决我们日常学习，工作中相关环境的问题以及如何在ubuntu下隔离各种各样的学习、开发环境的问题。　　



##　vargrant的几个常用命令  
```
vagrant box add（添加一个 box 到 Vagrant，这将存储 box 在特定的名称下，以便于多个 Vagrant 环境重复使用。）
例：vagrant box add　centos7 centos/7  
vagrant默认会从https://app.vagrantup.com上去下载，一般公司内网下载不了，我们可先下载好，直接指定本定地址，box的存放目录执行如下：
例：vagrant box add　centos7　centos7.box
```
```
vagrant init add　centos7
初始化vagrant项目，会生成一个Vagrantfile文件（后面会讲到），用于配置虚拟机相关信息。
```
```
vagrant up（启动当前虚拟机,项目默认基于 VirtualBox 的，但是Vagrant还可以与各式各样的其他 providers 一起工作，如 VMware , AWS 等等，vagrant up --provider=vmware_fusion）
vagrant ssh（连接虚拟机）
```
## 创建第一个虚拟机
