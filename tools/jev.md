# Jev 用法整理（TypeSafe AI · System One 模型）

> 整理日期：2026-10-22。来源均已逐条打开核对，核对结果见文末「来源核实情况」。价格、版本等信息会变，以官方文档为准。

## 一句话介绍

Jev 是 TypeSafe AI 在 2026 年 9 月 15 日发布的第一个 **System One 模型**。它**不生成文字**：你给它一段「状态」(state) 和几道预先定义好的「问题」(questions)，它直接返回带概率的结构化答案。可以把它看成写在代码里的「智能 if 语句」。

## 核心概念

### state（状态）
要判断的材料，比如一封邮件、一条工单、一段日志、一份研报摘要。可以是字符串、JSON 对象或文本数组。**只支持文本**，不支持图片、音频、视频。

### questions（问题）
一组带名字的问题，一次请求里可以同时问多道，并行作答，问 1 道和问好几道的延迟差不多。返回的答案用同样的名字对应回去。

### 三种问题类型（官方称为 primitives，原语）

| 类型 | 作用 | 返回 | 限制（据官方文档 / 论文） |
|---|---|---|---|
| **Choice** | 从你给的选项里选一个 | `choice` + 每个选项的 `probabilities` + `confidence` | 最多 255 个选项 |
| **Score** | 按你写的等级量表打分 | `score`（按概率加权的等级，如 0.67）+ `probabilities` + `confidence` | 2–10 个等级 |
| **Noul** | 判断一句话是否为真 | `noul`：0–1 之间的概率 | — |

### 概率输出
官方说 Jev 用一种叫 RLCD（校准决策强化学习）的方法训练，目标是让概率「校准」：说 90% 把握的判断，大量统计下来约 90% 是对的。官方也写明：校准是**整体统计意义**上的，不保证单个答案一定正确。

### 和 LLM（ChatGPT 这类）的区别

| | 传统 LLM | Jev |
|---|---|---|
| 输出 | 逐字生成文本，需要再解析 | 直接给类型固定的选择 / 分数 / 概率 |
| 能做 | 写作、解释、推理、写代码 | 分类、路由、打分、是非判断 |
| 格式 | 可能输出格式错误或选项以外的内容 | 结构上只会在你给的选项里返回 |
| 计费 | 输入输出都收费 | 只按输入 token 收费，输出免费 |

一句话：**LLM 管「写」和「想」，Jev 管「快速判断」**，两者搭配用。

## 官方资源

| 资源 | 链接 | 说明 |
|---|---|---|
| 快速开始 | https://docs.typesafe.ai/introduction/quickstart | 4 种用法：SDK、HTTP API、Agent Skill、Playground |
| 控制台 / Playground | https://console.typesafe.ai | 登录后创建 API Key、看用量、在网页里直接试 |
| API 端点 | `POST https://api.typesafe.ai/v1/systemone` | 请求头 `Authorization: Bearer <API_KEY>`，`Content-Type: application/json` |
| 模型名 | `jev-latest` | 稳定版别名，也是 SDK 默认值；截至 2026-10-22 官方 Models 页显示指向 `jev-1.13.0`，`jev-preview` 指向 `jev-1.14.0` |
| Models / 价格 | https://docs.typesafe.ai/models | 截至 2026-10-22：输入 $0.042 / 百万 token，输出免费；上下文 64k / 请求、32k / 单问题 |
| System One 概念 | https://docs.typesafe.ai/concepts/system-one | 与 LLM 的区别 |
| 发布博客 | https://typesafe.ai/blog/introducing-system-one-models-and-jev | 2026-09-15 发布 |
| Agent Skill | https://github.com/typesafe-ai/skills | 让 Claude Code / Cursor 等编程 Agent 会调用 Jev |
| 第三方评测论文 | https://arxiv.org/html/2609.37647v1 | 波恩大学，在 37 个数据集上评测 jev-1.13.0 |

请求体由 3 部分组成：`model`、`state`、`questions`（每个问题写 `type`、`instructions`，Choice / Score 还要写 `criteria`）。

## 最小 Python 示例

依据官方 Quick start。需要 Python 3.10 以上。

**第 1 步：安装 SDK 并设置密钥（密钥只放环境变量，千万别写进代码或仓库）**

```bash
python3 -m venv .venv
.venv/bin/pip install typesafe-sdk
export TYPESAFE_API_KEY="在控制台创建的 key"   # 只在自己终端里设置，不要提交到 GitHub
```

