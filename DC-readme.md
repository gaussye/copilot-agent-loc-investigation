# GitHub Copilot DC 独立测试：新增 400 行、删除 10 行

测试日期：**2026-11-12**；API 对账更新：**2026-11-16**。本文件单独记录本轮 DC 测试，不覆盖 [历史中文 README](README.md) 或 [历史英文 README](README.en.md)。

**本地结果：DC（Copilot Harness / 默认 Agent）新增 400 行、删除 10 行，净增 390 行，已 Allow Once 并 Keep。11 月 16 日 API 返回该用户日新增 800 行、删除 20 行：`agent_edit` 与 `chat_panel_agent_mode` 各为 400 / 10。日总计是本地受控编辑量的两倍，两个接口活动字段一致；疑似重叠归因，但尚不能证明同一次编辑被重复计算。**

## 1. 账号、环境与执行记录

除明确注明 UTC 或保留时区的 ISO 时间戳外，下列时间均为北京时间（UTC+8）。

| 项目 | 本轮记录 |
|---|---|
| 测试标识 | `20261112-DC-400-10` |
| 场景 | **DC：Copilot Harness / 内置默认 Agent** |
| Harness 含义 | Copilot SDK / Agent Host，本机合成工作区，不是 Cloud，也不是 Local Harness |
| 测试账号 / API 对账目标用户 | `gye6666` |
| 后续查询企业 | `gye` |
| 后续 API 读取账号 | `gaussye`，使用现有本地 GitHub CLI 凭据；读取账号与测试账号不同 |
| 账号核验 | Accounts 菜单显示 `gye6666`；扩展登录日志为 `2026-11-12 08:46:56.346` |
| VS Code | **1.145.3 (user setup)，Windows ARM64** |
| VS Code commit | `dcd3ad6e368fbe19d03f37054a40dbaf40d676f4` |
| 内置 Copilot 扩展 | **`GitHub.copilot-chat 0.73.0`** |
| `@github/copilot` | `1.0.103.r41379698026.g3219c66` |
| `@github/copilot-sdk` | `1.0.27.41379698026.g3219c66` |
| 版本核验来源 | IDE / runtime / SDK：Help > About；扩展：运行资源目录内置 `copilot` 的包元数据 |
| 模型 / Effort / Context | **GPT-6.1 Sol / Medium / 272K** |
| 测试入口 | VS Code 普通编辑器窗口的 Chat，使用新的会话 |
| 模式 / 权限 | **Interactive / Manual permissions** |
| 操作方式 | GitHub Copilot App 内置 Computer Use |
| 文件 | `copilot-loc-test\default_copilot_20261112.py`，独立新文件 |
| 发送时间 | **2026-11-12 08:55** |
| 界面完成时间 | **2026-11-12 08:56**，显示耗时 **39 秒** |
| 界面工具步骤 | **Read + Edit**，显示 2 个工具步骤，不是 2 次任务提交 |
| 写入确认 | `Write file?` 仅包含目标文件，预览 **+400 / -10** |
| 写入前操作 | 点击 **Allow Once**，未授予整场会话写入权限 |
| 完成后操作 | 点击 **Keep All Edits**，Keep / Undo 控件随后消失 |
| 任务提交次数 | **1 次**，没有重复发送或混入其他受控场景 |
| SDK 请求 ID | 未在检查的 Copilot 扩展日志中捕获，记录为 `null`，不表示没有请求 |

新 Chat 初始化时恢复过 LoC Probe，发送前已在 Agent 选择菜单显式选择内置默认 **Agent**；未向自定义 Agent 提交本轮测试。模型使用本轮界面实际显示的 GPT-6.1 Sol，不沿用旧测试的 GPT-6 Luna 标签。

本轮未主动升级、重启、切换账号、修改企业策略或执行 customization migration。账号菜单和登录日志核验不等于已验证单次请求的服务端身份或遥测送达。

## 2. 修改规格与真实落盘结果

### 基线与任务

为精确测量 **删除 10 行**，本轮新文件从 **15 行 CRLF 基线**开始，其中有 10 条旧字典项：

```python
VALUES = {
    "old_01": 1,
    "old_02": 2,
    "old_03": 3,
    "old_04": 4,
    "old_05": 5,
    "old_06": 6,
    "old_07": 7,
    "old_08": 8,
    "old_09": 9,
    "old_10": 10,
}

def total():
    return sum(VALUES.values())
```

任务要求内置文件编辑工具把这 10 条旧项替换为 **400 条字面字典项**，从 `"new_001": 1,` 到 `"new_400": 400,`，按升序排列，每行 4 空格缩进、双引号键、整数值和尾随逗号。其余 5 行及 CRLF 保持不变。

