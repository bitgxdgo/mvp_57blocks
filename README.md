# mvp_57blocks
## 基于 AI 的 MVP 协作开发工作流（GitHub 版使用说明）

### 适用对象与目标
- **适用对象**：PM、UI 设计师、开发工程师（Dev）、QA
- **目标**：用一套可复用的命令与文档结构，把“需求—设计—实现—测试—交付”变成**可追溯、可并行、可验收**的流水线，最终在 GitHub 上完成**项目管理 + 文档管理 + 代码交付**。

---

## 总原则（所有角色都必须遵守）

### 单一事实源（Single Source of Truth）
- **权威文档一律在 `docs/`**，其它地方只允许“链接/引用”，不允许出现第二份口径。
- 永远保留一组**稳定入口**，供 AI 与人类默认读取（不要改路径）：
  - `docs/PRD.md`
  - `docs/UI_Specifications.md`
  - `docs/Tech_Spec.md`
  - `docs/Test_Spec.md`
  - `docs/mockup/`
  - `docs/mockup-reviews/LATEST.md`

### “读索引再开工”
- 在 `docs/README.md`（或 `docs/INDEX.md`）维护 **Active Document Set（当前生效文档集）**，明确本次 MVP 的权威文档路径。
- **任何人/任何 AI 开始工作前**：先读索引，再读文档集。

### GitHub 作为项目管理中心
- **Issue**：承载需求条目、缺陷、待办与决策点
- **Pull Request**：承载实现变更与评审讨论
- **Milestone/Project（可选）**：承载迭代节奏与看板
- **每个重要产物（PRD/UI/Tech/Test/Test Run）都要在 GitHub 留痕**：提交到仓库，并在对应 Issue/PR 中链接。

---

## 目录与产物约定（建议统一）

### 推荐目录
- **命令模板**：`.cursor/commands/`（不放项目事实，只放“怎么生成/怎么检查”）
- **权威文档**：`docs/`
- **过程产物（可选提交）**：
  - `issues/`：保存从 `/create-issue` 生成的 issue 草稿（也可直接用 GitHub Issue，不一定需要文件）
  - `plans/`：从 `/create-plan` 生成的执行计划

### 多份 PRD/多版本如何管理
- 仍保留 `docs/PRD.md` 作为“当前生效版”入口
- 历史与并行放入二级目录（推荐按 initiative 分桶）：
  - `docs/initiatives/<slug>/PRD.md` 等全套文档
  - `docs/archive/...` 存历史版本（只增不改）
- `docs/README.md` 里声明：**当前 active 的 initiative 是哪个**，并链接其目录。

---

## 角色协作方式（谁用哪些命令、产出什么、怎么在 GitHub 管理）

### PM（产品）
- **主要职责**：定义“为什么做、做什么、怎么验收、哪些不做”，并对范围与优先级负责
- **主要命令**
  - `/create-prd` → 产出 `docs/PRD.md`
- **GitHub 管理方式**
  - 为每个 MVP/需求包建一个 Epic Issue（或 Milestone）
  - PRD 完成后，在 Epic Issue 中贴出链接：`docs/PRD.md`（并注明版本/日期）

### UI 设计师（Design）
- **主要职责**：把 PRD 转成可落地的 UI 规范，并通过 mockup 让“最终效果”可被确认
- **主要命令**
  - `/create-ui-specifications` → `docs/UI_Specifications.md`
  - `/create-mockup` → `docs/mockup/`
  - `/create-mockup-review` → `docs/mockup-reviews/` + `docs/mockup-reviews/LATEST.md`
- **GitHub 管理方式**
  - 在 Epic Issue 下拆子 Issue（或用任务清单）跟踪：UI Spec 完成、Mockup 完成、Mockup Review 通过
  - 每次 mockup review 更新后，在 Issue 里链接 `docs/mockup-reviews/LATEST.md`

### Dev（开发工程师）
- **主要职责**：基于权威文档集实现代码，并确保与 mockup 对齐；对实现质量、可维护性负责
- **主要命令**
  - `/create-tech-spec` → `docs/Tech_Spec.md`
  - `/explore`（开始编码前必须）→ 输出分析结论（建议汇总到 PR/Issue 评论或补充到 `docs/Tech_Spec.md`）
  - `/create-plan` → `plans/<feature>.md`（或将计划写入 GitHub Issue）
  - `/execute` → 实施代码
  - `/review`、`/peer-review` → 代码质量门禁
  - `/document` → 更新与代码相关的文档（至少保证 `docs/` 与根目录索引不漂移）
- **GitHub 管理方式**
  - 每个可交付变更对应一个 PR
  - PR 描述必须引用：PRD/UI/Tech/Test 的链接（至少 active 那组）
  - PR 合并前必须满足：Review 通过 + Test Run（或最小测试证据）齐全

