# Sitemap Creator
用 GitHub Action 🚀 在你的仓库中创建和更新网站地图。  

> [!TIP] 
> 这是 Sitemap Creator 的稳定版仓库。预发行版仓库请前往 [fjwxzde/Sitemap_Creator_Pre-Release](https://github.com/fjwxzde/Sitemap_Creator_Pre-Release) 查看。  

[![GitHub Release](https://img.shields.io/github/release/DuckDuckStudio/Sitemap_Creator?style=flat)](https://github.com/DuckDuckStudio/Sitemap_Creator/releases/latest)  
[反馈Bug🐛](https://github.com/DuckDuckStudio/Sitemap_Creator/issues) | [使用示例🚀](#3-使用示例)  

## 参数
| 参数 | 描述 | 默认值 | 是否必须 | 备注 |
|-----|-----|-----|-----|-----|
| `location` | 网站地图的存放位置 (例如 `docs/sitemap.xml`) | `./sitemap.xml` (即仓库根目录) | 否 | / |
| `token` | 用于创建更新网站地图的拉取请求的 Token | `${{ github.token }}` | 否 | 您的 Token 至少应该具有 `repo` 权限来创建拉取请求，如果使用默认的 Action Token 则需要在仓库设置中允许 GitHub Action 创建拉取请求<sup>[1](#如何允许-github-action-创建拉取请求)</sup> |
| `timezone` | 设置生成时使用的时区 | `Asia/Shanghai` (上海，UTF+8，CST) | 否 | 遵循 IANA时区数据库（也称为Olson时区数据库）的格式 |
| `basic_link` | 指向你网站的基础链接 | `https://${{ github.event.repository.owner.login }}.github.io/${{ github.event.repository.name }}` | 否 | 结尾不要带 `/` |
| `file_type` | 网页文件的类型 (例如使用 docsify 部署的就是 md，不指定则设为 html，可指定多个类型) | `html,md` | 否 | 不带`.`，`md`类型会自动去掉后缀名 |
| `ignore_file` | 指定哪些文件不包含在网站地图中 | `啥都没有` | 否 | `,`间隔 |
| `website_path` | 你的网站内容的位置 (例如 `./` (根目录) 或 `docs`) | `./` (根目录) | **是** | / |
| `base_branch` | 仓库主分支 (`main`，`master` 等) | `main` | 否 | / |
| `debug` | 控制调试输出的开关 | `false` | 否 | 你用`true`还是`1`随便，js里真值<sup>[2](#2-java-script-中有哪些可用真值)</sup>的都行 |

## 帮助
### 1. 如何允许 GitHub Action 创建拉取请求
打开仓库 Settings (上方栏) > Code and automation (左侧栏) > Actions (左侧栏子类别) > General (子类别) > Workflow permissions (划到最下面):  

![记得按 Save 保存](docs/imgs/README/1.png)

### 2. Java Script 中有哪些可用真值
请见[真值 - MDN Web 文档术语表：Web 相关术语的定义 | MDN](https://developer.mozilla.org/zh-CN/docs/Glossary/Truthy)。  

### 3. 使用示例
```yml
name: 生成 Sitemap

# GitHub Actiion DuckDuckStudio/Sitemap_Creator 示例工作流
# https://github.com/marketplace/actions/sitemap-creator-stable

on:
  push:
    branches:
      - main
      # 当 main 分支上有新推送且以下文件被更改时
    paths:
      - '**/*.html'
      - '**/*.md'
      - '.github/workflows/generate-sitemap.yml'
  workflow_dispatch: # 手动运行

jobs:
  generate_sitemap:
    runs-on: ubuntu-latest

    steps:
      - name: 更新网站地图
        uses: DuckDuckStudio/Sitemap_Creator@1.0.0
        with:
          location: "docs/sitemap.xml"
          basic_link: "https://duckduckstudio.github.io/Articles/#" # docsify 部署的
          file_type: "html,md" # 默认值也是这个，爱加不加
          ignore_file: "_Footer.md,404.html,某鸭的文章页面模板.html,营销号"
          website_path: "docs"
          base_branch: main # 默认值也是这个，爱加不加
          debug: true # 启用调试输出

      - name: 启用自动合并 (压缩) # 此步骤是我另外手动加的，后续可能会将其加入为新参数
        env:
          GH_TOKEN: ${{ github.token }}
        run: |
          sleep 10 # 等 PR 创建了再查找
          # 获取前面创建的 PR 编号，确保 PR 名包含 "Auto update sitemap"
          PR_NUMBER=$(gh pr list --limit 1 --search "Auto update sitemap" --json number | jq -r '.[0].number')
          gh pr merge $PR_NUMBER --squash --auto
          gh pr comment $PR_NUMBER --body "这看起来是更新网站地图的 PR，已自动启用自动合并。👍"
          gh pr edit $PR_NUMBER --add-label "DEV-已启用自动合并,工作流,DEV-开发分支合并" # 此处按照你自己仓库的标签来
```

## 星星🌟
如果您认为本项目对您有帮助，还请给本项目一个小小的 Star 。  
[![星标历史](https://api.star-history.com/svg?repos=DuckDuckStudio/Sitemap_Creator&type=Date)](https://star-history.com/#DuckDuckStudio/Sitemap_Creator&Date)  
