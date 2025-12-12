---
title: Ollama在服务器上部署与使用大模型
category_bar: true
math: false
tags: [AI, Ollama, Open-WebUI]
category: [工具]
date: 2025-12-12 09:08:26
---
> 这里是在Linux服务器上部署的过程。

> 需要先安装`ollama`，`tmux`和`uv`（非必须）。

# Ollama 配置与使用
## 环境配置与后台服务启动

设置一个tmux窗口运行ollama后台服务。
```bash
tmux new -s olm  # 创建并进入名为olm的tmux会话
```
使用Ctrl+B，D组合键退出tmux会话，保持后台运行。
```bash
tmux ls  # 查看tmux会话列表
tmux attach -t olm  # 重新连接到名为olm的tmux会话
```

在.bashrc或.zshrc中添加环境变量配置
```bash
export OLLAMA_MODELS="/path/to/your/ollamamodels/"
export OLLAMA_KEEP_ALIVE="15min"
export OLLAMA_NUM_GPU=4
export OLLAMA_HOST=0.0.0.0
export OLLAMA_PORT=11434
export OLLAMA_CONTEXT_LENGTH=128000
```

设置保存模型的路径后，通过`source ~/.bashrc`（或`source ~/.zshrc`等）更新当前终端的环境变量。

由于ollama可能没有该目录权限，仍然会拉取到默认路径。
因此使用`sudo`运行，但这样不会获取到当前用户的环境变量，所以最终使用如下命令：
```bash
sudo -E ollama serve
```
或使用如下命令增加权限：
```bash
sudo chown -R $(whoami) /path/to/your/ollamamodels/ 
```

需要关闭服务时，回到`olm`会话并Ctrl+C即可关闭。
最后使用Ctrl+B，&关闭该tmux会话。

## ollama命令
```bash
ollama serve # 启动ollama后台服务
ollama ls # 列出已安装的模型
ollama ps # 列出正在运行的模型
ollama pull <model-name> # 下载指定模型
ollama run <model-name> # 运行指定模型
ollama cp <model-name> <new-model-name> # 复制指定模型，可以创建短的别名，视作<modele-name>使用
ollama rm <model-name> # 删除指定模型
ollama stop <model-name> # 停止指定模型的运行
ollama show <model-name> # 显示指定模型的信息
```


# Open WebUI配置

[Open WebUI](https://docs.openwebui.cn/)可以作为服务器上部署模型的前端页面更方便地对话与使用。

新建一个文件夹作为open-webui的环境目录，例如`~/Projects/open-webui`，进入目录后创建环境。
。

```bash
uv python install 3.11  # 目前3.11为开发环境，推荐版本
uv venv --python 3.11
source .venv/bin/activate
uv pip install open-webui
```
全部安装完成后，运行以下命令启动webui：
```bash
open-webui serve
```
通过这种方式启动的默认访问地址是[http://localhost:8080/](http://localhost:8080/)。
这种方式也适用于使用vscode连接服务器后运行open-webui，然后在本地打开。不需要额外设置端口等等。
## 镜像网站配置
对于uv安装python和包的方式，可以设置镜像站。
新建文件`~/.config/uv/uv.toml`或`%APPDATA%\uv\uv.toml`，添加：
```toml
python-install-mirror = "https://ghfast.top/https://github.com/astral-sh/python-build-standalone/releases/download"

[[index]]
name = "tsinghua"
url = "https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple/"

[[index]]
name = "ustc"
url = "https://mirrors.ustc.edu.cn/pypi/simple"
default = true
```

在第一次运行open-webui时，会在HuggingFace下载必要的文件，也可以设置镜像站，将以下代码添加到终端配置文件中。
```bash
export HF_ENDPOINT=https://hf-mirror.com
```
