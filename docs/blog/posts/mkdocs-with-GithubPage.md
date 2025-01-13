---
# this is the meta data
date:
    created: 2025-01-12               # 创建日期
    updated: 2024-01-13               # 修改日期
catagories:                 # 文章类别
    - Workflow
tags:                       # 文章标签
    - mkdocs
draft: false                 # 是则先不更新到网页
pin: false                  # 是否置顶
readtime: 15                # 预计阅读时间
# links:                      # 外部链接
    # - Name: file.md / url   #
# author:                     # docs/blog/.authors.yml配置有哪些作者
---

# 使用Github Page发布Mkdocs网站

以下是使用 GitHub Pages 部署一个 Material for MkDocs 的default网站的详细流程指南，部署后你可以直接修改repo里面的代码，push之后即可实现自动更改网页信息：
<!-- more -->

---

## 1. 创建Mkdocs项目

首先你得搭建一个Mkdocs的项目环境，确保你的python环境中已经安装了`mkdocs-material`, 推荐使用conda环境管理。

```bash
pip install mkdocs-material
```

然后创建一个新的Mkdocs项目，并且进入该项目的文件夹

```bash
bash mkdocs new your-project-name
cd your-project-name
```

运行完之后你的项目目录结构应该是这样的，你可以用tree命令检查：

```bash
mkdocs.yml    # 项目的配置文件
docs/
    index.md # 站点的home page
    ... # 其他的markdown页面，图片和其他文件。
```

接下来就是运行本地开发服务器检查默认站点是否正常：

```bash
mkdocs serve
```

在浏览器中访问 <http://127.0.0.1:8000/> 查看网站效果，如果显示一个默认网页即成功。

## 2. 将项目推送到Github并设置Github Action

接下来就是创建一个Github存储库了，登录 GitHub，创建一个新的存储库，名称为 `your-repo-name`

![create a repo](../../assert/IMG/set_page_1.png)

记得这里公开性一定要设置为Public，不然你没办法使用免费的github pages服务

创建好远程仓库后，进入你的本地项目的根目录，然后运行如下脚本,把本地项目推送到你的远程仓库中，并且配置github actions自动部署：

```bash
# 在根目录下保存为一个Publish-Website.sh文件，然后执行

# This script push an mkdocs repo that already been make and serve on local machine, but have not set up git config nor publish on git page.
# Prerequisites：
# 1. have downloaded git and mkdocs
# 2. have a new remote git repo for your project
# 3. have run "mkdocs new ." at the root of your project
# 4. have test your website on your local machine
# When all of these are statisfied, Put this script at the root of your mkdocs project then run it.


function check_dependencies(){
    dependencies =  ("git", "pip", "mkdocs")
    for dep in "${dependencies[@]}"; do
        if ! command -v $dep &> /dev/null; then
            echo "[ERROR] $dep not installed, please download it before run this script."
            exit 1
        fi
    done
}

function setup_git(){
    echo "[INFO] initializing git repo..."
    git init
    git remote add origin "$repo_url"

    echo "[INFO] add all files and commit..."
    git add .
    git commit -m "Initial commit"

    echo "[INFO] configs branch as main and push..."
    git brach -M main
    git push -u origin main
}

function setup_github_actions() {
    echo "[INFO] 配置 GitHub Actions 工作流..."
    mkdir -p .github/workflows
    cat <<EOF > .github/workflows/ci.yml
name: Deploy MkDocs

on:
  push:
    branches:
      - main

permissions:
  contents: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.x'
      - name: Install dependencies
        run: |
          pip install mkdocs-material
      - name: Deploy to GitHub Pages
        run: |
          mkdocs gh-deploy --force
EOF

    git add .github/workflows/ci.yml
    git commit -m "Add GitHub Actions workflow for deployment"
    git push
    echo "[INFO] GitHub Actions 工作流已配置。"
}

function main(){
    echo "[INFO] start to deploy the publish process..."
    check_dependencies

    # 检查是否在项目根目录
    if [ ! -f "mkdocs.yml" ]; then
        echo "[ERROR] Please run this scripts at the root of your mkdocs project."
        exit 1
    fi

    setup_git
    setup_github_actions

    echo "[INFO] deploy process done! go to your github project setting:Page and change your branch as gh-pages!"
}


read -p "[USER INPUT] input remote git repo address(example: https://github.com/username/repo.git): " repo_url
if [-z "$repo_url"]; then
    echo "[ERROR] cannot run without remote git repo address!"
    exit 1
fi

main
```

## 3. 启动Github Pages

现在转到你的github仓库界面，点击Setting后选择Page页面，在将Branch切换为`gh-pages`，项目目录从`/root`开始：
![set page setting](../../assert/IMG/set_page_1.png)

现在全部流程已经走完了，每次你将更改推送到`main`分支时，GitHub Actions 将自动构建并部署你的站点。

你的网站将部署到如下URL:
<https://your-username.github.io/your-repo-name>

完成以上步骤后，你的 Material for MkDocs 网站将成功部署到 GitHub Pages，并且可以通过指定的 URL 访问!
