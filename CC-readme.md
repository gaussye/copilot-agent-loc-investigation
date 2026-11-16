# GitHub Copilot CC 独立测试：新增 400 行、删除 10 行

测试日期：**2026-11-13**；API 对账更新：**2026-11-16**。CC 指 **Copilot Harness / LoC Probe 自定义 Agent**，不是 Local Harness 或 Cloud。本文件独立记录本轮结果，不覆盖 [DC-readme.md](DC-readme.md) 或历史中英文 README。

**本地结果：新增 400 行、删除 10 行、净增 390 行，单次提交并 Allow Once / Keep。11 月 16 日 API 返回该用户日新增 800 行、删除 20 行，`agent_edit` 与 `chat_panel_agent_mode` 各为 400 / 10，与前一天 DC 的分类模式相同。自定义 Agent 编辑行数并非零，但自定义 Agent 名称列表仍为空；疑似重叠归因，尚未证明同一次编辑重复计数。**

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
| API 顶层实际增删 | **800 / 20** | **800 / 20** |
| API `agent_edit` | **400 / 10** | **400 / 10** |
| API `chat_panel_agent_mode` | **400 / 10** | **400 / 10** |
| API 自定义 Agent 名称 / distinct | `[]` / `0` | **`[]` / `0`，未体现界面 LoC Probe 名称** |

已记录的版本、模型、修改规格及最终内容哈希相同，但测试跨越**两个 UTC 活动日**，使用不同文件及会话，其他当日活动未完全排除，不能作为严格同日、单变量 A/B 研究。

此前 **11 月 2 日 CC +80/-20** 的日汇总虽然匹配本地增删，却只出现在 `chat_panel_agent_mode`，没有 `agent_edit` 或自定义 Agent 名称；该次没有观察到 Keep，且 IDE / 扩展为 1.143.1 / 0.71.0。本轮 Keep 行为不同，不能提前认定这就是指标归因变化的根因。详见 [历史 CC 对账](loc-ui-test-20261102-cc-server-comparison-20261105.json)。

## 5. API 对账结果：2026-11-16

使用本地 GitHub CLI 已存凭据，确认读取账号 `gaussye`，查询企业 `gye`、目标用户 `gye6666`。**latest 与单日接口均为 HTTP 200、正常报表对象，且各含 1 条 11 月 13 日目标用户记录；只有报表日期元数据不同，其余全部活动字段一致。没有创建自动查询或提醒。**

| 项目 | 本次结果 |
|---|---|
| 目标用户 / 企业 | `gye6666` / `gye` |
| 本次 API 活动日 | **UTC 2026-11-13** |
| 对应北京时间区间 | `[2026-11-13 08:00, 2026-11-14 08:00)` |
| 本地比较基准 | **新增 400 / 删除 10 / 净增 390**，已 Keep |
| API 实际新增 / 删除 / 净增 | **800 / 20 / 780** |
| API − 本地（新增 / 删除 / 净增） | **+400 / +10 / +390** |
| API / 本地 | 新增、删除均为 **2 倍**，不代表接受率 |
| latest 查询时间 | `2026-11-16T04:58:22.482530+00:00`，北京时间 **12:58:22** |
| latest 覆盖日期 | **UTC 2026-10-19 至 2026-11-15** |
| 单日查询时间 | `2026-11-16T04:58:22.340838+00:00` |
| 两接口比对方式 | 仅移除 `report_start_day` / `report_end_day` / `report_day` 元数据，比较其余所有字段 |
| 两个完整 UTC 日参考节点 | **2026-11-16 08:00 北京时间，已过** |
| 三个完整 UTC 日保守参考节点 | **2026-11-17 08:00 北京时间，尚未到** |

### 5.1 功能分类：有编辑 LoC，但没有自定义名称归因

| feature / 汇总 | 实际新增 / 删除 | 建议新增 / 删除 | 用户交互 | generation | acceptance |
|---|---:|---:|---:|---:|---:|
| `chat_panel_agent_mode` | **400 / 10** | 400 / 10 | 2 | 1 | 1 |
| `agent_edit` | **400 / 10** | 0 / 0 | 0 | 1 | 0 |
| **顶层用户日总计** | **800 / 20** | **400 / 10** | **2** | **2** | **1** |

顶层原始字段本身就是 800 / 20，**不是分析把不同 `totals_by_*` 维度相加，也不是把建议与实际相加**。单独的 `agent_edit` 400 / 10 数值与本地相同，但日汇总无法逐事件证明它只来自本轮任务。

`totals_by_custom_agent` 为 **`[]`**，`distinct_custom_agent_use_count` 为 **0**，没有 `chat_panel_custom_mode`；界面却明确选中 LoC Probe。应区分 **“自定义 Agent 名称 / 分类未体现”** 与 **“编辑行数没统计”**：当前用户日报有非零编辑 LoC，因此本轮不能支持自定义 Agent 一概不计 LoC 的结论；也不能把所有用户日 LoC 强行归因于 LoC Probe。

### 5.2 API 组件、遥测和请求口径

