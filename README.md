# GitHub Browser Ops

> ⭐ **Support This Project**
>
> [![GitHub stars](https://img.shields.io/github/stars/oeaf/github-browser-ops?style=social)](https://github.com/oeaf/github-browser-ops)
> [![GitHub forks](https://img.shields.io/github/forks/oeaf/github-browser-ops?style=social)](https://github.com/oeaf/github-browser-ops)
> [![GitHub issues](https://img.shields.io/github/issues/oeaf/github-browser-ops)](https://github.com/oeaf/github-browser-ops/issues)
>
> 如果这个项目对你有用，欢迎点个 ⭐ Star！你的支持是我持续更新的动力。

AI Agent 用浏览器操作 GitHub 仓库的标准流程：建库、传文件、改 README。

## 为什么需要这个

有些 GitHub App（如 `meta-muse-ai`）自身没有申请写权限，API 建库、写文件一律 403。这时唯一的写路径是走用户已登录的浏览器会话——但这条路坑很多：全文替换容易校验失败、键盘输入会错位、网页上传不会自动建目录……这个 skill 把实战中踩过的坑都记下来了，让新 agent 直接上手。

## 内容

- `SKILL.md`：完整操作手册——前置检查、建库、README 单行精确编辑、多文件上传、收尾验证、发布前个人信息审核铁律、禁止事项
- `references/quirks.md`：8 个已知坑点的详细记录（fill 换行翻倍、localhost 连不到、重名文件误传等）

## 适用场景

- GitHub App 没有写权限，只能走浏览器操作仓库
- 要把"怎么用浏览器改仓库"这套流程教给新的 agent
- 批量上传文件到新仓库、精确修改 README 而不破坏其他内容
