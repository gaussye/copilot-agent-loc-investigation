# GitHub Copilot CC 独立测试：新增 400 行、删除 10 行

测试及整理日期：**2026-11-13**。CC 指 **Copilot Harness / LoC Probe 自定义 Agent**，不是 Local Harness 或 Cloud。本文件独立记录本轮结果，不覆盖 [DC-readme.md](DC-readme.md) 或历史中英文 README。

**本地结果：新增 400 行、删除 10 行、净增 390 行。界面预览、完成后的变更计数和 Keep 后实际文件一致。仅发送 1 个受控任务，点击了 Allow Once 和 Keep All Edits，没有手工补改 Agent 输出。服务端 API 对账尚未执行，未知值不记为 0 / 0。**

## 1. 账号、环境与执行记录

除注明 UTC 的日期外，以下时间均为北京时间（UTC+8）。

| 项目 | 本轮记录 |
|---|---|
| 测试标识 | `20261113-CC-400-10` |
| Harness / Agent | **Copilot / LoC Probe** |
| 测试账号 / 查询企业 | **`gye6666` / `gye`** |
| 账号核验 | Accounts 菜单显示 `gye6666`；当前运行日志最后一条登录记录为 `2026-11-12 09:13:00.851`，不是本轮新登录时间 |
| 后续 API 读取账号 | `gaussye`，与 VS Code 测试账号不同 |
| VS Code | **1.145.3 (user setup)，Windows ARM64** |
| IDE commit | `dcd3ad6e368fbe19d03f37054a40dbaf40d676f4` |
| 内置扩展 | **`GitHub.copilot-chat 0.73.0`** |
| `@github/copilot` | `1.0.103.r41379698026.g3219c66` |
| `@github/copilot-sdk` | `1.0.27.41379698026.g3219c66` |
| 版本来源 | IDE / SDK / runtime：Help > About；扩展：运行资源目录内置包元数据 |
| 模型 / Effort / Context | **GPT-6.1 Sol / Medium / 272K** |
| 模式 / 权限 | **Interactive / Manual permissions** |
| 入口及操作方式 | VS Code 普通编辑器窗口 Chat；Copilot App 内置 Computer Use |
| 会话及文件 | 新会话、独立新文件 `custom_copilot_20261113.py` |
| Agent 选择 | 发送前显式选择 **LoC Probe**，界面持续显示该名称 |
| 基线采集 | `2026-11-13T09:07:26.994654+08:00` |
| 单次任务发送 | **2026-11-13 09:10** |
| 界面完成 | **2026-11-13 09:11**，显示耗时 **1 分 7 秒** |
| 界面工具调用 | **Edit**；没有观察到终端、脚本、MCP、子 Agent、skills 或 handoffs |
| 写入确认 | 唯一目标文件，预览 **+400 / -10**；点击 **Allow Once** |
| 接受修改 | 出现 **Keep All Edits**，点击后 Keep / Undo 控件消失 |
| Keep 后采集 | `2026-11-13T09:12:58.820708+08:00` |
| SDK 请求 ID | 未在检查的扩展日志中捕获，保存为 `null`，不是“没有请求” |

首次打开时模型为 Copilot Fusion，测试发送前已选择 GPT-6.1 Sol / Medium / 272K，以匹配昨天 DC 的记录；没有使用 Fusion 提交任务。默认 Agent 的角色选择器最初未显示，使用内置 `Chat: Switch to Next Agent` 命令显露选择器后，显式选中了 LoC Probe。选择器探索及草稿调整未提交为聊天消息，最终检查正确草稿后只点击了一次 Send。

本轮没有主动升级或重启 VS Code，没有切换账号、修改企业策略、执行 customization migration 或授予整场会话写入权限。账号菜单、登录日志和 Agent 名称是客户端证据，不独立证明单次请求的服务端归因。

## 2. 自定义 Agent 定义

沿用未变化的最小定义 [loc-probe.agent.md](copilot-loc-test/.github/agents/loc-probe.agent.md)：

```markdown
---
name: LoC Probe
description: Minimal custom agent for testing Copilot file-edit telemetry.
tools: ['edit']
---

Make only the file changes explicitly requested by the user.
Use built-in file editing tools. Do not use terminals, scripts, MCP tools,
subagents, skills, or handoffs. Do not modify other files.
```

定义 SHA-256：`b1a23873e2114b8560b734efb21344a251c1d9cb4ba5db9034c1bfed22463a15`。本轮没有为了通过测试修改 Agent 定义或工具权限。

## 3. 修改规格与实际结果

新文件从 **15 行 CRLF 基线**开始，包含 10 条旧字典项 `"old_01": 1,` 至 `"old_10": 10,`，以及保持不变的 5 行结构。受控任务要求把这 10 行替换为 **400 条字面项**，从 `"new_001": 1,` 至 `"new_400": 400,`，升序、4 空格缩进、双引号键、整数值及尾随逗号；保留其余行、CRLF 和最终换行。

不能用推导式、生成器或代码块代替实际修改。任务只允许编辑该文件；采集脚本只读测量差异、计算哈希和解析语法，没有执行被测 Python 文件或生成最终结果。

| 指标 | 请求目标 | 界面计数 | Keep 后实际文件 |
|---|---:|---:|---:|
| 新增行数 | **400** | **400** | **400** |
| 删除行数 | **10** | **10** | **10** |
| 净增行数 | **390** | 400 - 10 | **390** |
| 最终总行数 | 15 → 405 | 1 个文件变更 | **405** |
| CRLF 数量 | 405 | 不以界面推断 | **405** |
| 精确匹配目标内容 | 完全一致 | 不以完成文本替代 | **完全一致** |
| Python 语法 | 有效 | 不以界面推断 | **语法分析无错误** |

