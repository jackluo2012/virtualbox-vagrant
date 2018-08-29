# 第三章：搭建一个centos7虚拟环境.md

## １、添加box项目

### 创建一个目录存入box  
将下载好的centos-7.1.box放于此目录。
```
$ mkdir VM
$ cd VM
```
### 添加box到vagrant  
我们下载了centos-7.1.box，要使用它，必须将其加入到vagrant中。
```
$ vagrant box add　centos7　centos-7.1.box
$ vagrant box list
```
vagrant box add：添加一个 box 到 Vagrant，这将存储 box 在特定的名称下，以便于多个 Vagrant 环境重复使用。vagrant默认会从`https://app.vagrantup.com｀上去下载，一般公司内网下载不了，我们可先下载好，直接指定本定地址，如下的操作就是先下载到本地然后执行add的。

vagrant box list：查看已加入的项目。

## ２、初始化虚拟机  
创建一个目录，存放虚拟机相关配置信息（Vagrantfile），并执行初始化。  

```
$ mkdir vm-centos7
$ cd vm-centos7
$ vagrant init centos7
```
vagrant init centos7:这一步会在当前目录生成Vagrantfile，可以查看，这个文件主要用于配置虚拟机相关信息，如主机名，cpu，memory，IP等。

## 3、启动与停止  

### 启动并登录
```
$ vagrant up
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

### 停止虚拟机
```
vagrant halt
```
vagrant中，你可以通过 `suspend` 、 `halt` 或 `destroy` 来停止虚拟主机运行。这些选项各有利弊，你应该根据需求选择最适合你的方法。

**Suspending**

调用 `vagrant suspend` 命令将暂停虚拟主机，它会保存当前运行的机器状态然后停止 Vagrant。当你准备开始工作时，只需执行 `vagrant up`，它将从你离开的地方重新开始。这种方法的主要好处是，它是超级快的，停止和开始你的工作通常只需5至10秒。缺点是虚拟机仍然占用你的磁盘空间，并且需要更多的磁盘空间来存储磁盘上的虚拟机内存状态。

**Halting**

调用 `vagrant halt` 命令，vagrant 会优雅地关闭虚拟机操作系统并关闭虚拟机。当你准备开始工作时，只需执行 `vagrant up`。这种方法的好处是，它会干净地关闭您的机器，保存磁盘的内容，并允许它再次干净的启动。缺点是，它会需要一些额外的时间用来冷启动操作系统，并且虚拟机关闭期间仍然占用磁盘空间。

**Destroying**

调用 `vagrant destroy` 命令将从您的系统中删除虚拟机的所有痕迹。它会停止虚拟机，关闭电源，并删除所有的虚拟磁盘信息。再次，当你准备好开始工作时，只需执行 `vagrant up`。这样做的好处是，没有任何东西在你的机器上留下。由虚拟机消耗的磁盘空间和RAM被回收，并且主机保持清洁。缺点是，`vagrant up` 启动时间会花费一些额外的时间，因为它需要重新导入和初始化 provision 。

## ４、完善Vagrantfile
```
Vagrant.configure("2") do |config|
  #设置虚拟机的Box，与我们初始化项目时指定的名要保持一致，Vagrant 将通过该配置确定应该使用哪个 box。如果指定 box 在之前没有被添加到 Vagrant，当 Vagrant 运行时 Vagrant 将自动联网下载并添加他们。
  config.vm.box = "centos7"

  #指定主机名，这个在集群环境下很有用，通过主机名识别多个虚拟机。
  config.vm.hostname = "vm-centos7"

  #指定ip，这里是host-only模式。
  config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.provider "virtualbox" do |vb|
    #设置cpu数量
    vb.cpus = "1"
    #设置内存
    vb.memory = "2048"
    #设置虚拟机的名称
    vb.name = "vm-centos7"
  end
end
```
重启，再查看相关信息是否发生变更。

此时我们可以在主机上通过ssh，访问192.168.33.10，虚拟机root用户的默认密码为vgrant。
```
ssh root@192.168.33.10
```
