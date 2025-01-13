# 前言

如你所见，Material MkDocs就是搭建我这个网站的一个框架。它的网页部署内容都是符合markdown格式的，对程序员特别友好。如果你自己也想有一个自己的网站又不想折腾太多，你可以按照如下的简单指引去学习这个框架。

## 官方教程

1. 第一步必然是先把环境搭建起来，[官方环境搭建在这里](https://squidfunk.github.io/mkdocs-material/getting-started/)
2. 第二步：现在你需要自己建立一个网站网站来实践一下，教程在这里：[Material for MkDocs Tutorial](https://squidfunk.github.io/mkdocs-material/tutorials/)
3. 第三步：理论上到这里你应该已经可以在本地跑起来你的网站了，但是如何把它发布到网上呢？
    - [官方发布教程](https://squidfunk.github.io/mkdocs-material/publishing-your-site/)
        - 如果你只是想快速发布，不管国内的人看不看的到，而且还比较熟悉git和github，我推荐你用Github Pages来发布你的网页
        - 如果你想在国内发布...那你可能得折腾一整子，反正我现在还没折腾出来

## 中文教程

如果你有英语恐惧症，那你可以直接看我这个最简单的从搭建到部署一个默认网站的[教程](../../blog/posts/mkdocs-with-GithubPage.md)

> 施工中，不知道什么时候会出来

## 最常用指令速查

| commands                | usage                   |
| ----------------------- | ----------------------- |
| `mkdocs new [dir-name]` | 创建一个新的Mkdocs项目          |
| `mkdocs serve`          | 开启一个在本地运行，且实时重载的docs服务器 |
| `mkdocs build`          | build一个docs站点           |
| `mkdocs -h`             | 打印帮助信息并退出               |

## 项目结构

```sh
mkdocs.yml    # 项目的配置文件
docs/
    index.md # 站点的home page
    ... # 其他的markdown页面，图片和其他文件。
```
