# GitHub Copilot Chat Apply 独立实验：聊天代码块写入空文件

测试日期：**2026-11-16**。本文基于当日客户端 UI 观察与落盘文件只读核验，独立记录一次 **Agent 仅在聊天输出代码块，再由操作者点击内置 Apply / Keep** 的实验；不覆盖 [历史 README](README.md)、[DC 文档](DC-readme.md) 或 [CC 文档](CC-readme.md)。

**本地结果：生成阶段目标文件始终为空；随后通过代码块的 Apply in Editor 写入，Keep 后实际新增 3 行、删除 0 行，共 26 字节，Python AST 有效，没有执行目标文件。三行正文符合要求，但落盘没有末尾换行，不符合 prompt 的 final newline 要求，已保留真实输出，未补改。**

**结论边界：这不是生成回合中 Agent 直接调用文件编辑工具的操作路径。本轮未查询 Metrics API，实际 feature 归因、Apply 内部是否调用模型及调用次数均为 unknown；不能断言服务端一定不记录 `agent_edit`，也不能证明历史 +800 / -20 的根因。**

## 1. 账号、环境与控制条件

除明确注明 UTC 或保留时区的时间戳外，以下时间均为北京时间（UTC+8）。

| 项目 | 本轮记录 |
|---|---|
| 测试账号 | Accounts 菜单确认 `gye6666 (GitHub)` |
| VS Code | **1.145.3 (user setup)** |
| IDE commit | `dcd3ad6e368fbe19d03f37054a40dbaf40d676f4` |
| IDE build date | `2026-11-07T00:56:09Z` |
| embedded `@github/copilot` | `1.0.103.r41379698026.g3219c66` |
| embedded `@github/copilot-sdk` | `1.0.27.41379698026.g3219c66` |
| 操作系统 | `Windows_NT arm64 10.0.26300` |
| 版本来源 | VS Code Help > About 的实际显示 |
| Harness | **Copilot**，UI 描述为 SDK / Agent Host |
| 未选入口 | Local（legacy extension-host）、Cloud；Remote Connections 为 Off |
| Agent | **内置默认 Agent** |
| 模型 / Effort / Context | **GPT-6.1 Sol / Medium / 272K** |
| 模式 / 权限 | **Interactive / Manual permissions**，保持不变 |
| 操作方式 | Copilot App 内置 Computer Use |
| 独立目标文件 | 合成工作区 `copilot-loc-test` 中的 `chat_apply_20261116_1342.py` |

全新 Chat 最初继承了 LoC Probe 自定义 Agent，发送前已在选择器切换为内置 **Agent**；未修改 `loc-probe.agent.md`。本轮没有升级、重启或切换账号，没有新授予 Session 或永久权限。为读取可访问性文本曾临时启用 Screen Reader Optimized，实验后恢复为 disabled。

账号菜单、版本和 UI 状态是客户端证据，不等于逐请求确认了后端身份、模型请求数或遥测送达。

## 2. 空基线与原有文件保护

目标文件此前不存在，于 `2026-11-16T13:45:56.1103176+08:00` 使用排他 `CreateNew` 准备为 **0 字节空文件**；这一步只建立空基线，不写入三行结果。生成完成、Apply 之前于 `13:48:46.451` 再次核验，仍为 0 字节且哈希相同。

| 证据 | 结果 |
|---|---|
| 空文件 SHA-256 | `E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855` |
| 原文件保护范围 | 实验工作区已有 **27 项文件**，含历史 DC / CC 测试文件及 `.github/agents/loc-probe.agent.md`，不含本轮新文件 |
| 对照节点 | 实验前、生成后、Keep 后 |
| 对照结果 | 原有 27 项的路径及字节哈希 manifest 始终一致，未变化 |

此处的 27 项指**实验工作区中的原有文件**，不是本次 README 发布提交的文件数。最终三行内容没有通过脚本或手工文件编辑工具补写。

## 3. 单次请求与 Apply 操作

### 3.1 发送内容

发送前另行取得一次明确批准；未把此前笼统的“请操作”当作发送批准。检查草稿没有旧 slash 命令或重复内容后，只点击 **Send 一次**。以下英文 prompt 为一条消息，录入时没有真实回车：

```text
Controlled chat-only code-block Apply test 20261116. Do not read, edit, create, or delete files. Do not call tools, terminals, scripts, MCP, skills, handoffs, or subagents. Return only one Python fenced code block with exactly three lines in this order: first `a = 1`, second `b = 2`, third `print(a + b)`, with a final newline. I will apply this code block myself.
```

