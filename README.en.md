# GitHub Copilot: Local Test Results and API Metrics Reconciliation

[Chinese version](README.md)

Originally compiled: **2026-10-28**. Evidence last updated: **2026-11-06**. English version prepared: **2026-11-06**. This document uses the saved test evidence and API snapshots; individual query times are recorded below. The DC reconciliation now includes the November 6 14:04 Beijing-time API snapshots.

Sections 1-8 cover the **October 26 four-case test**; section 9 covers the **October 28 isolated CL retest**; section 10 covers the **October 30 isolated DL test**; section 11 covers the **November 2 isolated CC test**; section 12 covers the **November 5 isolated DC test**. Client versions and results are kept separate by test date.

**October 26: four local operations added 320 lines and deleted 80 lines. The API reported 160 added and 40 deleted for the user on that UTC day. The aggregate discrepancy is confirmed, but the API does not identify which operations contributed those lines.**

**Latest reconciliation, queried November 5: the November 2 CC user-day totals are +80/-20, matching the controlled local edit numerically. However, all activity is classified as `chat_panel_agent_mode`, with no custom-agent attribution. In the same latest snapshot, October 28 CL and October 30 DL still report actual LoC of 0/0. See section 11.**

**November 6 14:04 update: the November 5 DC report is available. Actual user-day LoC is +160/-40, with +80/-20 in each of `chat_panel_agent_mode` and `agent_edit`. The single controlled local edit was +80/-20, so the daily total exceeds it by +80/-20. Overlapping reporting or duplicate attribution is a possibility, not a confirmed finding; other same-day activity has not been excluded. See section 12.**

All dates are in **2026**. Local submission and observation times are **Beijing time (UTC+8)** unless explicitly marked UTC. ISO timestamps retain their original offsets.

## 1. Account, client versions, and reporting date

| Item | October 26 test record |
|---|---|
| Test account / API reporting subject | `gye6666` |
| Enterprise slug | `gye` |
| API reader | `gaussye`, using the existing local GitHub CLI login; different from the test account |
| VS Code | **1.142.0 ARM64** |
| GitHub Copilot Chat extension | **0.70.0**, verified locally |
| Model | GPT-6 Luna |
| Effort / Context | Medium / 272K |
| Entry point | Chat in the ordinary VS Code editor window, not the dedicated Agents window |
| Harnesses | Local and Copilot tested separately; Copilot means SDK / Agent Host here, not Cloud |
| Custom agent | `LoC Probe` |
| UI automation | GitHub Copilot App's built-in Computer Use |
| API version header | `X-GitHub-Api-Version: 2026-03-10` |
| API activity date | `2026-10-26`, aggregated by UTC day |
| Corresponding Beijing-time interval | `[2026-10-26 08:00, 2026-10-27 08:00)` |

The VS Code Accounts menu and extension login log confirmed `gye6666` before testing. This does not independently verify SDK server-side identity or telemetry delivery for each request.

The API reports IDE version `1.142.0`, but both the plugin name and version are `unknown`. **The locally verified extension version `0.70.0` and the API plugin version `unknown` must remain separate.**

## 2. Four local operations

The actual execution order was **CL -> CC -> DC -> DL**. Each case used a fresh chat and a separate synthetic file. Agent output was not manually corrected.

| Case | Submitted at, Beijing time | Harness | Agent | Added | Deleted | Acceptance / confirmation |
|---|---|---|---|---:|---:|---|
| CL | 2026-10-26 09:26 | Local | Custom LoC Probe | 80 | 20 | Keep All Edits clicked |
| CC | 2026-10-26 09:30 | Copilot | Custom LoC Probe | 80 | 20 | Single-write Allow clicked; no Keep step |
| DC | 2026-10-26 09:33 | Copilot | Default Agent | 80 | 20 | No write confirmation or Keep requiring a click was observed |
| DL | 2026-10-26 09:35 | Local | Default Agent | 80 | 20 | Keep All Edits clicked |
| **Local total** | | | | **320** | **80** | **Net +240 lines** |

### Files and local request identifiers

All files are in the synthetic `copilot-loc-test` workspace.

| Case | File | Locally captured request ID |
|---|---|---|
| CL | `custom_local_20261026.py` | `request_caf3b645-d531-4dd1-8140-ba1e12b6912c` |
| CC | `custom_copilot_20261026.py` | Not captured; does not imply no request occurred |
| DC | `default_copilot_20261026.py` | Not captured; does not imply no request occurred |
| DL | `default_local_20261026.py` | `request_59e2ebe8-c4a5-4a19-8792-3401306f687e` |

### Basis for local line counts

Every file started with the same 25-line CRLF baseline. Each edit replaced 20 dictionary entries, `old_01` through `old_20`, with 80 entries, `new_01` through `new_80`, leaving surrounding content unchanged.

All four UI counts matched the differences in the files on disk. Each final file contained 85 CRLF lines, had valid syntax, and exactly matched the expected content. Added and deleted lines are counted separately: net +60 is not the added-line count.

| Evidence | Value |
|---|---|
| Baseline collection | `2026-10-26T09:24:58.431439+08:00` |
| Final collection | `2026-10-26T09:36:03.208235+08:00` |
| Initial SHA-256, each file | `6298c00f97ffc8ecc7c34ba09dc14c021d83cf0ab507d3debd1be2d1d41e6bfe` |
| Final SHA-256, each file | `4c3675924615acad6d2074fd14baaae5114afa2fb89b8bd914f97c2ddc6c4fdd` |

The custom-agent definition was unchanged, no customization migration was applied, and the eight historical test files from October 22 and 23 were preserved.

## 3. API endpoints and query times

Host: `https://api.github.com`.

### Latest 28-day user report

```http
GET /enterprises/gye/copilot/metrics/reports/users-28-day/latest
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

### User report for a specific day

```http
GET /enterprises/gye/copilot/metrics/reports/users-1-day?day=2026-10-26
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

Existing authorization was used; credentials are not included in this document. The REST response provides download links. The NDJSON report is then downloaded and filtered to `user_login == "gye6666"`.

| Query time, Beijing time | Endpoint | HTTP | Reporting period | Covers the test day? |
|---|---|---:|---|---|
| 2026-10-26 09:24:28 | `users-28-day/latest` | 200 | 2026-09-27 through 2026-10-24 | No; pre-test reference |
| 2026-10-28 08:45:20 | `users-28-day/latest` | 200 | 2026-09-29 through 2026-10-26 | Yes |
| 2026-10-28 08:49:03 | `users-1-day?day=2026-10-26` | 200 | 2026-10-26 | Yes |

The single-day report contained **one user-day record** for this account. Other than the additional reporting-period fields in the 28-day report, it matched the corresponding record in the 08:45 snapshot.

## 4. API results

These results are from the single-day report queried at 2026-10-28 08:49:03 Beijing time.

