# DS Bot（DeepSeek Bot）用法整理

> 核实日期：2026-10-24。来源见文末。

## 它是什么

DS Bot（全称 DeepSeek Bot）是开发者 FeiZ（X：@Fei2411）做的开源 Bot 团队应用，基于 DeepSeek Harness（DSH）。你可以建好几个 Bot，每个 Bot 有自己的名字、角色、模型和长期记忆，可以拉进群聊分工协作。

- 官网：https://dsbot.app/
- 源码：https://github.com/Fei2411/DeepSeek-Bot（Apache-2.0 开源）
- 最新版本：v0.2.1（官网标注 2026-10-23）
- **不是 DeepSeek 官方产品**，与 DeepSeek 公司没有隶属关系。

## 有什么用

- **主 Bot「Chief」帮你建团队**：和 Chief 说一句话，它就把 Bot 和群聊建好。
- **长期记忆**：每个 Bot 记得你的偏好（Markdown 记忆，分个人和团队两级）。
- **群聊分工**：比如研究员、写作、程序员各管一块；群管理员默认先回复，需要时再叫其他 Bot，省 token。
- **每个 Bot 选不同模型和思考强度**。
- **定时任务**：一次、每天、每周或每隔几小时自动开工（如每个工作日 9 点汇总行业新闻）。
- **共享能力**：MCP 服务器、技能（skill）装一次，全队可用；有 GitHub connector。
- **权限可控**：每个 Bot 可设只读 / 仅工作区 / 完全访问；本地命令要你批准才执行。
- **用量统计**：按时间、Bot、模型看 token 消耗。
- **本地优先**：聊天、记忆、文件都存在自己电脑上。

## 怎么用

### 方法一：桌面版（最简单）

1. 打开 https://dsbot.app/ 或 GitHub Releases 下载：Windows（x64）、macOS（Apple 芯片 / Intel）、Linux（AppImage / deb）。桌面版自带 DSH，不用先装。
2. 安装包还没签名：
   - Windows 提示“已保护你的电脑”时，点 **更多信息 → 仍要运行**。
   - macOS 打开 **系统设置 → 隐私与安全性 → 仍要打开**。
3. 第一次启动选数据存放位置，然后在设置里登录 DeepSeek 账号或配置别的模型服务（用你自己的额度）。
4. 和 Chief 说你要什么团队，例如：“帮我建一个研究员和一个写作 Bot，每个工作日早上 9 点汇总前一天的行业新闻。”

### 方法二：作为 DSH 插件

已经在用 DSH 的话：
1. DSH 桌面端侧栏 **插件 → 添加插件**。
2. 包名填 `ds-bot`；中国大陆把 **安装源** 选 **中国大陆镜像源**。
3. 点 **安装**，再点 **立即启用**；提示下次启动生效就完全退出 DSH 再打开。

DSH 网页版（需 Node.js ≥ 22.19 和 pnpm，命令以 README 为准）：

```sh
npx @deepseek-ai/dsh@0.2.1-rc.2 plugin --profile web add ds-bot
npx @deepseek-ai/dsh@0.2.1-rc.2 web
```

官网还提供 Docker 版，适合 NAS 或常开服务器。

## 注意事项

- **费用**：软件免费开源，但模型调用花的是你自己的 API 额度。
- **安全**：Bot 能读写文件、运行命令；建议先给“只读”或“仅工作区”权限，重要操作自己检查。API 密钥别写进本仓库（公开仓库）。
- **安装包未签名**，系统会警告，请只从官网或官方 GitHub Releases 下载。
- **别和 DeepSeekBot（DSB）搞混**：BotHarness 维护的 DeepSeekBot（deepseekbot.app，npm `deepseekbot`，MIT）是另一个独立项目，重心是 Git 记忆、Bot 进飞书/Slack/微信等。本页说的是 DS Bot（npm `ds-bot`）。
- 项目很新（仓库创建于 2026-10-07），版本更新快，具体步骤以官方 README 为准。
- 原帖说的“非常有意思的新功能”具体是什么：【待核实】。

## 来源

- 原帖（@Fei2411，2026-10-23）：https://x.com/Fei2411/status/2113594455492325428
- 官网：https://dsbot.app/
- GitHub：https://github.com/Fei2411/DeepSeek-Bot （中文 README：https://github.com/Fei2411/DeepSeek-Bot/blob/main/README.zh.md）
- 两个项目的区别：https://github.com/BotHarness/DeepSeekBot