**第 2 步：保存为 `first_jev.py` 并运行**

```python
from typesafe_sdk import Choice, Noul, Score, TypeSafeClient

state = "Our API integration started returning 500 errors on every request about 20 minutes ago, and we can't process any customer orders until this is fixed."

with TypeSafeClient(model="jev-latest") as client:  # 自动读取环境变量 TYPESAFE_API_KEY
    response = client.system_one(
        state=state,
        questions={
            "department": Choice(
                instructions="Which team should handle this",
                criteria={
                    "billing": "Payment or subscription issues",
                    "technical": "Bugs or integration problems",
                    "sales": "Pricing or account questions",
                },
            ),
            "frustration": Score(
                instructions="How frustrated the customer appears",
                criteria=[
                    "Calm, just stating facts",
                    "Frustrated but civil",
                    "Very angry, strong language",
                ],
            ),
            "is_urgent": Noul(
                instructions="The message conveys urgency or time-sensitivity",
            ),
        },
    )

print(response.choices["department"].choice)   # 例：technical
print(response.scores["frustration"].score)    # 例：0.67（0–2 之间）
print(response.nouls["is_urgent"].noul)        # 例：0.99（紧急的概率）
```

```bash
.venv/bin/python first_jev.py
```

**不用 SDK 的 HTTP 写法（curl）**，请求体和官方一致：

```bash
curl https://api.typesafe.ai/v1/systemone \
  -H "Authorization: Bearer $TYPESAFE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"jev-latest","state":"I was charged twice. Please refund.","questions":{"is_billing":{"type":"noul","instructions":"Is this about billing?"}}}'
```

返回大致是：`{"model":"jev-1.13.0","answers":{"is_billing":{"type":"noul","noul":0.98}},"usage":{...}}`（数值是示例，不同版本会变）。

## 建议起步路径（10–30 分钟一步）

1. **只用浏览器试（10 分钟）**：登录 https://console.typesafe.ai 的 Playground，贴一段文字当 state，先加一道 Noul 问题，比如「这段话是否表达了紧急性？」，看返回的概率。
2. **三种题都试一遍（15 分钟）**：同一段材料，再加一道 Choice 和一道 Score，体会「一次请求问多道题」。
3. **拿 API Key、跑通 Python（20 分钟）**：在控制台建 Key，用环境变量设置，跑上面的 `first_jev.py`。
4. **换成金融场景（30 分钟）**：比如把一条公司公告或新闻标题当 state，问：
   - Choice「公告类型」：业绩预告 / 回购增持 / 减持 / 诉讼处罚 / 其他
   - Score「对公司的影响偏向」：明显利空 / 略偏利空 / 中性 / 略偏利好 / 明显利好
   - Noul「是否涉及具体金额或比例」
   结果写进 CSV，做成一个「文本 → 结构化标签」的小数据集。
5. **用历史数据陪跑检验（30 分钟）**：挑几十条自己判断过的公告，对比 Jev 的结果，重点看「七八成把握」的那些对了几成，再决定阈值（掘金教程提到官方起步阈值是 0.8，【待核实：未在官方页面看到原文】）。
6. **再进阶**：在 Python 里按 `confidence` 分档：高把握自动处理，低把握交给人或更强的大模型。

> 金融场景只当**研究工具**用：标签可以当作因子研究的一个输入，但不要直接拿概率下单交易。

## 社区资源

### X 帖文

| 作者 | 链接 | 一句话说明 |
|---|---|---|
| @0xMovez | https://x.com/0xMovez/status/2101026930967335040 | 「20 TIPS FOR USING JEV FROM 0 TO PRO」（2026-09-18），第一条：别把它当 LLM，它不写，只做决策 |
| @0x_rody | https://x.com/0x_rody/status/2112558839589417355 | X 长文「How To Wire Jev Into Grok Bot」(2026-10-20)；他还发过多条 Jev + Agent 玩法帖。【待核实：没找到明确的「实验 / 开源示例清单」那一条】 |
| @Saccc_c | https://x.com/Saccc_c/status/2104477450046685236 | 只找到一条中文简短回复「Jev 出现后 我在关注 agent 交易」。【待核实，「中文场景帖」原帖链接未找到】 |
| @_avichawla | https://x.com/_avichawla/status/2107767245917368751 | 「RAG vs. Jev + RAG, clearly explained!」（2026-10-07），图解在普通 RAG 流程中加入 Jev |

