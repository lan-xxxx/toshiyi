# modules

## Key Findings

- 模块通过 `internal/modules.go` blank import 注册，注释明确 SQL migration 执行顺序由 SQL 文件时间戳排序，不由 import 顺序决定。Source: `internal/modules.go:1-18`
- 主要业务模块包括 agentmailgateway、backup、base、robot、botfather、channel、file、group、message、messages_search、notify、oidc、openapi、project、space、thread、user、usersecret、bot_api、app_bot、bot_provision、webhook 等。Source: `internal/modules.go:23-82`
- `runtime` 模块已移除，runtime/bot orchestration 由独立服务 `octo-fleet` 拥有；历史 migration 记录留在表里。Source: `internal/modules.go:58-61`
- `usersecret` 模块负责用户外部密钥别名表、write-only CRUD、resolve；resolve 鉴权按 `bf_` bot token 反查 `robot.creator_uid`。Source: `internal/modules.go:68-70`
- `BotAPI` 是公开 Bot API gateway 模块，处理 `/v1/bot/*` 并统一鉴权。Source: `modules/bot_api/bot_api.go:34-36`
- `Route` 注册 bot 端点：sendMessage、typing、readReceipt、events、messages/sync、groups、group md、space members、create/update group、thread、file、card、voice、OBO 等。Source: `modules/bot_api/bot_api.go:400-468`
- 复用的单条消息查询通过 `authtree.Mount(TreeBotToken, ...)` 接到 bot token 树，并带 App Bot scope guard。Source: `modules/bot_api/bot_api.go:470-477`

## Source References

| Topic | File | Lines | Notes |
| --- | --- | --- | --- |
| 模块注册规则 | `internal/modules.go` | 1-18 | migration 排序与 Go init 说明 |
| 模块清单 | `internal/modules.go` | 23-82 | blank imports |
| runtime 移除 | `internal/modules.go` | 58-61 | octo-fleet |
| usersecret 说明 | `internal/modules.go` | 68-70 | key alias + resolve |
| BotAPI 定位 | `modules/bot_api/bot_api.go` | 34-36 | `/v1/bot/*` gateway |
| BotAPI 路由 | `modules/bot_api/bot_api.go` | 400-468 | endpoint 清单 |
| authtree | `modules/bot_api/bot_api.go` | 470-477 | bot-token tree |

## Open Questions

- 每个模块的数据库迁移文件未逐一展开；如问表结构，需要按模块查 migrations/SQL 文件。
