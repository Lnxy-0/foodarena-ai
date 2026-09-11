# Sprint 1 迭代报告 · 需求定义与 MVP 骨架

> 时间：Day 3–Day 4 ｜ 状态：已完成
> 主题：需求定义与 MVP 骨架
> 交付物：仓库拓扑与看板任务拆解、API 连通 Demo、系统架构图、轮次控制骨架

---

## 一、Sprint 目标

完成核心需求拆解与系统骨架搭建，使后续 Sprint 有稳定的契约与工程基座。

## 二、Sprint 1 Backlog（计划）

| Issue | 标题 | 负责人 | SP | 类型 | 优先级 |
|---|---|---|---|---|---|
| #1 | [TECH01] SiliconFlow API 连通 Demo | 林翔宇 | 3 | Tech | P0 |
| #2 | [US01] 偏好表单与会话创建 | 吴佳德 | 5 | User Story | P0 |
| #3 | [DOC01] 系统三大模型六图与 README 骨架 | 郑琢 | 3 | Docs | P1 |
| #4 | [DOC-S1] Sprint 1 报告与验收清单 | 郑琢 | 2 | Docs | P1 |
| #5 | [US02] 三轮双 Agent 轮次控制骨架 | 林翔宇 | 5 | User Story | P0 |
| #6 | [QA01] Pydantic 契约与 BDD/TDD 测试骨架 | 郑琢 | 3 | Test | P0 |
| #7 | [OPS01] 标准仓库拓扑、最低 CI 与分支保护 | 郑琢 | 3 | DevOps | P0 |

**计划总量：7 个 Issue / 24 SP**

## 三、计划 vs 实际

| Issue | 计划 | 实际 | 差异说明 |
|---|---|---|---|
| #1 | 连通 Demo | ✅ 完成 | 对应提交 `48f2631`，并经 PR #27 合并 |
| #2 | 偏好表单 + 会话创建 | ✅ 完成 | 会话创建契约随 US01 落地；前端表单在 Sprint 2 与后端一并完善 |
| #3 | 六图 + README 骨架 | ✅ 完成 | 六图见 `docs/system_design.md`；README 于 Sprint 4 升级为工业级 |
| #4 | Sprint 1 报告 | ✅ 本文档 | 交付于本次迭代 |
| #5 | 轮次控制骨架 | ✅ 完成 | 提交 `077dcb5`；`DebateController` + `MockDebateAgent` |
| #6 | 契约与测试骨架 | ✅ 完成 | `tests/features/*.feature` + `tests/test_domain_contracts.py` |
| #7 | 仓库拓扑与 CI | ✅ 完成 | `.github/workflows/ci.yml`、Issue/PR 模板、`AGENTS.md` |

**完成率：7/7 = 100%**（其中 #4 与 #3 的报告/README 在后续 Sprint 有增强）

## 四、看板与燃尽快照说明

- 看板列：To Do → In Progress → In Review → Done。
- Sprint 1 首日任务全部进入 To Do（24 SP）；Day 3 末 #1（3 SP）→ Done；Day 4 末 #5、#6、#7 进入 In Review，随 CI 可用转 Done；#2 的会话创建部分完成，前端交互顺延至 Sprint 2。
- 燃尽曲线特征：前段因环境搭建（仓库 + CI）进展偏慢，末段因契约先行使任务转 Done 集中。**实际燃尽并非理想直线**，详见 Retro 的改进项。

## 五、验收结果

| 验收点 | 结果 | 证据 |
|---|---|---|
| API 连通 Demo 可跑 | ✅ | `siliconflow.py` + CLI 自检，`tests/test_siliconflow.py` |
| 两 Agent 交替 3 轮 6 条（Mock） | ✅ | Gherkin `US02_debate.feature` 场景 1 |
| 轮次控制器不依赖真实模型 | ✅ | `MockDebateAgent`，测试离线通过 |
| Pydantic 契约齐备 | ✅ | `domain.py`；`test_domain_contracts.py` |
| CI 在 Push/PR 运行 | ✅ | `.github/workflows/ci.yml` |
| 未提交密钥/PII | ✅ | `.env` 忽略，`.env.example` 仅占位符 |

## 六、风险与阻塞

| 风险 | 影响 | 处置 |
|---|---|---|
| 真实模型响应不稳定 | 影响后续 Sprint | 提前设计 `ChefProvider` 可切换 + 离线 Mock 提供方 |
| 分支保护需仓库管理员设置 | #7 部分验收依赖仓库设置 | 记录为阻塞项，由仓库 owner 在 Settings 完成 |
| 契约先行但前端滞后 | #2 跨 Sprint | 明确顺延至 Sprint 2 并保留在 Backlog |

## 七、Review / Retro

- **继续（Keep）**：契约先行——先把 Pydantic 契约定死，前后端与测试并行不打架。
- **停止（Stop）**：不要在骨架阶段就追求前端完备度，避免阻塞后端契约节奏。
- **改进（Improve）**：环境搭建（仓库 + CI）应前置到 Sprint 0 完成，减少 Sprint 1 前段空转。

## 八、Issue 可追溯性

| Issue | 追溯 |
|---|---|
| #1 | 提交 `48f2631`；PR #27（已合并） |
| #2 | 契约与端点于 Sprint 2 提交 `4c17145`；前端 `9b2561e` |
| #3 | `docs/system_design.md` |
| #5 | 提交 `077dcb5` |
| #6 | `tests/`（契约与 BDD 骨架） |
| #7 | `.github/`、`AGENTS.md` |

## 九、贡献清单（本 Sprint）

| 成员 | 角色 | 交付 |
|---|---|---|
| 林翔宇 | Backend/AI | API 连通 Demo、轮次控制骨架 |
| 郑琢 | SM/QA/Docs | 六图与 README 骨架、契约与测试骨架、仓库拓扑与 CI、本报告 |
| 吴佳德 | PO/Frontend | 用户故事细化、偏好表单需求确认 |