助手返回一个 Python fenced code block，正文如下：

```python
a = 1
b = 2
print(a + b)
```

新 Chat 中观察到 **1 条用户消息 row、1 条助手代码块 row、0 个工具调用卡**。UI 出现过 Reasoning，但没有工具卡；结合生成后空文件哈希，只能支持“此次生成阶段没有改动目标文件”的客户端结论，不能写成“已审计所有后端调用”。

Response details 显示：completed `11/16/2026 1:48 PM`、elapsed **12s**、模型 GPT-6.1 Sol、input tokens **31,664**、output tokens **24**、**7.9 credits**。这些是 UI 显示值，不是 Apply 内部请求计数；下面的完成观察时间也不等同于服务端精确完成时间。

### 3.2 Apply 按钮在哪里

本轮 VS Code 的代码块 toolbar **默认隐藏**，进入或点到代码块区域后才显示。代码块右上角有四个图标，**最左侧“纸张带箭头”图标的可访问性名称为 `Apply in Editor`**；它不是常驻显示整段英文的文字按钮。紧右侧是 **`Insert At Cursor (Ctrl+Enter)`**，两者不要混淆。

`13:52` 左右仅点击代码块空白区域重新展示 toolbar、核对控件名，没有再次 Apply；这不构成第二次实验。

### 3.3 实际写入和接受

1. 将上述空文件打开为 active editor，点击代码块内置 **Apply in Editor 一次**。
2. 弹出 `Select where to apply the code block`，选项为 `Active editor 'chat_apply_20261116_1342.py'` 和 `New untitled editor`；选择前者一次。
3. 出现 `Pending changes from chat`、`1 change in 1 file` 及 Keep / Undo，读取 editor 文档确认三行。
4. 点击 **Keep Chat Edits in this File (Ctrl+Shift+Y) 一次**。Keep / Undo 和 Explorer pending 标记消失，稍后标题 pending 后缀也清除。Keep 后已落盘，无需另点 Save。

本轮没有 Copy、Insert At Cursor、额外聊天 Send、脚本补写或运行目标 Python；也未弹出额外写入权限提示。**出现 Pending changes / Keep 不能单独确定 Metrics 的 feature bucket；Apply 是否内部调用模型、调用几次没有可见请求计数，均记为 unknown。**

## 4. 实测时间线

日期均为 **2026-11-16**；UTC 列便于与后续活动日对齐，不代表已取得逐事件后端遥测。

| 事件 | 北京时间（UTC+8） | UTC | 观察 / 核验 |
|---|---|---|---|
| 建立空基线 | 13:45:56.1103176 | 05:45:56.1103176 | 排他 CreateNew，0 字节 |
| 点击 Send | 13:48:04.742 | 05:48:04.742 | 唯一一次发送 |
| 观察到回复完成 | 13:48:20.556 | 05:48:20.556 | 一个三行 Python 代码块 |
| Apply 前只读核验 | 13:48:46.451 | 05:48:46.451 | 文件仍为空、SHA-256 未变 |
| 点击 Apply in Editor | 13:48:54.566 | 05:48:54.566 | 唯一一次 Apply |
| 选择 Active editor | 13:49:10.303 | 05:49:10.303 | 指向目标空文件 |
| 点击 Keep | 13:49:24.177 | 05:49:24.177 | 接受该文件的 Chat Edits |
| 只读落盘核验 | 13:49:34.420629 | 05:49:34.420629 | bytes、差异、哈希和 AST |

## 5. 精确落盘结果与已知偏差

独立 inline Python 仅**读取目标 bytes 并调用 `ast.parse`**，没有执行目标文件，也没有修正输出。

| 指标 | 实测结果 |
|---|---|
| 基线 / 最终字节数 | **0 / 26** |
| 最终总行数 | **3** |
| 相对空基线新增 / 删除 / 净增 | **3 / 0 / 3** |
| 三行正文 | 按顺序精确匹配 `a = 1`、`b = 2`、`print(a + b)` |
| 换行 | **2 个 CRLF**，**无末尾换行** |
| Python AST | 有效，**3 个顶层语句** |
| 目标文件执行 | **未执行**，没有通过运行结果作验证 |
| final newline 要求 | **未满足**，保留真实落盘内容，未手工修正 |
| 最终 SHA-256 | `26257C60EC3F1CC7AA8333CE6A13AF6901EE73BE893624BFA787D09CEF0C8F85` |

