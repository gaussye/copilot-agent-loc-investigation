# GitHub Copilot 本地测试与 API 数据对照

[English version](README.en.md)

整理日期：2026-10-28；最后更新：2026-11-06。本文使用已保存的测试证据及 API 快照，各次查询时间分别记录。

记录范围：第 1 至 8 节为 **10 月 26 日四组测试**；第 9 节为 **10 月 28 日单独 CL 重测**；第 10 节为 **10 月 30 日单独 DL 测试**；第 11 节为 **11 月 2 日单独 CC 测试**；第 12 节为 **11 月 5 日单独 DC 测试**。各日的客户端版本和数据分别记录，不合并对账。

**10 月 26 日对照结果：四次本地操作实际新增 320 行、删除 80 行；API 对该用户 UTC 10 月 26 日的统计为新增 160 行、删除 40 行。已观察到汇总数值差异，但无法确认 API 中的行数具体来自哪次操作。**

**11 月 5 日最新对账：11 月 2 日 CC（Copilot Harness / 自定义 LoC Probe）的用户日汇总实际 LoC 为 +80 / -20，与本地修改量一致，但归入 `chat_panel_agent_mode`，没有自定义 Agent 归因。相同最新快照中，10 月 28 日 CL 和 10 月 30 日 DL 的实际 LoC 仍均为 0 / 0。详见第 11 节。**

**11 月 6 日 14:04 更新：11 月 5 日 DC 的报表已可获取，用户日实际 LoC 为 +160 / -40；`chat_panel_agent_mode` 和 `agent_edit` 各为 +80 / -20。本地仅有一项 +80 / -20 的受控编辑，日汇总多出 +80 / -20。可能的重叠上报或重复归因尚未确认，同日其他活动未完全排除。详见第 12 节。**

## 1. 账号、客户端版本与统计日期

| 项目 | 本轮记录 |
|---|---|
| 测试账号 / API 统计对象 | `gye6666` |
| 查询企业 slug | `gye` |
| API 读取账号 | `gaussye`，使用已有本地 GitHub CLI 登录；与测试账号不同 |
| VS Code | **1.142.0 ARM64** |
| GitHub Copilot Chat 扩展 | **0.70.0**，本地确认的版本 |
| 模型 | GPT-6 Luna |
| Thinking / Context | Medium / 272K |
| 测试入口 | VS Code 普通编辑器窗口的 Chat，不是专用 Agents 窗口 |
| Harness | Local 和 Copilot 分别测试；Copilot 在这里指 SDK / Agent Host，不是 Cloud |
| 自定义 Agent | `LoC Probe` |
| 操作方式 | GitHub Copilot App 内置 Computer Use |
| API 版本请求头 | `X-GitHub-Api-Version: 2026-03-10` |
| API 活动日期 | `2026-10-26`，按 UTC 日汇总 |
| 对应北京时间区间 | `[2026-10-26 08:00, 2026-10-27 08:00)` |

测试前已通过 VS Code Accounts 菜单和扩展登录日志确认客户端账号为 `gye6666`。这不等于已逐请求验证 SDK 服务端身份或遥测送达。

API 返回的 IDE 版本是 `1.142.0`，但插件名称和版本均为 `unknown`。**本地扩展版本 `0.70.0` 和 API 插件版本 `unknown` 应分别保留，不能互相替代。**

## 2. 本地四次操作

以下时间均为北京时间，实际执行顺序为 **CL -> CC -> DC -> DL**。每次使用新的 Chat 和独立文件，没有手工补改 Agent 输出。

| 测试 | 发送时间 | Harness | Agent | 实际新增 | 实际删除 | 接受 / 确认操作 |
|---|---|---|---|---:|---:|---|
| CL | 2026-10-26 09:26 | Local | 自定义 LoC Probe | 80 | 20 | 已点击 Keep All Edits |
| CC | 2026-10-26 09:30 | Copilot | 自定义 LoC Probe | 80 | 20 | 点击单次文件写入 Allow，无 Keep |
| DC | 2026-10-26 09:33 | Copilot | 默认 Agent | 80 | 20 | 未出现需要点击的写入确认或 Keep |
| DL | 2026-10-26 09:35 | Local | 默认 Agent | 80 | 20 | 已点击 Keep All Edits |
| **本地合计** | | | | **320** | **80** | **净增 240 行** |

### 文件与本地请求标识

文件均位于当前目录下的 `copilot-loc-test` 工作区。

| 测试 | 文件 | 本地捕获的请求 ID |
|---|---|---|
| CL | `custom_local_20261026.py` | `request_caf3b645-d531-4dd1-8140-ba1e12b6912c` |
| CC | `custom_copilot_20261026.py` | 未捕获，不表示请求不存在 |
| DC | `default_copilot_20261026.py` | 未捕获，不表示请求不存在 |
| DL | `default_local_20261026.py` | `request_59e2ebe8-c4a5-4a19-8792-3401306f687e` |

### 本地计数依据

四个文件的起始内容相同，均为 25 行、CRLF 换行；每次将 20 条 `old_01` 至 `old_20` 字典项替换为 80 条 `new_01` 至 `new_80` 字典项，其余内容不变。

四次界面计数均与实际落盘差异一致：每个文件最终 85 行、CRLF、语法有效，内容与预期完全一致。新增和删除分别统计，不能把净增 60 行当作新增行数。

| 证据 | 值 |
|---|---|
| 基线采集时间 | `2026-10-26T09:24:58.431439+08:00` |
| 最终采集时间 | `2026-10-26T09:36:03.208235+08:00` |
| 每个文件起始 SHA-256 | `6298c00f97ffc8ecc7c34ba09dc14c021d83cf0ab507d3debd1be2d1d41e6bfe` |
| 每个文件最终 SHA-256 | `4c3675924615acad6d2074fd14baaae5114afa2fb89b8bd914f97c2ddc6c4fdd` |

本轮未修改自定义 Agent 定义，未执行 customization migration；10 月 22、23 日的八个历史测试文件保持不变。

## 3. API 接口与查询时间

请求主机：`https://api.github.com`。

### 最近 28 天用户报表

```http
GET /enterprises/gye/copilot/metrics/reports/users-28-day/latest
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

### 指定日期用户报表

```http
GET /enterprises/gye/copilot/metrics/reports/users-1-day?day=2026-10-26
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

请求使用已有授权，认证信息不写入本文。REST 响应先提供报表下载链接，随后下载 NDJSON 报表并筛选 `user_login == "gye6666"`。

| 查询时间（北京时间） | 接口 | HTTP | 报表日期 | 是否覆盖本轮测试 |
|---|---|---:|---|---|
| 2026-10-26 09:24:28 | `users-28-day/latest` | 200 | 2026-09-27 至 2026-10-24 | 否，测试前基线 |
| 2026-10-28 08:45:20 | `users-28-day/latest` | 200 | 2026-09-29 至 2026-10-26 | 是 |
| 2026-10-28 08:49:03 | `users-1-day?day=2026-10-26` | 200 | 2026-10-26 | 是 |

单日报表中，该用户当天只有 **1 条用户日记录**。除 28 天报表附加的报告起止日期字段外，该记录与 08:45 的 28 天报表中对应日期记录一致。

## 4. API 返回数据

以下采用“指标 / API 结果”格式，数据来自 2026-10-28 08:49:03 的单日报表。

| 指标 | API 结果 |
|---|---|
| 默认 Agent：`chat_panel_agent_mode` | 1 次交互 |
| Custom Agent：`chat_panel_custom_mode` | 1 次交互 |
| IDE / 插件 | VS Code 1.142.0 / 插件名称和版本均为 `unknown` |
| `loc_added_sum` / `loc_deleted_sum` | **160 / 40** |
| `agent_edit` 分类 | **已出现**，代码生成活动 2 次，新增 160 行、删除 40 行 |

### 按 feature 返回的分组记录

这些是用户当天的功能分组统计，不是单次请求明细。

| `feature` | 交互次数 | 代码生成次数 | 代码接受次数 | 建议新增 / 删除 | 实际新增 / 删除 |
|---|---:|---:|---:|---:|---:|
| `chat_panel_agent_mode` | 1 | 0 | 0 | 0 / 0 | 0 / 0 |
| `chat_panel_custom_mode` | 1 | 0 | 0 | 0 / 0 | 0 / 0 |
| `agent_edit` | 0 | 2 | 0 | 0 / 0 | 160 / 40 |

其他原始字段：`totals_by_custom_agent` 为空，`distinct_custom_agent_use_count` 为 0；`totals_by_client` 中 `vscode` 的 `request_count` 为 18、`session_count` 为 4。后两项同样是汇总，不能作为四次受控测试各自入账的证明。

## 5. 对照结论与限制

| 实际编辑 LoC | 本地四次测试 | API 用户日统计 | 本地减 API |
|---|---:|---:|---:|
| 新增 | 320 | 160 | 160 |
| 删除 | 80 | 40 | 40 |
| 净增 | 240 | 120 | 120 |

**能确认的事实：** 本次快照已包含正确账号、正确活动日期的数据，但 API 中新增和删除行数各为受控本地记录的 50%。默认模式和自定义模式均有交互记录，实际编辑行数统一出现在 `agent_edit`。

**不能据此作出的判断：**

- 不能把 160 / 40 确定对应为 CL + DL。它们的合计虽为 160 / 40，但 CC + DC 的合计也相同；原始响应没有 Harness、请求 ID、会话 ID 或文件名可逐次关联。
- `chat_panel_custom_mode` 下的实际 LoC 为 0，不等于自定义 Agent 的文件编辑一定漏报；文件编辑 LoC 在另一分组 `agent_edit` 中。
- `code_acceptance_activity_count` 为 0，不等于实际编辑 LoC 为 0，也不是本地 Keep 点击次数。
- 没有 Keep 步骤不能单独证明 Copilot Harness 不统计 LoC。
- 本地采集器没有捕获 acceptance 遥测事件，不能由此断定事件未产生或未上传。
- 当前观察是该 API 快照与本地记录的差异，尚未证明永久丢失、具体漏报组别或根因；同日其他活动和后续报表修订也未排除。