**15 + 400 - 10 = 405 行**；API 对账需要分别核对新增和删除，不能把净增 390 当作新增 400。

| 证据 | 值 |
|---|---|
| 基线 SHA-256 | `25c636f128c6828ab64519734c73a4475145eee8d0bfcdc74b71d37a8e78bf29` |
| 最终 SHA-256 | `4053abdf45daa35f0d8a7d6f4fe587973f6062c1c6932b83830c1612d99bf898` |
| 原有文件保留 | **26 项哈希记录全部未变化**，含此前 DC 文件及 Agent 定义，不含本轮新文件 |
| 本地 acceptance 日志 | 采集到 **0 条**，不证明事件没有产生或上传 |

## 4. 与昨天 DC 的客户端对照

| 项目 | DC：2026-11-12 | CC：2026-11-13 |
|---|---|---|
| Harness | Copilot | Copilot |
| Agent | 内置默认 Agent | **LoC Probe 自定义 Agent** |
| IDE / 扩展 | 1.145.3 / 0.73.0 | 1.145.3 / 0.73.0 |
| SDK / runtime | About 版本见 [DC 记录](loc-ui-test-20261112-dc.json) | **重新核验，相同** |
| 模型 / Effort / Context | GPT-6.1 Sol / Medium / 272K | GPT-6.1 Sol / Medium / 272K |
| 基线及受控增删 | 15 行，+400 / -10 | 15 行，+400 / -10 |
| 实际增删 / 最终总行数 | **400 / 10 / 405** | **400 / 10 / 405** |
| 写入确认 / Keep | Allow Once / Keep All Edits | Allow Once / Keep All Edits |
| 服务端对账 | 待查询 | 待查询 |

已记录的版本、模型、修改规格及最终内容哈希相同，但测试跨越**两个 UTC 活动日**，使用不同文件及会话，其他当日活动未完全排除，不能作为严格同日、单变量 A/B 研究。

此前 **11 月 2 日 CC +80/-20** 的日汇总虽然匹配本地增删，却只出现在 `chat_panel_agent_mode`，没有 `agent_edit` 或自定义 Agent 名称；该次没有观察到 Keep，且 IDE / 扩展为 1.143.1 / 0.71.0。本轮 Keep 行为不同，不能提前认定这就是指标归因变化的根因。详见 [历史 CC 对账](loc-ui-test-20261102-cc-server-comparison-20261105.json)。

## 5. API 对账状态

**本轮尚未查询 API。服务端新增 / 删除均未知（`null`），不是 0 / 0；不把旧 CC 或昨天 DC 数据当成本次数据。没有创建自动查询或提醒。**

| 项目 | 对账口径 |
|---|---|
| 目标用户 / 企业 | `gye6666` / `gye` |
| 本次 API 活动日 | **UTC 2026-11-13** |
| 对应北京时间区间 | `[2026-11-13 08:00, 2026-11-14 08:00)` |
| 本地比较基准 | **新增 400 / 删除 10 / 净增 390**，已 Keep |
| 两个完整 UTC 日窗口结束 | **2026-11-16 08:00 北京时间** |
| 三个完整 UTC 日保守复查节点 | **2026-11-17 08:00 北京时间** |

后续读取 `users-28-day/latest` 和 `users-1-day?day=2026-11-13`，使用 API 版本 `2026-03-10`。先检查 HTTP 状态、响应类型、报表覆盖日和目标用户记录；HTTP 200 空字符串、未覆盖或无记录不能都解释为零 LoC。以上节点是处理窗口参考，不保证报表更新或最终定稿。

对账应保留完整目标用户原始 JSON，同时查看顶层 `loc_added_sum` / `loc_deleted_sum`、`agent_edit`、`chat_panel_agent_mode`、`chat_panel_custom_mode`、自定义 Agent 维度、生成 / 接受活动及 API 返回的真实组件版本。不同 `totals_by_*` 是统计视角，不能叠加重复计数；Agent 名称或交互数也不能替代编辑行数。

日汇总不能逐请求归因。若出现多 feature 重叠或高于本地 400 / 10 的总量，应保留异常证据，但不能仅凭汇总宣布同一次编辑重复计数。不得公开凭据、签名下载 URL、其他用户记录或完整客户端日志。

## 6. 本轮证据

| 文件 | 用途 |
|---|---|
| [loc-ui-test-20261113-cc.json](loc-ui-test-20261113-cc.json) | 账号、环境、完整单次任务、Allow / Keep、实际增删及待对账状态 |
| [基线快照](loc-ui-test-20261113-baseline-observed.json) | 修改前 15 行基线、预期内容与哈希 |
| [Keep 后快照](loc-ui-test-20261113-observed.json) | 实际内容、405 行、400 / 10 差异、语法及哈希 |
| [原有文件哈希](loc-ui-test-20261113-preserved-files.json) | 26 项原有 workspace 文件及 Agent 定义，结束后逐项核对未变 |
| [实际 CC 文件](copilot-loc-test/custom_copilot_20261113.py) | LoC Probe 实际修改的合成文件 |

## 7. 官方参考

- [Copilot usage metrics REST API](https://docs.github.com/en/rest/copilot/copilot-usage-metrics)
- [Lines of Code metrics](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/lines-of-code-metrics)
- [Reconciling usage metrics](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/reconciling-usage-metrics)
- [VS Code agent harnesses](https://code.visualstudio.com/docs/agents/run/agent-harnesses)
