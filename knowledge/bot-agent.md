# bot agent

## Key Findings

- OCTO 把 Lobster/OpenClaw digital double agents 作为一等会话参与者，服务端内置路由、会话、工具调用执行。Source: `README.md:39-43`
- `BotAPI` 是公开的 bot-facing gateway，处理 `/v1/bot/*`，统一认证。Source: `modules/bot_api/bot_api.go:34-36`
- Bot API 主路由包含 sendMessage、typing、readReceipt、events、event ack、messages sync、group/space、thread、file upload/download、card、voice context/transcribe、OBO grant 等。Source: `modules/bot_api/bot_api.go:400-468`
- register 与 heartbeat 不放在 `/v1/bot` 主组内，各自有特殊限流与恢复语义；heartbeat 是 bot 保活通道，register 是重连刷 IM token 的必经路径。Source: `modules/bot_api/bot_api.go:297-375`
- `POST /v1/bot/events` 默认 immediate read，`wait` 是 opt-in；`limit` 默认 20，上限 100。Source: `modules/bot_api/events.go:19-85`, `docs/bot-events-longpoll.md:10-31`
- App Bot 是 DM-only：群/子区操作被拒，事件中非 DM 会被防御性过滤并 auto-ACK。Source: `modules/bot_api/threads.go:20-28`, `modules/bot_api/events.go:106-118`
- Bot 事件 long-poll 的并发 hold 上限由 `OCTO_BOT_EVENTS_MAX_HOLDS` 控制，默认 64。Source: `docs/bot-events-longpoll.md:70-79`

## Source References

| Topic | File | Lines | Notes |
| --- | --- | --- | --- |
| Agent 一等公民 | `README.md` | 39-43 | Lobster orchestration |
| BotAPI 定位 | `modules/bot_api/bot_api.go` | 34-36 | gateway |
| register/heartbeat | `modules/bot_api/bot_api.go` | 297-375 | 恢复/保活路径 |
| 主路由 | `modules/bot_api/bot_api.go` | 400-468 | endpoint 清单 |
| events contract | `modules/bot_api/events.go` | 19-85 | cursor/limit/wait |
| long-poll doc | `docs/bot-events-longpoll.md` | 10-31, 70-79 | wire + knobs |
| App Bot DM-only | `modules/bot_api/threads.go` | 20-28 | group/thread denied |

## Open Questions

- `Agent` 具体 session/tool-call 实现不全在本仓 README 对应路径里；若考试追问 OpenClaw runtime，需要继续查相关 module 和外部服务 `octo-fleet`。