明确禁止终端、脚本、MCP、子 Agent、skills 和 handoffs；不接受仅输出代码块而不实际编辑。测试后的脚本只用于只读差异、哈希和语法分析，没有执行被测 Python 文件，也没有修正 Agent 输出。

| 指标 | 请求目标 | 界面观察 | Keep 后实际文件 |
|---|---:|---:|---:|
| 新增行数 | **400** | **400** | **400** |
| 删除行数 | **10** | **10** | **10** |
| 净增行数 | **390** | 400 - 10 | **390** |
| 文件行数 | 15 → 405 | 1 个文件变更 | **405** |
| CRLF 数量 | 405 | 不以界面推断 | **405** |
| 内容匹配预期 | 精确匹配 | 不以完成文本替代 | **完全一致** |
| Python 语法 | 有效 | 不以界面推断 | **语法分析无错误** |

最终公式为 **15 + 400 - 10 = 405 行**。新增 400 行、删除 10 行与净增 390 行是不同指标，下节 API 分别对账。

| 证据 | 值 |
|---|---|
| 基线采集时间 | `2026-11-12T08:53:53.824641+08:00` |
| Keep 后最终采集时间 | `2026-11-12T08:57:15.037493+08:00` |
| 基线 SHA-256 | `25c636f128c6828ab64519734c73a4475145eee8d0bfcdc74b71d37a8e78bf29` |
| 最终 SHA-256 | `4053abdf45daa35f0d8a7d6f4fe587973f6062c1c6932b83830c1612d99bf898` |
| 历史文件保留 | **25 项原有 Python 文件及 Agent 定义的合计哈希记录均未变化**，不包含本轮新文件 |
| 本地 acceptance 日志 | 采集到 **0 条**，不等于事件没有产生或上传 |

## 3. API 对账结果：2026-11-16

本次使用本地 GitHub CLI 已存凭据，确认读取账号 `gaussye`，查询企业 `gye`，仅保留目标用户 `gye6666` 的完整原始记录。**latest 和单日接口均为 HTTP 200、正常报表对象，且各含 1 条目标用户的 11 月 12 日记录，不是空响应或缺记录。**

| 对账项 | 本次结果 |
|---|---|
| 用户 / 企业 | `gye6666` / `gye` |
| API 活动日期 | **UTC 2026-11-12** |
| 对应北京时间区间 | `[2026-11-12 08:00, 2026-11-13 08:00)` |
| 本地受控编辑量 | **新增 400 / 删除 10 / 净增 390**，已 Keep |
| API 实际新增 / 删除 / 净增 | **800 / 20 / 780** |
| API − 本地（新增 / 删除 / 净增） | **+400 / +10 / +390** |
| API / 本地 | 新增、删除均为 **2 倍**，不是“接受率 200%” |
| latest 查询时间 | `2026-11-16T04:58:22.482530+00:00`，北京时间 **12:58:22** |
| latest 覆盖日期 | **UTC 2026-10-19 至 2026-11-15** |
| 单日查询时间 | `2026-11-16T04:58:22.307301+00:00` |
| 两接口比对 | 仅排除 `report_start_day` / `report_end_day` / `report_day` 元数据，**其余全部用户活动字段相同** |
| 处理窗口 | 已过 11 月 15 日 08:00 的两个完整 UTC 日节点，也已过 11 月 16 日 08:00 的三个完整 UTC 日参考节点 |
| 手动复查 / 自动任务 | 实际于 **11 月 16 日**手动复查；原拟 11 月 14 日未执行，没有创建自动任务 |

### 3.1 功能分类与日总计

| feature / 汇总 | 实际新增 / 删除 | 建议新增 / 删除 | 用户交互 | generation | acceptance |
|---|---:|---:|---:|---:|---:|
| `chat_panel_agent_mode` | **400 / 10** | 400 / 10 | 2 | 1 | 1 |
| `agent_edit` | **400 / 10** | 0 / 0 | 0 | 1 | 0 |
| **顶层用户日总计** | **800 / 20** | **400 / 10** | **2** | **2** | **1** |

**800 / 20 直接来自原始顶层字段**，不是把 IDE、语言、模型等不同维度相加，也不是把建议行数与实际行数相加。两个 feature 各自的实际 400 / 10 合计与顶层一致；单独看 `agent_edit` 则数值等于本地受控编辑量，但日汇总没有逐事件映射，不能证明该行仅由本次任务贡献。