| API 字段 | 实际返回 / 解释 |
|---|---|
| IDE | `vscode` / **1.145.3** |
| 插件 / 插件版本 | **`unknown` / `unknown`**，不以本地扩展 0.73.0 替换 |
| 版本采样时间 | `2026-11-13T01:16:49.363Z`，北京时间 09:16:49 |
| 模型 feature 标签 | `gpt-6.1-sol` |
| 语言 feature | chat 的 400 / 10 为 `python`；`agent_edit` 的 400 / 10 为 `unknown` |
| `totals_by_client` | `vscode`，**4 requests / 2 sessions**，不是 4 次或 2 次受控任务提交 |
| `client_telemetry_present` | `true` |
| `usage_metrics_requirements` | `met: ["vscode"]`，`unmet: []`，`unknown: []` |

官方把 client requests 解释为成功模型请求，不能等同于 IDE 提示词或任务数。本地单次受控提交与日级交互 2、请求 4、session 2 不构成矛盾；其他同日活动未完全排除，没有 SDK 请求 ID 或逐编辑事件建立一一对应。日级遥测标志也不证明本次具体请求送达。

### 5.3 与既往数据的比较及判断

同次 latest 快照显示：**11 月 2 日 CC 仍为 80 / 20，仅在 chat 分类；11 月 5 日 DC 仍为 160 / 40，chat 与 agent 各 80 / 20；11 月 12 日 DC 与本轮 11 月 13 日 CC 均为 800 / 20，chat 与 agent 各 400 / 10。** 10 月 28 日 CL 和 10 月 30 日 DL 仍为 0 / 0，不能因本轮 SDK 数据出现就认定历史缺失已解决。

这次 CC 已出现 `agent_edit`，不同于旧 CC；与昨天 DC 的已记录版本、模型、规格和 Keep 操作相同。但日期、文件、会话及旧 CC 的版本 / 修改量 / Keep 行为不同，不能认定由 custom/default、Keep 或任何单一因素导致变化。

[官方 LoC 说明](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/lines-of-code-metrics)（11 月 16 日复核）将直接文件编辑列入 `agent_edit`，chat 实际 LoC 说明为代码块 Copy / Apply。本次未手动复制或应用聊天代码块。默认和自定义两天都出现等量双分类，形成值得进一步排查的重叠报告 / 重复归因线索，**尚未证实同一次编辑被计两次，也没有证实 Keep 是触发因素**。官方同时说明遥测 LoC 不保证等于编辑器 diff，2 倍本身不是缺陷定论。

[官方 SDK 覆盖说明](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/usage-metrics-coverage)列出的 VS Code 身份缺失区间为 1.134–1.138 的 agent window；本轮普通编辑器 Chat、1.145.3 不在该区间，不应直接套用为根因。

本次已过两个完整 UTC 日参考节点，报表已覆盖且有非零数据；三个完整 UTC 日保守节点尚未到。**这些节点不保证最终定稿**，不据此宣布问题修复或数据不再变化。本次仅进行 API 对账，没有重跑 UI 编辑。

### 5.4 接口与完整原始记录