精确内容的 JSON 转义表示：

```json
"a = 1\r\nb = 2\r\nprint(a + b)"
```

完整字节 hex：

```text
61203d20310d0a62203d20320d0a7072696e742861202b206229
```

上面的 Python 展示代码块不用于表达文件的末尾换行状态，**应以 JSON 转义、hex 和哈希为准**。26 字节与 3 行并不矛盾：最后一行不需要以换行符结束才计为一行；不能把 2 个 CRLF 当成只有 2 行。

本地保留的唯一 JSON 证据文件为 `chat_apply_evidence_20261116_1349_3f4cd0b8.json`，SHA-256 为 `20693C92AD430391364FC76E6A1D4A63FBCEF9E8CC5F673739B7A792710DD584`。原始 JSON 含本机路径，**未上传至此公开仓库**；这里仅记录证据标识，不提供不存在的下载链接。本文不是完整后端遥测追踪。

## 6. 与直接 Agent 编辑的区别及待核验项

### 6.1 官方定义与本轮实测分开

[官方 LoC 说明](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/lines-of-code-metrics#how-agent-mode-affects-loc-metrics)区分：聊天中可见代码块的建议行数，以及 Agent 在文件中的编辑；其示例将 `chat_panel_agent_mode` 的实际新增行数说明为聊天代码块 **Copy / Apply**，而 `agent_edit` 记录 Agent / Edit 模式的文件编辑。官方同时提醒，LoC 只包含客户端通过遥测报告的变更，可能不等于编辑器 diff。

这是**公开定义与待对照口径**，不是本轮报表结果，更不能由按钮名称直接推出实际服务端分类。

| 对照项 | 本次 Chat Apply | 历史 DC / CC 直接编辑 |
|---|---|---|
| 生成回合 | 仅在聊天输出代码块；UI 观察到 0 个工具调用卡 | 观察到 Read / Edit 或 Edit 工具步骤 |
| 谁触发文件写入 | 操作者随后点击代码块 Apply，选择 active editor，再 Keep | Agent 在任务回合使用文件编辑工具，按当时 UI 确认并 Keep |
| 本地受控增删 | **+3 / -0** | 两轮各 **+400 / -10** |
| 本次是否查询对应 Metrics API | **未查询** | 既有文档保存了 11 月 16 日的历史对账 |
| 本次实际 feature / 模型请求数 | **unknown / unknown** | 不以旧报表推断本次 |

### 6.2 活动日与待 API 对账

| 待对账项目 | 当前状态 |
|---|---|
| 目标用户 | `gye6666` |
| 活动日 | **UTC 2026-11-16** |
| 对应北京时间区间 | `[2026-11-16 08:00, 2026-11-17 08:00)` |
| 同日其他活动 | **未排除** |
| 本轮 Metrics API | **not queried**，不是空响应、零值或已确认漏报 |
| `loc_suggested_to_add_sum` / `loc_suggested_to_delete_sum` | **unknown** |
| `loc_added_sum` / `loc_deleted_sum` | **unknown**；本地 +3 / -0 不是 API 实测值 |
| `totals_by_feature` 中 `chat_panel_agent_mode` / `agent_edit` | 是否出现及具体数值均 **unknown** |
| generation / acceptance / requests / sessions | **unknown**；Send、Apply、Keep 的点击次数不能代替这些字段 |
| Apply 内部模型调用 | 是否发生及次数均 **unknown** |

若后续另行查询，应按该用户和 UTC 活动日保留顶层与 feature 分组，区分建议行数、实际行数及不同计数口径；日级汇总仍不能独立映射到本次单个操作。字段参考 [官方 per-user report fields](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/copilot-usage-metrics#per-user-report-fields)。**本次文档整理没有复跑实验，也没有查询 Metrics API。**

历史 [11 月 12 日 DC](DC-readme.md) 与 [11 月 13 日 CC](CC-readme.md) 均为本地 **+400 / -10**，对应用户日 API 顶层均为 **+800 / -20**，`chat_panel_agent_mode` 与 `agent_edit` 各 **+400 / -10**。这些是旧文档中已记录的对账事实；同日其他活动及逐事件对应关系仍未解决，重叠上报 / 重复归因只是待确认线索。**本次三行 Chat Apply 实验只隔离了客户端操作路径，不能证明历史翻倍根因、Keep 的因果作用，或任一 feature 必然入账 / 不入账。**
