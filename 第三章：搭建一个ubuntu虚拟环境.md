# 第三章：搭建一个ubuntu虚拟环境

## １、添加box项目

### 1.1 创建一个目录存入box  
将下载好的xenial-server-cloudimg-amd64-vagrant.box放于此目录。
```
$ mkdir ubuntu16.04
$ cd ubuntu16.04
```
### 1.2 添加box到vagrant  
我们下载了xenial-server-cloudimg-amd64-vagrant.box，要使用它，必须将其加入到vagrant中。
```
$ vagrant box add　ubuntu16.04　xenial-server-cloudimg-amd64-vagrant.box
$ vagrant box list
```
vagrant box add：添加一个 box 到 Vagrant，这将存储 box 在特定的名称下，以便于多个 Vagrant 环境重复使用。vagrant默认会从`https://app.vagrantup.com` 上去下载，一般公司内网下载不了，我们可先下载好，直接指定本定地址，如下的操作就是先下载到本地然后执行add的。

vagrant box list：查看已加入的项目。

## ２、初始化虚拟机  
创建一个目录，存放虚拟机相关配置信息（Vagrantfile），并执行初始化。  

```
$ mkdir general
$ cd general
$ vagrant init ubuntu16.04
```
vagrant init ubuntu16.04:这一步会在当前目录生成Vagrantfile，可以查看，这个文件主要用于配置虚拟机相关信息，如主机名，cpu，memory，IP等。

## 3、启动与停止  

### 3.1 启动并登录
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

### 3.2 停止虚拟机
```
$ vagrant halt
```
vagrant中，你可以通过 `suspend` 、 `halt` 或 `destroy` 来停止虚拟主机运行。这些选项各有利弊，你应该根据需求选择最适合你的方法。

**Suspending**

调用 `vagrant suspend` 命令将暂停虚拟主机，它会保存当前运行的机器状态然后停止 Vagrant。当你准备开始工作时，只需执行 `vagrant up`，它将从你离开的地方重新开始。这种方法的主要好处是，它是超级快的，停止和开始你的工作通常只需5至10秒。缺点是虚拟机仍然占用你的磁盘空间，并且需要更多的磁盘空间来存储磁盘上的虚拟机内存状态。

**Halting**

调用 `vagrant halt` 命令，vagrant 会优雅地关闭虚拟机操作系统并关闭虚拟机。当你准备开始工作时，只需执行 `vagrant up`。这种方法的好处是，它会干净地关闭您的机器，保存磁盘的内容，并允许它再次干净的启动。缺点是，它会需要一些额外的时间用来冷启动操作系统，并且虚拟机关闭期间仍然占用磁盘空间。

**Destroying**

调用 `vagrant destroy` 命令将从您的系统中删除虚拟机的所有痕迹。它会停止虚拟机，关闭电源，并删除所有的虚拟磁盘信息。再次，当你准备好开始工作时，只需执行 `vagrant up`。这样做的好处是，没有任何东西在你的机器上留下。由虚拟机消耗的磁盘空间和RAM被回收，并且主机保持清洁。缺点是，`vagrant up` 启动时间会花费一些额外的时间，因为它需要重新导入和初始化 provision 。

## ４、完善Vagrantfile

### 4.1 基本配置
```
Vagrant.configure("2") do |config|
  #设置虚拟机的Box，与我们初始化项目时指定的名要保持一致，Vagrant 将通过该配置确定应该使用哪个 box。如果指定 box 在之前没有被添加到 Vagrant，当 Vagrant 运行时 Vagrant 将自动联网下载并添加他们。
  config.vm.box = "ubuntu16.04"

  #指定主机名，这个在集群环境下很有用，通过主机名识别多个虚拟机。
  config.vm.hostname = "ubuntu16.04"

  #指定ip，这里是host-only模式。
  config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.provider "virtualbox" do |vb|
    #设置cpu数量
    vb.cpus = "1"
    #设置内存
    vb.memory = "2048"
    #设置虚拟机的名称
    vb.name = "ubuntu16.04"
  end
end
```
执行`vagrant reload`，再查看相关配置是否生效。

> 注意：ubuntu默认是没有开始ssh密码登录的，需要手动开启；root用户也是单独禁止ssh密码登录的，需要手动开启密码登录；root用户的密码在每次开机随机生成的且默认需要手动修改密码和开启密码登录。默认的用户为vagrant，密码为vagrant。

