---
title: Notepad4安装与配置
category_bar: true
math: false
tags: [记事本]
category: [工具]
date: 2026-01-29 14:04:00
---

由于电脑出了神奇的问题，记事本挂了，因此寻找轻量级的替代品，发现了[Notepad4](https://github.com/zufuliu/notepad4)这样一个极好的软件，但是官网只提供压缩包版不提供安装包，因此记录一下安装与配置过程。

# 安装
可以使用官方仓库的压缩包，解压即可使用。如果需要命令行打开，可以将解压目录添加到环境变量Path中。

也可以使用WinGet安装：
```powershell
winget install -e --id zufuliu.notepad4  # 安装
winget upgrade notepad4  # 更新
```
这样安装的Notepad4会自动添加到环境变量Path中。可以使用`where.exe notepad4`查看安装路径。

# 添加到鼠标右键菜单
可以通过注册表添加Notepad4到鼠标右键菜单中，方便快速打开文件。
将以下代码保存为`.reg`文件，使用**UTF-16 LE**编码，将路径修改为实际安装路径，然后双击导入注册表即可。

```reg
Windows Registry Editor Version 5.00

; ================================================
; Notepad4 右键菜单配置
; 安装路径：C:\Users\admin\AppData\Local\Microsoft\WinGet\Packages\zufuliu.notepad4_Microsoft.Winget.Source_8wekyb3d8bbwe\Notepad4.exe
; ================================================

; ====== 添加"用Notepad4打开"到所有文件右键菜单 ======
[HKEY_CURRENT_USER\Software\Classes\*\shell\Notepad4]
@="用 Notepad4 打开"
"Icon"="C:\\Users\\admin\\AppData\\Local\\Microsoft\\WinGet\\Packages\\zufuliu.notepad4_Microsoft.Winget.Source_8wekyb3d8bbwe\\Notepad4.exe,0"

[HKEY_CURRENT_USER\Software\Classes\*\shell\Notepad4\command]
@="\"C:\\Users\\admin\\AppData\\Local\\Microsoft\\WinGet\\Packages\\zufuliu.notepad4_Microsoft.Winget.Source_8wekyb3d8bbwe\\Notepad4.exe\" \"%1\""
```

如果要删除注册表对应项，可以使用以下代码保存为`.reg`文件导入.

```reg
Windows Registry Editor Version 5.00

; ================================================
; 删除 Notepad4 右键菜单
; ================================================

; 删除"用Notepad4打开"菜单项
[-HKEY_CURRENT_USER\Software\Classes\*\shell\Notepad4]
```

