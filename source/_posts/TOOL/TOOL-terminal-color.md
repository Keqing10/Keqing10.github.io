---
title: 简易的终端染色方案
category_bar: true
math: false
tags: [终端]
category: [工具]
date: 2025-04-14 21:09:41
---

在使用Windows终端中的PowerShell时，经常会有很多命令与输出混在一起，分辨不出每条命令的起始位置。一种解决方案是通过on my posh的主题美化Windows终端，但有一个缺点是性能非常低，每次启动、回车之后都有肉眼可见的延迟。
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

---

{% note %}
更新
{% endnote %}
## 使用on my posh的方法
首先下载安装on my posh，可以直接在微软商店安装。
然后安装[Nerd字体](https://www.nerdfonts.com/font-downloads)。

打开PowerShell，执行`code $profile`打开配置文件，加入如下代码：
```powershell
oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH/theme_example.omp.json" | Invoke-Expression
```
其中文件名可以替换为其他主题或自定义文件，路径为`C:\Users\<用户名>\AppData\Local\Programs\oh-my-posh\themes`。

### 一个主题分享

```json
{
  "$schema": "https://raw.githubusercontent.com/JanDeDobbeleer/oh-my-posh/main/themes/schema.json",
  "blocks": [
    {
      "alignment": "left",
      "segments": [
        {
          "background": "#003543",
          "foreground": "#00c983",
          "leading_diamond": "\ue0b6",
          "style": "diamond",
          "template": "{{ .Icon }}  {{ .UserName }}<#ffffff>@</><#e06c75>{{ .HostName }}</> ",
          "type": "os"
        },
        {
          "background": "#ff90a8",
          "foreground": "#000000",
          "powerline_symbol": "\ue0b0",
          "properties": {
            "folder_icon": "\uf115",
            "folder_separator_icon": "\\",
            "home_icon": "\ueb06",
            "style": "full"
          },
          "style": "powerline",
          "template": " <#000>\uf07b</> {{ .Path }} ",
          "type": "path"
        },
        {
          "background": "#fe804e",
          "foreground": "#000000",
          "powerline_symbol": "\ue0b0",
          "properties": {
            "branch_icon": " <#ffffff>\ue0a0 </>",
            "fetch_status": false,
            "fetch_upstream_icon": true
          },
          "style": "powerline",
          "template": " {{ .UpstreamIcon }}{{ .HEAD }}{{ if gt .StashCount 0 }} \ueb4b {{ .StashCount }}{{ end }} ",
          "type": "git"
        },
        {
          "background": "#76b367",
          "foreground": "#000000",
          "powerline_symbol": "\ue0b0",
          "style": "powerline",
          "template": " \ue718 {{ if .PackageManagerIcon }}{{ .PackageManagerIcon }} {{ end }}{{ .Full }} ",
          "type": "node"
        },
        {
          "background": "#16d46b",
          "foreground": "#000000",
          "powerline_symbol": "\ue0b0",
          "style": "powerline",
          "template": " \ue235 {{ if .Error }}{{ .Error }}{{ else }}{{ if .Venv }}{{ .Venv }} {{ end }}{{ .Full }}{{ end }} ",
          "properties": {
            "display_virtual_env": true,
            "dispplay_default": true,
            "display_version": true,
            "home_enabled": true
          },
          "type": "python"
        },
        {
          "background": "#83769c",
          "foreground": "#000000",
          "powerline_symbol": "\ue0b0",
          "properties": {
            "always_enabled": true
          },
          "style": "powerline",
          "template": " \ueba2 {{ .FormattedMs }} ",
          "type": "executiontime"
        },
        {
          "background": "#2e9599",
          "background_templates": [
            "{{ if gt .Code 0 }}red{{ end }}"
          ],
          "foreground": "#000000",
          "powerline_symbol": "\ue0b0",
          "properties": {
            "always_enabled": true
          },
          "style": "diamond",
          "template": " {{ if gt .Code 0 }}\uf421{{ else }}\uf469{{ end }} ",
          "trailing_diamond": "\ue0b4",
          "type": "status"
        }
      ],
      "type": "prompt"
    },
    {
      "alignment": "left",
      "newline": true,
      "segments": [
        {
          "foreground": "#cd5e42",
          "style": "plain",
          "template": " \ue3bf",
          "type": "root"
        },
        {
          "foreground": "#ffffff",
          "style": "plain",
          "template": " >",
          "type": "text"
        }
      ],
      "type": "prompt"
    }
  ],
  "final_space": true,
  "version": 3
}
```

