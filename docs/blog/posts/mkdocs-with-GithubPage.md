---
# this is the meta data
date:
    created: 2025-01-12               # 创建日期
    # updated:                # 修改日期
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

以下是使用 **GitHub Pages** 部署 **Material for MkDocs** 网站的详细流程指南：
<!-- more -->

---

## 1. **创建 MkDocs 项目**

1. **安装 MkDocs 和 Material for MkDocs**：
   确保你的环境中已安装 `mkdocs` 和 `mkdocs-material`。如果尚未安装，可以运行以下命令：

   ```bash
   pip install mkdocs-material
   ```

2. **初始化 MkDocs 项目**：
   创建一个新的 MkDocs 项目并进入项目文件夹：

   ```bash
   mkdocs new my-project
   cd my-project
   ```

3. **测试本地运行**：
   运行本地开发服务器，检查默认站点是否正常显示：

   ```bash
   mkdocs serve
   ```

   在浏览器中访问 [http://127.0.0.1:8000/](http://127.0.0.1:8000/) 查看网站效果。

---

## 2. **将项目推送到 GitHub**

1. **创建 GitHub 存储库**：
   - 登录 GitHub，创建一个新的存储库，名称为 `your-repo-name`。

2. **将 MkDocs 项目上传到 GitHub**：
   在本地项目文件夹中运行以下命令，将项目推送到 GitHub：

    ```bash
    git init
    git remote add origin https://github.com/your-username/your-repo-name.git
    git add .
    git commit -m "Initial commit"
    git branch -M main
    git push -u origin main
    ```

---

## 3. **配置 GitHub Actions 自动部署**

1. **创建 GitHub Actions 工作流文件**：
   在项目的根目录中创建文件夹 `.github/workflows/`，然后创建文件 `ci.yml`：

   ```bash
   mkdir -p .github/workflows
   vim .github/workflows/ci.yml
   ```

2. **添加以下内容到 `ci.yml`**：

   ```yaml
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
   ```

3. **提交并推送更改**：

   ```bash
   git add .github/workflows/ci.yml
   git commit -m "Add GitHub Actions workflow for deployment"
   git push
   ```

---

## 4. **启用 GitHub Pages**

1. **等待部署完成**：
   每次你将更改推送到 `main` 分支时，GitHub Actions 将自动构建并部署你的站点。

2. **检查 Pages 设置**：
   - 转到 GitHub 存储库页面。
   - 点击 **Settings** > **Pages**。
   - 在 **Source** 下，确保选择了 `gh-pages` 分支。

3. **访问你的站点**：
   你的网站将部署在以下 URL：

   ```bash
   https://<your-username>.github.io/<your-repo-name>/
   ```

---

## 5. **测试和更新**

1. **测试站点**：
   确保站点在部署后运行正常。

2. **修改和重新部署**：
   对项目进行更改后，提交并推送代码即可触发自动部署。

---

完成以上步骤后，你的 **Material for MkDocs** 网站将成功部署到 **GitHub Pages**，并且可以通过指定的 URL 访问!

## 一个还在改进中的自动化部署脚本

```bash
#!/bin/bash

# 检查必要的软件是否已安装
function check_dependencies() {
    dependencies=("git" "pip" "mkdocs")
    for dep in "${dependencies[@]}"; do
        if ! command -v $dep &> /dev/null; then
            echo "[ERROR] $dep 未安装，请先安装 $dep 后再运行此脚本。"
            exit 1
        fi
    done
}

# 初始化 MkDocs 项目
function init_mkdocs_project() {
    echo "[INFO] 初始化 MkDocs 项目..."
    mkdocs new "$project_name"
    echo "[INFO] MkDocs 项目已创建：$project_name"
}

# 设置 Git 并推送到 GitHub
function setup_git() {
    echo "[INFO] 初始化 Git 仓库..."
    git init
    git remote add origin "$repo_url"

    echo "[INFO] 添加文件并提交..."
    git add .
    git commit -m "Initial commit"

    echo "[INFO] 设置分支为 main 并推送..."
    git branch -M main
    git push -u origin main
}

# 配置 GitHub Actions 自动部署
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

# 主函数
function main() {
    echo "[INFO] 开始部署流程..."
    check_dependencies

    # 检查是否在项目根目录
    if [ ! -f "mkdocs.yml" ]; then
        echo "[ERROR] 请在包含 mkdocs.yml 的项目根目录下运行此脚本。"
        exit 1
    fi

    # 初始化项目
    if [ ! -d "$project_name" ]; then
        init_mkdocs_project
    else
        echo "[INFO] 项目目录已存在，跳过初始化：$project_name"
    fi

    # 配置 Git 并推送
    if git remote -v | grep origin &> /dev/null; then
        echo "[INFO] 远程仓库已存在，跳过配置。"
    else
        setup_git
    fi

    # 配置 GitHub Actions
    if [ ! -f .github/workflows/ci.yml ]; then
        setup_github_actions
    else
        echo "[INFO] GitHub Actions 工作流已存在，跳过配置。"
    fi

    echo "[INFO] 部署流程完成！请前往 GitHub Pages 设置启用并访问您的站点。"
}

# 用户输入
read -p "请输入项目名称（默认: my-mkdocs-project）：" project_name
project_name=${project_name:-my-mkdocs-project}

read -p "请输入远程仓库地址（例如：https://github.com/username/repo.git）：" repo_url
if [ -z "$repo_url" ]; then
    echo "[ERROR] 必须提供远程仓库地址！"
    exit 1
fi

# 执行主函数
main

```