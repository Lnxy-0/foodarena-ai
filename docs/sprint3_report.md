# Sprint 3 迭代报告 · 交互体验与防御性编程

> 时间：Day 7–Day 8 ｜ 状态：已完成（含 2 项部分完成，见第七节）
> 主题：交互体验与防御性编程
> 交付物：高完备度应用、完整测试用例库、结构化日志

---

## 一、Sprint 目标

优化前端 UI/UX，实现流式响应（SSE）、错误重试与 Prompt 防护，并建立完整回归测试与评测集。

## 二、Sprint 3 Backlog（计划）

| Issue | 标题 | 负责人 | SP | 类型 | 优先级 |
|---|---|---|---|---|---|
| #14 | [SEC01] 输入校验与 Prompt 注入防护 | 郑琢 | 3 | Test | P0 |
| #15 | [UX02] SSE 流式辩论与状态恢复 | 吴佳德 | 5 | Tech | P1 |
| #16 | [QA03] 完整测试库与故障场景回归 | 郑琢 | 5 | Test | P0 |
| #17 | [UX01] React/Vite 完整交互体验 | 吴佳德 | 5 | User Story | P1 |
| #18 | [EVAL01] 20 条选餐评测集与轨迹评分脚本 | 郑琢 | 5 | Test | P1 |
| #19 | [RES01] 超时重试、频控与结构化脱敏日志 | 林翔宇 | 3 | Tech | P0 |
| #20 | [DOC-S3] Sprint 3 报告、EDD 评测与复盘 | 郑琢 | 2 | Docs | P1 |

**计划总量：7 个 Issue / 28 SP**

## 三、计划 vs 实际

| Issue | 计划 | 实际 | 差异说明 |
|---|---|---|---|
| #14 | 注入防护 | ✅ 完成 | 关键词中和 + 控制字符剥离 + 数据分隔块；`security.py` |
| #15 | SSE 流式 + 状态恢复 | ⚠️ 部分完成 | SSE 流式与断线恢复已实现；**事件命名与 issue 约定不一致**（见第七节） |
| #16 | 完整测试库 | ✅ 完成 | 后端 **96 测试**，覆盖交替/重复启动/超时/429/5xx/Schema/SSE 中断/注入/脱敏/失败查询 |
| #17 | 完整交互体验 | ✅ 完成 | 表单字段级校验、辩论页区分 Agent/轮次/论点/证据、战报四维展示、响应式 |
| #18 | ≥20 条评测集 | ✅ 完成（超额） | **22 条**场景 + 评分脚本，四维评分 **100%** |
| #19 | 重试/频控/脱敏日志 | ⚠️ 部分完成 | 超时 30s ✅、重试 3 次 ✅、指数退避 ✅、脱敏日志 ✅；**抖动、retry_count 计数、频控未实现** |
| #20 | Sprint 3 报告 | ✅ 本文档 | — |

**完成率：5/7 完全完成，2/7 部分完成（占总 SP 的约 29%）**

## 四、看板与燃尽快照说明

- Sprint 3 引入两处重要返工：SSE 从"整场跑完一次性返回"重构为"逐事件推送"（提交 `acb1be7`），以及随后进一步修复交替发言的实时性（提交 `4d91493`）。
- 燃尽特征：中段因 SSE 重构出现"已完成项回退"，属正常技术债偿还；末段测试与评测集（#16/#18）集中转 Done。

## 五、验收结果（含真实证据）

