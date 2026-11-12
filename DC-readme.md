# GitHub Copilot DC 独立测试：新增 400 行、删除 10 行

测试日期及整理日期：**2026-11-12**。本文件单独记录本轮 DC 测试，不覆盖 [历史中文 README](README.md) 或 [历史英文 README](README.en.md)。

**本地结果：DC（Copilot Harness / 默认 Agent）成功新增 400 行、删除 10 行，净增 390 行。界面与落盘计数一致，已点击 Allow Once 和 Keep All Edits。仅提交 1 个受控任务，没有手工补改 Agent 输出。API 对账尚未执行，服务端行数未知，不是 0 / 0。**

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

最终公式为 **15 + 400 - 10 = 405 行**。新增 400 行、删除 10 行与净增 390 行是不同指标，后续 API 必须分别对账。

| 证据 | 值 |
|---|---|
| 基线采集时间 | `2026-11-12T08:53:53.824641+08:00` |
| Keep 后最终采集时间 | `2026-11-12T08:57:15.037493+08:00` |
| 基线 SHA-256 | `25c636f128c6828ab64519734c73a4475145eee8d0bfcdc74b71d37a8e78bf29` |
| 最终 SHA-256 | `4053abdf45daa35f0d8a7d6f4fe587973f6062c1c6932b83830c1612d99bf898` |
| 历史文件保留 | **25 项原有 Python 文件及 Agent 定义的合计哈希记录均未变化**，不包含本轮新文件 |
| 本地 acceptance 日志 | 采集到 **0 条**，不等于事件没有产生或上传 |

## 3. API 对账状态与后续时间

**当前仅完成客户端测试。没有在本轮测试日调用 Metrics API，因此没有本轮活动日的原始服务端用户记录，也没有服务端 LoC 数值可填。**

| 对账项 | 当前状态 |
|---|---|
| 用户 / 企业 | `gye6666` / `gye` |
| API 活动日期 | **UTC 2026-11-12** |
| 对应北京时间区间 | `[2026-11-12 08:00, 2026-11-13 08:00)` |
| 本地受控编辑量 | **新增 400 / 删除 10 / 净增 390**，已 Keep |
| API 实际新增 / 删除 | **未知（`null` / `null`），不是 0 / 0** |
| 本地与 API 差值 | **待查询，不能先计算为 400 / 10** |
| 两天后首次复查 | **11 月 14 日，用户手动要求查询**；原拟 09:00 仅作时间参考，未创建日程 |
| 两个完整 UTC 日窗口结束 | **2026-11-15 08:00 北京时间** |
| 三个完整 UTC 日保守复查节点 | **2026-11-16 08:00 北京时间** |
| 自动任务 / 提醒 | **未创建**，用户选择两天后手动发起 |

**“两天后”与“活动日结束后两个完整 UTC 日”不同。** 11 月 14 日可以先查，但它早于上述完整处理窗口；未覆盖、空响应、没有目标用户记录或仍在处理的情况必须分别记录，不能一律解释为 LoC 为零。这些日期也不保证届时报表更新、回填或最终定稿。

### 后续使用的接口

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

以上是**后续查询目标，不是本轮已完成的调用**。查询时先检查认证、HTTP 状态和真实响应结构，再确认报表覆盖日期，筛选 `user_login == "gye6666"` 和 `day == "2026-11-12"`。

后续原始 JSON 应完整保留目标用户记录的所有字段和值，另外记录查询时间和接口。不得上传凭据、签名下载 URL、其他用户数据或完整客户端日志。

## 4. 后续重点分析与解释限制

| 核对项 | 分析口径 |
|---|---|
| 顶层实际 LoC | 首先读取用户日 `loc_added_sum` / `loc_deleted_sum`，与 **400 / 10** 对照 |
| 功能分类 | 分别查看 `chat_panel_agent_mode`、`agent_edit` 的实际增删、建议、生成与接受活动 |
| 可能重叠 | 如果两个 feature 各出现 400 / 10，且顶层为 800 / 20，应记录异常线索，**不能直接宣布同一次编辑被重复计算** |
| 不同维度 | IDE、语言、模型和 feature 是不同统计视角，不能相加重复计数 |
| 交互 / session | 本地受控任务提交 1 次；用户日汇总可能含其他活动，交互数不能机械等同于本轮提交次数 |
| 版本 | 保存 API 实际返回的 IDE 和插件名称 / 版本，不用本地 `copilot-chat 0.73.0` 替代 `unknown` 或其他组件 |
| 逐请求归因 | 目前没有 SDK 请求 ID，日汇总也不能提供本次编辑的逐事件映射 |
| 遥测状态 | 本地落盘、Allow Once、Keep 及日级遥测标志都不能单独证明具体受控请求已正确接收和归因 |

历史 11 月 5 日 DC 测试的本地修改量为 **80 / 20**，11 月 6 日快照中的用户日总计为 **160 / 40**，其中 `chat_panel_agent_mode` 和 `agent_edit` 各为 **80 / 20**。该现象是本轮关注分类与可能重叠计数的原因，不是已确认重复遥测的证据；详见 [历史对账记录](loc-ui-test-20261105-dc-server-comparison-20261106-1404.json)。

本轮修改量、基线、IDE / 扩展和模型均与前轮 DC 不同，**不是同环境 A/B 对照**。前轮没有重新核验 SDK / runtime 的具体版本，不能认定它们与本轮相同或不同。同日其他活动未完全排除，不提前认定默认 / 自定义 Agent、Keep 或 Harness 的根因。

## 5. 本轮证据

| 文件 | 用途 |
|---|---|
| [loc-ui-test-20261112-dc.json](loc-ui-test-20261112-dc.json) | 场景、账号、版本、单次提交、Allow / Keep、实际计数及待对账状态 |
| [loc-ui-test-20261112-baseline-observed.json](loc-ui-test-20261112-baseline-observed.json) | 修改前 15 行 CRLF 基线、预期最终内容及哈希 |
| [loc-ui-test-20261112-observed.json](loc-ui-test-20261112-observed.json) | Keep 后实际 405 行内容、差异、语法和哈希 |
| [loc-ui-test-20261112-preserved-files.json](loc-ui-test-20261112-preserved-files.json) | 本轮开始前的 25 项历史文件及 Agent 定义哈希，结束后逐项核对未变 |
| [DC 测试文件](copilot-loc-test/default_copilot_20261112.py) | 默认 Agent 实际写入的合成文件，不是脚本生成的最终结果 |

## 6. 官方参考

- [Copilot usage metrics REST API](https://docs.github.com/en/rest/copilot/copilot-usage-metrics)
- [Lines of Code metrics](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/lines-of-code-metrics)
- [Reconciling usage metrics](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/reconciling-usage-metrics)
- [VS Code agent harnesses](https://code.visualstudio.com/docs/agents/run/agent-harnesses)