### 3.2 版本、遥测与归因

| API 字段 | 实际返回 / 解释 |
|---|---|
| IDE | `vscode` / **1.145.3** |
| 插件 | **`plugin: "unknown"`、`plugin_version: "unknown"`**，不以本地扩展 0.73.0 替换 |
| 版本采样时间 | `2026-11-12T01:01:40.290Z`，北京时间 09:01:40 |
| 模型 feature 标签 | `gpt-6.1-sol` |
| 语言 feature | chat 的 400 / 10 为 `python`；`agent_edit` 的 400 / 10 为 `unknown` |
| `totals_by_client` | `vscode`，**5 requests / 2 sessions**；不等于发送了 5 次或 2 次受控任务 |
| `totals_by_custom_agent` / distinct | `[]` / `0`；本轮使用默认 Agent，不据此判断 custom 是否受支持 |
| `client_telemetry_present` | `true` |
| `usage_metrics_requirements` | `met: ["vscode"]`，`unmet: []`，`unknown: []` |

本地只提交 1 个受控任务。日级交互 2、请求 5、session 2 的口径不同，其他当日活动未完全排除；没有 SDK 请求 ID 或逐编辑事件证明 feature 间的关系。本地 acceptance 日志捕获 0 条，与这次日级 API 有非零 LoC 不矛盾，也不能据此验证本次请求的遥测完整送达。

### 3.3 本次使用的接口

```http
GET https://api.github.com/enterprises/gye/copilot/metrics/reports/users-28-day/latest
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

```http
GET https://api.github.com/enterprises/gye/copilot/metrics/reports/users-1-day?day=2026-11-12
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

以上两次调用均已完成，使用 API 版本 `2026-03-10`，按 `user_login == "gye6666"` 和 `day == "2026-11-12"` 对账。完整原始记录及查询时间见下节证据，不含凭据、签名下载 URL 或其他用户。

### 3.4 完整原始用户日记录

以下从 [11 月 12 日单日报表](metrics-enterprise-gye-gye6666-20261112-one-day-20261116-raw.json) 原样保留目标用户记录的所有字段和值，仅调整 JSON 排版；不是分析摘要。

<details>
<summary>展开 2026-11-12 用户日完整 JSON</summary>