### 4.2 开始ssh密码登录的
```
vagrant ssh
#修改root密码
sudo passwd root
sudo vi /etc/ssh/sshd_config
#修改PasswordAuthentication no 为：yes，开启变通用户的密码登录
PasswordAuthentication yes
#PermitRootLogin prohibit-password 为：yes，开启root用户的密码登录
PermitRootLogin yes
重启服务
sudo /etc/init.d/ssh restart

```
```
#执行ssh远程登录
ssh root@192.168.33.10
```
## ５、在Vagrantfile中执行脚本
以上面修改ssh相关配置为例，将其相关脚本直接在vagrantfile中配置好，在系统启动时就修改好。在上一步的基本上执行`vagrant destroy`，废弃相关修改。可`vagrant up`看看，之前的ssh相关修改已没了。

### 5.1修改配置
```
Vagrant.configure("2") do |config|
  #设置虚拟机的Box，与我们初始化项目时指定的名要保持一致，Vagrant 将通过该配置确定应该使用哪个 box。如果指定 box 在之前没有被添加到 Vagrant，当 Vagrant 运行时 Vagrant 将自动联网下载并添加他们。
  config.vm.box = "ubuntu16.04"

  #指定主机名，这个在集群环境下很有用，通过主机名识别多个虚拟机。
  config.vm.hostname = "ubuntu16.04"

  #指定ip，这里是host-only模式。
  config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.provider "virtualbox" do |vb|
    #设置cpu数量
    vb.cpus = "1"
    #设置内存
    vb.memory = "2048"
    #设置虚拟机的名称
    vb.name = "ubuntu16.04"
  end

  #使用shell脚本进行软件安装和配置
  config.vm.provision "shell", inline: <<-SHELL
    (echo "vagrant";sleep 1;echo "vagrant") | sudo passwd root > /dev/null
    sudo sed -i "s/PasswordAuthentication no/PasswordAuthentication yes/g" /etc/ssh/sshd_config
    sudo sed -i "s/PermitRootLogin prohibit-password/PermitRootLogin yes/g" /etc/ssh/sshd_config
    sudo /etc/init.d/ssh restart
  SHELL

end
```
执行`vagrant destroy`或直接删除整个虚拟机文件(可打开virtualbox，通过界面进行删除)，然后执行`vagrant up`


## 6、其他配置

### 6.1 共享目录设置
```
# Share an additional folder to the guest VM. The first argument is
# the path on the host to the actual folder. The second argument is
# the path on the guest to mount the folder. And the optional third
# argument is a set of non-required options.
config.vm.synced_folder "../data", "/vagrant_data"
```
### 6.2 共享目录挂载出错

VirtualBox设置共享目录时需要在虚拟机中安装VirtualBox Guest Additions，这个Vagrant会自动安装。但是，VirtualBox Guest Additions是内核模块，当虚拟机的内核升级之后，VirtualBox Guest Additions会失效，导致共享目录挂载失败，出错信息如下:
```
Failed to mount folders in Linux guest. This is usually because
the "vboxsf" file system is not available. Please verify that
the guest additions are properly installed in the guest and
can work properly. The command attempted was:

mount -t vboxsf -o uid=`id -u vagrant`,gid=`getent group vagrant | cut -d: -f3` vagrant /vagrant
mount -t vboxsf -o uid=`id -u vagrant`,gid=`id -g vagrant` vagrant /vagrant

The error output from the last command was:

stdin: is not a tty
/sbin/mount.vboxsf: mounting failed with the error: No such device
```

安装Vagrant插件`vagrant-vbguest`可以解决这个问题，因为该插件会在虚拟机内核升级之后重新安装VirtualBox Guest Additions。
```
vagrant plugin install vagrant-vbguest
```

[第一章：工具介绍](第一章：工具介绍.md)  
[第二章：搭建环境](第二章：搭建环境.md)  
[第三章：搭建一个ubuntu虚拟环境](第三章：搭建一个ubuntu虚拟环境.md)  
[第四章：搭建集群ubuntu虚拟环境](第四章：搭建集群ubuntu虚拟环境.md)  
[附录一：虚拟机网络模式](附录一：虚拟机网络模式.md)  
