# rbac acl

## Key Findings

- README 把授权阶段概括为 org-aware RBAC、per-channel ACL、agent-identity gating。Source: `README.md:85-91`
- Bot 查询群列表只返回自己是成员的群：SQL 从 `group_member` join `group`，条件 `gm.uid = robotID AND gm.is_deleted = 0`，可选按 `space_id` 过滤。Source: `modules/bot_api/groups.go:32-67`
- Bot 读群信息/群成员前会检查自己是否在 `group_member` 中且 `is_deleted=0`；不在群则返回 `ErrBotAPINotGroupMember`。Source: `modules/bot_api/groups.go:69-130`
- Thread 相关端点共用 `validateBotGroupAccess`：App Bot 直接禁止群/子区操作；User Bot 必须是活跃父群成员（`ExistMemberActive`，排除被拉黑成员）。Source: `modules/bot_api/threads.go:20-52`
- Thread 创建/列表/读取等路由都依赖上述门禁。Source: `modules/bot_api/threads.go:72-145`
- 消息同步时，群聊要求 bot 是群成员；App Bot 对群同步直接走 DM-only 拒绝。Source: `modules/bot_api/sync.go:48-69`
- usersecret resolve 通过 `bf_` bot token 反查 `robot.creator_uid`，只允许解析该 owner 的密钥。Source: `modules/usersecret/db.go:162-184`, `modules/usersecret/api.go:306-314`

## Source References

| Topic | File | Lines | Notes |
| --- | --- | --- | --- |
| 授权阶段 | `README.md` | 85-91 | RBAC/ACL/Agent gate |
| 群列表 ACL | `modules/bot_api/groups.go` | 32-67 | 仅 bot 所在群 |
| 群详情/成员门禁 | `modules/bot_api/groups.go` | 69-130 | membership check |
| 子区门禁 | `modules/bot_api/threads.go` | 20-52 | App Bot 禁止，User Bot 活跃父群成员 |
| 子区路由复用门禁 | `modules/bot_api/threads.go` | 72-145 | create/list/get |
| 消息同步 ACL | `modules/bot_api/sync.go` | 48-69 | group membership / App Bot DM-only |
| 密钥 owner 限定 | `modules/usersecret/db.go` | 162-184 | bot token → creator_uid |

## Open Questions

- 还需继续展开普通用户/组织管理场景下的管理员、manager、owner 权限矩阵。
