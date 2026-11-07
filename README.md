# 姜泽之的 Hugo 学习博客

Hugo + PaperMod 个人博客，包含首页、关于、标签、分类、归档、搜索和三篇非草稿文章。仓库为 `3046780699-cmd/linux-blog`，GitHub Pages 目标地址为 `https://3046780699-cmd.github.io/linux-blog/`。是否已部署请以仓库 Actions 和 Pages 的实时状态为准。

## 在 Linux 运行

本轮验证版本为 Ubuntu 24.04.5 LTS、Hugo 0.123.7 extended、Git 2.43.0，主题锁定 PaperMod v8.0。建议使用相同 Hugo 版本复现。

```bash
sudo apt update
sudo apt install --no-install-recommends -y hugo
sudo apt install -y git
hugo version
git --version
cd my-blog
hugo server --bind 0.0.0.0 --baseURL http://虚拟机IP:1313/ --appendPort=false
```

把命令里的“虚拟机IP”替换成 `ip -4 address show` 显示的实际地址。在宿主浏览器打开 `http://虚拟机IP:1313/`，按 `Ctrl+C` 停止开发服务器。本轮地址为 `192.168.163.143`，该地址由 DHCP 分配，重启或迁移后应重新检查。

本地服务器绑定全部网卡以便宿主访问，只用于本地开发和作业演示。三篇文章均为 `draft: false`，无需 `-D` 也会参与正式构建。

## 源码包与 Git 子模块

离线源码包带有主题的实际文件，可以直接运行 `hugo`。主仓库与主题的 Git 历史另存于两个 `.bundle` 文件中，便于完整恢复子模块。

若需要保留真实 Git 索引和子模块关系，按源码包内 `恢复Git仓库.md` 操作。不要在已有目录内盲目执行 `git submodule add`，否则会与已经存在的主题目录冲突。

若以后从已经发布的 Git 仓库克隆，使用：

```bash
git clone --recurse-submodules <实际仓库地址>
```

## 个人资料

关于页已填写姓名姜泽之、专业软件工程。按本人要求不放照片，也不显示照片占位。报告按另行提供的个人资料填写；班级、学号仅用于本地报告。

## 内容结构

```text
hugo.toml                    核心配置、六菜单及 JSON 搜索输出
content/about.md             关于页
content/search.md            搜索页，layout: search
content/archives.md          归档页，layout: archives
content/posts/               三篇正式文章
static/images/               图片资源
themes/PaperMod/             真实 PaperMod 主题
.github/workflows/hugo.yaml   待手动触发的 Pages 工作流
.gitignore                   排除 public/ 和构建缓存
```

## GitHub Pages 后续发布

工作流支持推送 `main` 自动部署，也支持 `workflow_dispatch` 手动触发。首次部署前需要在仓库 Pages 设置中启用 GitHub Actions。

1. 在本人 GitHub 账号新建一个空仓库，恢复本地 Git 仓库后配置真实 `origin` 并推送 `main`。
2. 进入仓库 Settings → Pages，把 Source 设为 **GitHub Actions**。本工作流使用 Pages artifact 部署，不使用 `gh-pages` 分支。
3. 进入 Actions，选择 **Deploy Hugo to GitHub Pages**，手动点击 Run workflow。
4. 工作流递归检出子模块，使用 `peaceiris/actions-hugo@v3` 安装固定 Hugo 版本，再从 `actions/configure-pages` 读取真实 `base_url`。发布路径由实际仓库决定，避免用户站点与项目站点的 CSS 路径混淆。
5. 构建与部署都成功后，从部署结果取得真实公网 URL，打开首页、关于、标签、分类、归档和搜索，并补拍绿色 Actions、Pages 设置和公网浏览器截图。

`.gitignore` 排除了 `public/`，构建产物不进入主分支。开发预览内容与将来公网部署使用同一份源码；发布前补齐个人资料。

## 常见问题

- 宿主打不开：先核对虚拟机IP、服务是否仍在运行、是否绑定 `0.0.0.0`、端口1313是否放行。本轮 UFW 处于 inactive，未修改宿主或共享网络。
- 搜索无结果：确认首页输出包含 JSON、`/index.json` 可读取，搜索页使用 `layout: search`。归档需要显式建立 `content/archives.md`，只有菜单链接不够。
- 主题找不到：恢复主题目录，或在 Git 克隆后执行 `git submodule update --init --recursive`。
- Hugo和主题不兼容：先使用本轮验证的 Hugo 0.123.7 + PaperMod v8.0，不直接把旧Hugo配到未核对的主题新版本。

## 内容与许可

三篇文章围绕所提供《Linux基础及应用》实验指导书中的文件管理、权限、Shell和Makefile知识自行编写；文章中的示例命令不冒称已完成对应三个独立实验。主题许可见 `themes/PaperMod/LICENSE`。
