# 第四章：搭建集群ubuntu虚拟环境

## 1、初始化虚拟机  
接[第三章：搭建一个ubuntu虚拟环境](第三章：搭建一个ubuntu虚拟环境.md)，在ubuntu16.04下创建一个`cluster`目录，存放虚拟机相关配置信息（Vagrantfile）。  

```
$ mkdir cluster
$ cd cluster
$ cp ../general/Vagrantfile .
```
这里直接复制[第三章：搭建一个ubuntu虚拟环境](第三章：搭建一个ubuntu虚拟环境.md)中最后的Vagrantfile文件。

## ２、修改配置
通过修改Vagrantfile配置文件实现集群环境。
```
Vagrant.configure("2") do |config|
  (1..3).each do |i|
      config.vm.define "ubuntu16.04-node#{i}" do |node|
          #设置虚拟机的Box，与我们初始化项目时指定的名要保持一致，Vagrant 将通过该配置确定应该使用哪个 box。如果指定 box 在之前没有被添加到 Vagrant，当 Vagrant 运行时 Vagrant 将自动联网下载并添加他们。
          node.vm.box = "ubuntu16.04"

          #指定主机名，这个在集群环境下很有用，通过主机名识别多个虚拟机。
          node.vm.hostname = "ubuntu16.04-node#{i}"

          #指定ip，这里是host-only模式。
          node.vm.network "private_network", ip: "192.168.33.#{i+10}"
          node.vm.provider "virtualbox" do |vb|
            #设置cpu数量
            vb.cpus = "1"
            #设置内存
            vb.memory = "2048"
            #设置虚拟机的名称
            vb.name = "ubuntu16.04-node#{i}"
          end

          #使用shell脚本进行软件安装和配置
          node.vm.provision "shell", inline: <<-SHELL
            (echo "vagrant";sleep 1;echo "vagrant") | sudo passwd root > /dev/null
            sudo sed -i "s/PasswordAuthentication no/PasswordAuthentication yes/g" /etc/ssh/sshd_config
            sudo sed -i "s/PermitRootLogin prohibit-password/PermitRootLogin yes/g" /etc/ssh/sshd_config
            sudo /etc/init.d/ssh restart
          SHELL
      end
  end
end
```
与创建单个虚拟机相比，创建多个虚拟机时多了一层循环，而变量i可以用于设置节点的名称与IP，使用#{i}取值：
```
(1..3).each do |i|

end
```
注意：`config.vm.box`、`config.vm.hostname`、`config.vm.network`...都变为了`node.vm.box`、`node.vm.hostname`、`node.vm.network`。

## 3、启动
```
vagrant up
Bringing machine 'ubuntu16.04-node1' up with 'virtualbox' provider...
Bringing machine 'ubuntu16.04-node2' up with 'virtualbox' provider...
Bringing machine 'ubuntu16.04-node3' up with 'virtualbox' provider...
==> ubuntu16.04-node1: You assigned a static IP ending in ".1" to this machine.
==> ubuntu16.04-node1: This is very often used by the router and can cause the
==> ubuntu16.04-node1: network to not work properly. If the network doesn't work
==> ubuntu16.04-node1: properly, try changing this IP.
==> ubuntu16.04-node1: Importing base box 'ubuntu16.04'...
==> ubuntu16.04-node1: Matching MAC address for NAT networking...
==> ubuntu16.04-node1: You assigned a static IP ending in ".1" to this machine.
==> ubuntu16.04-node1: This is very often used by the router and can cause the
==> ubuntu16.04-node1: network to not work properly. If the network doesn't work
==> ubuntu16.04-node1: properly, try changing this IP.
==> ubuntu16.04-node1: Setting the name of the VM: ubuntu16.04-node1
==> ubuntu16.04-node1: Clearing any previously set network interfaces...
==> ubuntu16.04-node1: Preparing network interfaces based on configuration...
    ubuntu16.04-node1: Adapter 1: nat
    ubuntu16.04-node1: Adapter 2: hostonly
....
```
## 4、集群管理
### 常用命令

下面是一些常用的Vagrant管理命令，操作特定虚拟机时仅需指定虚拟机的名称。
```
vagrant ssh: SSH登陆虚拟机
vagrant halt: 关闭虚拟机
vagrant destroy: 删除虚拟机
vagrant ssh-config 查看虚拟机SSH配置
```

启动单个虚拟机：
```
vagrant up node1
```

启动多个虚拟机：
```
vagrant up node1 node3
```

启动所有虚拟机：
```
vagrant up
```

### SSH免密码登陆

使用vagrant ssh命令登陆虚拟机必须切换到Vagrantfile所在的目录，而直接使用虚拟机IP登陆虚拟机则更为方便:
``
ssh vagrant@192.168.33.11
```
此时SSH登陆需要输入虚拟机vagrant用户的密码，即vagrant
将主机的公钥复制到虚拟机的authorized_keys文件中即可实现SSH免密码登陆:
```
cat $HOME/.ssh/id_rsa.pub | ssh vagrant@192.168.59.1 'cat >> $HOME/.ssh/authorized_keys'
```
