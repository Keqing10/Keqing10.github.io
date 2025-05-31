---
title: 安装WSL后移动到其他硬盘
category_bar: true
math: false
tags: [WSL]
category: [工具]
date: 2025-05-28 20:49:17
---

WSL默认是安装在C盘的，可以通过如下方法移动到其他硬盘，同时也可以对已安装的wsl进行重命名。
本文以安装FedoraLinux-42（安装后的默认名字）为例，从C盘移动到D盘。

### 终止当前WSL进程
```bash
wsl --shutdown
```

### 导出当前的WSL发行版

```bash
wsl --export FedoraLinux-42 D:/Fedora.tar
```
第一个参数是当前WSL的名字，一定要严格一致，可以通过`wsl -l -v`查看；第二个参数是导出的压缩包保存路径（临时）。

### 注销当前发行版
```bash
wsl --unregister FedoraLinux-42
```
同样注意名字一致。

### 在新位置导入

```bash
# wsl --import <发行版名称> <新安装路径> <导出的tar文件路径>
wsl --import Fedora D:/VM/WSL/Fedora D:/Fedora.tar
```

此时进入路径`D:/VM/WSL/Fedora`可以看到有如下两个文件：
```bash
~ > ls
Mode         LastWriteTime         Length Name
----         -------------         ------ ----
-a----                xxxx      616562688 ext4.vhdx
-a----                xxxx           8420 shortcut.ico
```
分别是虚拟硬盘文件和图标文件。