### 中文教程（掘金）

| 标题 / 作者 | 链接 | 一句话说明 |
|---|---|---|
| 全网刷屏的 Jev 模型正式开放！一手实战测评 + 保姆级教程（程序员鱼皮，2026-09-21） | https://juejin.cn/post/7687816505830490150 | 原理通俗、三种原语举例、接 Skill、与 DeepSeek 对比测评。注意：文中 Python 示例写的是 `response.answers[...]`，官方 Python 写法是 `response.choices / scores / nouls[...]` |
| Jev 从入门到实战：读懂 System One 模型并跑通智能 if 语句（洞窝技术，2026-10-09） | https://juejin.cn/post/7694115089583947785 | 带完整工单路由 Demo（按置信度分流），并整理了上线前要注意的问题 |
| 最近爆火的 Jev，怎么玩？小白入门手册（运营黑客，2026-09-22） | https://juejin.cn/post/7688144300723863598 | 讲怎么提问（大问题拆成小问题、一次多问）+ 20 个开源项目清单 |
| 保姆级手把手教程：玩转发布即爆火的 Jev（阿坡RPA，2026-09-29） | https://juejin.cn/post/7690797132370706447 | 从注册、控制台建 Key 到 Windows 下用 curl 调用，截图很细。文中提到的 Playground 域名 thejevai.com【待核实：不是 typesafe.ai 官方域名，建议用 console.typesafe.ai】 |

### GitHub 示例

| 仓库 | 链接 | 一句话说明 |
|---|---|---|
| paramjeetn/jev-cookbook | https://github.com/paramjeetn/jev-cookbook | 英文 cookbook：120+ 用例、10 个可运行示例、4 种组合模式（MIT） |
| rajivkuriakose/typesafe-jev-examples | https://github.com/rajivkuriakose/typesafe-jev-examples | 可通过 OpenRouter（`typesafe/jev-1.13`）运行的示例，有官方 Key 时也能直连 |
| Datawhale Jev 中文课程 | https://datawhalechina.github.io/jev-cookbook/ | 中文课程：认识 Jev、核心概念、架构模式、实战指南、Agent 集成等 11 章 |

## 注意事项

- **密钥安全**：API Key 只放环境变量或密码管理器（如 Bitwarden），不要写进代码或提交到仓库，也不要截图发给别人。
- **只支持文本**：图片、表格截图要先转成文字或 JSON。
- **格式不会错，不代表判断不会错**：Jev 只会在你给的选项里选，但也可能选错。上线前要用自己的数据检验。
- **官方列出的短板**：算术、日期计算、多步推理不可靠（据官方文档与教程）；论文显示低资源语言、细粒度标签上表现会下降。中文效果：有教程说一般，也有实测说正常，【待核实】，请用自己的中文数据测。
- **Noul 阈值不要死用 0.5**：论文发现二分类概率排序好，但固定 0.5 阈值效果差，阈值要用自己的数据调。
- **`jev-latest` 会变**：别名升级后答案可能变化；响应里的 `model` 字段会写实际版本，建议记录下来，回测时可复现。
- **速度、价格对比数字多来自官方自测**，当参考即可。
- **不要用于确定性买卖决策**。

## 来源核实情况（2026-10-22）

| 来源 | 状态 |
|---|---|
| 官方 Quick start | ✅ 已打开核对（端点、请求体、Python SDK 写法、示例输出） |
| console.typesafe.ai | ✅ 页面可访问（需登录，内容未进入查看） |
| API `POST /v1/systemone`、`jev-latest` | ✅ 官方文档确认 |
| @0xMovez 20 Tips | ✅ 原帖存在，内容一致（只读到开头部分） |
| @0x_rody 实验清单 | ⚠️ 账号和多条 Jev 帖存在；「实验 / 开源示例清单」那一条【待核实】 |
| @Saccc_c 中文场景帖 | ⚠️【待核实，链接未找到】，只找到一条相关回复 |
| @_avichawla RAG vs Jev+RAG | ✅ 原帖找到（链接见上表） |
| 掘金 4 篇 | ✅ 都已打开核对，标题、作者、日期见上表 |
| jev-cookbook / typesafe-jev-examples / Datawhale 中文课程 | ✅ 都已打开核对 |
