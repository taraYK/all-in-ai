# agency-agents：AI 专家智能体合集

- 项目地址：<https://github.com/msitarzewski/agency-agents>
- 配套桌面应用：<https://agencyagents.app>
- 价格：免费开源（MIT 许可证）
- 推荐人：taraYK

## 它是什么

一套现成的 AI "专家人设"合集。每个智能体就是一个 Markdown 文件，写好了这个专家的身份和性格、核心任务、工作流程、交付物（含代码示例）、成功标准和沟通风格。

智能体按"部门"分组。比如工程部门就有前端开发、后端架构师、AI 工程师、代码审查员、数据库优化师、技术文档写手、提示词工程师、微信小程序开发、飞书集成开发等几十个角色。其他部门可以在项目 README 里浏览。

## 用途

- **让 AI 更专业**：不用自己从零写提示词，直接给 AI 套上一个写好的专家角色，回答会更聚焦、更有章法。
- **组建 AI 团队**：一个项目要用到前端、后端、测试、文档等多个角色时，可以按需挑几个专家一起用。
- **学写提示词**：每个文件都是高质量提示词的范例，可以参考它的结构来写自己的。

## 怎么用（从简单到进阶）

### 方法一：复制粘贴（最简单，不用装任何东西）

1. 打开项目页面，进入某个部门文件夹，例如 `engineering/`。
2. 点开想用的角色文件，例如 `engineering-code-reviewer.md`（代码审查员）。
3. 复制全文，粘贴到 ChatGPT、Kimi、豆包、Claude 等任意 AI 对话的开头，然后接着提你的需求。

### 方法二：安装桌面应用（推荐给用 AI 编程工具的人）

官方出了桌面应用 **Agency Agents**，支持 macOS、Linux、Windows。它可以浏览所有角色，一键装进 Claude Code、Cursor、Codex、Gemini CLI、OpenCode、Qwen 等工具，还会自动更新。

- 下载：<https://github.com/msitarzewski/agency-agents-app/releases/latest>
- Mac 也可以用命令装：`brew install --cask msitarzewski/agency-agents/agency-agents`

### 方法三：用命令行装进 Claude Code

先把项目下载（clone）到本地，再在项目目录里运行：

```bash
# 全部装进 Claude Code
./scripts/install.sh --tool claude-code

# 或者只复制某个部门
cp engineering/*.md ~/.claude/agents/
```

装好后，在 Claude Code 里直接说"激活 Frontend Developer 模式，帮我写一个 React 组件"就行。

### 方法四：装进其他 AI 编程工具

支持 GitHub Copilot、Cursor、Gemini CLI、OpenCode、Aider、Windsurf、Kimi Code、Codex 等。

```bash
# 第一步：生成各工具需要的文件
./scripts/convert.sh

# 第二步：交互式安装（会自动识别你装了哪些工具）
./scripts/install.sh

# 也可以指定工具，例如 Cursor
./scripts/install.sh --tool cursor
```

只想装部分角色：

```bash
./scripts/install.sh --list teams                                      # 查看所有部门和角色数
./scripts/install.sh --tool claude-code --division engineering,security # 只装某几个部门
./scripts/install.sh --tool cursor --agent frontend-developer,ui-designer # 只装某几个角色
```

## 注意事项

- 角色文件都是英文写的。直接贴进中文对话也能用，最后加一句"请用中文回答"就行。
- 用 OpenCode 的话，它目前最多只认大约 119 个智能体，多出来的会被悄悄丢掉。建议用 `--division` 只装需要的部门。
