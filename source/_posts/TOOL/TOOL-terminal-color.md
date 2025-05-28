---
title: 简易的终端染色方案
category_bar: true
math: false
tags: [终端]
category: [工具]
date: 2025-04-14 21:09:41
---

在使用Windows终端中的PowerShell时，经常会有很多命令与输出混在一起，分辨不出每条命令的起始位置。一种解决方案是通过`on-my-posh`的主题美化Windows终端，但有一个缺点是性能非常低，每次启动、回车之后都有肉眼可见的延迟。
那么退而求其次，当不需要非常漂亮的美化主题，而只是需要将命令行的每次输出路径染色，可以通过修改PowerShell的配置文件来完成。
当进入git仓库时，也可以配置像Git Bash一样显示分支名。

## 显示效果

![终端染色效果](img/TOOL/terminal-color-version.png)

这个染色效果在vscode集成的终端中也可以正常显示。

## 配置方法
打开PowerShell（并非命令提示符）通过如下命令打开配置文件：
```powershell
code $profile  # 使用vscode打开
notepad $profile  # 使用记事本打开
```
加入以下代码：
```powershell
function global:prompt {
    # 构建提示字符串
    Write-Host "PS $($executionContext.SessionState.Path.CurrentLocation)>>" -NoNewline -ForegroundColor DarkMagenta
    # 颜色为DarkMagenta深紫色，也可以修改为其他颜色
    
    # 尝试获取Git分支信息（原生方式）
    try {
        $branch = git rev-parse --abbrev-ref HEAD 2>$null
        if ($branch) {
            Write-Host "[$branch]" -NoNewline -ForegroundColor Green
            # git分支名显示为Green绿色
        }
    } catch {}
    
    # 设置窗口标题，也可以不修改
    $host.UI.RawUI.WindowTitle = "PS $($executionContext.SessionState.Path.CurrentLocation)"
    
    return " "
}
```
保存并重启终端即可看到显示效果。

可以使用如下命令查看一些常见的标准颜色：
```powershell
[Enum]::GetValues([System.ConsoleColor]) | ForEach-Object { Write-Host $_ -ForegroundColor $_ }
```

