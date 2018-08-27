# ubuntu下virtualbox与vagrant的安装、配置以及日常使用
本项目主要介绍在ubuntu下进行单个、集群的虚拟机环境的快速搭建，以解决我们日常学习，工作中相关环境的问题以及如何在ubuntu下隔离各种各样的学习、开发环境的问题。　　
## 工具介绍：
- virtualbox
VirtualBox is a general-purpose full virtualizer for x86 hardware, targeted at server, desktop and embedded use.  
For a thorough introduction to virtualization and VirtualBox, please refer to the [online version](https://www.virtualbox.org/manual/ch01.html) of the VirtualBox User Manual's first chapter.   

- vagrant
Vagrant is a tool for building and managing virtual machine environments in a single workflow. With an easy-to-use workflow and focus on automation, Vagrant lowers development environment setup time, increases production parity, and makes the "works on my machine" excuse a relic of the past.

## 工具安装
- ubuntu  
本次使用的是16.04
- [下载virtualbox](https://www.virtualbox.org/wiki/Linux_Downloads)  
本次下载使用的是：virtualbox-5.2_5.2.18-124319~Ubuntu~xenial_amd64.deb，下载完成执行dpkg -i安装即可。
- [下载vagrant](https://www.vagrantup.com/downloads.html)  
本次下载使用的是：vagrant_2.1.2_x86_64.deb，下载完成执行dpkg -i安装即可。

## 概念说明
- virtualbox  
[虚拟机网络模式](Bridged-NAT-Host-Only.md)

- vagrant  
**box：**box相当于虚拟机所依赖的镜像文件，可在[这里下载](https://app.vagrantup.com/boxes/search)相关系统的box