### 逐请求明细接口的尝试

2026-10-28 08:50:19 另外请求过：

```http
GET /enterprises/gye/copilot/usage-records
X-GitHub-Api-Version: 2026-03-10
```

该接口返回 **404 `Not Found`**，未取得会话明细；不能只凭 404 确定原因。它不是此前一直成功调用的 Metrics 报表接口，不应把这次 404 描述为原 Metrics 接口失效。没有修改企业策略或权限。

## 6. API 原始返回数据

下面是 **单日报表下载内容中 `gye6666` 的完整一条原始用户日记录**，仅做 JSON 格式化，保留全部字段和值；不是手工拼装的 REST 下载链接响应，也不是四条请求事件。

原始目标用户快照：[metrics-enterprise-gye-gye6666-20261026-one-day-20261028-0848-raw.json](metrics-enterprise-gye-gye6666-20261026-one-day-20261028-0848-raw.json)。

快照外层的 `queried_at_utc`、`endpoint`、`local_api_account`、`report_user`、`http_status`、`matching_user_records` 是本地采集程序添加的记录包装；下面代码块只包含报表本身的一条用户记录。未保存或展示签名下载 URL、令牌及其他用户的数据。

<details>
<summary>展开完整原始用户日记录 JSON</summary>

```json
{
  "user_id": 274194146,
  "user_login": "gye6666",
  "day": "2026-10-26",
  "enterprise_id": "590626",
  "user_initiated_interaction_count": 2,
  "code_generation_activity_count": 2,
  "code_acceptance_activity_count": 0,
  "totals_by_ide": [
    {
      "ide": "vscode",
      "user_initiated_interaction_count": 2,
      "code_generation_activity_count": 2,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 160,
      "loc_deleted_sum": 40,
      "last_known_plugin_version": {
        "sampled_at": "2026-10-26T01:39:13.936Z",
        "plugin": "unknown",
        "plugin_version": "unknown"
      },
      "last_known_ide_version": {
        "sampled_at": "2026-10-26T01:39:13.936Z",
        "ide_version": "1.142.0"
      }
    }
  ],
  "totals_by_feature": [
    {
      "feature": "agent_edit",
      "user_initiated_interaction_count": 0,
      "code_generation_activity_count": 2,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 160,
      "loc_deleted_sum": 40
    },
    {
      "feature": "chat_panel_agent_mode",
      "user_initiated_interaction_count": 1,
      "code_generation_activity_count": 0,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 0,
      "loc_deleted_sum": 0
    },
    {
      "feature": "chat_panel_custom_mode",
      "user_initiated_interaction_count": 1,
      "code_generation_activity_count": 0,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 0,
      "loc_deleted_sum": 0
    }
  ],
  "totals_by_language_feature": [
    {
      "language": "unknown",
      "feature": "agent_edit",
      "code_generation_activity_count": 2,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 160,
      "loc_deleted_sum": 40
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
    },
    {
      "language": "unknown",
      "feature": "chat_panel_custom_mode",
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
      "language": "unknown",
      "model": "gpt-6-luna",
      "code_generation_activity_count": 2,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 160,
      "loc_deleted_sum": 40
    }
  ],
  "totals_by_model_feature": [
    {
      "model": "gpt-6-luna",
      "feature": "agent_edit",
      "user_initiated_interaction_count": 0,
      "code_generation_activity_count": 2,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 160,
      "loc_deleted_sum": 40
    },
    {
      "model": "gpt-6-luna",
      "feature": "chat_panel_agent_mode",
      "user_initiated_interaction_count": 1,
      "code_generation_activity_count": 0,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 0,
      "loc_deleted_sum": 0
    },
    {
      "model": "gpt-6-luna",
      "feature": "chat_panel_custom_mode",
      "user_initiated_interaction_count": 1,
      "code_generation_activity_count": 0,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 0,
      "loc_deleted_sum": 0
    }
  ],
  "used_agent": true,
  "used_chat": true,
  "loc_suggested_to_add_sum": 0,
  "loc_suggested_to_delete_sum": 0,
  "loc_added_sum": 160,
  "loc_deleted_sum": 40,
  "used_cli": false,
  "used_copilot_coding_agent": false,
  "used_copilot_cloud_agent": false,
  "ai_adoption_phase": {
    "phase_number": 1,
    "phase": "Phase 1",
    "version": "v1"
  },
  "ai_credits_used": 1.8965515000000002,
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
      "request_count": 18,
      "session_count": 4,
      "token_usage": {
        "avg_tokens_per_request": 16558.72,
        "output_tokens_sum": 5822,
        "prompt_tokens_sum": 292235
      },
      "totals_by_model": [
        {
          "model": "gpt-6-luna",
          "model_selection_mode": "user_selected",
          "request_count": 10,
          "token_usage": {
            "avg_tokens_per_request": 29170.1,
            "output_tokens_sum": 5680,
            "prompt_tokens_sum": 286021
          }
        },
        {
          "model": "unknown",
          "model_selection_mode": "user_selected",
          "request_count": 8,
          "token_usage": {
            "avg_tokens_per_request": 794.5,
            "output_tokens_sum": 142,
            "prompt_tokens_sum": 6214
          }
        }
      ]
    }
  ]
}
```

</details>

## 7. 原始证据索引

| 文件 | 用途 |
|---|---|
| [loc-ui-test-20261026.json](loc-ui-test-20261026.json) | 本轮主记录：账号、环境、各次操作、确认行为和对账状态 |
| [loc-ui-test-20261026-baseline-observed.json](loc-ui-test-20261026-baseline-observed.json) | 四个文件修改前基线 |
| [loc-ui-test-20261026-observed.json](loc-ui-test-20261026-observed.json) | 最终文件内容、差异、行数、换行和哈希 |
| [metrics-enterprise-gye-gye6666-20261026-baseline-raw.json](metrics-enterprise-gye-gye6666-20261026-baseline-raw.json) | 10 月 26 日测试前的 API 基线，不覆盖测试日 |
| [metrics-enterprise-gye-gye6666-20261028-0845-refresh-raw.json](metrics-enterprise-gye-gye6666-20261028-0845-refresh-raw.json) | 10 月 28 日 08:45 的 28 天报表，仅保留目标账号全部原始字段 |
| [metrics-enterprise-gye-gye6666-20261026-one-day-20261028-0848-raw.json](metrics-enterprise-gye-gye6666-20261026-one-day-20261028-0848-raw.json) | 10 月 28 日 08:49 的指定单日报表原始目标用户记录；文件名中的 0848 是调用准备标识，实际查询时间以文件字段为准 |
| [loc-ui-test-20261026-server-comparison-20261028.json](loc-ui-test-20261026-server-comparison-20261028.json) | 本地 / 服务端数值对照及归因限制 |
| [loc-ui-test-20261026-per-request-check-20261028.json](loc-ui-test-20261026-per-request-check-20261028.json) | 四次操作的逐请求核对状态；服务端未知值为 null，不是 0 |
| [copilot-usage-records-gye-probe-20261028-0850.json](copilot-usage-records-gye-probe-20261028-0850.json) | 另一 Usage Records 接口的 404 记录 |

## 8. 官方参考

