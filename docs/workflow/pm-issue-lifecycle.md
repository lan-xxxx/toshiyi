# PM Issue Lifecycle

本流程沉淀自一次完整的 Octo-server 产品管家考试链路，用于把群聊需求转成可验收、可追踪、可复盘的 GitHub issue 闭环。

## 1. 群里收到需求

- 在群聊中识别明确需求、bug、问题或验收请求。
- 记录用户给出的背景、目标、影响模块、验收标准。
- 若需求涉及源码事实，后续 PRD / review 必须补充可核验的源码路径和行号。

## 2. 创建 GitHub issue

- 在产品需求池仓库创建 issue。
- issue 内容至少包含：背景、需求说明 / 问题定义、影响范围、验收标准、风险或待确认点。
- 如用户已经给出标题和验收标准，应保留原始意图，不擅自扩大范围。

## 3. 打 type / priority / module / status labels

创建或更新 issue 后，需要按项目标签体系打标：

- `type/*`：需求类型，例如 `type/feature`、`type/bug`、`type/prd`、`type/question`
- `priority/*`：优先级，例如 `priority/P1`
- `module/*`：影响模块，例如 `module/api`、`module/im`
- `status/*`：当前状态，例如 `status/new`、`status/prd-draft`、`status/in-review`、`status/need-info`、`status/done`

同一 issue 原则上只保留一个 `status/*` 标签，状态流转时移除旧状态并添加新状态。

## 4. 补 PRD draft

进入 PRD 阶段时，将 issue 状态推进到 `status/prd-draft`，并补充 PRD 草案。建议结构：

- 背景
- 问题定义
- 影响范围
- 需求说明
- 验收标准
- 风险 / 待确认点
- 源码证据（如适用）

PRD 应区分“新增能力”“修复问题”“验证现有实现”“文档补充”等不同任务类型，避免把已经存在的能力误写成新增需求。

## 5. 发起 review

PRD 草案完成后：

- 将 issue 推进到 `status/in-review`
- 在 issue comment 中发起 review 请求
- 明确请 reviewer 关注需求方向、错误码 / API 行为、影响范围、验收标准可测性、源码证据是否充分。

## 6. reviewer 打回

如果 reviewer/QC 发现方向不对、证据不足或实现已存在，应明确打回：

- 在 issue comment 中记录打回结论和原因
- 将状态退回 `status/need-info` 或 `status/prd-draft`
- 标出需要修正的点，例如：需求类型调整、源码证据补充、验收标准调整、子任务标题 / 内容修正。

## 7. 按打回点修正

根据 reviewer 打回点更新 issue：

- 修正 PRD 内容和问题定义
- 补充源码证据路径 / 行号
- 调整 labels 和状态
- 必要时更新子任务标题、正文和验收标准
- 在 comment 中说明“已按打回点修正，请复审”

## 8. 拆验证 / 文档子任务

对于需要落地的事项，可以拆成可独立验收的子任务：

- 验证任务：确认现有实现覆盖范围、错误码、源码证据、测试覆盖情况
- 文档任务：补充 API 文档、知识库、排查建议、验收说明
- 修复任务：仅在确认存在缺陷时再拆，例如文案本地化缺失、测试缺失、行为不一致

子任务同样需要 labels、验收标准和状态流转。

## 9. 完成后 status/done

当验证、文档、修复等子任务完成后：

- 在每个 issue 中补充完成 comment
- 写清楚完成结论、变更文件、提交链接或证据链接
- 将相关 issue 状态推进到 `status/done`
- 确认旧状态标签已移除

## 10. close issue

当 issue 已达到 `status/done` 且无需继续跟进时：

- 在 issue comment 中写明：`流程已完成，状态为 status/done，issue closed。`
- 将 GitHub issue 状态改为 closed
- 如只是阶段完成但仍需开发排期，不要提前 close，应保留 open 并标明下一步 owner / blocker

## 11. 回群同步

完成后在群里同步：

- 主 issue 和子任务链接
- 每个 issue 的最终状态（例如 `status/done` + closed）
- 文档或提交链接
- 本次链路最终结论

同步内容应简洁、可核验，避免只说“已处理”而不给链接或证据。

## 状态流转参考

```text
status/new
  -> status/prd-draft
  -> status/in-review
  -> status/need-info   # reviewer 打回时
  -> status/prd-draft   # 修正后可重新进入草案态
  -> status/in-review   # 复审
  -> status/done
  -> closed
```

## 本次考试链路示例

- #2 主需求：Bot API 发送消息到已解散群时，返回更明确的错误说明与排查建议
- #3 验证任务：Bot API 已解散群错误前置识别现有实现覆盖情况
- #4 文档任务：Bot API 文档 + `knowledge/api-error.md` 错误场景说明

本次关键经验：review/QC 发现核心实现已经存在时，应及时把方向从“新增实现”纠偏为“现有实现验证 + 文档/知识库补充”，并同步修正 issue、labels、子任务和验收标准。