```json
{
  "user_id": 274194146,
  "user_login": "gye6666",
  "day": "2026-11-12",
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
        "sampled_at": "2026-11-12T01:01:40.290Z",
        "plugin": "unknown",
        "plugin_version": "unknown"
      },
      "last_known_ide_version": {
        "sampled_at": "2026-11-12T01:01:40.290Z",
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
  "ai_credits_used": 13.95458,
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
      "request_count": 5,
      "session_count": 2,
      "token_usage": {
        "avg_tokens_per_request": 20993.8,
        "output_tokens_sum": 4314,
        "prompt_tokens_sum": 100655
      },
      "totals_by_model": [
        {
          "model": "gpt-6.1-sol",
          "model_selection_mode": "user_selected",
          "is_custom_enterprise_model": false,
          "request_count": 3,
          "token_usage": {
            "avg_tokens_per_request": 34607.67,
            "output_tokens_sum": 4304,
            "prompt_tokens_sum": 99519
          }
        },
        {
          "model": "unknown",
          "model_selection_mode": "user_selected",
          "is_custom_enterprise_model": false,
          "request_count": 2,
          "token_usage": {
            "avg_tokens_per_request": 573.0,
            "output_tokens_sum": 10,
            "prompt_tokens_sum": 1136
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

## 4. 分析结论与解释限制

| 核对项 | 分析口径 |
|---|---|
| 顶层实际 LoC | 首先读取用户日 `loc_added_sum` / `loc_deleted_sum`，与 **400 / 10** 对照 |
| 功能分类 | 分别查看 `chat_panel_agent_mode`、`agent_edit` 的实际增删、建议、生成与接受活动 |
| 可能重叠 | 实际已出现两个 feature 各 400 / 10、顶层 800 / 20；这是异常线索，**不能直接宣布同一次编辑被重复计算** |
| 不同维度 | IDE、语言、模型和 feature 是不同统计视角，不能相加重复计数 |
| 交互 / session | 本地受控任务提交 1 次；用户日汇总可能含其他活动，交互数不能机械等同于本轮提交次数 |
| 版本 | 保存 API 实际返回的 IDE 和插件名称 / 版本，不用本地 `copilot-chat 0.73.0` 替代 `unknown` 或其他组件 |
| 逐请求归因 | 目前没有 SDK 请求 ID，日汇总也不能提供本次编辑的逐事件映射 |
| 遥测状态 | 本地落盘、Allow Once、Keep 及日级遥测标志都不能单独证明具体受控请求已正确接收和归因 |

历史 11 月 5 日 DC 测试的本地修改量为 **80 / 20**，11 月 6 日快照中的用户日总计为 **160 / 40**，其中 `chat_panel_agent_mode` 和 `agent_edit` 各为 **80 / 20**。该现象是本轮关注分类与可能重叠计数的原因，不是已确认重复遥测的证据；详见 [历史对账记录](loc-ui-test-20261105-dc-server-comparison-20261106-1404.json)。

本次 latest 快照中该历史 DC 仍为 160 / 40。**11 月 13 日 CC 也返回 800 / 20，且两个 feature 各为 400 / 10**，详见 [CC-readme.md](CC-readme.md)。因此，双 feature 等量计数现象已经出现在不同编辑量的 DC，以及自定义 Agent 的 CC；这增强了“可能重叠报告 / 重复归因”的调查线索，但不支持“只有默认 Agent 有 LoC、自定义 Agent 一概没有”的解释。

[官方 LoC 文档](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/lines-of-code-metrics)（11 月 16 日复核）把直接文件编辑列入 `agent_edit`，chat 的实际 LoC 则说明为代码块 Copy / Apply。本次受控任务未手动复制或应用聊天代码块，双分类与受控操作之间的对应关系应进一步核查；**Keep 与 chat 分类同时出现不证明 Keep 导致重复统计**。官方也提醒遥测 LoC 不保证与编辑器 diff 相等，不能仅凭 2 倍就定性为产品缺陷。

[官方 SDK 覆盖说明](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/usage-metrics-coverage)列出的 VS Code 身份缺失区间为 1.134–1.138 的 agent window；本轮是普通编辑器 Chat、1.145.3，不在该已知版本区间，不能直接套用为根因。API 插件版本仍应保留 `unknown`。

本次已过三个完整 UTC 日参考节点，不能只说“测试日尚未进入报表”；但经过时间和报表可用也不保证数据最终定稿。后续可用逐请求 / 逐编辑遥测核对两类事件，当前根因未确认。本次仅查询与更新证据，没有重跑编辑或修改历史被测文件。

本轮修改量、基线、IDE / 扩展和模型均与前轮 DC 不同，**不是同环境 A/B 对照**。前轮没有重新核验 SDK / runtime 的具体版本，不能认定它们与本轮相同或不同。同日其他活动未完全排除，不提前认定默认 / 自定义 Agent、Keep 或 Harness 的根因。

## 5. 本轮证据

| 文件 | 用途 |
|---|---|
| [loc-ui-test-20261112-dc.json](loc-ui-test-20261112-dc.json) | 场景、账号、版本、单次提交、Allow / Keep、实际计数及本次对账结果 |
| [11 月 16 日对账分析](loc-ui-test-20261112-dc-server-comparison-20261116.json) | 双接口一致性、功能分类、800 / 20 与本地 400 / 10 的差异及限制 |
| [11 月 12 日单日原始 API](metrics-enterprise-gye-gye6666-20261112-one-day-20261116-raw.json) | 唯一目标用户日完整记录及调用元数据 |
| [11 月 16 日 latest 原始 API](metrics-enterprise-gye-gye6666-20261116-followup-raw.json) | 同次覆盖 10 月 19 日至 11 月 15 日的目标用户记录，供历史对照 |
| [loc-ui-test-20261112-baseline-observed.json](loc-ui-test-20261112-baseline-observed.json) | 修改前 15 行 CRLF 基线、预期最终内容及哈希 |
| [loc-ui-test-20261112-observed.json](loc-ui-test-20261112-observed.json) | Keep 后实际 405 行内容、差异、语法和哈希 |
| [loc-ui-test-20261112-preserved-files.json](loc-ui-test-20261112-preserved-files.json) | 本轮开始前的 25 项历史文件及 Agent 定义哈希，结束后逐项核对未变 |
| [DC 测试文件](copilot-loc-test/default_copilot_20261112.py) | 默认 Agent 实际写入的合成文件，不是脚本生成的最终结果 |

## 6. 官方参考

- [Copilot usage metrics REST API](https://docs.github.com/en/rest/copilot/copilot-usage-metrics)
- [Lines of Code metrics](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/lines-of-code-metrics)
- [Reconciling usage metrics](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/reconciling-usage-metrics)
- [VS Code agent harnesses](https://code.visualstudio.com/docs/agents/run/agent-harnesses)
