---
# this is the meta data
date:
    created: 2025-01-21               # 创建日期
    # updated:                # 修改日期
catagories:                 # 文章类别
    - Workflow
tags:                       # 文章标签
    - automate
draft: false                 # 是则先不更新到网页
pin: false                  # 是否置顶
readtime: 5                # 预计阅读时间
# links:                      # 外部链接
    # - Name: file.md / url   #
# author:                     # docs/blog/.authors.yml配置有哪些作者
---


# 使用Powershell脚本解压当前目录下所有.zip文件

今天在整理托福资料的时候，发现有一堆发过来的zip压缩包，一个个手动解压可以说是相当的麻烦，解压之后还老是出现文件夹嵌套的情况，非常不优雅。因此我编写了一个powershell脚本，其作用为解压当前目录下所有的.zip文件，并且对每个.zip文件都创建一个对应BaseName的文件夹，然后将对应的zip文件解压的内容放入到里面。

一开始我的脚本内容如下：

```powershell
param(
    [switch]$help
)

if ($help) {
    Write-Output @"
Usage: .\UnzipFiles.ps1 [--help]

This script extracts all .zip files in the current directory into folders with the same name as the zip file.

Options:
    --help  Display this help message and exit.
"@
    exit
}

# 遍历当前目录下所有 .zip 文件
Get-ChildItem -Path . -Filter *.zip | ForEach-Object {
    $folderName = $_.BaseName
    # 创建一个与文件名相同的文件夹，并抑制输出
    New-Item -ItemType Directory -Path $folderName -Force | Out-Null 
    # 解压文件到对应的文件夹
    Expand-Archive -Path $_.FullName -DestinationPath $folderName -Force
    Write-Output "Unzip Done: $($_.Name) 到 $folderName" # 两个美元符号强制解析括号内内容
}

# 提示所有解压已完成
Write-Host "All zips have been unzipped." -ForegroundColor Blue
```

结果运行之后发现有点奇怪，有些解压出来的文件夹名和文件名都是乱码，排查之后发现是因为unzip默认使用utf-8解压，但是有些压缩包的中文名编码是GBK的，导致无法正常解析。

对此，我重新下载了一个跨平台的解压工具unar，并且重构了我的代码：

```powershell
param(
    [string]$encoding = "UTF-8", # 默认使用 UTF-8 编码
    [switch]$help
)

if ($help) {
    Write-Output @"
Usage: .\UnzipFiles.ps1 [--encoding <encoding>] [--help]

This script extracts all .zip files in the current directory into folders with the same name as the zip file.
You can specify the file name encoding using the --encoding parameter.

Options:
    --encoding <encoding>   Specify the file name encoding (e.g., UTF-8, GBK).
    --help                  Display this help message and exit.
"@
    exit
}

# 遍历当前目录下所有 .zip 文件
Get-ChildItem -Path . -Filter *.zip | ForEach-Object {
    $folderName = $_.BaseName
    # 创建解压目标文件夹
    New-Item -ItemType Directory -Path $folderName -Force | Out-Null
    # 调用 unar 解压文件，并指定编码
    $unarCommand = "unar -e $encoding -o `"$folderName`" `"$($_.FullName)`""
    Write-Output "Running: $unarCommand..."
    Invoke-Expression $unarCommand |Out-Null # 不输出提示信息

    if ($LASTEXITCODE -eq 0) {
        Write-Output "Unzip Done: $($_.Name) 到 $folderName"
    } else {
        Write-Host "Failed to unzip: $($_.Name)" -ForegroundColor Red
    }
}

# 提示所有解压已完成
Write-Host "All zips have been unzipped." -ForegroundColor Blue
```

现在就可以正常地解压GBK编码的压缩文件了。虽然说是想造一个方便自己的工具，但是写出这个脚本也花了我1个多小时...希望之后它能帮我节省出一个小时出来吧。
