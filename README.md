# GitHub Copilot 本地测试与 API 数据对照

整理日期：2026-10-28；最后更新：2026-10-30。本文使用已保存的测试证据及 API 快照，各次查询时间分别记录。

记录范围：第 1 至 8 节为 **10 月 26 日四组测试**；第 9 节为 **10 月 28 日单独 CL 重测**。两天的客户端版本和数据分别记录，不合并对账。

**10 月 26 日对照结果：四次本地操作实际新增 320 行、删除 80 行；API 对该用户 UTC 10 月 26 日的统计为新增 160 行、删除 40 行。已观察到汇总数值差异，但无法确认 API 中的行数具体来自哪次操作。**

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

### 失败尝试与环境变化

09:31 的第一次受控请求使用 VS Code **1.142.0 / Copilot Chat 0.70.0**，请求 ID 为 `request_a570ca2c-685f-4764-83e8-36fdb963d859`。扩展日志报告：

> Failed to get copilot token due to: Your Copilot access has been revoked by the providing organization or enterprise. You are currently logged in as gye6666.

该次请求没有修改文件，之后仍核对到 25 行 CRLF 基线及原始哈希。用户处理 VS Code 问题并重启后，安装版本已变为 **1.143.1 / 0.71.0**，重新登录账号仍是 `gye6666`。成功测试是在另一个新 Chat 中、单独获得发送批准后执行，未重置或手工修正 Agent 输出。

因此，本轮不是与 26 日完全相同客户端版本的重复实验。10 月 22、23、26 日共 12 个历史测试文件及 LoC Probe 定义均保持不变。

原始失败会话里还观察到 Try Again 和问候消息，因此不能假定该账号当天只有一次交互。本轮自动化自身提交了两次受控 CL 请求，其中一次失败、一次成功编辑；其他消息不能混入“成功文件编辑次数”。

成功执行时状态栏仍显示过 `Quota reached`，但实际任务完成并写入了预期内容；该提示不能在本次记录中代替执行结果。未修改预算、企业权限或自定义 Agent 定义。

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

以下是原始报表用户记录中的完整 `totals_by_feature` 字段，仅做格式化。**完整目标用户原始数据**见 [10 月 30 日 API 快照](metrics-enterprise-gye-gye6666-20261030-0853-refresh-raw.json)；该文件保留 28 天范围内 `gye6666` 的全部原始记录，查看本轮时选择 `day == "2026-10-28"`，不要混入 26 日的数据。

```json
{
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