### QA（测试）
- **主要职责**：把需求与技术方案转成可执行测试计划，并用证据驱动判定是否可发布
- **主要命令**
  - `/create-test-spec` → `docs/Test_Spec.md`
  - `/test` → 建议产出 `docs/Test_Run_YYYY-MM-DD.md`（并在 GitHub Issue/PR 中链接）
- **GitHub 管理方式**
  - 缺陷一律用 GitHub Issue 记录（附复现步骤与证据）
  - 发布结论在 Epic Issue（或 Release Issue）里给出：PASS/FAIL/BLOCKED 与阻塞项列表

---

## 端到端流程（从 0 到 MVP 上线）

### 阶段 0：建立 GitHub 项目骨架（一次性）
- 建仓库并约定：
  - `docs/` 为权威文档目录
  - `docs/README.md` 维护 Active Document Set
- 建一个 Epic Issue：`MVP: <名称>`
-（可选）建 GitHub Project 看板：Backlog / In Progress / Review / Done

### 阶段 1：需求成文（PM 主导）
- PM 运行 `/create-prd`
- 产出并提交 `docs/PRD.md`
- 在 Epic Issue 中链接 PRD，并由 Design/Dev/QA 做一次快速确认（范围、验收、Out of Scope）

### 阶段 2：设计规范与可运行 mockup（Design 主导）
- Design 运行 `/create-ui-specifications` → 提交 `docs/UI_Specifications.md`
- Design 运行 `/create-mockup` → 提交 `docs/mockup/`
- Design 运行 `/create-mockup-review` → 提交 `docs/mockup-reviews/*` + 更新 `LATEST.md`
- 关键门禁：**Design 确认 mockup 可作为“视觉与交互基准”**

### 阶段 3：技术方案与实现对齐（Dev 主导）
- Dev 运行 `/create-tech-spec` → 提交 `docs/Tech_Spec.md`
- 关键门禁（强烈建议写入 Tech Spec）：
  - 页面/组件对照表（mockup 源文件 ↔ 目标实现位置）
  - 允许偏差清单（若 mockup 与 UI Spec 不一致，如何裁决）

### 阶段 4：测试方案（QA 主导）
- QA 运行 `/create-test-spec` → 提交 `docs/Test_Spec.md`
- 关键门禁：P0 用例与 Release Gate 明确、可执行、可留证据

### 阶段 5：探索、计划、实现（Dev 主导，其他角色协同）
- Dev 在开工前运行 `/explore`（必须）
- 复杂任务运行 `/create-plan`（输出到 `plans/` 或直接贴进 GitHub Issue 作为 checklist）
- Dev 运行 `/execute` 增量实现，持续更新进度（Issue checklist 或 `plans/`）
- Dev 在 PR 上运行 `/review`（必要时 `/peer-review`）

### 阶段 6：测试执行与发布判定（QA 主导）
- QA 运行 `/test` 按 `docs/Test_Spec.md` 执行
- 产出并提交 `docs/Test_Run_YYYY-MM-DD.md`（或同等证据链接）
- 在 Epic Issue 给出发布结论与阻塞项；阻塞项对应 GitHub Issues

### 阶段 7：文档收口（所有角色）
- Dev 运行 `/document` 做最终一致性检查（docs 索引、Tech/Test 与实现一致）
- PM 更新 PRD 的版本历史/变更说明（如范围变更）
- Design 更新 UI Spec 或在偏差清单里明确裁决

---

## 让 AI “索引正确文档”的落地规则（强烈建议写进团队约定）

### AI 每次输出必须附带“依据清单”
在任何关键产出（plan/tech decisions/review/test run）开头写清楚：
- “本次基于：`docs/PRD.md`（版本/日期），`docs/UI_Specifications.md` ……”

### 禁止混用多份文档口径
- 同一个 PR/Issue 只允许引用**同一套 Active Document Set**
- 如果要切换 initiative 或切换 PRD 版本，必须先更新 `docs/README.md` 的 Active 指针，并在 Epic Issue 留痕说明

---

## 建议的 GitHub 标签与最小流程（可直接采用）
- Labels（示例）
  - `role:pm` `role:design` `role:dev` `role:qa`
  - `type:prd` `type:ui` `type:tech` `type:test` `type:bug` `type:chore`
  - `priority:p0` `priority:p1` `priority:p2`
- 最小流程
  - 任何代码变更必须走 PR
  - PR 合并需要：Review 通过 + 最小测试证据（至少 P0）

---

## 快速上手（给每个角色的一句话行动指南）
- **PM**：先用 `/create-prd` 把范围与验收写进 `docs/PRD.md`，并在 Epic Issue 链接它。
- **Design**：用 `/create-ui-specifications` → `/create-mockup` → `/create-mockup-review` 把“最终效果”落到 `docs/mockup/` 并可审查。
- **Dev**：用 `/create-tech-spec` 把 mockup 映射到实现结构；开工必 `/explore`，复杂任务必 `/create-plan`，实现后 `/review`。
- **QA**：用 `/create-test-spec` 定义 P0 门禁；用 `/test` 出具 `docs/Test_Run_...md` 作为发布依据。


