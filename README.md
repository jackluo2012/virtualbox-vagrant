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
```
$ mkdir VM（将下载好的centos7.box放于此目录）
$ cd VM
```
```
$ vagrant box add　centos7　centos7.box（将centos7.box加到vagrant中）
$ vagrant box list（查看已加入的项目）
```
```
$ mkdir master（存放虚拟相关信息，如Vagrantfile）
$ cd master
$ vagrant init centos7（这一步会在当前目录生成Vagrantfile，可以查看）
```
```
$ vagrant up（启动虚拟机）
启动部分信息如下，默认是以NAT模式配置网络信息
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key

$ vagrant ssh(登录)
```
## Vagrantfile配置及说明
[Var]


## 停止虚拟机
Vagrant 中，你可以通过 `suspend` 、 `halt` 或 `destroy` 来停止虚拟主机运行。这些选项各有利弊，你应该根据需求选择最适合你的方法。

- Suspending

调用 `vagrant suspend` 命令将暂停虚拟主机，它会保存当前运行的机器状态然后停止 Vagrant。当你准备开始工作时，只需执行 `vagrant up`，它将从你离开的地方重新开始。这种方法的主要好处是，它是超级快的，停止和开始你的工作通常只需5至10秒。缺点是虚拟机仍然占用你的磁盘空间，并且需要更多的磁盘空间来存储磁盘上的虚拟机内存状态。
- Halting

调用 `vagrant halt` 命令，vagrant 会优雅地关闭虚拟机操作系统并关闭虚拟机。当你准备开始工作时，只需执行 `vagrant up`。这种方法的好处是，它会干净地关闭您的机器，保存磁盘的内容，并允许它再次干净的启动。缺点是，它会需要一些额外的时间用来冷启动操作系统，并且虚拟机关闭期间仍然占用磁盘空间。

- Destroying
调用 `vagrant destroy` 命令将从您的系统中删除虚拟机的所有痕迹。它会停止虚拟机，关闭电源，并删除所有的虚拟磁盘信息。再次，当你准备好开始工作时，只需执行 `vagrant up`。这样做的好处是，没有任何东西在你的机器上留下。由虚拟机消耗的磁盘空间和RAM被回收，并且主机保持清洁。缺点是，`vagrant up` 启动时间会花费一些额外的时间，因为它需要重新导入和初始化 provision 。
