# im boundary

## Key Findings

- `octo-server` 通过 WuKongIM 驱动实时消息底座，README 称其为 IM core / real-time messaging core。Source: `README.md:29-37`, `README.md:165-168`
- 高层请求流程中，业务执行后会向 WuKongIM fan out IM message，并在需要外部桥接时触发 adapter。Source: `README.md:85-91`
- 配置层的 WuKongIM 控制面字段是 `wukongIM.apiURL` 和 `wukongIM.managerToken`。Source: `configs/tsdd.yaml:21-24`, `QUICKSTART.md:82-85`
- Bot sendMessage 入口是 `POST /v1/bot/sendMessage`，请求体包含 `channel_id`、`channel_type`、`payload` 等；先做参数和 OBO 保留字段校验，再进入权限/发送链路。Source: `modules/bot_api/send.go:36-96`
- Bot events long-poll 不是消息本身，而是 bot event queue 的读取；Redis sorted set `robotEvent:{robotID}` 是权威事件队列。Source: `docs/bot-events-longpoll.md:1-5`, `docs/bot-events-longpoll.md:60-65`
- 新事件生产者会 ring per-bot doorbell；doorbell 是 hint，不是权威数据。Source: `docs/bot-events-longpoll.md:36-68`
- `BotAPI` 的 OBO fan-out 注释说明 `SendMessageWithResult` 成功后才把 synthetic event enqueue 给 grantee bot。Source: `modules/bot_api/bot_api.go:44-54`, `modules/bot_api/bot_api.go:101-112`

## Source References

| Topic | File | Lines | Notes |
| --- | --- | --- | --- |
| WuKongIM 定位 | `README.md` | 29-37, 165-168 | IM core |
| fan out 流程 | `README.md` | 85-91 | Execute 后 fan out |
| WuKongIM 配置 | `configs/tsdd.yaml` | 21-24 | apiURL/managerToken |
| 发送入口 | `modules/bot_api/send.go` | 36-96 | sendMessage request |
| 事件队列 | `docs/bot-events-longpoll.md` | 1-5 | Redis zset |
| doorbell | `docs/bot-events-longpoll.md` | 36-68 | hint only |
| OBO fanout | `modules/bot_api/bot_api.go` | 44-54, 101-112 | synthetic event enqueue |

## Open Questions

- 具体 WuKongIM HTTP client 调用封装在 octo-lib/config 或相关包中，需要跨仓查 `octo-lib` 才能完整追踪。
