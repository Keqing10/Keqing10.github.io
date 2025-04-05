---
title: GitHub Pages + Hexo网站搭建
category_bar: true
math: false
tags: ["GitHub Pages", "Hexo"]
category: [工具]
date: 2025-04-05 18:05:53
---


## 准备工作
需要做的环境配置：
- git
- node.js : [安装程序](https://nodejs.org/zh-cn/download/)

注意配置好环境变量。node.js版本要尽量高，可以使用`node --version`命令查看版本号。

## 配置过程
### 安装hexo框架
```shell
npm install -g hexo-cli
```
然后进入指定目录执行如下命令：
```shell
hexo init <folder>  # <floder>就是要存储网站资源的文件夹，这一步可以任意设置，因为之后并不使用这个文件夹
cd <folder>
npm install
```
此时得到如下目录：
```file
.
├── _config.yml 
├── package.json
├── scaffolds
├── source
|   ├── _posts
|   └── img
└── themes
```
其中`_config.yml`保存网站的一些基本信息。`package.json`里面有工作流程。`scaffolds`文件夹内保存一些模板文件。`source/_posts`文件夹内存储文章（post），`source/img`内存储图片等资源文件。`themes`文件夹内保存主题文件，此时应该为空，没有子文件夹，即使用默认主题。

### GitHub 配置
#### 创建仓库
首先创建公共仓库`UserName.github.io`。GitHub上的用户名和仓库名是大小写不敏感的，也就是说你也可以使用`username.github.io`，与前者视为同一个仓库名，但建议与原始用户名保持一致。创建仓库时可以使用README.md初始化。

> 注意：仓库名即要带有用户名，不是`github.io`，也不是`NotYourName.github.io`。对于任何其他仓库名，最终Pages域名会变成`https://username.github.io/othername`而不是`https://username.github.io`。

将仓库克隆到本地，此时本地的目录就是你在本地真实存储网站资源的文件夹。
#### 修改仓库的Pages配置
进入网页端的仓库，点击`Setting->Pages`，将`Build and deployment`下的`Source`修改为使用GitHub Actions。如果你要使用Hexo配置网站，就不要点击GitHub提供的Jekyll或其他选项。不需要创建其他分支，主分支保持为main即可。
#### 推送文件
把前面hexo命令产生的目录下的全部文件都复制到你本地克隆的仓库根目录内。

在根目录下创建文件`.github/workflows/pages.yml`，这就是GitHub中的工作流，每次push内容到主分支时就会自动构建并发布网站。填写以下内容，并将`node-version: "20"`中的20替换为本地`node --version`显示的主版本号（如`v20.18.0`对应20）。
```yml
name: Pages

on:
  push:
    branches:
      - main # default branch

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          # If your repository depends on submodule, please see: https://github.com/actions/checkout
          submodules: recursive
      - name: Use Node.js 20
        uses: actions/setup-node@v4
        with:
          # Examples: 20, 18.19, >=16.20.2, lts/Iron, lts/Hydrogen, *, latest, current, node
          # Ref: https://github.com/actions/setup-node#supported-version-syntax
          node-version: "20"
      - name: Cache NPM dependencies
        uses: actions/cache@v4
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    needs: build
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

最后将本地所有的修改提交推送到GitHub上，可以在网页端`Actions`中看到工作流`Pages`，等到构建完成后打开网页`https://username.github.io`即可看到默认模板配置的网站。后续写文章每次push之后都会自动构建发布。

### 主题配置
如果你不想使用默认的风格，可以在[Hexo主题](https://hexo.io/themes/)页面下载其他主题安装，这里以Fluid主题为例。
#### 下载
下载[Release压缩包](https://github.com/fluid-dev/hexo-theme-fluid/releases)并解压到themes目录，将解压文件夹重命名为`fluid`。在根目录下创建`_config.fluid.yml`文件，将`themes/fluid`文件夹下的`_config.yml`内容复制过来（注意：不是根目录的`_config_yml`文件）。


这里`_config.yml`保存了一些默认的主题设置，而如果存在`_config.themename.yml`文件时，优先级更高，具体配置方法见下。
#### 配置
修改根目录的`_config.yml`文件：
```yml
theme: fluid  # 指定主题
language: zh-CN  # 指定语言，会影响主题显示的语言，按需修改
```

创建“关于”页面：
```shell
hexo new page about
```
创建成功后修改`/source/about/index.md`，添加 layout 属性。修改后的文件示例如下：
```yml
---
title: 标题  # 这一项实际上不显示，可以是其他值
layout: about  # 这一项不能修改
---

这里写关于页的正文，支持 Markdown, HTML。
```
最后，可以修改`themes/fluid/.gitignore`文件，添加以下条目，减少无用文件的push。
```text
.github
LICENSE
README*.md
```

## Hexo命令与本地构建
本地安装node.js的最大作用就是可以本地构建运行，而不是只有push到仓库才能看到效果，在最终push之前本地构建，查看效果，调整好再上传可以提高效率（据说GitHub Pages免费版限制每小时10次Actions）。
### `hexo generate`或`hexo g`
生成静态文件，运行后本地根目录会出现`public`文件夹和`db.json`，这是默认不会被上传的，可以在此查看生成项目的目录结构。注意该文件夹下的`index.html`直接打开并不是网页效果。
### `hexo clean`或`hexo cl`
清除缓存文件 (`db.json`) 和已生成的静态文件 (`public`)。
在某些修改操作之后可能需要先运行此命令再重新构建才能正确显示效果。

### `hexo server`或`hexo s`
启动服务器，默认情况下，访问网址为：`http://localhost:4000/`。如果出现端口冲突可以在根目录的`_config.yml`文件添加如下代码，建议优先选择 4000-4010 或 8000-8999 范围内的端口，这些端口通常不会被系统服务占用。。
```yml
server:
  port: 8080  # 自定义端口号
```
### `hexo new`创建文章
- `hexo new "titlename"`
根据模板创建文章`titlname.md`，默认创建在`source/_posts/`目录下，模板文件默认为`scaffolds/post.md`。
一个简单的模板示例为：
```yml
---
title: {{ title }}
date: {{ date }}
category_bar: true
math: false
tags: []
category: []
---
```

## 参考资料
1. [Hexo 官方文档](https://hexo.io/zh-cn/docs/)
2. [Hexo Fluid 用户手册](https://fluid-dev.github.io/hexo-fluid-docs/)

