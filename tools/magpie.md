# magpie

- 仓库：https://github.com/yetone/magpie
- 官网 / 下载：https://usemagpie.ai
- 中文说明：https://github.com/yetone/magpie/blob/main/README.zh-CN.md
- 开发者：yetone
- 许可证：MIT
- Star 数：约 3.4 万（GitHub API 显示 33,667，2026-11-10 查询）
- 最新版本：v0.1.3587（2026-11-10 发布，几乎每天更新）
- 推荐人：taraYK

## 它是什么

magpie 是一个装在自己电脑上的 AI 编程 Agent 模型管理器，同时带一个本地网关（默认 `127.0.0.1:3425`）。Claude Code、Codex、Gemini CLI、OpenCode 这类 Agent 原本各自只能用自家模型，配置文件格式和 key 也各管各的。magpie 把它们收到一个界面里，让任意 Agent 用任意模型，比如 Claude Code 用 Kimi，Codex 用 DeepSeek，Gemini CLI 用智谱 GLM。

## 有什么用

- **任意 Agent 用任意模型**：在 Agents 页从一张表里选模型，magpie 只改 Agent 配置里对应的那个键，注释和格式保留。可以把整套配置存成「方案」（Profile），一键切换。
- **一个账号处处能用**：可以接 API key（DeepSeek、Kimi、智谱、通义千问、火山方舟、OpenRouter 等预设）、本地模型（Ollama、LM Studio），也可以把已登录的 Claude、ChatGPT、Copilot、Gemini、Grok 订阅当成模型来源，给其他 Agent 用。
- **额度用完自动接上**：同一服务商加多个账号会自动轮换；「路由组」可以在多个模型或服务商之间按规则切换（`smart`、`order`、`rotate`、`usage`、`pace` 等模式），Agent 端不会看到报错。只有一个账号且没建路由组时，报错照常返回。
- **本地脱敏**（默认关闭）：在 **设置 → 隐私** 打开后，提示词里的 API key、token、邮箱、银行卡号等在发出前换成占位符，回复里再换回原值。
- **通用网关**：任何能填 base URL 的工具都能接，网关兼容 OpenAI、Anthropic、Gemini 三种接口格式。

## 适合谁

同时用不止一个 Agent、不止一家模型服务商或不止一个账号时才值得装。只用一个 Agent 配它自家订阅且够用，就不需要。

## 怎么用（简述）

详细步骤以[官方中文 README](https://github.com/yetone/magpie/blob/main/README.zh-CN.md) 为准。

1. **安装**：从官网下载应用（macOS、Windows、Linux，也有 Docker 和 Termux 版），或运行官方脚本：

   ```sh
   curl -fsSL https://usemagpie.ai/install.sh | sh
   ```

2. **添加服务商**：打开 magpie，进入 **Providers → 添加 provider**，选预设后粘贴 key，或登录一个订阅。
3. **选模型**：在 **Agents** 页给每个 Agent 选模型，开一个新会话就生效。

也可以全程用命令行，例如：

```sh
magpie provider add deepseek sk-…           # 添加 DeepSeek（只需 key）
magpie claude deepseek/deepseek-v4-pro       # 让 Claude Code 用 DeepSeek
magpie quota                                 # 查看每个套餐剩余额度
magpie tui                                   # 终端界面
```

卸载：`curl -fsSL https://usemagpie.ai/install.sh | sh -s -- --uninstall`，会先把各 Agent 的配置恢复原样。

## 注意事项

- **会读取登录信息**：magpie 会读取 Claude Code、Codex 的登录文件和系统钥匙串来发现订阅账号。不想让它读，可以在 设置 → 隐私 关闭「订阅账号」，或运行 `magpie accounts discovery off`。
- **会改 Agent 配置**：它直接改写各 Agent 的配置文件。卸载脚本会恢复，但重要配置最好先自己备份。
- **安装方式**：`curl | sh` 会直接执行远程脚本，介意的话改用官网安装包或 `go install`。
- **脱敏默认关闭**：不打开的话，提示词原样发给模型服务商。打开后，针对密钥本身的问题（比如某个 token 为什么被拒）模型看不到真值。
- **API Key**：不要把任何 key 写进这个仓库（这是公开仓库）。
- **赞助商**：README 列了几家 API 中转站作为赞助商，选服务商时自己判断，不代表推荐。

## 待核实

- 【待核实】用第三方工具共用 Claude、ChatGPT 等订阅是否符合各厂商的服务条款，README 没有说明。
- 【待核实】实际使用效果和稳定性，没有亲自安装测试。

## 来源

- 官方 README 和中文 README：https://github.com/yetone/magpie （核实日期 2026-11-10）
- GitHub API 仓库与版本信息：2026-11-10 查询
