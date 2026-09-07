# api error

## Key Findings

- 新的业务错误 facade 是 `httperr.ResponseErrorL` / `ResponseErrorLWithStatus`，统一交给 `wkhttp.ErrorRenderer` 渲染本地化错误信封。Source: `pkg/httperr/respond.go:13-23`, `pkg/httperr/respond.go:53-82`
- `ResponseErrorL` 保留 legacy wire/body `status=400` 兼容；`ResponseErrorLWithStatus` 用 code 的真实 HTTPStatus，仅适合无 legacy 依赖的新端点。Source: `pkg/httperr/respond.go:26-49`
- 未注册错误码会记录日志并 fallback 到 `err.shared.internal`。Source: `pkg/httperr/respond.go:62-66`
- Bot API 错误码命名空间是 `err.server.bot_api.*`；注释说明 bot_api 面向外部 bot adapters/integrations，许多站点要保留真实 HTTP 状态。Source: `pkg/errcode/bot_api.go:9-20`
- 常见 400 类错误包含 invalid request、limit exceeded、content/file too large、payload too large、unsupported file type、member not human、thread channel not accepted。Source: `pkg/errcode/bot_api.go:22-90`
- 常见 403 类错误包含 not group member、group disbanded、not group admin、not space member、App Bot unsupported/DM-only、not friend、conversation not started、message edit forbidden、OBO unauthorized、bot unavailable。Source: `pkg/errcode/bot_api.go:117-203`
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
| not found errors | `pkg/errcode/bot_api.go` | 209-258 | 404 类 |

## Open Questions

- 旧的 `c.ResponseError` 仍有 legacy 点位；若要做全局错误码改造，需要扫全仓调用点。