| 验收点 | 结果 | 证据 |
|---|---|---|
| Prompt 注入样例进入自动化测试 | ✅ | `tests/test_security.py` + `eval/` injection 类别 |
| 模型不回显系统 Prompt/密钥/堆栈 | ✅ | 评测集 safety 维度 22/22 |
| 结构化脱敏日志 | ✅ | `security.redact_secrets`；`sk-`/Bearer/邮箱 → `[REDACTED]` |
| 单次请求超时 30 秒 | ✅ | `SiliconFlowConfig.timeout_seconds = 30.0` |
| 429/5xx 最多重试 3 次 | ✅ | `max_retries = 3`；`_retryable` 覆盖 429 与 5xx |
| 指数退避 | ✅ | `delay = backoff_seconds * 2**attempt` |
| 退避含抖动 | ❌ | **未实现**（无随机抖动） |
| 记录 retry_count | ❌ | **未实现**（重试时仅告警，未计数上报） |
| 频控（令牌桶/限流） | ❌ | **未实现** |
| 达到上限会话进入 FAILED、不生成部分消息 | ✅ | `test_resilience.py`、`test_service.py` |
| SSE 事件：status/round_started/message | ✅ | `service.py` |
| SSE 事件：retrying/completed/failed | ❌ | 实际为 `report`/`info`/`done`，与 issue 约定不同 |
| 断线后按 session_id 恢复、不重复渲染 | ✅ | `test_resilience.py::test_sse_replay_after_disconnect...` |
| 评测集 ≥20 条、四维评分 | ✅ | `eval/evalset.json`（22 条）+ `eval/score_evalset.py` |

## 六、评测与质量指标（真实）

| 指标 | 数值 |
|---|---|
| 评测集版本 | `evalset-2026-09-01` |
| 评测条数 | 22（口味/预算/人数/天气/口味冲突/注入 六类） |
| 通过率 | **22/22 = 100%** |
| 四维评分 | format/consistency/recovery/safety 均 100% |
| 后端测试 | **96 passed** |
| 静态检查 | `ruff check`/`format` 全绿 |

复现命令：

```bash
pytest tests/                      # 96 passed
python eval/score_evalset.py       # 22/22 = 100%
ruff check src tests               # All checks passed
```

## 七、已知风险与技术债（如实记录）

| 项 | 状态 | 影响 | 计划 |
|---|---|---|---|
| 退避无抖动 | 未实现 | 并发重试可能同步冲击上游 | 列入 Sprint 4 遗留 Backlog |
| retry_count 未计数上报 | 未实现 | 可观测性受限 | 同上 |
| 频控未实现 | 未实现 | 高频调用可能触及上游配额 | 同上 |
| SSE 事件命名与 issue 不一致 | 已实现但命名不同 | 与 #15 验收文本不符 | 建议对齐为 `retrying`/`completed`/`failed` |
| 日志源目录未持久化 | 仅控制台 | 历史问题难追溯 | 建议增加文件日志开关 |

> 说明：上述未完成项**不虚报为已完成**，按指导书要求保留在 Backlog 并说明原因。

## 八、Review / Retro

- **继续（Keep）**：用评测集做"外部质量基线"——22 条场景把"是否泄露""是否超预算"变成可量化指标。
- **停止（Stop）**：停止在需求未冻结时先写实现（SSE 事件命名返工即由此而来）。
- **改进（Improve）**：防御性编程项（重试/频控/日志）应在 Sprint 内一次性做全，避免"半成品"跨 Sprint。

## 九、Issue 可追溯性

| Issue | 追溯 |
|---|---|
| #14 | `security.py`、`tests/test_security.py` |
| #15 | 提交 `acb1be7`、`4d91493`；`main.py` SSE 端点、`useLiveSession.ts` |
| #16 | `tests/test_resilience.py` 等 |
| #17 | 提交 `9b2561e`；`frontend/src/components/` |
| #18 | `eval/evalset.json`、`eval/score_evalset.py` |
| #19 | `siliconflow.py`（超时/重试/退避）、`security.py`（脱敏日志） |

## 十、贡献清单（本 Sprint）

| 成员 | 角色 | 交付 |
|---|---|---|
| 林翔宇 | Backend/AI | RES01 超时/重试/退避/脱敏日志（部分）；SSE 流式后端重构 |
| 郑琢 | SM/QA/Docs | SEC01 注入防护、QA03 完整测试库、EVAL01 评测集与评分脚本、本报告 |
| 吴佳德 | PO/Frontend | UX01 完整交互、UX02 SSE 前端接入与状态恢复 |
