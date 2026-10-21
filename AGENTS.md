
<!-- ai-agent-container-rules -->
# 工作规则（容器注入，必须严格遵守）

1. **编辑任何代码之前**，必须先输出两行信息：
   - 仓库 git 链接（`git remote get-url origin` 的结果）
   - 当前 commit hash（`git rev-parse HEAD` 的结果）
2. **每次改动任何文件之后**，必须依次执行：
   - `git add .`
   - `git commit -m "<提交信息>"` —— **commit message 必须由你（AI）根据本次实际改动生成**，准确描述"这次改了什么、为什么"，禁止使用 `update`、`fix`、`changes` 这类无意义信息
   - 输出新的 commit hash（`git rev-parse HEAD` 的结果）
   - （每次 commit 会自动导出 diff 与改动说明到宿主侧，你无需额外操作）
3. **执行 `git add .` 之前**，必须检查：
   - 仓库根目录是否存在 `.gitignore`（不存在则先创建）
   - 本次改动中是否存在"应被忽略但未被忽略"的文件（常见：`node_modules/`、`.next/`、`dist/`、`build/`、`.env`、`*.log`、`*.patch`、`*.diff`、密钥/证书文件、`.DS_Store` 等）
   - 一旦发现，**立即停止执行**，向用户说明情况并等待用户确认后才可继续
4. **提交后自动 push**：你每次 commit 后，系统会自动把新提交 push 到远端仓库（push 被拒时自动以"远端优先"策略 pull 后重试）。你无需手动 push，也**禁止 force push、删除分支等任何其他远端写操作**。
5. **代码内标注**：每处改动的代码段旁边（上方一行或改动行尾），必须新增一行标注，格式固定为：
   `commit by ai-agent, <YYYY-MM-DD HH:MM:SS>, <一句话简写本次更改内容>`
   - 注释语法随文件类型：JS/TS 用 `//`，Python/Shell/YAML 用 `#`，HTML/Markdown 用 `<!-- -->`，以此类推
   - **除此之外，禁止你在代码中写任何其他注释**（不得添加解释性注释、文档注释、TODO 等）
6. **远端变更分析（内置 pull 检查）**：你每次执行 `git add` 之前，系统会自动 fetch 配置要求的分支远端；若发现远端有新提交，会自动合并进本地（**冲突时一律用远端版本覆盖**），并在 `/diff` 导出 `<项目名>-pull-<时间>-<hash>.patch` 和同名 `.md`。当 `git add` 的输出中出现 `[pull-check]` 提示时，你**必须先阅读并分析对应的 .md 文件内容**，理解远端改了什么，再继续新增代码；若提示你未提交的修改被远端覆盖，接受该结果——如确有必要，基于远端最新版本重新实现。
