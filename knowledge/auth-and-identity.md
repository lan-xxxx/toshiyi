# auth and identity

## Key Findings

- Bot API 使用 `Authorization: Bearer <token>`，`extractBotToken` 只接受 `Bearer ` 前缀。Source: `modules/bot_api/auth.go:143-148`
- Bot token 按前缀分流：`app_` 走 App Bot，其他（含 `bf_` 与 legacy）走 User Bot。Source: `modules/bot_api/auth.go:25-41`
- User Bot 鉴权查询 `robot` 表：`bot_token=? AND bot_token!='' AND status=1`。Source: `modules/bot_api/db.go:56-64`
- App Bot 鉴权先查内存/共享 registry，miss 后回退 DB，DB 命中后异步 warm cache；注释强调 Warm 是 SETNX，避免并发 revoke 被旧值复活。Source: `modules/bot_api/auth.go:64-123`
- `/v1/bot` 主组中间件顺序是 `authBot → requireBotIdentity → per-bot limiter`，顺序不能颠倒，否则限流拿不到身份会静默失效。Source: `modules/bot_api/bot_api.go:377-408`
- 登录态用户 token 解析在 `main.go` 注入自定义 `TokenParser`，支持缓存 token、token validator、语言解析和角色解析。Source: `main.go:205-227`
- `bot_provision` 的旧 JWT 签发/校验/JWKS 已移除，当前剩 bot mint/token lookup 相关入口。Source: `modules/bot_provision/jwt.go:1-10`
- `uk_` user API key 由 botfather 的 `UserAPIKeyService` 管理，支持 get-or-create、integration client 维度和 `AuthByKey`。Source: `modules/botfather/service_userapikey.go:30-60`

## Source References

| Topic | File | Lines | Notes |
| --- | --- | --- | --- |
| Bearer 解析 | `modules/bot_api/auth.go` | 143-148 | 仅 Authorization Bearer |
| Bot 类型 | `modules/bot_api/auth.go` | 10-13, 25-41 | `bf_`/legacy vs `app_` |
| User Bot DB 鉴权 | `modules/bot_api/db.go` | 56-64 | robot 表 active bot_token |
| App Bot cache/DB | `modules/bot_api/auth.go` | 64-123 | registry + DB fallback + warm |
| 主组认证顺序 | `modules/bot_api/bot_api.go` | 377-408 | auth/identity/limiter |
| 用户 token parser | `main.go` | 205-227 | cache token parser + validator |
| JWT 移除 | `modules/bot_provision/jwt.go` | 1-10 | signing/JWKS removed |
| `uk_` key | `modules/botfather/service_userapikey.go` | 30-60 | User API key service |

## Open Questions

- 需要进一步梳理普通 Web/API 用户登录 token 的生成路径；当前页只覆盖了解析与 bot/API-key 相关身份。
