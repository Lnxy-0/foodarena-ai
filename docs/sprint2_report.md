# Sprint 2 迭代报告 · 核心功能与 AI 逻辑实现

> 时间：Day 5–Day 6 ｜ 状态：已完成
> 主题：核心功能与 AI 逻辑实现
> 交付物：MVP 可运行版本、基础单元测试 / BDD 脚本、Sprint 2 进展报告

---

## 一、Sprint 目标

将 Sprint 1 的 Mock 控制器接入真实模型适配层，实现完整的三轮双 Agent 辩论链与结构化战报，形成可运行的 MVP。

## 二、Sprint 2 Backlog（计划）

| Issue | 标题 | 负责人 | SP | 类型 | 优先级 |
|---|---|---|---|---|---|
| #8 | [AI01] Agent 人设、Prompt 与结构化 Schema | 林翔宇 | 5 | Tech | P0 |
| #9 | [DOC-S2] Sprint 2 报告与核心逻辑验收 | 郑琢 | 2 | Docs | P1 |
| #10 | [US02] 完整三轮双 Agent 辩论链 | 林翔宇 | 8 | User Story | P0 |
| #11 | [TECH02] FastAPI 会话接口与 SQLite 持久化 | 林翔宇 | 5 | Tech | P0 |
| #12 | [US03] 结构化战报与个性化推荐 | 林翔宇 | 5 | User Story | P0 |
| #13 | [QA02] 核心逻辑单测、BDD 与 Mock 集成 | 郑琢 | 3 | Test | P0 |

**计划总量：6 个 Issue / 28 SP**

## 三、计划 vs 实际

| Issue | 计划 | 实际 | 差异说明 |
|---|---|---|---|
| #8 | 人设/Prompt/Schema | ✅ 完成 | `prompts.py`（含 `PROMPT_VERSION`）+ Pydantic 强校验 + 有限修复重试 |
| #9 | Sprint 2 报告 | ✅ 本文档 | — |
| #10 | 完整辩论链 | ✅ 完成 | 提交 `4c17145`；逐条持久化、幂等、可恢复 |
| #11 | FastAPI + SQLite | ✅ 完成 | 4 张表 + 4 个核心端点；提交 `4c17145` |
| #12 | 结构化战报 | ✅ 完成 | `DebateReport` + 四维评分 + 409 门禁 |
| #13 | 单测/BDD/Mock 集成 | ✅ 完成 | Gherkin US01–US03 + `test_service.py` 等 |

**完成率：6/6 = 100%**

## 四、看板与燃尽快照说明

- Sprint 2 起点承接 Sprint 1 的顺延项（#2 前端交互）。
- Day 5 集中在 #8（Prompt/Schema）与 #11（接口/持久化），两者互为依赖故并行推进；Day 6 完成 #10、#12 串联，CI 转绿后批量转 Done。
- 燃尽特征：因 #8–#12 高度耦合于同一服务层，存在"末段集中转 Done"的堆积，提示任务拆解粒度可更细。

## 五、验收结果

| 验收点 | 结果 | 证据 |
|---|---|---|
| 6 条结构化 AgentMessage 固定顺序 | ✅ | `US02_debate.feature` |
| 每轮上下文含偏好 + 对手上轮观点 + 安全边界 | ✅ | `prompts.debate_user_prompt` + `sanitise_user_text` |
| 重复启动返回当前状态、不产生第二条链 | ✅ | `test_resilience.py::test_running_then_rerun_is_idempotent...` |
| 单次临时错误可恢复且无重复消息 | ✅ | `test_service.py` 失败恢复用例 |
| 战报含 dish/reason/confidence/score_breakdown | ✅ | `US03_report.feature` |
| 未完成会话不返回伪造战报（409） | ✅ | `test_api.py` / `test_resilience.py` |
| 外部模型经 Fake/Mock 注入，测试不联网 | ✅ | `MockChefProvider` |

## 六、质量指标（真实）

| 指标 | 数值 |
|---|---|
| 后端测试（本 Sprint 末） | 覆盖领域契约、服务、API、提供商、安全五类 |
| BDD 场景 | US01（2）+ US02（2）+ US03（2）= 6 个 |
| 外部依赖 | 测试全程离线（Mock 提供方） |

## 七、API 延迟 / 重试 / 失败样例说明

- 真实 SiliconFlow 调用由 `SiliconFlowClient` 统一承载：**超时/429/5xx 触发指数退避重试**（`siliconflow.py::_wait_before_retry`）。
- 每次模型调用的完成记录以结构化日志输出（`model request completed`，含 `request_id`/`session_id`）。
- 失败样例（脱敏，不含敏感内容）：
  - 模型返回空内容 → `DebateProviderError("model returned an empty response")` → 会话转 `FAILED`；
  - 裁判缺失 dish/reason → `DebateProviderError("judge reply missing dish or reason")`；
  - Schema 校验失败 → 有限修复重试，仍失败进入 `FAILED`。
- 上述日志均经 `redact_secrets` 脱敏（`sk-`、Bearer、邮箱 → `[REDACTED]`）。

## 八、风险与阻塞

| 风险 | 影响 | 处置 |
|---|---|---|
| 真实模型输出不可复现 | 影响测试稳定性 | Schema 强校验 + Mock 集成测试 |
| SQLite 单文件并发写 | 高并发下受限 | 明确单进程单 worker 假设，写入事务化 |
| 服务层任务耦合度高 | 燃尽堆积 | Backlog 拆解粒度改进（见 Retro） |

## 九、Review / Retro

- **继续（Keep）**：接口契约先行 + Mock 集成，使前后端与测试可并行。
- **停止（Stop）**：停止把多个高耦合任务压在 Sprint 末段一起完成。
- **改进（Improve）**：将服务层任务进一步拆分为"路由/持久化/编排/裁决"子任务，使燃尽更平滑。

## 十、未完成项回流

| 项 | 原因 | 去向 |
|---|---|---|
| 前端完整交互体验 | 本 Sprint 聚焦后端与 AI 逻辑 | 进入 Sprint 3（#17 UX01） |
| SSE 流式与状态恢复 | 依赖后端编排稳定后开展 | 进入 Sprint 3（#15 UX02） |

## 十一、可重复 Demo 说明

```bash
pip install -e ".[dev]"
python -m uvicorn foodarena_ai.main:app --port 8000   # 默认 mock 提供方，无需 Key
```

1. `POST /api/v1/sessions` 创建会话；
2. `POST /api/v1/sessions/{id}/debate` 跑完三轮辩论；
3. `GET /api/v1/sessions/{id}/report` 读取结构化战报。

## 十二、Issue 可追溯性

| Issue | 追溯 |
|---|---|
| #8 | `prompts.py`、`security.py` |
| #10 | 提交 `4c17145` |
| #11 | 提交 `4c17145`、`db.py`、`main.py` |
| #12 | `service.py`（`report`）、`main.py`（409 门禁） |
| #13 | `tests/features/`、`tests/test_service.py`、`tests/test_api.py` |

## 十三、贡献清单（本 Sprint）

| 成员 | 角色 | 交付 |
|---|---|---|
| 林翔宇 | Backend/AI | AI01 人设/Prompt/Schema、US02 完整辩论链、TECH02 接口与持久化、US03 战报 |
| 郑琢 | SM/QA/Docs | QA02 单测/BDD/Mock 集成、本报告、质量指标汇总 |
| 吴佳德 | PO/Frontend | 战报与辩论的体验需求确认、验收口径把关 |
