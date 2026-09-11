# Sprint 4 迭代报告 · CI/CD、开源发布与答辩

> 时间：Day 9–Day 10 ｜ 状态：已完成（含 3 项未完成，见第七节）
> 主题：CI/CD、开源发布与答辩
> 交付物：公开 GitHub 仓库、Demo/Docker 镜像、实训报告、答辩 PPT

---

## 一、Sprint 目标

配置 GitHub Actions 自动化测试，撰写 README，完成容器化与发布，准备答辩材料。

## 二、Sprint 4 Backlog（计划）

| Issue | 标题 | 负责人 | SP | 类型 | 优先级 |
|---|---|---|---|---|---|
| #21 | [OPS02] Docker、Compose 与 GHCR 镜像 | 林翔宇 | 3 | DevOps | P1 |
| #22 | [MEDIA01] 演示视频与答辩材料 | 吴佳德 | 3 | Docs | P1 |
| #23 | [DOC05] 工业级 README 与开源发布文档 | 吴佳德 | 3 | Docs | P1 |
| #24 | [REL01] 发布验收、Demo 数据与 v0.1.0 | 林翔宇 | 3 | Tech | P0 |
| #25 | [CI02] 完整 GitHub Actions 质量流水线 | 郑琢 | 3 | DevOps | P0 |
| #26 | [DOC-S4] Sprint 4 报告、最终复盘与贡献清单 | 郑琢 | 2 | Docs | P1 |

**计划总量：6 个 Issue / 17 SP**

## 三、计划 vs 实际

| Issue | 计划 | 实际 | 差异说明 |
|---|---|---|---|
| #21 | Docker/Compose/GHCR | ⚠️ 部分完成 | Dockerfile（非 root + 健康检查）✅、compose（前后端 + 持久化卷）✅；**GHCR 版本化镜像推送未配置** |
| #22 | 演示视频与答辩材料 | ⚠️ 部分完成 | 答辩材料 ✅（`docs/答辩汇报.md`）；**演示视频未录制** |
| #23 | 工业级 README | ✅ 完成 | Badges、定位、MVP、架构图、技术栈、快速启动、环境变量、API、测试、Docker、贡献清单、许可 |
| #24 | 发布验收/Demo/v0.1.0 | ⚠️ 部分完成 | Mock Demo 数据 ✅ 与三轮辩论 + 战报 Smoke Test ✅（已在有效 Key 下跑通）；**v0.1.0 tag 未创建** |
| #25 | 完整 CI 流水线 | ✅ 完成 | 后端（lint/format/pytest/BDD/eval）+ 前端（lint/typecheck/test/build）+ Docker 构建，三 job |
| #26 | Sprint 4 报告 | ✅ 本文档 | — |

**完成率：3/6 完全完成，3/6 部分完成**

## 四、Sprint 4 交付的实际产出

| 交付物 | 状态 | 位置 |
|---|---|---|
| Docker 镜像（后端） | ✅ | [Dockerfile](../Dockerfile)（非 root、HEALTHCHECK） |
| Compose 编排 | ✅ | [docker-compose.yml](../docker-compose.yml)（前后端 + SQLite 卷 + nginx SSE 透传） |
| CI 流水线 | ✅ | [.github/workflows/ci.yml](../.github/workflows/ci.yml) |
| 一键启动器 | ✅ | [dev.py](../dev.py) |
| 工业级 README | ✅ | [README.md](../README.md) |
| 系统设计六图 | ✅ | [docs/system_design.md](system_design.md) |
| 贡献清单 | ✅ | 见第九节 |
| 答辩材料 | ✅ | [docs/答辩汇报.md](答辩汇报.md) |

## 五、四 Sprint 汇总（真实数据）

| Sprint | 计划 SP | 完全完成 | 部分完成 | 完成率（按 SP 计） |
|---|---|---|---|---|
| S1 需求与骨架 | 24 | 7/7 | 0 | 100% |
| S2 核心功能与 AI | 28 | 6/6 | 0 | 100% |
| S3 体验与防御 | 28 | 5/7 | 2 | 约 71% |
| S4 CI/CD 与发布 | 17 | 3/6 | 3 | 约 50% |
| **合计** | **97** | **21/26** | **5** | **约 81%** |

> 说明：S3/S4 的部分完成项已在各 Sprint 报告"已知风险与技术债"中如实列出，未虚报。

## 六、质量指标（项目终态，可复现）

| 指标 | 数值 | 命令 |
|---|---|---|
| 后端测试 | **96 passed** | `pytest tests/` |
| 前端测试 | **6 passed** | `cd frontend && npm run test` |
| BDD 验收场景 | 3 个故事 / 6 个场景 | `tests/features/*.feature` |
| 评测集通过率 | **22/22 = 100%** | `python eval/score_evalset.py` |
| 静态检查 | ruff 全绿 | `ruff check src tests` |
| git 提交 | 15 次，Angular 规范 | `git log --oneline` |
| CI job | 3（backend / frontend / docker-build） | `.github/workflows/ci.yml` |