| Metric | API result |
|---|---|
| Default-agent mode: `chat_panel_agent_mode` | 1 interaction |
| Custom-agent mode: `chat_panel_custom_mode` | 1 interaction |
| IDE / Plugin | VS Code 1.142.0 / plugin name and version both `unknown` |
| `loc_added_sum` / `loc_deleted_sum` | **160 / 40** |
| `agent_edit` | **Present**; 2 generation activities, 160 added lines, 40 deleted lines |

### Feature breakdown

These are daily feature aggregates, not individual request records.

| `feature` | Interactions | Generation activities | Acceptance activities | Suggested added / deleted | Actual added / deleted |
|---|---:|---:|---:|---:|---:|
| `chat_panel_agent_mode` | 1 | 0 | 0 | 0 / 0 | 0 / 0 |
| `chat_panel_custom_mode` | 1 | 0 | 0 | 0 / 0 | 0 / 0 |
| `agent_edit` | 0 | 2 | 0 | 0 / 0 | 160 / 40 |

Other fields: `totals_by_custom_agent` is empty and `distinct_custom_agent_use_count` is 0. The `vscode` entry in `totals_by_client` reports `request_count = 18` and `session_count = 4`. These aggregates do not prove that each of the four controlled edits was recorded.

## 5. Reconciliation and limitations

| Actual edit LoC | Four local tests | API user-day total | Local minus API |
|---|---:|---:|---:|
| Added | 320 | 160 | 160 |
| Deleted | 80 | 40 | 40 |
| Net | 240 | 120 | 120 |

**Confirmed:** the snapshot covers the correct account and UTC activity date, but API added and deleted totals are each 50% of the controlled local totals. Both default and custom chat modes have interaction records; the actual edit LoC appears in `agent_edit`.

**Not established by this evidence:**

- The 160/40 cannot be assigned specifically to CL + DL. CC + DC produce the same totals, as do other pairs. The activity records do not expose a harness, request ID, session ID, or filename to associate those lines with individual operations.
- Zero actual LoC under `chat_panel_custom_mode` alone does not prove missing custom-agent file-edit telemetry. File edits are represented separately by `agent_edit` in this snapshot.
- Zero `code_acceptance_activity_count` does not imply zero actual edit LoC and is not the number of local Keep clicks.
- Absence of a Keep step alone does not prove that the Copilot harness cannot report LoC.
- Failure to capture an acceptance event in the local collector does not prove the event was never generated or uploaded.
- This is an observed discrepancy between a specific API snapshot and local evidence, not proof of permanent loss, a particular missing case, or a root cause. Other activity on the same UTC day and later report revisions have not been excluded.

### Attempt to obtain request-level details

A separate request was made at 2026-10-28 08:50:19 Beijing time:

```http
GET /enterprises/gye/copilot/usage-records
X-GitHub-Api-Version: 2026-03-10
```

It returned **404 `Not Found`** and no session details. The reason cannot be determined from the status alone. This is not the Metrics report endpoint that had been returning HTTP 200; the 404 must not be described as a failure of the original Metrics API. No enterprise policy or permission was changed during this attempt.

## 6. Complete raw API user record

The following is the **complete original user-day record for `gye6666` from the downloaded single-day report**, formatted as JSON with every field and value preserved. It is not a manually assembled REST download-link response and does not represent four request events.

Source snapshot: [metrics-enterprise-gye-gye6666-20261026-one-day-20261028-0848-raw.json](metrics-enterprise-gye-gye6666-20261026-one-day-20261028-0848-raw.json).

The snapshot wrapper's `queried_at_utc`, `endpoint`, `local_api_account`, `report_user`, `http_status`, and `matching_user_records` fields were added by the local collector. The block below contains only the original report record. Signed download URLs, tokens, and other users' records are not included.

<details>
<summary>Expand the complete raw user-day JSON record</summary>

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

## 7. Evidence index

| File | Purpose |
|---|---|
| [loc-ui-test-20261026.json](loc-ui-test-20261026.json) | Test manifest: account, environment, operations, confirmations, and reconciliation status |
| [loc-ui-test-20261026-baseline-observed.json](loc-ui-test-20261026-baseline-observed.json) | Pre-edit baselines for the four files |
| [loc-ui-test-20261026-observed.json](loc-ui-test-20261026-observed.json) | Final contents, differences, line counts, line endings, and hashes |
| [metrics-enterprise-gye-gye6666-20261026-baseline-raw.json](metrics-enterprise-gye-gye6666-20261026-baseline-raw.json) | October 26 pre-test API reference; does not cover the activity date |
| [metrics-enterprise-gye-gye6666-20261028-0845-refresh-raw.json](metrics-enterprise-gye-gye6666-20261028-0845-refresh-raw.json) | October 28 08:45 latest report, retaining all original fields for the target account |
| [metrics-enterprise-gye-gye6666-20261026-one-day-20261028-0848-raw.json](metrics-enterprise-gye-gye6666-20261026-one-day-20261028-0848-raw.json) | Single-day record queried October 28 at 08:49; `0848` is a preparation label, not the actual query time |
| [loc-ui-test-20261026-server-comparison-20261028.json](loc-ui-test-20261026-server-comparison-20261028.json) | Local/API numerical comparison and attribution limitations |
| [loc-ui-test-20261026-per-request-check-20261028.json](loc-ui-test-20261026-per-request-check-20261028.json) | Per-operation reconciliation status; unknown server values are `null`, not 0 |
| [copilot-usage-records-gye-probe-20261028-0850.json](copilot-usage-records-gye-probe-20261028-0850.json) | 404 from the separate Usage Records endpoint |

## 8. Official references

