# api error

## Key Findings

- 新的业务错误 facade 是 `httperr.ResponseErrorL` / `ResponseErrorLWithStatus`，统一交给 `wkhttp.ErrorRenderer` 渲染本地化错误信封。Source: `pkg/httperr/respond.go:13-23`, `pkg/httperr/respond.go:53-82`
- `ResponseErrorL` 保留 legacy wire/body `status=400` 兼容；`ResponseErrorLWithStatus` 用 code 的真实 HTTPStatus，仅适合无 legacy 依赖的新端点。Source: `pkg/httperr/respond.go:26-49`
- 未注册错误码会记录日志并 fallback 到 `err.shared.internal`。Source: `pkg/httperr/respond.go:62-66`
- Bot API 错误码命名空间是 `err.server.bot_api.*`；注释说明 bot_api 面向外部 bot adapters/integrations，许多站点要保留真实 HTTP 状态。Source: `pkg/errcode/bot_api.go:9-20`
- 常见 400 类错误包含 invalid request、limit exceeded、content/file too large、payload too large、unsupported file type、member not human、thread channel not accepted。Source: `pkg/errcode/bot_api.go:22-90`
- 常见 403 类错误包含 not group member、group disbanded、not group admin、not space member、App Bot unsupported/DM-only、not friend、conversation not started、message edit forbidden、OBO unauthorized、bot unavailable。Source: `pkg/errcode/bot_api.go:117-203`
- `err.server.bot_api.group_disbanded` 表示目标群已解散，`/v1/bot/sendMessage` 在发送前通过 server 侧 `group.status` 前置识别，不依赖 IM send 失败猜测；文案为“群已解散，无法继续发送消息”。Source: `pkg/errcode/bot_api.go:122-131`, `modules/bot_api/send.go:503-522`, `modules/bot_api/send.go:661-685`, `modules/bot_api/send.go:505-510`
- 源码注释明确说明 WuKongIM `/message/send` 对已解散群可能返回 HTTP 200 且无失败信号，因此 octo-server 必须在发送前自检群状态；thread 发送也会复用该前置判断。Source: `modules/bot_api/send.go:505-510`, `modules/bot_api/send.go:661-685`
- 常见 404 类错误包含 group/message/user not found、bot not registered、OBO grant/scope/channel not found。Source: `pkg/errcode/bot_api.go:209-258`
- Internal=true 的错误码不得把内部 message 暴露到 wire；调用方必须先记录带上下文的 zap.Error。Source: `pkg/errcode/bot_api.go:17-20`

## Source References

| Topic | File | Lines | Notes |
| --- | --- | --- | --- |
| localized error facade | `pkg/httperr/respond.go` | 13-23 | ResponseErrorL |
| semantic status | `pkg/httperr/respond.go` | 26-49 | ResponseErrorLWithStatus |
| render implementation | `pkg/httperr/respond.go` | 53-82 | status/code/details |
| bot_api namespace | `pkg/errcode/bot_api.go` | 9-20 | external adapters |
| validation errors | `pkg/errcode/bot_api.go` | 22-90 | 400 类 |
| forbidden errors | `pkg/errcode/bot_api.go` | 117-203 | 403 类 |
| group_disbanded error code | `pkg/errcode/bot_api.go` | 122-131 | 已解散群错误码定义 |
| group_disbanded precheck | `modules/bot_api/send.go` | 503-522 | sendMessage 前置判断 |
| group.status lookup | `modules/bot_api/send.go` | 661-685 | server 侧群状态查询 |
| not found errors | `pkg/errcode/bot_api.go` | 209-258 | 404 类 |

## Answer Snippets

### Bot 发消息返回 `err.server.bot_api.group_disbanded` 是什么意思？
表示目标群已解散，Bot API 在发送前已通过 server 侧 `group.status` 检测到该状态，因此直接返回 403 错误，错误文案为“群已解散，无法继续发送消息”。不能依赖 IM send 链路失败来判断，因为 WuKongIM `/message/send` 在该场景下可能返回 HTTP 200 且不给出失败信号。参考：`pkg/errcode/bot_api.go:122-131`、`modules/bot_api/send.go:503-522`、`modules/bot_api/send.go:661-685`。

### 这个错误覆盖 thread 吗？
覆盖。thread 发送也会进入 group 状态校验路径，已解散群会被同一前置逻辑拦截并返回 `err.server.bot_api.group_disbanded`。参考：`modules/bot_api/send.go:661-685`。

### 调用方如何处理？
不要对同一已解散群 ID 持续重试；如需继续通知，请确认群是否仍有效，或改用有效群/新建群后再发送。

## Open Questions

- 旧的 `c.ResponseError` 仍有 legacy 点位；若要做全局错误码改造，需要扫全仓调用点。
