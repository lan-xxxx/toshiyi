# storage dependency

## Key Findings

- 本地 Go build 运行要求可达的 WuKongIM、MySQL 8、Redis 7；对象存储对文件模块是可选项。Source: `QUICKSTART.md:52-58`
- 配置中的 DB 字段包括 MySQL DSN、Redis 地址/密码、Redis TLS、自定义 CA、异步任务 Redis。Source: `configs/tsdd.yaml:26-34`
- Redis wrapper 默认 MaxRetries 为 3，并封装 Set/Get/List/Hash/Set 等常用操作。Source: `pkg/redis/redis.go:19-37`, `pkg/redis/redis.go:39-180`
- 基础 DB model 带 `Id`、`CreatedAt`、`UpdatedAt`，时间 JSON 格式为 `2006-01-02 15:04:05`。Source: `pkg/db/db.go:7-35`
- 文件服务配置说明了 browser-direct upload 的 CORS 要求、签名 header 契约和预签名支持矩阵：MinIO/COS/OSS 支持 PUT/GET，Qiniu 不支持单 PUT，SeaweedFS 不支持 presign。Source: `configs/tsdd.yaml:69-103`
- MinIO/COS/OSS/Qiniu/SeaweedFS 的配置字段写在 `configs/tsdd.yaml`。Source: `configs/tsdd.yaml:135-170`
- usersecret 数据访问层依赖 MySQL 表 `user_secret_alias` 和 `user_secret_resolve_audit`，resolve 成功后 best-effort 回写 `last_used_at` 且不污染 `updated_at`。Source: `modules/usersecret/db.go:43-141`, `modules/usersecret/db.go:119-130`
- usersecret resolve 只有 secret_id 或 display_name 精确唯一命中才自动解密返明文；模糊命中一律返回脱敏候选，避免静默错选。Source: `modules/usersecret/service.go:213-300`

## Source References

| Topic | File | Lines | Notes |
| --- | --- | --- | --- |
| 运行依赖 | `QUICKSTART.md` | 52-58 | WuKongIM/MySQL/Redis/object store |
| DB 配置 | `configs/tsdd.yaml` | 26-34 | mysql/redis/TLS |
| Redis wrapper | `pkg/redis/redis.go` | 19-180 | client + ops |
| DB BaseModel | `pkg/db/db.go` | 7-35 | id/time fields |
| 文件服务矩阵 | `configs/tsdd.yaml` | 69-103 | presigned support |
| 对象存储字段 | `configs/tsdd.yaml` | 135-170 | minio/oss/seaweed/qiniu |
| usersecret store | `modules/usersecret/db.go` | 43-141 | alias/audit tables |
| usersecret resolve | `modules/usersecret/service.go` | 213-300 | exact/fuzzy/ambiguous |

## Open Questions

- SQL migration 文件需单独索引，才能完整列出每个模块的表结构与索引。