```http
GET https://api.github.com/enterprises/gye/copilot/metrics/reports/users-28-day/latest
GET https://api.github.com/enterprises/gye/copilot/metrics/reports/users-1-day?day=2026-11-13
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

完整原始 JSON 仅保留目标用户，不公开凭据、签名下载 URL 或其他用户记录。以下从 [11 月 13 日单日报表](metrics-enterprise-gye-gye6666-20261113-one-day-20261116-raw.json) 保留用户记录的所有字段和值，仅调整排版。

<details>
<summary>展开 2026-11-13 用户日完整 JSON</summary>

```json
{
  "user_id": 274194146,
  "user_login": "gye6666",
  "day": "2026-11-13",
  "enterprise_id": "590626",
  "user_initiated_interaction_count": 2,
  "code_generation_activity_count": 2,
  "code_acceptance_activity_count": 1,
  "totals_by_ide": [
    {
      "ide": "vscode",
      "user_initiated_interaction_count": 2,
      "code_generation_activity_count": 2,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 400,
      "loc_suggested_to_delete_sum": 10,
      "loc_added_sum": 800,
      "loc_deleted_sum": 20,
      "last_known_plugin_version": {
        "sampled_at": "2026-11-13T01:16:49.363Z",
        "plugin": "unknown",
        "plugin_version": "unknown"
      },
      "last_known_ide_version": {
        "sampled_at": "2026-11-13T01:16:49.363Z",
        "ide_version": "1.145.3"
      }
    }
  ],
  "totals_by_feature": [
    {
      "feature": "chat_panel_agent_mode",
      "user_initiated_interaction_count": 2,
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 400,
      "loc_suggested_to_delete_sum": 10,
      "loc_added_sum": 400,
      "loc_deleted_sum": 10
    },
    {
      "feature": "agent_edit",
      "user_initiated_interaction_count": 0,
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 400,
      "loc_deleted_sum": 10
    }
  ],
  "totals_by_language_feature": [
    {
      "language": "python",
      "feature": "chat_panel_agent_mode",
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 400,
      "loc_suggested_to_delete_sum": 10,
      "loc_added_sum": 400,
      "loc_deleted_sum": 10
    },
    {
      "language": "unknown",
      "feature": "agent_edit",
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 400,
      "loc_deleted_sum": 10
    },
    {
      "language": "unknown",
      "feature": "chat_panel_agent_mode",
      "code_generation_activity_count": 0,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 0,
      "loc_deleted_sum": 0
    }
  ],
  "totals_by_language_model": [
    {
      "language": "python",
      "model": "gpt-6.1-sol",
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 400,
      "loc_suggested_to_delete_sum": 10,
      "loc_added_sum": 400,
      "loc_deleted_sum": 10
    },
    {
      "language": "unknown",
      "model": "gpt-6.1-sol",
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 400,
      "loc_deleted_sum": 10
    }
  ],
  "totals_by_model_feature": [
    {
      "model": "gpt-6.1-sol",
      "feature": "chat_panel_agent_mode",
      "user_initiated_interaction_count": 2,
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 400,
      "loc_suggested_to_delete_sum": 10,
      "loc_added_sum": 400,
      "loc_deleted_sum": 10
    },
    {
      "model": "gpt-6.1-sol",
      "feature": "agent_edit",
      "user_initiated_interaction_count": 0,
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 400,
      "loc_deleted_sum": 10
    }
  ],
  "used_agent": true,
  "used_chat": true,
  "loc_suggested_to_add_sum": 400,
  "loc_suggested_to_delete_sum": 10,
  "loc_added_sum": 800,
  "loc_deleted_sum": 20,
  "used_cli": false,
  "used_copilot_coding_agent": false,
  "used_copilot_cloud_agent": false,
  "ai_adoption_phase": {
    "phase_number": 1,
    "phase": "Phase 1",
    "version": "v1"
  },
  "ai_credits_used": 8.95762,
  "used_copilot_app": false,
  "totals_by_skill": [],
  "totals_by_custom_agent": [],
  "totals_by_mcp": [],
  "totals_by_slash_cmd": [],
  "distinct_skill_use_count": 0,
  "distinct_custom_agent_use_count": 0,
  "distinct_mcp_use_count": 0,
  "distinct_slash_cmd_use_count": 0,
  "totals_by_plugin": [],
  "distinct_plugin_use_count": 0,
  "totals_by_client": [
    {
      "client": "vscode",
      "request_count": 4,
      "session_count": 2,
      "token_usage": {
        "avg_tokens_per_request": 9187.0,
        "output_tokens_sum": 4358,
        "prompt_tokens_sum": 32390
      },
      "totals_by_model": [
        {
          "model": "gpt-6.1-sol",
          "model_selection_mode": "user_selected",
          "is_custom_enterprise_model": false,
          "request_count": 2,
          "token_usage": {
            "avg_tokens_per_request": 17909.0,
            "output_tokens_sum": 4346,
            "prompt_tokens_sum": 31472
          }
        },
        {
          "model": "unknown",
          "model_selection_mode": "user_selected",
          "is_custom_enterprise_model": false,
          "request_count": 2,
          "token_usage": {
            "avg_tokens_per_request": 465.0,
            "output_tokens_sum": 12,
            "prompt_tokens_sum": 918
          }
        }
      ]
    }
  ],
  "used_local_byok": false,
  "client_telemetry_present": true,
  "usage_metrics_requirements": {
    "met": [
      "vscode"
    ],
    "unmet": [],
    "unknown": []
  }
}
```

</details>


## 6. 本轮证据

| 文件 | 用途 |
|---|---|
| [loc-ui-test-20261113-cc.json](loc-ui-test-20261113-cc.json) | 账号、环境、完整单次任务、Allow / Keep、实际增删及本次对账结果 |
| [11 月 16 日对账分析](loc-ui-test-20261113-cc-server-comparison-20261116.json) | 双接口一致性、功能 / custom 分类、差异及解释限制 |
| [11 月 13 日单日原始 API](metrics-enterprise-gye-gye6666-20261113-one-day-20261116-raw.json) | 唯一目标用户日完整记录及调用元数据 |
| [11 月 16 日 latest 原始 API](metrics-enterprise-gye-gye6666-20261116-followup-raw.json) | 同次目标用户历史快照及查询时间 |
| [基线快照](loc-ui-test-20261113-baseline-observed.json) | 修改前 15 行基线、预期内容与哈希 |
| [Keep 后快照](loc-ui-test-20261113-observed.json) | 实际内容、405 行、400 / 10 差异、语法及哈希 |
| [原有文件哈希](loc-ui-test-20261113-preserved-files.json) | 26 项原有 workspace 文件及 Agent 定义，结束后逐项核对未变 |
| [实际 CC 文件](copilot-loc-test/custom_copilot_20261113.py) | LoC Probe 实际修改的合成文件 |

## 7. 官方参考

- [Copilot usage metrics REST API](https://docs.github.com/en/rest/copilot/copilot-usage-metrics)
- [Lines of Code metrics](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/lines-of-code-metrics)
- [Reconciling usage metrics](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/reconciling-usage-metrics)
- [VS Code agent harnesses](https://code.visualstudio.com/docs/agents/run/agent-harnesses)