- [Copilot usage metrics REST API](https://docs.github.com/en/enterprise-cloud@latest/rest/copilot/copilot-usage-metrics?apiVersion=2026-03-10)
- [Lines of code metrics](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/lines-of-code-metrics)
- [VS Code agent harnesses](https://code.visualstudio.com/docs/agents/run/agent-harnesses)
- [Copilot agent session streaming / Usage Records 公告](https://github.blog/changelog/2026-07-02-copilot-agent-session-streaming-is-now-in-public-preview/)

## 9. 2026-10-28 单独 CL 重测

**11 月 2 日复查补充：最新报表中 28 日仍为 Custom 模式 5 次交互、实际 LoC 0 / 0、没有 `agent_edit`；已超过此前记录的三日保守复查节点。30 日默认 Agent 隔离测试也出现编辑 LoC 为零，见第 10 节的同次快照对照。以下保留 10 月 30 日查询结果及原始 JSON，不覆盖历史快照。**

### 本地结果

**CL（Local Harness / 自定义 LoC Probe）已完成一次成功编辑并点击 Keep All Edits：新增 80 行、删除 20 行，净增 60 行。**

| 项目 | 本轮记录 |
|---|---|
| 测试账号 / 后续查询企业 | `gye6666` / `gye` |
| 成功重试的测试标识 | `20261028-CL-R2` |
| 发送时间（北京时间） | 2026-10-28 09:43；扩展请求日志为 `09:43:50.128` |
| Harness / Agent | Local / LoC Probe |
| VS Code / Copilot Chat | **1.143.1 / 0.71.0**，重启后的安装元数据 |
| 模型 / Effort / Context | GPT-6 Luna / Medium / 272K |
| 文件 | `copilot-loc-test\custom_local_20261028.py` |
| 界面计数 | +80 / -20 |
| 实际落盘差异 | +80 / -20 |
| 文件状态 | 25 行变为 85 行，CRLF，语法有效，内容与预期完全一致 |
| 接受操作 | 已点击 Keep All Edits，Keep / Undo 控件随后消失 |
| 成功请求 ID | `request_a3f28585-fde1-4a2f-9281-69300e8e66b3` |
| 最终证据采集时间 | `2026-10-28T09:45:44.194519+08:00` |
| 最终文件 SHA-256 | `4c3675924615acad6d2074fd14baaae5114afa2fb89b8bd914f97c2ddc6c4fdd` |

### API 查询时间线与观察口径

测试前于 **2026-10-28 09:30:36 北京时间**调用 `users-28-day/latest`，HTTP 200，报表范围为 **2026-09-29 至 2026-10-26**，尚不覆盖本轮 **UTC 2026-10-28**。

查询企业 `gye`、用户 `gye6666`、UTC 日期 `2026-10-28`。该 UTC 日对应北京时间 `[2026-10-28 08:00, 2026-10-29 08:00)`。

```http
GET https://api.github.com/enterprises/gye/copilot/metrics/reports/users-28-day/latest
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

| 查询时间（北京时间） | HTTP | 报表范围 | UTC 10 月 28 日覆盖情况 |
|---|---:|---|---|
| 2026-10-28 09:30:36 | 200 | 2026-09-29 至 2026-10-26 | 未覆盖，测试前基线 |
| 2026-10-29 10:36:14 | 200 | 2026-09-30 至 2026-10-27 | 未覆盖，不能将缺少记录解释为 0 行 |
| **2026-10-30 08:53:48** | **200** | **2026-10-01 至 2026-10-28** | **已覆盖，目标用户当天有 1 条记录** |

### 2026-10-30 API 返回结果

**最新快照中已出现 10 月 28 日的自定义模式交互，但未出现 `agent_edit`，实际编辑 LoC 为 0 / 0。**

| 指标 | UTC 2026-10-28 的 API 结果 |
|---|---|
| 默认 Agent：`chat_panel_agent_mode` | 未出现该分类 |
| Custom Agent：`chat_panel_custom_mode` | **5 次交互** |
| IDE / 插件 | **VS Code 1.143.1 / Copilot Chat 0.71.0** |
| 模型 | `gpt-6-luna` |
| 代码生成 / 接受活动次数 | **0 / 0** |
| 建议新增 / 删除行数 | 0 / 0 |
| `loc_added_sum` / `loc_deleted_sum` | **0 / 0** |
| `agent_edit` 分类 | **未出现** |
| `used_agent` / `used_chat` | `false` / `true`，保留原始值，不能代替本地执行事实 |
| `totals_by_custom_agent` | `[]` |
| `totals_by_client` 中的 `vscode` | `request_count = 11`，`session_count = 1`；是另一个统计维度，不能逐请求关联 LoC |

版本采样时间为 `2026-10-28T01:45:25.217Z`，即北京时间 09:45:25.217。它是版本元数据采样时间，不是文件编辑事件的时间或编辑行数已入账的证明。

### API 原始用户日记录

以下是 **2026-10-30 08:53:48 北京时间查询返回的 `gye6666` 在 UTC 2026-10-28 的完整用户日记录**，仅做 JSON 格式化，保留全部字段和值，不是只摘录 `totals_by_feature`。数据来自下载的 NDJSON 报表，不是 REST 接口返回下载链接的响应。

完整采集快照见 [10 月 30 日 API 快照](metrics-enterprise-gye-gye6666-20261030-0853-refresh-raw.json)，其中还包含该账号其他日期的记录；下面只展示与本轮测试对应的 28 日记录。采集程序添加的外层查询元数据不在下面代码块中。

```json
{
  "report_start_day": "2026-10-01",
  "report_end_day": "2026-10-28",
  "day": "2026-10-28",
  "enterprise_id": "590626",
  "user_id": 274194146,
  "user_login": "gye6666",
  "user_initiated_interaction_count": 5,
  "code_generation_activity_count": 0,
  "code_acceptance_activity_count": 0,
  "totals_by_ide": [
    {
      "ide": "vscode",
      "user_initiated_interaction_count": 5,
      "code_generation_activity_count": 0,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 0,
      "loc_deleted_sum": 0,
      "last_known_plugin_version": {
        "sampled_at": "2026-10-28T01:45:25.217Z",
        "plugin": "copilot-chat",
        "plugin_version": "0.71.0"
      },
      "last_known_ide_version": {
        "sampled_at": "2026-10-28T01:45:25.217Z",
        "ide_version": "1.143.1"
      }
    }
  ],
  "totals_by_feature": [
    {
      "feature": "chat_panel_custom_mode",
      "user_initiated_interaction_count": 5,
      "code_generation_activity_count": 0,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 0,
      "loc_deleted_sum": 0
    }
  ],
  "totals_by_language_feature": [
    {
      "language": "unknown",
      "feature": "chat_panel_custom_mode",
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
      "language": "unknown",
      "model": "gpt-6-luna",
      "code_generation_activity_count": 0,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 0,
      "loc_deleted_sum": 0
    }
  ],
  "totals_by_model_feature": [
    {
      "model": "gpt-6-luna",
      "feature": "chat_panel_custom_mode",
      "user_initiated_interaction_count": 5,
      "code_generation_activity_count": 0,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 0,
      "loc_deleted_sum": 0
    }
  ],
  "used_agent": false,
  "used_chat": true,
  "loc_suggested_to_add_sum": 0,
  "loc_suggested_to_delete_sum": 0,
  "loc_added_sum": 0,
  "loc_deleted_sum": 0,
  "used_cli": false,
  "used_copilot_coding_agent": false,
  "used_copilot_cloud_agent": false,
  "ai_adoption_phase": {
    "phase_number": 1,
    "phase": "Phase 1",
    "version": "v1"
  },
  "ai_credits_used": 0.6303314999999999,
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
      "request_count": 11,
      "session_count": 1,
      "token_usage": {
        "avg_tokens_per_request": 7512.55,
        "output_tokens_sum": 6248,
        "prompt_tokens_sum": 76390
      },
      "totals_by_model": [
        {
          "model": "gpt-6-luna",
          "model_selection_mode": "user_selected",
          "request_count": 7,
          "token_usage": {
            "avg_tokens_per_request": 11367.57,
            "output_tokens_sum": 6059,
            "prompt_tokens_sum": 73514
          }
        },
        {
          "model": "unknown",
          "model_selection_mode": "user_selected",
          "request_count": 4,
          "token_usage": {
            "avg_tokens_per_request": 766.25,
            "output_tokens_sum": 189,
            "prompt_tokens_sum": 2876
          }
        }
      ]
    }
  ]
}
```

### 本地与 API 对照及当前结论

| 实际编辑 LoC | 成功 CL 测试的本地记录 | API 用户日统计 | 本地减 API |
|---|---:|---:|---:|
| 新增 | 80 | 0 | 80 |
| 删除 | 20 | 0 | 20 |
| 净增 | 60 | 0 | 60 |

本地成功编辑及 Keep 已确认；API 中也有自定义模式交互，但本次快照尚未体现该编辑的 LoC。**不能将此解释为最后一次操作失败，也不能从聊天分类 LoC 为 0 单独推断漏报：关键在于整个用户日记录及所有已返回分组的实际 LoC 都为 0，且没有 `agent_edit`。**

编辑执行与指标统计是不同链路：文件落盘不等于编辑遥测已成功产生、发送、接收、分类并聚合到报表。现有日志中可见成功请求的 `apply_patch` 元数据，但未捕获本轮 acceptance 事件；未捕获日志不能证明事件未产生或未上传。

当前根因未确认，仍需区分异步处理未完成，或编辑遥测产生、传输、分类 / 聚合环节的问题。不能据此认定数据永久丢失、客户端升级引入回归，或自定义 Agent 不支持文件编辑统计。

本次成功受控编辑的本地量是 **+80 / -20**，但当天还观察到失败、重试和问候活动。**5 次交互不等于 5 次成功编辑**，不能要求日汇总的交互 / 生成次数必然为 1，也不能排除同日其他活动。API 没有请求级标识，尚未逐请求验证成功编辑的遥测送达。

### 处理窗口与后续复查

报表覆盖活动日不代表该日所有指标都已处理完成。截至 10 月 30 日 08:53 的本次快照，仍未过完下述两个完整 UTC 日窗口：

| 官方说明 | UTC 2026-10-28 对应的北京时间观察节点 |
|---|---|
| [Data freshness](https://docs.github.com/en/copilot/concepts/billing-and-usage/copilot-usage-metrics/copilot-metrics#data-freshness)：活动日结束后两个完整 UTC 日 | **2026-10-31 08:00** |
| [Delayed telemetry](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/reconciling-usage-metrics#delayed-telemetry)：通常在三个完整 UTC 日内完成，建议该窗口后重新导出 | **2026-11-01 08:00**，更保守的复查节点 |

以上是官方文档的观察口径，不是对本轮数据一定回填或届时更新的保证。若窗口后仍无 `agent_edit` 和实际编辑 LoC，应将成功请求 ID、本地差异、Keep 记录、客户端版本及后续 API 原始快照一起用于排查。

### 本轮证据

| 本轮证据 | 用途 |
|---|---|
| [loc-ui-test-20261028-cl.json](loc-ui-test-20261028-cl.json) | 首次失败、重启版本变化、成功重试、请求 ID 和 Keep 记录 |
| [loc-ui-test-20261028-baseline-observed.json](loc-ui-test-20261028-baseline-observed.json) | 新文件修改前基线 |
| [loc-ui-test-20261028-observed.json](loc-ui-test-20261028-observed.json) | 点击 Keep 后的实际内容、差异、行数、换行和哈希 |
| [metrics-enterprise-gye-gye6666-20261028-cl-baseline-raw.json](metrics-enterprise-gye-gye6666-20261028-cl-baseline-raw.json) | 09:30 查询的 API 原始目标用户快照，尚不包含 UTC 10 月 28 日 |
| [metrics-enterprise-gye-gye6666-20261029-1035-refresh-raw.json](metrics-enterprise-gye-gye6666-20261029-1035-refresh-raw.json) | 10 月 29 日 10:36 查询，报表仅至 27 日；实际查询时间以文件字段为准 |
| [metrics-enterprise-gye-gye6666-20261030-0853-refresh-raw.json](metrics-enterprise-gye-gye6666-20261030-0853-refresh-raw.json) | 10 月 30 日 08:53 查询，已覆盖 28 日，保留目标账号全部原始字段 |
| [loc-ui-test-20261028-cl-server-comparison-20261030.json](loc-ui-test-20261028-cl-server-comparison-20261030.json) | 本地 +80 / -20 与 API 0 / 0 的对账、功能分组状态及解释限制 |

## 10. 2026-10-30 单独 DL 测试

**11 月 2 日最新对账：30 日默认 Agent 和 28 日自定义 Agent 的本地编辑均为 +80 / -20，但两日 API 实际 LoC 仍均为 0 / 0，且都没有 `agent_edit`。当前缺失现象不是自定义模式独有，不能把 26 日少一半直接归因于 Custom Agent 被排除。根因尚未确认。**

### 本地结果

**DL（Local Harness / 默认 Agent）已完成一次文件编辑并点击 Keep All Edits：新增 80 行、删除 20 行，净增 60 行。** 本轮只运行默认 Agent，不混入新的自定义 Agent 或 Copilot Harness 受控测试，以便后续按独立 UTC 日期核对。

| 项目 | 本轮记录 |
|---|---|
| 测试账号 / 后续查询企业 | `gye6666` / `gye` |
| 测试标识 | `20261030-DL` |
| 发送时间（北京时间） | 2026-10-30 10:04；请求日志为 `10:04:58.777` |
| Harness / Agent | **Local / 默认 Agent** |
| VS Code / Copilot Chat | **1.143.1 / 0.71.0** |
| 模型 / Effort / Context | GPT-6 Luna / Medium / 272K |
| 测试入口 | VS Code 普通编辑器窗口的 Chat，使用新的会话 |
| 文件 | `copilot-loc-test\default_local_20261030.py`，独立新文件 |
| 编辑工具 | 内置 `apply_patch` |
| 界面计数 / 实际落盘差异 | 均为 **+80 / -20** |
| 文件状态 | 25 行变为 85 行，CRLF，语法有效，内容与预期完全一致 |
| 接受操作 | 已点击 Keep All Edits，Keep / Undo 控件随后消失 |
| 请求 ID | `request_9ae3199a-a208-4ab2-b9ad-217b59a06f81` |
| 基线采集时间 | `2026-10-30T10:01:25.008762+08:00` |
| 最终证据采集时间 | `2026-10-30T10:05:51.965708+08:00` |
| 最终文件 SHA-256 | `4c3675924615acad6d2074fd14baaae5114afa2fb89b8bd914f97c2ddc6c4fdd` |
| 自动化提交的测试任务次数 | **1 次**，未发送重复测试请求 |

本轮客户端版本、模型及 Local Harness 与 28 日成功的 CL 测试相同；区别是本轮选择默认 Agent，并使用独立 UTC 日期和文件。两次均使用相同的 25 行基线，替换 20 条旧字典项为 80 条新项。没有手工修正 Agent 输出，13 个历史测试文件及自定义 Agent 定义均保持不变。

### API 查询与统计口径

10 月 30 日测试时，已有 API 快照仅覆盖到 UTC 10 月 28 日，尚不能核对本轮结果。**2026-11-02 11:00:19 北京时间**已重新获取最新报表并完成本次快照对账：

```http
GET https://api.github.com/enterprises/gye/copilot/metrics/reports/users-28-day/latest
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

| 查询项 | 本次结果 |
|---|---|
| 查询企业 / 目标用户 | `gye` / `gye6666` |
| API 读取账号 | `gaussye`，使用已有本地 GitHub CLI 登录 |
| 查询时间（UTC） | `2026-11-02T03:00:19.639276+00:00` |
| HTTP 状态 | **200** |
| 报表范围 | **2026-10-04 至 2026-10-31** |
| 是否覆盖测试日 | **是，UTC 2026-10-30** |
| 目标用户测试日记录数 | **1 条用户日记录** |

也可在后续复查时使用指定单日接口；以下不是本次实际调用的接口：

```http
GET https://api.github.com/enterprises/gye/copilot/metrics/reports/users-1-day?day=2026-10-30
X-GitHub-Api-Version: 2026-03-10
```

无论采用哪一个报表接口，都应先检查覆盖日期，再选择 `user_login == "gye6666"`、`day == "2026-10-30"` 的记录。

| 核对项 | 口径 |
|---|---|
| 活动日期 | **UTC 2026-10-30** |
| 对应北京时间区间 | `[2026-10-30 08:00, 2026-10-31 08:00)` |
| 本地受控编辑量 | **新增 80 / 删除 20**，已 Keep |
| 重点功能分组 | `agent_edit` 的实际增删；默认模式交互查看 `chat_panel_agent_mode` |
| 两个完整 UTC 日处理窗口结束 | **2026-11-02 08:00 北京时间** |
| 三个完整 UTC 日的保守复查节点 | **2026-11-03 08:00 北京时间** |

截至本次查询，两个完整 UTC 日窗口已结束，三个完整 UTC 日的保守复查节点尚未到达。上述日期不保证数据届时更新或回填；没有创建自动查询或提醒。

### 2026-11-02 API 返回结果

| 指标 | UTC 2026-10-30 的 API 结果 |
|---|---|
| 默认 Agent：`chat_panel_agent_mode` | **1 次交互** |
| Custom Agent：`chat_panel_custom_mode` | 未出现该分类 |
| IDE / 插件 | **VS Code 1.143.1 / Copilot Chat 0.71.0** |
| 模型 | `gpt-6-luna` |
| 代码生成 / 接受活动次数 | **0 / 0** |
| 建议新增 / 删除行数 | 0 / 0 |
| `loc_added_sum` / `loc_deleted_sum` | **0 / 0** |
| `agent_edit` 分类 | **未出现** |
| `used_agent` / `used_chat` | `true` / `true` |
| `used_local_byok` | `false` |
| `totals_by_client` 中的 `vscode` | `request_count = 6`，`session_count = 1`；不是逐请求编辑行数明细 |

版本采样时间为 `2026-10-30T02:05:41.380Z`，即北京时间 10:05:41.380。采样时间与版本信息不能作为本轮编辑遥测已经接收或聚合成功的证明。

### CL / DL 对照与当前结论

以下两日的 API 值均来自 **11 月 2 日 11:00 的同一次最新报表**，不是把旧 CL 快照与新 DL 快照混用。

| 活动日期（UTC） | 测试 | 本地新增 / 删除 | Keep | API 聊天交互 | API 实际新增 / 删除 | `agent_edit` |
|---|---|---:|---|---|---:|---|
| 2026-10-28 | CL：Local / 自定义 LoC Probe | 80 / 20 | 已点击 | Custom 模式 5 次 | **0 / 0** | **未出现** |
| 2026-10-30 | DL：Local / 默认 Agent | 80 / 20 | 已点击 | 默认模式 1 次 | **0 / 0** | **未出现** |

**本轮默认 Agent 同样存在“本地实际编辑成功，但当前 API 未体现编辑 LoC”的差异，因此不能继续将缺失限定为 Custom Agent 专属问题，也不能据此认定 26 日的 +160 / -40 就是默认 Agent 的贡献。**

本次 DL 快照与受控本地记录相差新增 **80** 行、删除 **20** 行。返回的 IDE、feature、语言 / feature、语言 / 模型和模型 / feature 分组中，实际编辑 LoC 均为 0，不是只看聊天分类造成的误判。

本次刷新中，28 日 CL 的编辑 LoC 仍是 0 / 0，并已超过此前记录的 11 月 1 日 08:00 保守复查节点。30 日 DL 已过两日窗口，仍可在 11 月 3 日 08:00 后再次复查。执行成功与遥测送达、接收、分类和报表聚合是不同环节；具体异常环节以及后续是否会修订仍未知，不能宣称永久丢失或确定为某一版本回归。

同日其他活动未完全排除，日汇总不能逐请求关联本轮编辑。现存采集日志没有捕获 acceptance 事件，不等于事件没有产生或上传。

### API 原始用户日记录

以下为 **11 月 2 日查询返回的 `gye6666` 在 UTC 10 月 30 日的完整用户日记录**，仅做 JSON 格式化，保留全部原始字段和值。它是下载 NDJSON 报表中的一条记录，不是 REST 接口返回下载链接的响应，也不包含采集程序添加的外层查询元数据。

完整快照见 [11 月 2 日 API 原始数据](metrics-enterprise-gye-gye6666-20261102-1100-refresh-raw.json)。该文件也保留目标账号其他日期的原始记录，可选择 `day == "2026-10-28"` 核对上述 CL 刷新结果；不要将其他日期相加后作为本次 DL 的 LoC。

```json
{
  "report_start_day": "2026-10-04",
  "report_end_day": "2026-10-31",
  "day": "2026-10-30",
  "enterprise_id": "590626",
  "user_id": 274194146,
  "user_login": "gye6666",
  "user_initiated_interaction_count": 1,
  "code_generation_activity_count": 0,
  "code_acceptance_activity_count": 0,
  "totals_by_ide": [
    {
      "ide": "vscode",
      "user_initiated_interaction_count": 1,
      "code_generation_activity_count": 0,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 0,
      "loc_deleted_sum": 0,
      "last_known_plugin_version": {
        "sampled_at": "2026-10-30T02:05:41.380Z",
        "plugin": "copilot-chat",
        "plugin_version": "0.71.0"
      },
      "last_known_ide_version": {
        "sampled_at": "2026-10-30T02:05:41.380Z",
        "ide_version": "1.143.1"
      }
    }
  ],
  "totals_by_feature": [
    {
      "feature": "chat_panel_agent_mode",
      "user_initiated_interaction_count": 1,
      "code_generation_activity_count": 0,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 0,
      "loc_deleted_sum": 0
    }
  ],
  "totals_by_language_feature": [
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
      "language": "unknown",
      "model": "gpt-6-luna",
      "code_generation_activity_count": 0,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 0,
      "loc_deleted_sum": 0
    }
  ],
  "totals_by_model_feature": [
    {
      "model": "gpt-6-luna",
      "feature": "chat_panel_agent_mode",
      "user_initiated_interaction_count": 1,
      "code_generation_activity_count": 0,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 0,
      "loc_deleted_sum": 0
    }
  ],
  "used_agent": true,
  "used_chat": true,
  "loc_suggested_to_add_sum": 0,
  "loc_suggested_to_delete_sum": 0,
  "loc_added_sum": 0,
  "loc_deleted_sum": 0,
  "used_cli": false,
  "used_copilot_coding_agent": false,
  "used_copilot_cloud_agent": false,
  "ai_adoption_phase": {
    "phase_number": 1,
    "phase": "Phase 1",
    "version": "v1"
  },
  "ai_credits_used": 0.5734725,
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
      "request_count": 6,
      "session_count": 1,
      "token_usage": {
        "avg_tokens_per_request": 13159.67,
        "output_tokens_sum": 1439,
        "prompt_tokens_sum": 77519
      },
      "totals_by_model": [
        {
          "model": "gpt-6-luna",
          "model_selection_mode": "user_selected",
          "request_count": 2,
          "token_usage": {
            "avg_tokens_per_request": 37966.0,
            "output_tokens_sum": 1237,
            "prompt_tokens_sum": 74695
          }
        },
        {
          "model": "unknown",
          "model_selection_mode": "user_selected",
          "request_count": 4,
          "token_usage": {
            "avg_tokens_per_request": 756.5,
            "output_tokens_sum": 202,
            "prompt_tokens_sum": 2824
          }
        }
      ]
    }
  ],
  "used_local_byok": false
}
```

### 本轮证据

| 文件 | 用途 |
|---|---|
| [loc-ui-test-20261030-dl.json](loc-ui-test-20261030-dl.json) | 账号、环境、默认模式、请求 ID、界面 / 落盘计数及 Keep 记录 |
| [loc-ui-test-20261030-baseline-observed.json](loc-ui-test-20261030-baseline-observed.json) | 修改前的 25 行 CRLF 基线 |
| [loc-ui-test-20261030-observed.json](loc-ui-test-20261030-observed.json) | Keep 后的实际内容、差异、行数、换行和哈希 |
| [DL 测试文件](copilot-loc-test/default_local_20261030.py) | 默认 Agent 实际写入的合成文件 |
| [metrics-enterprise-gye-gye6666-20261102-1100-refresh-raw.json](metrics-enterprise-gye-gye6666-20261102-1100-refresh-raw.json) | 11 月 2 日 11:00 的原始目标用户快照，包含 28 日 CL 和 30 日 DL |
| [loc-ui-test-20261030-dl-server-comparison-20261102.json](loc-ui-test-20261030-dl-server-comparison-20261102.json) | DL 本地 / API 对账及同次快照中的 CL 刷新结果 |

## 11. 2026-11-02 单独 CC 测试

**11 月 5 日最新对账：两个实际调用的报表接口均返回 HTTP 200，11 月 2 日用户日汇总实际新增 80 行、删除 20 行，与本地 CC 修改量一致。不过，全部活动归入 `chat_panel_agent_mode`，未出现 `agent_edit`、`chat_panel_custom_mode` 或具名自定义 Agent 记录。行数匹配与自定义 Agent 归因应分开判断，尚不能逐请求确认。**

### 本地结果

**CC（Copilot Harness / 自定义 LoC Probe）已完成一次文件编辑：新增 80 行、删除 20 行，净增 60 行。** 写入前选择 **Allow Once**，完成后未出现 Keep 步骤。界面计数与实际落盘差异一致。

| 项目 | 本轮记录 |
|---|---|
| 测试账号 / 后续查询企业 | `gye6666` / `gye` |
| 测试标识 | `20261102-CC` |
| 发送时间（北京时间） | **2026-11-02 11:09** |
| Harness / Agent | **Copilot / 自定义 LoC Probe** |
| Harness 含义 | Copilot SDK / Agent Host，本机工作区，不是 Cloud |
| VS Code / Copilot Chat | **1.143.1 / 0.71.0**，按运行界面的资源版本目录核验 |
| 模型 / Effort / Context | GPT-6 Luna / Medium / 272K |
| 入口 / 模式 / 权限 | 普通编辑器窗口 Chat / Interactive / Manual permissions |
| 文件 | `copilot-loc-test\custom_copilot_20261102.py`，独立新文件 |
| 写入确认 | `Write file?` 明确显示目标文件和 **+80 / -20** |
| 实际确认操作 | 从下拉菜单选择 **Allow Once**；未选择主按钮的 `Allow in this Session` |
| Keep / Accept | **未出现，未点击 Keep** |
| 界面计数 | `Session Changes: 1 File, +80, -20` |
| 实际落盘差异 | **+80 / -20** |
| 文件状态 | 25 行变为 85 行，CRLF，语法有效，内容与预期完全一致 |
| 自动化提交的测试任务次数 | **1 次**，没有重复发送 |
| 基线采集时间 | `2026-11-02T11:08:23.162521+08:00` |
| 最终证据采集时间 | `2026-11-02T11:11:20.154006+08:00` |
| 最终文件 SHA-256 | `4c3675924615acad6d2074fd14baaae5114afa2fb89b8bd914f97c2ddc6c4fdd` |
| SDK 请求 ID | 未在检查的 Copilot Chat 扩展日志中捕获，记录为 `null`，不表示没有请求 |

本轮只提交 CC 场景，不混入新的 Local / 默认 Agent 受控任务。采用新的 Chat、与前轮相同的 25 行基线和相同目标修改量；没有手工修正 Agent 输出。14 个历史测试文件及 LoC Probe 定义均保持不变。

Accounts 菜单显示 `gye6666`，扩展登录日志时间为 11:06:12.460；SDK 服务端账号归属及编辑遥测送达未独立验证。SDK / CLI runtime 的具体版本本轮没有重新核验，不将 IDE / 扩展版本等同于 SDK 版本。

开始观察时 IDE 状态栏曾显示正在安装更新，后恢复为 Update；自动化没有点击更新、重启或 customization migration。运行界面的资源路径仍对应上述 1.143.1 / 0.71.0，不能把待安装版本作为本轮运行版本。

### API 查询与统计口径

11 月 2 日测试前 11:00 获取的报表仅覆盖到 UTC 10 月 31 日，不包含本轮测试日。**2026-11-05 08:57 北京时间**已重新获取最新报表，并调用指定单日接口核对，两者均返回 HTTP 200：

```http
GET https://api.github.com/enterprises/gye/copilot/metrics/reports/users-28-day/latest
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

```http
GET https://api.github.com/enterprises/gye/copilot/metrics/reports/users-1-day?day=2026-11-02
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

| 查询项 | 本次结果 |
|---|---|
| 查询企业 / 目标用户 | `gye` / `gye6666` |
| API 读取账号 | `gaussye`，使用重新授权的本地 GitHub CLI 凭据，已确认具有 `read:enterprise`；与测试账号不同 |
| 最新报表查询时间（UTC） | `2026-11-05T00:57:21.299438+00:00` |
| 单日报表查询时间（UTC） | `2026-11-05T00:57:54.193584+00:00` |
| HTTP 状态 | 两个接口均为 **200** |
| 最新报表范围 | **2026-10-07 至 2026-11-03** |
| 是否覆盖测试日 | **是，UTC 2026-11-02** |
| 目标用户测试日记录数 | 两个接口各 **1 条用户日记录** |
| 两接口一致性 | 除报表范围等外层日期字段外，目标用户当日活动字段和值完全一致 |

最新报表先按 `user_login == "gye6666"`、`day == "2026-11-02"` 选择记录；单日接口返回的也是用户日汇总，不是逐请求编辑明细。

| 核对项 | 口径 |
|---|---|
| 活动日期 | **UTC 2026-11-02** |
| 对应北京时间区间 | `[2026-11-02 08:00, 2026-11-03 08:00)` |
| 受控本地编辑量 | **新增 80 / 删除 20**，已单次允许写入 |
| 实际 LoC 口径 | 检查用户日总计及 IDE、feature、语言、model 等实际返回分组；不能仅凭缺少 `agent_edit` 就认定总 LoC 为零 |
| 自定义模式 / 名称 | 分别查看 `chat_panel_custom_mode`、`totals_by_custom_agent`、`distinct_custom_agent_use_count`；不能将名称或启动次数当作编辑 LoC |
| 两个完整 UTC 日窗口结束 | **2026-11-05 08:00 北京时间** |
| 三个完整 UTC 日的保守复查节点 | **2026-11-06 08:00 北京时间** |

截至本次查询，两个完整 UTC 日窗口已结束，三个完整 UTC 日的保守复查节点尚未到达。上述日期不保证报表届时更新或数据回填，也不证明当前报表已最终定稿；没有创建自动查询或提醒。

### 2026-11-05 API 返回结果

| 指标 | UTC 2026-11-02 的 API 结果 |
|---|---|
| 默认 Agent 模式分类：`chat_panel_agent_mode` | **2 次交互**；这是 API 分类，不能据此改写本地实际选择的自定义 LoC Probe |
| Custom Agent 模式分类：`chat_panel_custom_mode` | **未出现** |
| IDE | **VS Code 1.143.1** |
| API 插件 / 版本 | **`vscode-agent-host` / `1.143.1`** |
| 本地安装的 Copilot Chat 扩展 | **0.71.0**，与 API 插件字段不是同一个组件 |
| 有非零 LoC 的语言 / 模型 | `python` / `gpt-6-luna` |
| 代码生成 / 接受活动次数 | **1 / 1** |
| 建议新增 / 删除行数 | **80 / 20** |
| `loc_added_sum` / `loc_deleted_sum` | **80 / 20**，净增 60 |
| `agent_edit` 分类 | **未出现** |
| `totals_by_custom_agent` | **`[]`**，没有 LoC Probe 名称记录 |
| `distinct_custom_agent_use_count` | **0** |
| `used_agent` / `used_chat` | `true` / `true` |
| `used_local_byok` | `false` |
| `totals_by_client` 中的 `vscode` | `request_count = 4`，`session_count = 2`；不是逐请求编辑行数明细 |

版本采样时间为 `2026-11-02T03:10:49.892Z`，即北京时间 11:10:49.892。API 上报组件 `vscode-agent-host 1.143.1` 应与本地安装的 `copilot-chat 0.71.0` 分开记录，不能把前者写成 Copilot Chat 扩展版本，也不能把采样时间当作某次编辑遥测的接收时间。

用户日总计、IDE、feature、Python / feature、Python / model 和 model / feature 分组均体现 **+80 / -20**。这些是同一活动的不同维度，不能相加重复计数。

### 跨日期对照与当前结论

以下 API 值全部来自 **11 月 5 日 08:57 的同一次最新报表**；不是把不同查询日期的快照混合对账。

| 活动日期（UTC） | 受控测试 | 本地新增 / 删除 | API 实际新增 / 删除 | 本次快照中的分类 / 归因 |
|---|---|---:|---:|---|
| 2026-10-26 | CL、CC、DC、DL 四组 | 320 / 80 | **160 / 40** | `agent_edit` 贡献 160 / 40；不能拆分为具体哪两组 |
| 2026-10-28 | CL：Local / 自定义 LoC Probe | 80 / 20 | **0 / 0** | Custom 模式 5 次交互；没有 `agent_edit` |
| 2026-10-30 | DL：Local / 默认 Agent | 80 / 20 | **0 / 0** | 默认模式 1 次交互；没有 `agent_edit` |
| 2026-11-02 | CC：Copilot / 自定义 LoC Probe | 80 / 20 | **80 / 20** | 全部归入 `chat_panel_agent_mode`；自定义 Agent 列表为空 |

**本轮已观察到用户日汇总行数与本地 CC 修改量一致，但尚未证明这条受控请求已正确归因给自定义 Agent。** 本地只提交 1 个受控任务，API 却记录 2 次交互、2 个 client session；额外活动来源尚未确认，不能解释为重复发送，也不能凭数值吻合完成逐请求关联。

API 将本轮全部 LoC 放在 `chat_panel_agent_mode`，同时没有 `agent_edit`、`chat_panel_custom_mode` 或具名自定义 Agent 活动。官方 [LoC 文档](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/lines-of-code-metrics)将直接文件编辑描述为 `agent_edit`，将 chat panel 中的实际 LoC 描述为代码块 Copy / Apply；本轮实际归类与该描述不一致，是否存在 Harness 特定的分类口径或上报差异仍待确认，不能直接宣布为已确认的缺陷或预期行为。

没有 Keep 步骤不意味着 API 必然不统计编辑行数。本轮未观察到 Keep，但日汇总有 1 次接受活动；不能据此认定 `Allow Once` 与 acceptance 一一对应。现有本地采集日志未捕获 acceptance 事件，也不能据此认定没有上传。

**CL 和 DL 在同一次最新报表中仍均为 0 / 0，说明 Local 默认模式也有缺失现象；本轮 CC 又出现了匹配的行数。因此，现有证据不支持“所有自定义 Agent 都不统计 LoC”，也不能将 26 日少一半直接归因于 Custom Agent 被排除。** Local 与 Copilot Harness 的采集、上报和分类差异是值得继续定位的方向，不是已确认根因。

本轮与 CL / DL 分属不同 UTC 日期。即使 IDE、扩展版本、模型及修改量相同，也不能排除日期间环境变化或同日其他活动；SDK runtime 的具体版本没有重新核验，日汇总也没有逐请求编辑归因。

### API 原始用户日记录

以下为 **11 月 5 日最新 28 天报表返回的 `gye6666` 在 UTC 11 月 2 日的完整用户日记录**，仅做 JSON 格式化，保留全部原始字段和值。它是下载 NDJSON 报表中的一条记录，不是 REST 接口返回下载链接的响应，也不包含采集程序添加的外层查询元数据。

完整快照见 [11 月 5 日最新 API 原始数据](metrics-enterprise-gye-gye6666-20261105-reauthorized-refresh-raw.json)，其中保留目标账号其他日期的原始记录，可核对上表历史结果。指定单日接口的完整快照另见 [11 月 2 日单日报表](metrics-enterprise-gye-gye6666-20261102-one-day-20261105-raw.json)；两者当日活动字段和值完全一致，报表范围等日期字段按各自接口保留。

```json
{
  "report_start_day": "2026-10-07",
  "report_end_day": "2026-11-03",
  "day": "2026-11-02",
  "enterprise_id": "590626",
  "user_id": 274194146,
  "user_login": "gye6666",
  "user_initiated_interaction_count": 2,
  "code_generation_activity_count": 1,
  "code_acceptance_activity_count": 1,
  "totals_by_ide": [
    {
      "ide": "vscode",
      "user_initiated_interaction_count": 2,
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 80,
      "loc_suggested_to_delete_sum": 20,
      "loc_added_sum": 80,
      "loc_deleted_sum": 20,
      "last_known_plugin_version": {
        "sampled_at": "2026-11-02T03:10:49.892Z",
        "plugin": "vscode-agent-host",
        "plugin_version": "1.143.1"
      },
      "last_known_ide_version": {
        "sampled_at": "2026-11-02T03:10:49.892Z",
        "ide_version": "1.143.1"
      }
    }
  ],
  "totals_by_feature": [
    {
      "feature": "chat_panel_agent_mode",
      "user_initiated_interaction_count": 2,
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 80,
      "loc_suggested_to_delete_sum": 20,
      "loc_added_sum": 80,
      "loc_deleted_sum": 20
    }
  ],
  "totals_by_language_feature": [
    {
      "language": "python",
      "feature": "chat_panel_agent_mode",
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 80,
      "loc_suggested_to_delete_sum": 20,
      "loc_added_sum": 80,
      "loc_deleted_sum": 20
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
      "model": "gpt-6-luna",
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 80,
      "loc_suggested_to_delete_sum": 20,
      "loc_added_sum": 80,
      "loc_deleted_sum": 20
    },
    {
      "language": "unknown",
      "model": "gpt-6-luna",
      "code_generation_activity_count": 0,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 0,
      "loc_deleted_sum": 0
    }
  ],
  "totals_by_model_feature": [
    {
      "model": "gpt-6-luna",
      "feature": "chat_panel_agent_mode",
      "user_initiated_interaction_count": 2,
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 80,
      "loc_suggested_to_delete_sum": 20,
      "loc_added_sum": 80,
      "loc_deleted_sum": 20
    }
  ],
  "used_agent": true,
  "used_chat": true,
  "loc_suggested_to_add_sum": 80,
  "loc_suggested_to_delete_sum": 20,
  "loc_added_sum": 80,
  "loc_deleted_sum": 20,
  "used_cli": false,
  "used_copilot_coding_agent": false,
  "used_copilot_cloud_agent": false,
  "ai_adoption_phase": {
    "phase_number": 1,
    "phase": "Phase 1",
    "version": "v1"
  },
  "ai_credits_used": 0.24177200000000001,
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
        "avg_tokens_per_request": 6885.5,
        "output_tokens_sum": 1336,
        "prompt_tokens_sum": 26206
      },
      "totals_by_model": [
        {
          "model": "gpt-6-luna",
          "model_selection_mode": "user_selected",
          "is_custom_enterprise_model": false,
          "request_count": 2,
          "token_usage": {
            "avg_tokens_per_request": 13146.0,
            "output_tokens_sum": 1306,
            "prompt_tokens_sum": 24986
          }
        },
        {
          "model": "unknown",
          "model_selection_mode": "user_selected",
          "is_custom_enterprise_model": false,
          "request_count": 2,
          "token_usage": {
            "avg_tokens_per_request": 625.0,
            "output_tokens_sum": 30,
            "prompt_tokens_sum": 1220
          }
        }
      ]
    }
  ],
  "used_local_byok": false
}
```

### 本轮证据

| 文件 | 用途 |
|---|---|
| [loc-ui-test-20261102-cc.json](loc-ui-test-20261102-cc.json) | 账号、环境、CC 场景、单次写入确认、界面 / 落盘计数及对账口径 |
| [loc-ui-test-20261102-baseline-observed.json](loc-ui-test-20261102-baseline-observed.json) | 修改前 25 行 CRLF 基线 |
| [loc-ui-test-20261102-observed.json](loc-ui-test-20261102-observed.json) | 完成后的实际内容、差异、行数、换行和哈希 |
| [CC 测试文件](copilot-loc-test/custom_copilot_20261102.py) | Copilot Harness / 自定义 Agent 实际写入的合成文件 |
| [测试前参考 API 快照](metrics-enterprise-gye-gye6666-20261102-1100-refresh-raw.json) | 已保存的同日 11:00 报表，截至 UTC 10 月 31 日，未覆盖本轮测试日 |
| [metrics-enterprise-gye-gye6666-20261105-reauthorized-refresh-raw.json](metrics-enterprise-gye-gye6666-20261105-reauthorized-refresh-raw.json) | 11 月 5 日最新 28 天报表的原始目标用户快照，包含本轮 CC 和历史测试日 |
| [metrics-enterprise-gye-gye6666-20261102-one-day-20261105-raw.json](metrics-enterprise-gye-gye6666-20261102-one-day-20261105-raw.json) | 指定 UTC 11 月 2 日的单日报表原始目标用户记录 |
| [loc-ui-test-20261102-cc-server-comparison-20261105.json](loc-ui-test-20261102-cc-server-comparison-20261105.json) | CC 本地 / API 对账、归类差异及同次快照中的历史结果 |

## 12. 2026-11-05 单独 DC 测试

**11 月 6 日 14:04 最新对账：最新 28 天和指定单日报表均返回 HTTP 200，已覆盖 UTC 11 月 5 日，当日活动字段和值一致。API 用户日实际新增 160 行、删除 40 行，是本地受控编辑 +80 / -20 的两倍；两个不同 feature 各记录 +80 / -20。已确认数值差异，但未确认同一次编辑被重复计算。**

### 本地结果

**DC（Copilot Harness / 默认 Agent）已完成一次文件编辑：新增 80 行、删除 20 行，净增 60 行。** 写入前选择 **Allow Once**，完成后出现 **Keep All Edits**，已点击保留，随后 Keep / Undo 控件消失。界面计数与实际落盘差异一致；11 月 6 日 API 对账结果见下文。

| 项目 | 本轮记录 |
|---|---|
| 测试账号 / 后续查询企业 | `gye6666` / `gye` |
| 测试标识 | `20261105-DC` |
| 发送时间（北京时间） | **2026-11-05 09:26** |
| Harness / Agent | **Copilot / 内置默认 Agent**，不是 LoC Probe |
| Harness 含义 | Copilot SDK / Agent Host，本机工作区，不是 Cloud |
| VS Code / Copilot 扩展 | **1.144.0 / 0.72.0** |
| 版本核验 | 运行界面资源目录的应用元数据；内置扩展 `copilot` 的包标识为 `GitHub.copilot-chat`，版本 `0.72.0` |
| 模型 / Effort / Context | GPT-6 Luna / Medium / 272K |
| 入口 / 模式 / 权限 | 普通编辑器窗口 Chat / Interactive / Manual permissions |
| 文件 | `copilot-loc-test\default_copilot_20261105.py`，独立新文件 |
| 写入确认 | `Write file?` 明确显示目标文件和 **+80 / -20** |
| 实际确认操作 | 从下拉菜单选择 **Allow Once**；未选择主按钮的 `Allow in this Session` |
| Keep / Accept | **出现并点击 Keep All Edits**，Keep / Undo 控件随后消失 |
| 界面计数 | `1 file changed, 80 lines added, 20 lines removed` |
| 实际落盘差异 | **+80 / -20** |
| 文件状态 | 25 行变为 85 行，CRLF，语法有效，内容与预期完全一致 |
| 自动化提交的测试任务次数 | **1 次**，没有重复发送或混入其他受控场景 |
| 基线采集时间 | `2026-11-05T09:25:02.694896+08:00` |
| Keep 后最终证据采集时间 | `2026-11-05T09:27:51.368909+08:00` |
| 基线 SHA-256 | `6298c00f97ffc8ecc7c34ba09dc14c021d83cf0ab507d3debd1be2d1d41e6bfe` |
| 最终文件 SHA-256 | `4c3675924615acad6d2074fd14baaae5114afa2fb89b8bd914f97c2ddc6c4fdd` |
| SDK 请求 ID | 未在检查的 Copilot Chat 扩展日志中捕获，记录为 `null`，不表示没有请求 |

测试前已通过 Accounts 菜单确认账号为 `gye6666`，扩展登录日志时间为 `2026-11-05 09:15:19.330`；这不等于已逐请求验证 SDK 服务端身份或遥测送达。新 Chat 初始化时异步恢复过 LoC Probe，发送前已显式切换并复核为内置默认 **Agent**；没有向自定义 Agent 发送本轮任务。

本轮只运行 DC，沿用相同的 25 行基线，要求内置文件编辑工具将 20 条旧字典项替换为 80 条新项，禁止终端、脚本、MCP、子 Agent、skills 和 handoffs。没有手工修正 Agent 输出，15 个历史测试文件及 LoC Probe 定义均保持不变；自动化没有点击更新、重启或 customization migration。

### 与前轮 CC 的可比性

| 项目 | 11 月 2 日 CC | 11 月 5 日 DC |
|---|---|---|
| Harness / Agent | Copilot / 自定义 LoC Probe | Copilot / 默认 Agent |
| VS Code / Copilot 扩展 | **1.143.1 / 0.71.0** | **1.144.0 / 0.72.0** |
| 模型 / Effort / Context | GPT-6 Luna / Medium / 272K | GPT-6 Luna / Medium / 272K |
| 本地新增 / 删除 | 80 / 20 | 80 / 20 |
| 写入前确认 | Allow Once | Allow Once |
| 完成后 Keep | 未出现 | **出现，已点击 Keep All Edits** |
| API 状态 | 11 月 6 日 14:04 同次快照中仍为 80 / 20，仅在 `chat_panel_agent_mode` 下 | **日汇总 160 / 40**；`chat_panel_agent_mode` 和 `agent_edit` 各 80 / 20 |

**本轮运行时 IDE / 扩展已比 CC 更新，不能作为完全同版本的默认 / 自定义 Agent A/B 对照。** Keep 步骤差异是真实观察，但日期、版本和 Agent 类型均不同，不能单独归因于默认 / 自定义 Agent。SDK / CLI runtime 的具体版本没有重新核验，不将 IDE / 扩展版本等同于 SDK 版本。

### API 查询与统计口径

11 月 5 日 08:57 的测试前参考报表仅覆盖到 UTC 11 月 3 日。11 月 6 日上午两次查询尚无本轮活动日的可下载数据，**14:04 查询时两个接口均已可取得目标用户记录**：

```http
GET https://api.github.com/enterprises/gye/copilot/metrics/reports/users-28-day/latest
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

```http
GET https://api.github.com/enterprises/gye/copilot/metrics/reports/users-1-day?day=2026-11-05
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

| 查询时间（北京时间） | 最新 28 天报表 | 指定 UTC 11 月 5 日的单日接口 |
|---|---|---|
| 2026-11-06 08:11 | HTTP 200，至 UTC 11 月 4 日，不覆盖本轮 | HTTP 200，JSON 值为 `""`，无下载链接 |
| 2026-11-06 10:10 | HTTP 200，仍至 UTC 11 月 4 日 | HTTP 200，仍为 `""`，无下载链接 |
| **2026-11-06 14:04** | **HTTP 200，2026-10-09 至 2026-11-05，已覆盖本轮** | **HTTP 200，返回 11 月 5 日报表下载链接，取得 1 条目标用户日记录** |

上午的空字符串不是 0 行用户记录，不能解释为 LoC 0 / 0。14:04 的结果证明报表已可获取，但不能反推出上午空响应的具体原因。

| 查询项 | 本次结果 |
|---|---|
| 查询企业 / 目标用户 | `gye` / `gye6666` |
| API 读取账号 | `gaussye`，使用重新授权的本地 GitHub CLI 凭据；与测试账号不同 |
| 最新报表查询时间（UTC） | `2026-11-06T06:04:35.772464+00:00` |
| 单日报表查询时间（UTC） | `2026-11-06T06:04:35.675161+00:00` |
| HTTP 状态 | 两个接口均为 **200** |
| 最新报表范围 | **2026-10-09 至 2026-11-05** |
| 目标用户测试日记录数 | 两个接口各 **1 条用户日记录** |
| 两接口一致性 | 除报表范围等日期字段外，当日活动字段和值完全一致 |

最新报表筛选 `user_login == "gye6666"`、`day == "2026-11-05"`。两个接口均提供用户日汇总，不是逐请求文件编辑明细。

| 核对项 | 口径 |
|---|---|
| API 读取账号 / 企业 / 目标用户 | `gaussye` / `gye` / `gye6666`；读取账号与测试账号不同 |
| 活动日期 | **UTC 2026-11-05** |
| 对应北京时间区间 | `[2026-11-05 08:00, 2026-11-06 08:00)` |
| 受控本地编辑量 | **新增 80 / 删除 20**，已单次允许写入并 Keep |
| 实际 LoC 口径 | 检查用户日总计及 IDE、feature、语言、model 等实际返回分组，不重复相加 |
| Feature 归类 | 同时检查 `agent_edit` 和 `chat_panel_agent_mode`；前轮 CC 缺少 `agent_edit` 但总 LoC 非零，不能仅按分类是否出现判断缺失 |
| API 组件版本 | 本次实际返回插件名称 / 版本均为 `unknown`，不能用本地 `GitHub.copilot-chat 0.72.0` 替换 |
| 两个完整 UTC 日窗口结束 | **2026-11-08 08:00 北京时间** |
| 三个完整 UTC 日的保守复查节点 | **2026-11-09 08:00 北京时间** |

截至 11 月 6 日 14:04，报表已提前可获取，但上述两个完整 UTC 日窗口尚未结束。报表覆盖不等于已最终定稿，仍可能后续修订；这些日期不保证更新或回填，没有创建自动查询或提醒。

### 2026-11-06 14:04 API 返回结果

| 指标 | UTC 2026-11-05 的 API 结果 |
|---|---|
| 默认 Agent 模式：`chat_panel_agent_mode` | **2 次交互；实际新增 80 / 删除 20** |
| Custom Agent 模式：`chat_panel_custom_mode` | 未出现 |
| `agent_edit` | **已出现；实际新增 80 / 删除 20** |
| 用户日总计 `loc_added_sum` / `loc_deleted_sum` | **160 / 40**，净增 120 |
| 本地受控修改量 | **80 / 20**，净增 60 |
| API 总计减本地受控量 | **新增多 80 / 删除多 20**，净增多 60 |
| 建议新增 / 删除行数 | **80 / 20** |
| 代码生成 / 接受活动次数 | **2 / 1** |
| IDE | **VS Code 1.144.0** |
| API 插件名称 / 版本 | **`unknown` / `unknown`**；本地安装扩展为 `GitHub.copilot-chat 0.72.0` |
| 模型 | `gpt-6-luna` |
| 非零 LoC 的语言归类 | `python` 对应 chat 模式 80 / 20；`unknown` 对应 `agent_edit` 80 / 20 |
| `used_agent` / `used_chat` | `true` / `true` |
| `totals_by_custom_agent` / `distinct_custom_agent_use_count` | `[]` / `0`，本轮测试选择的是默认 Agent |
| `totals_by_client` 中的 `vscode` | `request_count = 4`，`session_count = 2` |
| `client_telemetry_present` | **`true`** |
| `usage_metrics_requirements` | `met: ["vscode"]`，`unmet: []`，`unknown: []` |

版本采样时间为 `2026-11-05T01:32:12.582Z`，即北京时间 09:32:12.582。版本采样和日级遥测要求满足标志不是本次受控编辑的接收时间或逐请求送达证明。

| `feature` | 交互次数 | 代码生成次数 | 代码接受次数 | 建议新增 / 删除 | 实际新增 / 删除 |
|---|---:|---:|---:|---:|---:|
| `chat_panel_agent_mode` | 2 | 1 | 1 | 80 / 20 | **80 / 20** |
| `agent_edit` | 0 | 1 | 0 | 0 / 0 | **80 / 20** |
| **用户日总计** | **2** | **2** | **1** | **80 / 20** | **160 / 40** |

**原始用户日顶层字段本身就是 160 / 40，两个 feature 行相加也等于该总计。** 这不是分析时把 IDE、语言、模型等不同维度重复相加造成的结果；不同维度仍不能再相加。

### 同次快照对照与当前结论

以下 API 值均来自 **11 月 6 日 14:04 的同一次最新报表**：

| 活动日期（UTC） | 受控测试 | 本地新增 / 删除 | API 实际新增 / 删除 | Feature 归类 |
|---|---|---:|---:|---|
| 2026-10-26 | CL、CC、DC、DL 四组 | 320 / 80 | **160 / 40** | 实际 LoC 位于 `agent_edit`，具体贡献组别未知 |
| 2026-10-28 | CL：Local / 自定义 LoC Probe | 80 / 20 | **0 / 0** | Custom 模式交互存在；无 `agent_edit` |
| 2026-10-30 | DL：Local / 默认 Agent | 80 / 20 | **0 / 0** | 默认模式交互存在；无 `agent_edit` |
| 2026-11-02 | CC：Copilot / 自定义 LoC Probe | 80 / 20 | **80 / 20** | 仅 `chat_panel_agent_mode`；无 `agent_edit` |
| 2026-11-05 | DC：Copilot / 默认 Agent | 80 / 20 | **160 / 40** | `chat_panel_agent_mode` 与 `agent_edit` 各 80 / 20 |

**已确认：DC 活动日已出现非零 LoC，且日汇总是本地受控修改量的两倍。尚未确认：同一次编辑是否被两个 feature 重复上报或重复归因。** 两个 feature 的数值完全相同，是进一步排查重叠计数的线索，但不是已确认根因。

自动化只提交 1 个受控任务，API 有 2 次交互和 2 个 client session；额外活动来源未确认，同日其他活动未完全排除。API 没有逐请求编辑标识，不能证明两个 feature 行都来自该受控编辑，也不能把日汇总额外活动说成自动化重复发送。

CC 与 DC 的差异还包括日期、IDE / 扩展版本及 Keep 步骤。不能据此认定默认 Agent 固定多计一倍、自定义 Agent 少计一份，或 Keep 必然生成 `agent_edit`。当前证据仍不支持“所有 Custom Agent 都不统计 LoC”；需要继续区分上报覆盖、feature 分类与可能重叠归因。

Allow Once、Keep 和落盘文件证明本地编辑及保留过程。本地未捕获 acceptance 日志不代表没有上传；API 的 `client_telemetry_present: true` 和要求满足标志也只说明日级记录状态，不提供该受控请求的逐事件映射。

### API 原始用户日记录

以下为 **11 月 6 日 14:04 最新 28 天报表中 `gye6666` 在 UTC 11 月 5 日的完整用户日记录**，仅做 JSON 格式化，保留全部原始字段和值，包括本次返回的 `client_telemetry_present` 和 `usage_metrics_requirements`。

这是下载 NDJSON 报表中的一条用户记录，不是 REST 下载链接响应，也不包含本地采集器添加的查询元数据。完整快照见 [最新报表原始数据](metrics-enterprise-gye-gye6666-20261106-1404-refresh-raw.json)；[指定单日报表](metrics-enterprise-gye-gye6666-20261105-one-day-20261106-1404-raw.json)的当日活动字段和值相同。

```json
{
  "report_start_day": "2026-10-09",
  "report_end_day": "2026-11-05",
  "day": "2026-11-05",
  "enterprise_id": "590626",
  "user_id": 274194146,
  "user_login": "gye6666",
  "user_initiated_interaction_count": 2,
  "code_generation_activity_count": 2,
  "code_acceptance_activity_count": 1,
  "totals_by_ide": [
    {
      "ide": "vscode",
      "user_initiated_interaction_count": 2,
      "code_generation_activity_count": 2,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 80,
      "loc_suggested_to_delete_sum": 20,
      "loc_added_sum": 160,
      "loc_deleted_sum": 40,
      "last_known_plugin_version": {
        "sampled_at": "2026-11-05T01:32:12.582Z",
        "plugin": "unknown",
        "plugin_version": "unknown"
      },
      "last_known_ide_version": {
        "sampled_at": "2026-11-05T01:32:12.582Z",
        "ide_version": "1.144.0"
      }
    }
  ],
  "totals_by_feature": [
    {
      "feature": "chat_panel_agent_mode",
      "user_initiated_interaction_count": 2,
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 80,
      "loc_suggested_to_delete_sum": 20,
      "loc_added_sum": 80,
      "loc_deleted_sum": 20
    },
    {
      "feature": "agent_edit",
      "user_initiated_interaction_count": 0,
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 80,
      "loc_deleted_sum": 20
    }
  ],
  "totals_by_language_feature": [
    {
      "language": "python",
      "feature": "chat_panel_agent_mode",
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 80,
      "loc_suggested_to_delete_sum": 20,
      "loc_added_sum": 80,
      "loc_deleted_sum": 20
    },
    {
      "language": "unknown",
      "feature": "agent_edit",
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 80,
      "loc_deleted_sum": 20
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
      "model": "gpt-6-luna",
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 80,
      "loc_suggested_to_delete_sum": 20,
      "loc_added_sum": 80,
      "loc_deleted_sum": 20
    },
    {
      "language": "unknown",
      "model": "gpt-6-luna",
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 80,
      "loc_deleted_sum": 20
    }
  ],
  "totals_by_model_feature": [
    {
      "model": "gpt-6-luna",
      "feature": "chat_panel_agent_mode",
      "user_initiated_interaction_count": 2,
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 1,
      "loc_suggested_to_add_sum": 80,
      "loc_suggested_to_delete_sum": 20,
      "loc_added_sum": 80,
      "loc_deleted_sum": 20
    },
    {
      "model": "gpt-6-luna",
      "feature": "agent_edit",
      "user_initiated_interaction_count": 0,
      "code_generation_activity_count": 1,
      "code_acceptance_activity_count": 0,
      "loc_suggested_to_add_sum": 0,
      "loc_suggested_to_delete_sum": 0,
      "loc_added_sum": 80,
      "loc_deleted_sum": 20
    }
  ],
  "used_agent": true,
  "used_chat": true,
  "loc_suggested_to_add_sum": 80,
  "loc_suggested_to_delete_sum": 20,
  "loc_added_sum": 160,
  "loc_deleted_sum": 40,
  "used_cli": false,
  "used_copilot_coding_agent": false,
  "used_copilot_cloud_agent": false,
  "ai_adoption_phase": {
    "phase_number": 1,
    "phase": "Phase 1",
    "version": "v1"
  },
  "ai_credits_used": 0.45216449999999997,
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
        "avg_tokens_per_request": 15068.0,
        "output_tokens_sum": 1121,
        "prompt_tokens_sum": 59151
      },
      "totals_by_model": [
        {
          "model": "gpt-6-luna",
          "model_selection_mode": "user_selected",
          "is_custom_enterprise_model": false,
          "request_count": 2,
          "token_usage": {
            "avg_tokens_per_request": 29511.0,
            "output_tokens_sum": 1091,
            "prompt_tokens_sum": 57931
          }
        },
        {
          "model": "unknown",
          "model_selection_mode": "user_selected",
          "is_custom_enterprise_model": false,
          "request_count": 2,
          "token_usage": {
            "avg_tokens_per_request": 625.0,
            "output_tokens_sum": 30,
            "prompt_tokens_sum": 1220
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

### 本轮证据

| 文件 | 用途 |
|---|---|
| [loc-ui-test-20261105-dc.json](loc-ui-test-20261105-dc.json) | 账号、运行版本、默认 Agent、单次写入确认、Keep 及后续对账口径 |
| [loc-ui-test-20261105-baseline-observed.json](loc-ui-test-20261105-baseline-observed.json) | 修改前 25 行 CRLF 基线 |
| [loc-ui-test-20261105-observed.json](loc-ui-test-20261105-observed.json) | Keep 后的实际内容、差异、行数、换行和哈希 |
| [DC 测试文件](copilot-loc-test/default_copilot_20261105.py) | Copilot Harness / 默认 Agent 实际写入的合成文件 |
| [测试前参考 API 快照](metrics-enterprise-gye-gye6666-20261105-reauthorized-refresh-raw.json) | 11 月 5 日 08:57 的报表，截至 UTC 11 月 3 日，未覆盖本轮测试日 |
| [metrics-enterprise-gye-gye6666-20261106-1404-refresh-raw.json](metrics-enterprise-gye-gye6666-20261106-1404-refresh-raw.json) | 11 月 6 日 14:04 最新目标用户报表，覆盖 DC 及历史测试日 |
| [metrics-enterprise-gye-gye6666-20261105-one-day-20261106-1404-raw.json](metrics-enterprise-gye-gye6666-20261105-one-day-20261106-1404-raw.json) | 11 月 5 日单日报表原始目标用户记录 |
| [loc-ui-test-20261105-dc-server-comparison-20261106-1404.json](loc-ui-test-20261105-dc-server-comparison-20261106-1404.json) | 本地 80 / 20 与日汇总 160 / 40 的对账及归因限制 |
| [loc-ui-test-20261105-dc-server-comparison-20261106.json](loc-ui-test-20261105-dc-server-comparison-20261106.json) | 11 月 6 日 08:11 尚未覆盖活动日的历史对账状态，未知值未改写为零 |
| [08:11 最新报表](metrics-enterprise-gye-gye6666-20261106-0811-refresh-raw.json) / [08:11 单日响应](metrics-enterprise-gye-gye6666-20261105-one-day-20261106-raw.json) | 最新报表仅至 11 月 4 日，单日接口为 HTTP 200 / `""` |
| [10:10 最新报表](metrics-enterprise-gye-gye6666-20261106-1009-refresh-raw.json) / [10:10 单日响应](metrics-enterprise-gye-gye6666-20261105-one-day-20261106-1009-raw.json) | 上午第二次查询仍无可下载的 11 月 5 日用户记录 |