## 七、已知风险与遗留 Backlog（如实记录）

| 项 | 状态 | 归属 Issue | 计划 |
|---|---|---|---|
| 退避无抖动 | 未实现 | #19 | 后续迭代：加入 `random.uniform(0, delay*0.5)` |
| retry_count 未计数上报 | 未实现 | #19 | 后续迭代：日志/指标补充重试计数 |
| 频控未实现 | 未实现 | #19 | 后续迭代：为会话级/用户级加令牌桶 |
| SSE 事件命名未对齐 | 已实现但命名不同 | #15 | 对齐为 `retrying`/`completed`/`failed` |
| GHCR 镜像推送未配置 | 未实现 | #21 | 增加 workflow 的 `docker push` 到 ghcr.io |
| v0.1.0 tag 未创建 | 未实现 | #24 | CI 全绿后 `git tag v0.1.0` |
| 演示视频未录制 | 未实现 | #22 | 按答辩脚本录屏 |

## 八、项目复盘（Retro）

### 做得好（Keep）

1. **双提供方可切换架构**——`real`/`mock` 一键切换，使演示、CI、测试均不依赖外部服务，是本项目最有价值的设计决策。
2. **契约先行**——Pydantic 契约定死后，前端、后端、测试三方并行不打架。
3. **离线可复现的测试体系**——96 后端 + 6 前端 + 22 评测集，全部离线可复现，重构有底气。

### 做得不好（Stop）

1. **防御性编程项做成了半成品**——#19 的重试做了，但抖动/计数/频控漏了，跨 Sprint 遗留。
2. **SSE 事件命名未先对齐需求**——返工一次，本可避免。
3. **文档滞后于代码**——Sprint 报告直到 Sprint 4 才补齐，违背了敏捷"每 Sprint 有交付物"的要求。

### 下轮改进（Improve）

1. **DoD 逐条勾选后再关闭 Issue**——避免"部分完成"被误记为"完成"。
2. **需求冻结后再进入实现**——接口命名、事件名等先评审。
3. **Sprint 内文档随任务一起完成**，不积压到最后。

## 九、贡献清单

| 成员 | 角色 | 负责 Issue | 交付内容 |
|---|---|---|---|
| **林翔宇** | Backend/AI | #1 #5 #8 #10 #11 #12 #19 #21 #24（9 个 / 40 SP） | SiliconFlow 连通、轮次控制、人设与 Prompt/Schema、完整辩论链、FastAPI 与 SQLite、结构化战报、重试与脱敏日志、Docker/Compose、发布验收 |
| **郑琢** | SM/QA/Docs | #3 #4 #6 #7 #9 #13 #14 #16 #18 #20 #25 #26（12 个 / 36 SP） | 系统六图与 README、Sprint 报告、Pydantic 契约与 BDD 骨架、仓库拓扑与 CI、单测/BDD/Mock 集成、注入防护、完整测试库、评测集与评分脚本、完整 CI 流水线 |
| **吴佳德** | PO/Frontend | #2 #15 #17 #22 #23（5 个 / 21 SP） | 偏好表单与会话创建、SSE 流式与状态恢复、React/Vite 完整交互、演示视频与答辩材料、工业级 README |

**贡献均衡性说明**：三人各承担一条完整纵线（后端/AI、QA/文档/DevOps、前端/PO），且各自都同时书写了生产代码与测试/文档，未出现"一人只写测试"或"一人只做前端"的不均衡。贡献分布（按 Story Points）为 40 : 36 : 21。

## 十、后续 Backlog（项目结项后）

| 优先级 | 项 | 说明 |
|---|---|---|
| P0 | 补齐 #19 抖动/计数/频控 | 见第七节 |
| P1 | 对齐 #15 SSE 事件命名 | 见第七节 |
| P1 | 配置 GHCR 推送 + v0.1.0 tag | 见第七节 |
| P2 | 录制演示视频 | 见第七节 |
| P2 | 接入真实食堂/外卖菜单数据源 | 拓展方向 |
| P3 | 多模态（菜品图片生成 / TTS 播报） | 拓展方向 |

## 十一、Issue 可追溯性

| Issue | 追溯 |
|---|---|
| #21 | `Dockerfile`、`docker-compose.yml`、`frontend/Dockerfile` |
| #23 | `README.md` |
| #24 | 提交 `1710952`（`dev.py`）；`.env.example` |
| #25 | 提交 `7ff8586`；`.github/workflows/ci.yml` |
| #26 | 本文档 |
