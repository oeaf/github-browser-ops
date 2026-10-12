---
name: "github_browser_ops"
description: "用浏览器操作 GitHub 仓库的标准流程：建库、传文件、改 README。适用于 meta-muse-ai 这类没有写权限的 GitHub App 场景——所有写操作走已登录的浏览器会话完成。包含单行精确编辑、多文件上传、发布前个人信息审核铁律和全部已知坑点。当用户要求新建 GitHub 仓库、上传文件到仓库、修改仓库 README，或让新 agent 接手 GitHub 仓库操作时使用。"
metadata: { "includeInPrompt": true }
---

# GitHub Browser Ops

## 为什么走浏览器

`meta-muse-ai` 这个 GitHub App 自身**没有申请写权限**（Administration / Contents 都没有），所以 API 建库、写文件一律 403（`Resource not accessible by integration`），重装 App 也没用——前提是 App 先申请权限。读操作正常。

**结论：凡是 GitHub 写操作，一律走浏览器（用户已登录的会话）完成，不要在 API 这条路上反复试。**

## 前置检查

1. 打开目标仓库页或 https://github.com/new，确认右上角已登录账号为预期账号（如 `ui822`）。
2. 如果未登录或账号不对：**不要做任何登录尝试**，直接结束并回报实际状态。
3. 人机验证按用户的 standing CAPTCHA 规则处理（自动解决，事后说明）。

## 操作手册

### 建库

1. 打开 https://github.com/new。
2. Repository name / Description 按要求填；选 Public（或按要求选 Private）。
3. **不要**勾选 Add a README file，**不选** .gitignore / license 模板（README 由任务手动创建，避免后续冲突）。
4. 点 Create repository。

### 改 README（加行 / 改单行）

1. 进文件页 → 点铅笔图标进编辑页。
2. **绝不用 fill 做全文替换**：容易 verification mismatch，且页面可能失去响应。
3. 单行/多行插入用键盘精确定位：
   - 追加到末尾：`Ctrl+End` 到文末 → 回车 → 逐行输入。
   - 插到中间：用编辑器查找定位到目标行末 → 回车 → 输入。
   - 如果键盘输入错位（文本落到非光标处）：立即 `Ctrl+Z` 撤销重来，不要将错就错。
   - 备选：用编辑器查找替换框（正则模式）做精确插入，先拿无害替换验证机制再动真格。
4. 点 **Preview** 标签页核对渲染效果，确认无误再 Commit。
5. 直接 Commit 到 main 分支（除非用户要求开分支），commit message 写清楚做了什么。

### 传多个文件（新仓库 / 批量上传）

浏览器任务支持 `files` 参数：把本地文件作为上传授权（upload grant）传给任务，字节直达，无转述误差。**不要**让任务从 localhost 读文件——浏览器跑在云端 VM 上，连不到本机回环地址。

流程（以 21 个文件、7 个目录为例）：

1. **根目录文件**：仓库主页 → Add file → Upload files → 选择授权文件 → Commit。
2. **子目录**：GitHub 网页上传不支持自动建目录，先建目录再传：
   - Add file → Create new file，文件名填 `<目录>/.gitkeep`（如 `assets/template/.gitkeep`），内容随便填一行 `placeholder`，Commit。
   - 进入该目录页 → Add file → Upload files → 选择该目录的文件 → Commit。
   - 打开 `.gitkeep` 文件页 → Delete file → Commit，保持目录干净。
3. **重名文件**（如根目录和子目录各有一个 `README.md`）：靠内容特征或大小区分，上传前向任务说明判别方法（如"选以 `# Feishu Bridge` 开头的那个"）。
4. 传完后逐个目录页核对文件数量。

### 收尾验证（每次必做）

1. Preview / 文件页确认内容正确。
2. 提交后打开仓库首页，确认最新 commit 就是本次提交。
3. 出错时 `Ctrl+Z` 撤销，**绝不强行提交**；如实回报当时状态（是否提交、仓库是否有变化），不编造。

## 发布前审核铁律

任何发到 GitHub 仓库的内容，发布前必须做一次严格的个人信息审核。以下**绝不**写入仓库：

- 登录凭据、API key / token
- 各类 ID（账号 ID、应用 ID、open_id 等）
- 真实姓名、邮箱、电话、地址

做法：上传前对全部文件 `grep` 扫一遍（如 `sk-`、邮箱正则、手机号正则、`cli_` 等），确认干净再发。配置文件只放占位符（如 `$INTERN_API_KEY`、空字符串），真实值走 Secure Vault 或 600 权限的本地文件，永不进仓库。

## 禁止事项

- 不点 star / fork，不动其他仓库。
- 不做任务之外的任何操作。
- 浏览器 approval 弹窗的"总是准许"只能由用户在 Muse App 的 **Settings → Permissions** 里自己开，agent 无权更改；不要替用户做这个决定。

## 已知坑点

详见 [references/quirks.md](references/quirks.md)。核心几条：

- fill 全文替换 README 易 verification mismatch。
- 键盘输入可能错位到非光标处，错位就 Ctrl+Z。
- fill 会把连续换行数翻倍（k→2k-1），整体写入时要补偿。
- localhost 本机文件服务器浏览器连不到。
- GitHub App 无写权限是 App 申请问题，重装/换 token 解决不了。