- [Copilot usage metrics REST API](https://docs.github.com/en/enterprise-cloud@latest/rest/copilot/copilot-usage-metrics?apiVersion=2026-03-10)
- [Lines of Code metrics](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/lines-of-code-metrics)
- [VS Code agent harnesses](https://code.visualstudio.com/docs/agents/run/agent-harnesses)
- [Copilot agent session streaming / Usage Records announcement](https://github.blog/changelog/2026-07-02-copilot-agent-session-streaming-is-now-in-public-preview/)

## 9. October 28 isolated CL retest

**November 2 follow-up:** October 28 still reports 5 custom-mode interactions, actual LoC 0/0, and no `agent_edit`, after the previously recorded conservative three-day checkpoint. The October 30 isolated default-agent test also reports zero edit LoC; see the same-snapshot comparison in section 10. The October 30 query and raw JSON below are preserved as historical evidence rather than overwritten.

### Local result

**CL (Local harness / custom LoC Probe) completed one successful edit, followed by Keep All Edits: 80 added lines, 20 deleted lines, net +60.**

| Item | Test record |
|---|---|
| Test account / Enterprise | `gye6666` / `gye` |
| Successful retry identifier | `20261028-CL-R2` |
| Submitted at, Beijing time | 2026-10-28 09:43; extension request log at `09:43:50.128` |
| Harness / Agent | Local / LoC Probe |
| VS Code / Copilot Chat | **1.143.1 / 0.71.0**, installation metadata after restart |
| Model / Effort / Context | GPT-6 Luna / Medium / 272K |
| File | `copilot-loc-test\custom_local_20261028.py` |
| UI counts | +80 / -20 |
| Actual difference on disk | +80 / -20 |
| File status | 25 to 85 lines, CRLF, valid syntax, exact expected content |
| Acceptance | Keep All Edits clicked; Keep / Undo controls then disappeared |
| Successful request ID | `request_a3f28585-fde1-4a2f-9281-69300e8e66b3` |
| Final evidence collection | `2026-10-28T09:45:44.194519+08:00` |
| Final SHA-256 | `4c3675924615acad6d2074fd14baaae5114afa2fb89b8bd914f97c2ddc6c4fdd` |

### API query timeline and reporting scope

The pre-test query at **2026-10-28 09:30:36 Beijing time** returned HTTP 200 for September 29 through October 26. It did not cover this test's UTC activity date.

Enterprise: `gye`. User: `gye6666`. Activity date: UTC `2026-10-28`, corresponding to `[2026-10-28 08:00, 2026-10-29 08:00)` Beijing time.

```http
GET https://api.github.com/enterprises/gye/copilot/metrics/reports/users-28-day/latest
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

| Query time, Beijing time | HTTP | Reporting period | Covers UTC October 28? |
|---|---:|---|---|
| 2026-10-28 09:30:36 | 200 | 2026-09-29 through 2026-10-26 | No; pre-test reference |
| 2026-10-29 10:36:14 | 200 | 2026-09-30 through 2026-10-27 | No; missing coverage is not zero LoC |
| **2026-10-30 08:53:48** | **200** | **2026-10-01 through 2026-10-28** | **Yes; one user-day record** |

### API results queried October 30

**The snapshot contains custom-mode interactions for October 28 but no `agent_edit`; actual edit LoC is 0/0.**

| Metric | API result for UTC 2026-10-28 |
|---|---|
| Default-agent mode: `chat_panel_agent_mode` | Not present |
| Custom-agent mode: `chat_panel_custom_mode` | **5 interactions** |
| IDE / Plugin | **VS Code 1.143.1 / Copilot Chat 0.71.0** |
| Model | `gpt-6-luna` |
| Generation / Acceptance activities | **0 / 0** |
| Suggested added / deleted | 0 / 0 |
| `loc_added_sum` / `loc_deleted_sum` | **0 / 0** |
| `agent_edit` | **Not present** |
| `used_agent` / `used_chat` | `false` / `true`; preserve the raw values rather than substituting them for the local execution evidence |
| `totals_by_custom_agent` | `[]` |
| `vscode` in `totals_by_client` | `request_count = 11`, `session_count = 1`; a different aggregate dimension, not request-level LoC |

The version sample is `2026-10-28T01:45:25.217Z`, or 09:45:25.217 Beijing time. It is a version-metadata sample, not the file-edit event timestamp or proof of edit ingestion.

### Complete raw API user-day record

The following is the **complete October 28 user-day record returned by the October 30 08:53:48 query**. All original fields and values are preserved, not just `totals_by_feature`. This is a downloaded NDJSON record, not the REST download-link response.

Source: [October 30 API snapshot](metrics-enterprise-gye-gye6666-20261030-0853-refresh-raw.json). That snapshot also contains other dates for the same account; only the October 28 record is shown here. Collector-added query metadata is not included in the block.

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

### Local/API comparison and interpretation

| Actual edit LoC | Successful local CL edit | API user-day total | Local minus API |
|---|---:|---:|---:|
| Added | 80 | 0 | 80 |
| Deleted | 20 | 0 | 20 |
| Net | 60 | 0 | 60 |

The successful local edit and Keep are confirmed, and the report contains custom-mode interactions, but the edit's LoC is not reflected in this snapshot. **This does not mean the final edit failed. The finding is not based solely on a zero chat bucket: actual LoC is zero throughout the user-day record and every returned breakdown, with no `agent_edit`.**

Execution and metrics are separate paths. Writing the file does not prove edit telemetry was generated, transmitted, received, classified, and aggregated. Successful request `apply_patch` metadata was observed locally, but no acceptance event was captured for this run. Missing log evidence does not prove that the event was never produced or uploaded.

The root cause remains unconfirmed. Potentially incomplete asynchronous processing must be distinguished from problems in edit-telemetry generation, transmission, classification, or aggregation. The evidence does not establish permanent loss, an upgrade regression, or that custom agents do not support file-edit metrics.

The successful controlled edit is **+80/-20**, but failed, retry, and greeting activity also occurred that day. **Five interactions do not mean five successful edits.** Daily interaction or generation totals need not equal one; other same-day activity has not been excluded. There are no request-level identifiers to verify delivery of the successful edit individually.

### Processing windows and follow-up

Report coverage does not imply that every field for that day is finalized. At the October 30 08:53 query, the two-full-UTC-day window below had not ended.

| Official guidance | Beijing-time checkpoint for UTC 2026-10-28 |
|---|---|
| [Data freshness](https://docs.github.com/en/copilot/concepts/billing-and-usage/copilot-usage-metrics/copilot-metrics#data-freshness): two full UTC days after the activity day closes | **2026-10-31 08:00** |
| [Delayed telemetry](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/reconciling-usage-metrics#delayed-telemetry): typically within three full UTC days; re-export after that window | **2026-11-01 08:00**, the more conservative checkpoint |

These are reconciliation checkpoints, not guarantees of a refresh or backfill. If actual edit LoC remains absent after the window, investigate using the successful request ID, local differences, Keep evidence, client versions, and subsequent raw API snapshots.

### Evidence

| File | Purpose |
|---|---|
| [loc-ui-test-20261028-cl.json](loc-ui-test-20261028-cl.json) | Initial failure, version change after restart, successful retry, request ID, and Keep evidence |
| [loc-ui-test-20261028-baseline-observed.json](loc-ui-test-20261028-baseline-observed.json) | New file's pre-edit baseline |
| [loc-ui-test-20261028-observed.json](loc-ui-test-20261028-observed.json) | Post-Keep contents, differences, line counts, line endings, and hash |
| [metrics-enterprise-gye-gye6666-20261028-cl-baseline-raw.json](metrics-enterprise-gye-gye6666-20261028-cl-baseline-raw.json) | 09:30 pre-test API snapshot, not yet covering October 28 |
| [metrics-enterprise-gye-gye6666-20261029-1035-refresh-raw.json](metrics-enterprise-gye-gye6666-20261029-1035-refresh-raw.json) | October 29 10:36 query, ending October 27; actual timestamp is in the file |
| [metrics-enterprise-gye-gye6666-20261030-0853-refresh-raw.json](metrics-enterprise-gye-gye6666-20261030-0853-refresh-raw.json) | October 30 08:53 query covering October 28, all original target-user fields retained |
| [loc-ui-test-20261028-cl-server-comparison-20261030.json](loc-ui-test-20261028-cl-server-comparison-20261030.json) | Local +80/-20 versus API 0/0, feature presence, and interpretation limits |

## 10. October 30 isolated DL test

**November 2 reconciliation:** both October 30 default-agent and October 28 custom-agent edits are +80/-20 locally, but both API days still report actual LoC 0/0 and no `agent_edit`. The missing-LoC observation is not exclusive to custom mode. The October 26 half-count cannot be attributed directly to exclusion of custom agents. The root cause remains unconfirmed.

### Local result

**DL (Local harness / default Agent) completed one file edit and Keep All Edits: 80 added lines, 20 deleted lines, net +60.** Only the default agent was tested on this separate UTC date, without another controlled custom-agent or Copilot-harness case.

| Item | Test record |
|---|---|
| Test account / Enterprise | `gye6666` / `gye` |
| Test identifier | `20261030-DL` |
| Submitted at, Beijing time | 2026-10-30 10:04; request log at `10:04:58.777` |
| Harness / Agent | **Local / default Agent** |
| VS Code / Copilot Chat | **1.143.1 / 0.71.0** |
| Model / Effort / Context | GPT-6 Luna / Medium / 272K |
| Entry point | Ordinary VS Code editor-window Chat; fresh session |
| File | `copilot-loc-test\default_local_20261030.py`, new and separate |
| Editing tool | Built-in `apply_patch` |
| UI counts / Actual difference on disk | Both **+80 / -20** |
| File status | 25 to 85 lines, CRLF, valid syntax, exact expected content |
| Acceptance | Keep All Edits clicked; Keep / Undo controls disappeared |
| Request ID | `request_9ae3199a-a208-4ab2-b9ad-217b59a06f81` |
| Baseline collection | `2026-10-30T10:01:25.008762+08:00` |
| Final evidence collection | `2026-10-30T10:05:51.965708+08:00` |
| Final SHA-256 | `4c3675924615acad6d2074fd14baaae5114afa2fb89b8bd914f97c2ddc6c4fdd` |
| Controlled prompts submitted by automation | **1**, with no duplicate submission |

The client versions, model, and Local harness matched the successful October 28 CL run; the selected agent differed, and this run used a separate date and file. Both used the same 25-line baseline and 20-to-80 entry replacement. Output was not manually corrected. The 13 historical test files and custom-agent definition were preserved.

### API query and reporting scope

At test time, the available report ended October 28 and could not reconcile this run. The latest report was retrieved again at **2026-11-02 11:00:19 Beijing time**:

```http
GET https://api.github.com/enterprises/gye/copilot/metrics/reports/users-28-day/latest
Accept: application/vnd.github+json
X-GitHub-Api-Version: 2026-03-10
```

| Query item | Result |
|---|---|
| Enterprise / Target user | `gye` / `gye6666` |
| API reader | `gaussye`, existing local GitHub CLI login |
| Query time, UTC | `2026-11-02T03:00:19.639276+00:00` |
| HTTP | **200** |
| Reporting period | **2026-10-04 through 2026-10-31** |
| Covers the test date? | **Yes, UTC 2026-10-30** |
| Target-user records for that day | **1 user-day record** |

The following single-day endpoint is an option for follow-up; it was **not** called for the November 2 reconciliation:

```http
GET https://api.github.com/enterprises/gye/copilot/metrics/reports/users-1-day?day=2026-10-30
X-GitHub-Api-Version: 2026-03-10
```

For either report, check coverage before selecting `user_login == "gye6666"` and `day == "2026-10-30"`.

| Reconciliation item | Scope |
|---|---|
| Activity date | **UTC 2026-10-30** |
| Beijing-time interval | `[2026-10-30 08:00, 2026-10-31 08:00)` |
| Controlled local edit | **80 added / 20 deleted**, kept |
| Feature checks | Actual added/deleted under `agent_edit`; default-mode interactions under `chat_panel_agent_mode` |
| Two-full-UTC-day window ends | **2026-11-02 08:00 Beijing time** |
| Conservative three-full-UTC-day checkpoint | **2026-11-03 08:00 Beijing time** |

At this November 2 query, the two-day window had ended but the conservative three-day checkpoint had not been reached. These dates do not guarantee refreshes or backfills. No automated query or reminder was created.

### API results queried November 2

| Metric | API result for UTC 2026-10-30 |
|---|---|
| Default-agent mode: `chat_panel_agent_mode` | **1 interaction** |
| Custom-agent mode: `chat_panel_custom_mode` | Not present |
| IDE / Plugin | **VS Code 1.143.1 / Copilot Chat 0.71.0** |
| Model | `gpt-6-luna` |
| Generation / Acceptance activities | **0 / 0** |
| Suggested added / deleted | 0 / 0 |
| `loc_added_sum` / `loc_deleted_sum` | **0 / 0** |
| `agent_edit` | **Not present** |
| `used_agent` / `used_chat` | `true` / `true` |
| `used_local_byok` | `false` |
| `vscode` in `totals_by_client` | `request_count = 6`, `session_count = 1`; not per-request edit details |

The version sample is `2026-10-30T02:05:41.380Z`, or 10:05:41.380 Beijing time. A matching version or sample time is not proof that this edit's telemetry was received or aggregated.

### CL/DL comparison and interpretation

Both API results below come from the **same latest report queried November 2 at 11:00**, not an older CL snapshot combined with a newer DL snapshot.

| UTC activity date | Case | Local added / deleted | Keep | API chat interactions | API actual added / deleted | `agent_edit` |
|---|---|---:|---|---|---:|---|
| 2026-10-28 | CL: Local / custom LoC Probe | 80 / 20 | Clicked | Custom mode: 5 | **0 / 0** | **Not present** |
| 2026-10-30 | DL: Local / default Agent | 80 / 20 | Clicked | Default mode: 1 | **0 / 0** | **Not present** |

**The default agent also shows successful local editing without edit LoC in the report. The observation cannot be described as custom-agent-only, and the October 26 +160/-40 cannot be assigned specifically to default-agent contributions.**

The DL discrepancy is 80 added and 20 deleted lines. Actual LoC is zero across every returned IDE, feature, language/feature, language/model, and model/feature breakdown, not just the chat-mode bucket.

In this refresh, CL remained 0/0 after its November 1 08:00 conservative checkpoint. DL had passed its two-day window and could be checked again after November 3 08:00. Execution, telemetry delivery, receipt, classification, and aggregation are different stages. The affected stage and possibility of later revisions remain unknown; permanent loss or a specific version regression is not established.

Other same-day activity has not been fully excluded, and the daily record cannot identify this edit individually. An uncaptured acceptance event does not prove it was never generated or uploaded.

### Complete raw API user-day record

This is the **complete UTC October 30 record returned by the November 2 query**, with every original field and value preserved. It is a downloaded NDJSON record, not a REST download-link response or the collector's outer wrapper.

Source: [November 2 raw API snapshot](metrics-enterprise-gye-gye6666-20261102-1100-refresh-raw.json). That file also contains October 28 CL; select the relevant `day` rather than combining dates to obtain this DL run's LoC.

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

### Evidence

| File | Purpose |
|---|---|
| [loc-ui-test-20261030-dl.json](loc-ui-test-20261030-dl.json) | Account, environment, default mode, request ID, local counts, and Keep evidence |
| [loc-ui-test-20261030-baseline-observed.json](loc-ui-test-20261030-baseline-observed.json) | 25-line CRLF pre-edit baseline |
| [loc-ui-test-20261030-observed.json](loc-ui-test-20261030-observed.json) | Post-Keep contents, differences, line counts, line endings, and hash |
| [DL test file](copilot-loc-test/default_local_20261030.py) | Synthetic file written by the default agent |
| [metrics-enterprise-gye-gye6666-20261102-1100-refresh-raw.json](metrics-enterprise-gye-gye6666-20261102-1100-refresh-raw.json) | November 2 11:00 target-user snapshot, including CL and DL |
| [loc-ui-test-20261030-dl-server-comparison-20261102.json](loc-ui-test-20261030-dl-server-comparison-20261102.json) | DL reconciliation and CL results from the same refresh |

## 11. November 2 isolated CC test

**November 5 reconciliation: both report endpoints returned HTTP 200. The November 2 user-day record reports 80 added and 20 deleted lines, numerically matching the local CC edit. However, all activity is classified as `chat_panel_agent_mode`, without `agent_edit`, `chat_panel_custom_mode`, or named custom-agent records. Matching line totals and correct custom-agent attribution are separate questions; request-level confirmation is unavailable.**

### Local result

**CC (Copilot harness / custom LoC Probe) completed one edit: 80 added lines, 20 deleted lines, net +60.** Allow Once was selected before writing; no Keep step appeared afterward. UI and on-disk counts matched.

| Item | Test record |
|---|---|
| Test account / Enterprise | `gye6666` / `gye` |
| Test identifier | `20261102-CC` |
| Submitted at, Beijing time | **2026-11-02 11:09** |
| Harness / Agent | **Copilot / custom LoC Probe** |
| Harness meaning | Copilot SDK / Agent Host, local workspace, not Cloud |
| VS Code / Copilot Chat | **1.143.1 / 0.71.0**, verified against the running UI's resource-version directory |
| Model / Effort / Context | GPT-6 Luna / Medium / 272K |
| Entry point / Mode / Permissions | Ordinary editor-window Chat / Interactive / Manual permissions |
| File | `copilot-loc-test\custom_copilot_20261102.py`, new and separate |
| Write confirmation | `Write file?`, showing exactly the target file and **+80 / -20** |
| Actual authorization | **Allow Once** from the dropdown; `Allow in this Session` was not selected |
| Keep / Accept | **No Keep step observed; Keep not clicked** |
| UI counts | `Session Changes: 1 File, +80, -20` |
| Actual difference on disk | **+80 / -20** |
| File status | 25 to 85 lines, CRLF, valid syntax, exact expected content |
| Controlled prompts submitted by automation | **1**, no duplicate submission |
| Baseline collection | `2026-11-02T11:08:23.162521+08:00` |
| Final evidence collection | `2026-11-02T11:11:20.154006+08:00` |
| Final SHA-256 | `4c3675924615acad6d2074fd14baaae5114afa2fb89b8bd914f97c2ddc6c4fdd` |
| SDK request ID | Not captured in the inspected Copilot Chat log; `null` does not imply no request |

Only CC was submitted, without another controlled Local/default-agent case. It used a fresh chat, a new file, and the same 25-line baseline and target changes as earlier runs. Output was not manually corrected; 14 historical test files and the LoC Probe definition were unchanged.

Accounts showed `gye6666`, and the extension login log was timestamped 11:06:12.460. SDK server-side account attribution and individual edit-telemetry delivery were not independently verified. The SDK/CLI runtime version was not reverified and must not be inferred from IDE or extension versions.

At the start of observation, the IDE briefly indicated an update installation, then returned to an Update button. Automation did not trigger an update, restart, or customization migration. The running UI resource path still corresponded to 1.143.1 / 0.71.0; a pending version must not be treated as the running version.

### API query and reporting scope

The November 2 11:00 pre-test reference ended October 31 and did not cover this run. At **2026-11-05 08:57 Beijing time**, the latest report and a specified single-day report were retrieved; both returned HTTP 200.

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

| Query item | Result |
|---|---|
| Enterprise / Target user | `gye` / `gye6666` |
| API reader | `gaussye`, reauthorized local GitHub CLI credential with verified `read:enterprise`; different from the test account |
| Latest-report query, UTC | `2026-11-05T00:57:21.299438+00:00` |
| Single-day query, UTC | `2026-11-05T00:57:54.193584+00:00` |
| HTTP | **200 for both endpoints** |
| Latest reporting period | **2026-10-07 through 2026-11-03** |
| Covers the test date? | **Yes, UTC 2026-11-02** |
| Target-user records for the test day | **1 user-day record in each report** |
| Consistency | All user activity fields and values match; reporting-period/date fields differ by report shape |

Filter the latest report to `user_login == "gye6666"` and `day == "2026-11-02"`. The single-day endpoint also returns user-day aggregates, not individual edit-request details.

| Reconciliation item | Scope |
|---|---|
| Activity date | **UTC 2026-11-02** |
| Beijing-time interval | `[2026-11-02 08:00, 2026-11-03 08:00)` |
| Controlled local edit | **80 added / 20 deleted**, single write authorized |
| Actual LoC | Check user totals and all returned IDE, feature, language, and model breakdowns; absence of `agent_edit` alone does not imply zero total LoC |
| Custom mode / Names | Check `chat_panel_custom_mode`, `totals_by_custom_agent`, and `distinct_custom_agent_use_count` separately; names or starts are not edit LoC |
| Two-full-UTC-day window ends | **2026-11-05 08:00 Beijing time** |
| Conservative three-full-UTC-day checkpoint | **2026-11-06 08:00 Beijing time** |

At the November 5 query, the two-day window had ended, but the three-day checkpoint had not been reached. These dates neither guarantee refresh/backfill nor prove report finalization. No automated query or reminder was created.

### API results queried November 5

| Metric | API result for UTC 2026-11-02 |
|---|---|
| Default-agent-mode bucket: `chat_panel_agent_mode` | **2 interactions**; this is the API classification, not a replacement for the local custom LoC Probe selection |
| Custom-agent-mode bucket: `chat_panel_custom_mode` | **Not present** |
| IDE | **VS Code 1.143.1** |
| API plugin / Version | **`vscode-agent-host` / `1.143.1`** |
| Locally installed Copilot Chat extension | **0.71.0**, a different component from the API plugin field |
| Language / Model with nonzero LoC | `python` / `gpt-6-luna` |
| Generation / Acceptance activities | **1 / 1** |
| Suggested added / deleted | **80 / 20** |
| `loc_added_sum` / `loc_deleted_sum` | **80 / 20**, net +60 |
| `agent_edit` | **Not present** |
| `totals_by_custom_agent` | **`[]`**, no LoC Probe name |
| `distinct_custom_agent_use_count` | **0** |
| `used_agent` / `used_chat` | `true` / `true` |
| `used_local_byok` | `false` |
| `vscode` in `totals_by_client` | `request_count = 4`, `session_count = 2`; not per-request edit LoC |

The version sample is `2026-11-02T03:10:49.892Z`, or 11:10:49.892 Beijing time. **API component `vscode-agent-host 1.143.1` and locally installed `copilot-chat 0.71.0` must not be conflated.** The sample timestamp is not an individual edit's telemetry receipt time.

User totals, IDE, feature, Python/feature, Python/model, and model/feature breakdowns all report **+80/-20**. These are different views of the same activity, not additive contributions.

### Cross-date comparison and interpretation

All API values below come from the **same latest report queried November 5 at 08:57**, not a combination of snapshots from different query dates.

| UTC activity date | Controlled case(s) | Local added / deleted | API actual added / deleted | Classification / Attribution |
|---|---|---:|---:|---|
| 2026-10-26 | CL, CC, DC, DL | 320 / 80 | **160 / 40** | `agent_edit` reports 160/40; individual contributing cases cannot be identified |
| 2026-10-28 | CL: Local / custom LoC Probe | 80 / 20 | **0 / 0** | 5 custom-mode interactions; no `agent_edit` |
| 2026-10-30 | DL: Local / default Agent | 80 / 20 | **0 / 0** | 1 default-mode interaction; no `agent_edit` |
| 2026-11-02 | CC: Copilot / custom LoC Probe | 80 / 20 | **80 / 20** | Entirely `chat_panel_agent_mode`; custom-agent list empty |

**The user-day LoC totals match the controlled CC edit numerically, but correct attribution of that individual request to the custom agent is not established.** Automation submitted one controlled task, whereas the report contains two interactions and two client sessions. The source of additional activity is unknown; it must not be called duplicate submission or used to establish request-level linkage.

The report places all LoC in `chat_panel_agent_mode`, without `agent_edit`, `chat_panel_custom_mode`, or a named custom-agent record. The official [LoC reference](https://docs.github.com/en/copilot/reference/copilot-usage-metrics/lines-of-code-metrics) describes direct file edits under `agent_edit` and actual chat-panel LoC as code-block Copy/Apply activity. The observed classification differs from that description. A harness-specific reporting/classification difference remains to be investigated; it is not yet a confirmed defect or confirmed expected behavior.

No Keep step appeared locally, but the daily report contains one acceptance activity. This does not establish a one-to-one relationship between Allow Once and acceptance events. No local acceptance event was captured, which does not prove no upload occurred.

**Local CL and DL still report 0/0 in the same latest snapshot, while CC has matching totals. The evidence does not support a blanket claim that all custom-agent edits are uncounted, or assigning the October 26 half-count to custom-agent exclusion.** Differences between Local and Copilot harness telemetry collection, reporting, and classification are a useful investigation direction, not a confirmed root cause.

The tests occurred on different UTC dates. Even with matching IDE/extension versions, model, and edit size, date-dependent environment changes and other same-day activity cannot be excluded. SDK runtime versions were not reverified; the daily aggregates lack per-request edit attribution.

### Complete raw API user-day record

This is the **complete November 2 user-day record in the latest 28-day report queried November 5**, formatted without changing any original field or value. It is one downloaded NDJSON record, not the REST download-link response or collector-added wrapper.

Source: [November 5 latest raw API snapshot](metrics-enterprise-gye-gye6666-20261105-reauthorized-refresh-raw.json), also retaining the target user's historical dates. The [November 2 single-day report](metrics-enterprise-gye-gye6666-20261102-one-day-20261105-raw.json) is saved separately. All daily activity fields and values match; reporting-period/date fields are preserved as returned by each endpoint.

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

### Evidence

| File | Purpose |
|---|---|
| [loc-ui-test-20261102-cc.json](loc-ui-test-20261102-cc.json) | Account, environment, CC selection, single-write authorization, local counts, and reconciliation scope |
| [loc-ui-test-20261102-baseline-observed.json](loc-ui-test-20261102-baseline-observed.json) | 25-line CRLF pre-edit baseline |
| [loc-ui-test-20261102-observed.json](loc-ui-test-20261102-observed.json) | Final contents, differences, line counts, line endings, and hash |
| [CC test file](copilot-loc-test/custom_copilot_20261102.py) | Synthetic file written by Copilot harness / custom agent |
| [Pre-test API reference](metrics-enterprise-gye-gye6666-20261102-1100-refresh-raw.json) | November 2 11:00 report ending October 31; does not cover this test |
| [metrics-enterprise-gye-gye6666-20261105-reauthorized-refresh-raw.json](metrics-enterprise-gye-gye6666-20261105-reauthorized-refresh-raw.json) | November 5 latest target-user report, including CC and earlier test dates |
| [metrics-enterprise-gye-gye6666-20261102-one-day-20261105-raw.json](metrics-enterprise-gye-gye6666-20261102-one-day-20261105-raw.json) | Specified UTC November 2 single-day target-user record |
| [loc-ui-test-20261102-cc-server-comparison-20261105.json](loc-ui-test-20261102-cc-server-comparison-20261105.json) | CC reconciliation, classification differences, and same-snapshot historical comparison |

## 12. November 5 isolated DC test

**November 6 14:04 reconciliation: both latest and single-day endpoints returned HTTP 200 and a user-day record for UTC November 5. All daily activity fields and values match. The API reports 160 added and 40 deleted lines, twice the controlled local +80/-20. Two distinct feature rows each contain +80/-20. The numerical discrepancy is confirmed; duplicate counting of the same edit is not.**

### Local result

**DC (Copilot harness / default Agent) completed one edit: 80 added lines, 20 deleted lines, net +60.** Allow Once was selected before writing. Keep All Edits appeared after completion and was clicked; Keep / Undo controls then disappeared. UI and on-disk counts matched. The November 6 API reconciliation is recorded below.

| Item | Test record |
|---|---|
| Test account / Enterprise | `gye6666` / `gye` |
| Test identifier | `20261105-DC` |
| Submitted at, Beijing time | **2026-11-05 09:26** |
| Harness / Agent | **Copilot / built-in default Agent**, not LoC Probe |
| Harness meaning | Copilot SDK / Agent Host, local workspace, not Cloud |
| VS Code / Copilot extension | **1.144.0 / 0.72.0** |
| Version verification | Running UI resource-directory application metadata; bundled `copilot` package identifies itself as `GitHub.copilot-chat`, version `0.72.0` |
| Model / Effort / Context | GPT-6 Luna / Medium / 272K |
| Entry point / Mode / Permissions | Ordinary editor-window Chat / Interactive / Manual permissions |
| File | `copilot-loc-test\default_copilot_20261105.py`, new and separate |
| Write confirmation | `Write file?`, exactly the target file and **+80 / -20** |
| Actual authorization | **Allow Once** from the dropdown; `Allow in this Session` was not selected |
| Keep / Accept | **Keep All Edits appeared and was clicked**; Keep / Undo controls disappeared |
| UI counts | `1 file changed, 80 lines added, 20 lines removed` |
| Actual difference on disk | **+80 / -20** |
| File status | 25 to 85 lines, CRLF, valid syntax, exact expected content |
| Controlled prompts submitted by automation | **1**, no duplicate submission or other controlled case |
| Baseline collection | `2026-11-05T09:25:02.694896+08:00` |
| Final post-Keep collection | `2026-11-05T09:27:51.368909+08:00` |
| Baseline SHA-256 | `6298c00f97ffc8ecc7c34ba09dc14c021d83cf0ab507d3debd1be2d1d41e6bfe` |
| Final SHA-256 | `4c3675924615acad6d2074fd14baaae5114afa2fb89b8bd914f97c2ddc6c4fdd` |
| SDK request ID | Not captured in the inspected Copilot Chat log; `null` does not imply no request |

Accounts confirmed `gye6666`; the extension login log was timestamped `2026-11-05 09:15:19.330`. This is not per-request verification of SDK identity or telemetry delivery. During fresh-chat initialization, LoC Probe was asynchronously restored; the built-in default **Agent** was explicitly selected and verified before submission. No test task was sent to the custom agent.

Only DC was run, using the same 25-line baseline and built-in file-edit tools to replace 20 old entries with 80 new entries. The prompt prohibited terminals, scripts, MCP, subagents, skills, and handoffs. Output was not manually corrected. The 15 historical test files and LoC Probe definition were preserved; automation did not trigger an update, restart, or customization migration.

### Comparability with the previous CC run

| Item | November 2 CC | November 5 DC |
|---|---|---|
| Harness / Agent | Copilot / custom LoC Probe | Copilot / default Agent |
| VS Code / Copilot extension | **1.143.1 / 0.71.0** | **1.144.0 / 0.72.0** |
| Model / Effort / Context | GPT-6 Luna / Medium / 272K | GPT-6 Luna / Medium / 272K |
| Local added / deleted | 80 / 20 | 80 / 20 |
| Pre-write authorization | Allow Once | Allow Once |
| Post-completion Keep | Not observed | **Observed; Keep All Edits clicked** |
| API status | Still 80/20 in the same November 6 14:04 snapshot, solely under `chat_panel_agent_mode` | **Daily total 160/40**; 80/20 under each of `chat_panel_agent_mode` and `agent_edit` |

**DC ran with newer IDE/extension versions and is not a same-version default/custom-agent A/B comparison.** The Keep difference is an actual observation, but date, versions, and agent type all differ; it cannot be attributed solely to default versus custom agent selection. SDK/CLI runtime versions were not reverified and must not be inferred from IDE/extension versions.

### API queries and reporting scope

The November 5 08:57 pre-test report ended UTC November 3. Neither of the two morning checks on November 6 provided a downloadable report for the activity day. **At 14:04, both endpoints provided the target user's record:**

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

| Query time, Beijing time | Latest 28-day report | Single-day endpoint for UTC November 5 |
|---|---|---|
| 2026-11-06 08:11 | HTTP 200; ends UTC November 4, not covering this test | HTTP 200; JSON value `""`, no download links |
| 2026-11-06 10:10 | HTTP 200; still ends UTC November 4 | HTTP 200; still `""`, no download links |
| **2026-11-06 14:04** | **HTTP 200; October 9 through November 5, covering this test** | **HTTP 200; November 5 report download links, one target-user record retrieved** |

The morning empty string is not a zero-line user record and must not be interpreted as LoC 0/0. The 14:04 result establishes report availability, not the cause of the earlier empty responses.

| Query item | Result |
|---|---|
| Enterprise / Target user | `gye` / `gye6666` |
| API reader | `gaussye`, reauthorized local GitHub CLI credential; different from the test account |
| Latest-report query, UTC | `2026-11-06T06:04:35.772464+00:00` |
| Single-day query, UTC | `2026-11-06T06:04:35.675161+00:00` |
| HTTP | **200 for both endpoints** |
| Latest reporting period | **2026-10-09 through 2026-11-05** |
| Target-user records for the test day | **1 user-day record in each report** |
| Consistency | All daily activity fields and values match; reporting-period/date fields differ by report shape |

Filter the latest report to `user_login == "gye6666"` and `day == "2026-11-05"`. Both endpoints provide user-day aggregates, not individual file-edit request records.

| Reconciliation item | Scope |
|---|---|
| API reader / Enterprise / Target user | `gaussye` / `gye` / `gye6666`; reader and test account differ |
| Activity date | **UTC 2026-11-05** |
| Beijing-time interval | `[2026-11-05 08:00, 2026-11-06 08:00)` |
| Controlled local edit | **80 added / 20 deleted**, single write authorized and kept |
| Actual LoC | Check user totals and returned IDE, feature, language, and model breakdowns without double-counting |
| Feature classification | Check both `agent_edit` and `chat_panel_agent_mode`; the previous CC report lacked `agent_edit` but had nonzero total LoC |
| API component version | Both plugin name and version are `unknown`; do not replace them with local `GitHub.copilot-chat 0.72.0` |
| Two-full-UTC-day window ends | **2026-11-08 08:00 Beijing time** |
| Conservative three-full-UTC-day checkpoint | **2026-11-09 08:00 Beijing time** |

At November 6 14:04, the report is available before the two-full-UTC-day window ends. Coverage does not establish finalization; later revisions remain possible. These dates do not guarantee refresh or backfill, and no automated query or reminder was created.

### API results queried November 6 at 14:04

| Metric | API result for UTC 2026-11-05 |
|---|---|
| Default-agent mode: `chat_panel_agent_mode` | **2 interactions; 80 added / 20 deleted** |
| Custom-agent mode: `chat_panel_custom_mode` | Not present |
| `agent_edit` | **Present; 80 added / 20 deleted** |
| User-day `loc_added_sum` / `loc_deleted_sum` | **160 / 40**, net +120 |
| Controlled local edit | **80 / 20**, net +60 |
| API total minus controlled local edit | **80 more added / 20 more deleted**, net +60 more |
| Suggested added / deleted | **80 / 20** |
| Generation / Acceptance activities | **2 / 1** |
| IDE | **VS Code 1.144.0** |
| API plugin name / Version | **`unknown` / `unknown`**; locally installed extension is `GitHub.copilot-chat 0.72.0` |
| Model | `gpt-6-luna` |
| Language attribution of nonzero LoC | `python`: chat-mode 80/20; `unknown`: `agent_edit` 80/20 |
| `used_agent` / `used_chat` | `true` / `true` |
| `totals_by_custom_agent` / `distinct_custom_agent_use_count` | `[]` / `0`; the controlled test used the default Agent |
| `vscode` in `totals_by_client` | `request_count = 4`, `session_count = 2` |
| `client_telemetry_present` | **`true`** |
| `usage_metrics_requirements` | `met: ["vscode"]`, `unmet: []`, `unknown: []` |

The version sample is `2026-11-05T01:32:12.582Z`, or 09:32:12.582 Beijing time. The version sample and daily telemetry-requirements flags do not provide this individual controlled edit's receipt time or proof of per-request delivery.

| `feature` | Interactions | Generation activities | Acceptance activities | Suggested added / deleted | Actual added / deleted |
|---|---:|---:|---:|---:|---:|
| `chat_panel_agent_mode` | 2 | 1 | 1 | 80 / 20 | **80 / 20** |
| `agent_edit` | 0 | 1 | 0 | 0 / 0 | **80 / 20** |
| **User-day total** | **2** | **2** | **1** | **80 / 20** | **160 / 40** |

**The original top-level user-day fields themselves report 160/40; the two feature rows also sum to that total.** This is not an analysis error caused by adding IDE, language, and model breakdowns together. Those separate dimensional views must still not be added again.

### Same-snapshot comparison and interpretation

All API values below come from the **same latest report queried November 6 at 14:04**:

| UTC activity date | Controlled case(s) | Local added / deleted | API actual added / deleted | Feature classification |
|---|---|---:|---:|---|
| 2026-10-26 | CL, CC, DC, DL | 320 / 80 | **160 / 40** | Actual LoC in `agent_edit`; contributing cases unknown |
| 2026-10-28 | CL: Local / custom LoC Probe | 80 / 20 | **0 / 0** | Custom-mode interactions present; no `agent_edit` |
| 2026-10-30 | DL: Local / default Agent | 80 / 20 | **0 / 0** | Default-mode interactions present; no `agent_edit` |
| 2026-11-02 | CC: Copilot / custom LoC Probe | 80 / 20 | **80 / 20** | Only `chat_panel_agent_mode`; no `agent_edit` |
| 2026-11-05 | DC: Copilot / default Agent | 80 / 20 | **160 / 40** | 80/20 in each of `chat_panel_agent_mode` and `agent_edit` |

**Confirmed: the DC activity day now has nonzero LoC, and its daily total is twice the controlled local edit. Unconfirmed: whether the same edit was reported or attributed twice across the two features.** Identical feature values are a useful lead for investigating overlap, not a confirmed root cause.

Automation submitted one controlled task, whereas the report contains two interactions and two client sessions. The source of additional activity is unknown; other same-day activity has not been excluded. No request-level edit identifiers establish that both feature rows came from this controlled edit. Additional daily activity must not be described as duplicate automated submission.

CC and DC also differ in date, IDE/extension versions, and Keep behavior. The result does not establish that default agents always double-count, custom agents omit one contribution, or Keep necessarily produces `agent_edit`. It still does not support a blanket claim that custom-agent LoC is unsupported. Reporting coverage, feature classification, and possible overlapping attribution require separate investigation.

Allow Once, Keep, and the file on disk establish local editing and retention. Uncaptured local acceptance logs do not prove no upload occurred. API `client_telemetry_present: true` and satisfied requirements are daily indicators, not an event-by-event mapping for this controlled request.

### Complete raw API user-day record

The following is the **complete UTC November 5 user-day record for `gye6666` from the latest report queried November 6 at 14:04**. All original fields and values are preserved, including `client_telemetry_present` and `usage_metrics_requirements`.

This is a downloaded NDJSON user record, not the REST download-link response or collector-added query metadata. Sources: [latest raw report](metrics-enterprise-gye-gye6666-20261106-1404-refresh-raw.json) and [specified single-day report](metrics-enterprise-gye-gye6666-20261105-one-day-20261106-1404-raw.json), whose daily activity fields and values match.

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

### Evidence

| File | Purpose |
|---|---|
| [loc-ui-test-20261105-dc.json](loc-ui-test-20261105-dc.json) | Account, running versions, default Agent, single-write authorization, Keep, and reconciliation scope |
| [loc-ui-test-20261105-baseline-observed.json](loc-ui-test-20261105-baseline-observed.json) | 25-line CRLF pre-edit baseline |
| [loc-ui-test-20261105-observed.json](loc-ui-test-20261105-observed.json) | Post-Keep contents, differences, line counts, line endings, and hash |
| [DC test file](copilot-loc-test/default_copilot_20261105.py) | Synthetic file written by Copilot harness / default Agent |
| [Pre-test API reference](metrics-enterprise-gye-gye6666-20261105-reauthorized-refresh-raw.json) | November 5 08:57 report ending UTC November 3; does not cover this test day |
| [metrics-enterprise-gye-gye6666-20261106-1404-refresh-raw.json](metrics-enterprise-gye-gye6666-20261106-1404-refresh-raw.json) | November 6 14:04 latest target-user report, covering DC and historical test dates |
| [metrics-enterprise-gye-gye6666-20261105-one-day-20261106-1404-raw.json](metrics-enterprise-gye-gye6666-20261105-one-day-20261106-1404-raw.json) | Original November 5 single-day target-user record |
| [loc-ui-test-20261105-dc-server-comparison-20261106-1404.json](loc-ui-test-20261105-dc-server-comparison-20261106-1404.json) | Local 80/20 versus daily 160/40 reconciliation and attribution limits |
| [loc-ui-test-20261105-dc-server-comparison-20261106.json](loc-ui-test-20261105-dc-server-comparison-20261106.json) | Historical November 6 08:11 pending-coverage state, not overwritten with zero values |
| [08:11 latest report](metrics-enterprise-gye-gye6666-20261106-0811-refresh-raw.json) / [08:11 single-day response](metrics-enterprise-gye-gye6666-20261105-one-day-20261106-raw.json) | Latest report ends November 4; single-day endpoint returned HTTP 200 / `""` |
| [10:10 latest report](metrics-enterprise-gye-gye6666-20261106-1009-refresh-raw.json) / [10:10 single-day response](metrics-enterprise-gye-gye6666-20261105-one-day-20261106-1009-raw.json) | Second morning check still lacked a downloadable November 5 user record |
