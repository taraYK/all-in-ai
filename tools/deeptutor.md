# DeepTutor

- 仓库：https://github.com/HKUDS/DeepTutor
- 官网：https://deeptutor.info/
- 论文：https://arxiv.org/abs/2604.26962
- 开发者：香港大学数据智能实验室（HKUDS）
- 许可证：Apache 2.0
- Star 数：约 4.9 万（GitHub API 显示 48,997，2026-10-29 查询）
- 最新版本：v1.7.0（2026-10-28 发布）

## 它是什么

DeepTutor 是一个开源的个性化 AI 学习工作台，可以自己部署。它把辅导对话、解题、出题、研究、可视化和掌握度练习放在同一个系统里，并共用同一套知识库和记忆。模型需要自己配置，可以接各家云端模型的 API。

## 有什么用

- **多种学习模式**：Chat（对话）、Ask Questions（提问）、Quiz（出题测验）、Research（研究）、Visualize（可视化）、Solve（解题）、Mastery Path（掌握路径）、Immersive Reading（沉浸式阅读）、Audio Overview（音频概览），共用同一个会话上下文。
- **知识库（RAG）**：上传自己的资料建库，支持 LlamaIndex、PageIndex、GraphRAG、LightRAG 等多种引擎，也能连接 Obsidian 笔记库、腾讯 IMA、MarginNote 4 等。
- **可查看、可修改的记忆**：分三层（L1 原始记录、L2 摘要、L3 综合归纳），能看到它记住了你什么，也能改。
- **学习管理**：题库、学习笔记本、任务看板（待办 / 进行中 / 完成）、每日练习。
- **视频学习**：可以添加 YouTube、B 站链接，按字幕检索、按时间点提问。
- **扩展**：支持 MCP 服务、命令行工具，也能在对话里调用 Claude Code、Codex 等编程 Agent。

## 怎么用（部署简述）

官方给了几种安装方式，详细步骤以[官方 README](https://github.com/HKUDS/DeepTutor#-get-started) 为准。

**方式一：Docker 一条命令试用**（先装好 Docker）

```bash
docker run --rm --name deeptutor \
  -p 127.0.0.1:3782:3782 \
  -v deeptutor-data:/app/data \
  ghcr.io/hkuds/deeptutor:latest
```

浏览器打开 http://127.0.0.1:3782 ，在 **Settings → Providers** 里填模型服务商和 API Key。按 `Ctrl+C` 停止，数据保存在 `deeptutor-data` 卷里。

**方式二：pip 安装**（需要 Python 3.11–3.14 和 Node.js 20+）

```bash
mkdir -p my-deeptutor && cd my-deeptutor
pip install -U "deeptutor[full]"
deeptutor init     # 设置端口、模型服务商、API Key，可选 embedding（知识库用）和搜索服务
deeptutor start    # 启动后打开终端里显示的地址，默认 http://127.0.0.1:3782
```

从 1.7.0 起，`pip install deeptutor` 只装核心对话功能，完整网页版要用 `deeptutor[full]`。

**方式三：让 AI Agent 帮你装**：官方 README 里有一段现成提示词，复制给 Claude Code、Codex 这类能操作终端的 Agent，它会按官方文档完成安装和配置。

## 对量化 / 金融自学的潜在用途

以下是使用设想，还没有实际试用验证：

- **教材和笔记建知识库**：把金融理论教材、因子研究论文、自己的学习笔记放进知识库，提问时回答带出处，方便回查原文。
- **出题自测**：用 Quiz 和题库针对资产定价、风险模型、组合优化等内容出练习题，错题进题库复习。
- **掌握路径**：用 Mastery Path 按章节推进，适合补系统性的理论缺口。
- **配合现有流程**：DeepTutor 负责系统化学习和自测，碎片化的实践小任务照旧，学习结论仍整理到自己的笔记仓库。

它是学习工具，生成的内容不能当作投资或交易依据。

## 注意事项

- **内容准确性**：题目和讲解由大模型生成，可能出错，关键公式和结论要对照教材核实。它不是正规课程，也没有资质认证。
- **成本**：软件免费，但模型和 embedding 用的是自己的 API 额度；建知识库、生成长内容会消耗较多 token。本地运行也需要一定的内存和磁盘。
- **数据隐私**：资料、记忆和设置存在你自己部署的机器上，但对话和文档内容会发给你配置的模型服务商。公司内部资料、个人敏感信息不要上传。
- **更新很快**：几乎每周发新版本，安装命令和功能名称可能变化，以官方 README 为准。
- **API Key**：不要把 Key 写进这个仓库（这是公开仓库）。

## 待核实

- 【待核实】实际使用效果、中文资料的解析和检索质量，没有亲自安装测试。
- 【待核实】Docker 和 pip 版本对电脑配置的具体要求，README 未写明最低配置。

## 来源

- 官方 README：https://github.com/HKUDS/DeepTutor （核实日期 2026-10-29）
- GitHub API 仓库信息（Star 数、许可证）：2026-10-29 查询
