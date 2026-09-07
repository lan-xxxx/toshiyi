# Bot API `/v1/bot/sendMessage` Errors: 群已解散

## 场景

当 Bot 调用 `/v1/bot/sendMessage` 向群或 thread 发送消息时，如果目标群已解散，调用方不应继续按“发送中/发送成功”处理，而应收到明确、可程序化识别的错误响应。

该错误场景由 octo-server 在发送前基于 server 侧 `group.status` 做前置识别；不能依赖下游 IM send 返回失败信号来判断。源码注释明确说明：WuKongIM `/message/send` 对已解散群可能返回 HTTP 200 且无失败信号，因此必须由 octo-server 自检。

## 错误码

- 错误码：`err.server.bot_api.group_disbanded`

## 响应行为

- HTTP 语义：403 Forbidden
- 响应文案：`群已解散，无法继续发送消息`
- 返回时机：发送前检测到目标群状态为已解散时直接返回，不继续进入消息发送链路
- 兜底逻辑：IM send 侧不作为主要判断来源，仅作为背景链路说明；核心判断在 Bot API + Group 状态侧前置完成

## 覆盖范围

- group 发送：已覆盖
- thread 发送：已覆盖（thread 发送仍会进入 group 状态校验，已解散群会被同一前置逻辑拦截）

## 源码证据

- 错误码定义：`pkg/errcode/bot_api.go:122-131`
- Bot sendMessage 前置判断群解散：`modules/bot_api/send.go:503-522`
- 解散状态来源为 server 侧 `group.status`：`modules/bot_api/send.go:661-685`
- WuKongIM 返回 HTTP 200 且无失败信号的注释说明：`modules/bot_api/send.go:505-510`、`pkg/errcode/bot_api.go:122-127`

## 调用方排查建议

1. 确认传入 `groupId` / thread 所属群是否仍有效。
2. 若目标群已解散，不要对同一群 ID 持续重试发送。
3. 如需继续通知相关用户，请重新选择有效群，或创建新群后再发送。
4. 若业务上出现“明明有群但仍返回 group_disbanded”，请核对群状态数据、缓存状态和请求使用的 groupId 是否正确。

## 验收检查点

- 发送到已解散 group 时返回 `err.server.bot_api.group_disbanded`
- 发送到已解散 thread 时返回 `err.server.bot_api.group_disbanded`
- 错误文案为 `群已解散，无法继续发送消息`
- 该判断发生在实际 IM send 之前，而非依赖 IM send 失败推断
