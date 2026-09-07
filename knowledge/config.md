# config

## Key Findings

- 配置模板是 `configs/tsdd.yaml`；基础项包含 `mode`、`addr`、`grpcAddr`、`appName`、`rootDir`、消息跨设备保存、群升级阈值等。Source: `configs/tsdd.yaml:1-14`
- Webhook 可配置 HMAC-SHA256 签名密钥 `webhookSecretKey`，也可由环境变量 `TS_WEBHOOK_SECRET_KEY` 提供；配置后请求需带 `X-Signature-256`。Source: `configs/tsdd.yaml:15-19`
- WuKongIM 配置包括 `wukongIM.apiURL` 和 `wukongIM.managerToken`。Source: `configs/tsdd.yaml:21-24`, `QUICKSTART.md:82-85`
- DB 配置包括 MySQL DSN、Redis 地址/密码/TLS、异步任务 Redis。Source: `configs/tsdd.yaml:26-34`, `QUICKSTART.md:82-84`
- 外网配置包含 `external.ip`、`external.baseURL`、`external.webLoginURL`；`webLoginURL` 影响卡片通知 deep-link，缺失/非 https 时会降级纯文本 DM。Source: `configs/tsdd.yaml:36-44`
- 文件服务支持 minio、aliyunOSS、seaweedFS、qiniu 等；预签名 PUT/GET 支持矩阵写在配置注释里。Source: `configs/tsdd.yaml:69-103`, `configs/tsdd.yaml:135-170`
- `viper` 设置了环境变量前缀 `TS`，并把点号替换为下划线，因此配置可通过环境变量覆盖。Source: `main.go:140-145`
- runtime 语言 fallback 用环境变量而非 YAML，`OCTO_DEFAULT_LANGUAGE` 支持 `zh-CN` / `en-US`。Source: `QUICKSTART.md:89-93`

## Source References

| Topic | File | Lines | Notes |
| --- | --- | --- | --- |
| 基础配置 | `configs/tsdd.yaml` | 1-14 | mode/addr/rootDir 等 |
| Webhook 签名 | `configs/tsdd.yaml` | 15-19 | HMAC + X-Signature-256 |
| WuKongIM | `configs/tsdd.yaml` | 21-24 | apiURL/managerToken |
| DB/Redis | `configs/tsdd.yaml` | 26-34 | mysqlAddr/redisAddr/TLS |
| 外网 URL | `configs/tsdd.yaml` | 36-44 | baseURL/webLoginURL |
| 文件服务 | `configs/tsdd.yaml` | 69-170 | CORS、presigned、MinIO/OSS/COS/Qiniu |
| 环境变量覆盖 | `main.go` | 140-145 | `TS_` prefix |
| 语言环境变量 | `QUICKSTART.md` | 89-93 | `OCTO_DEFAULT_LANGUAGE` |

## Open Questions

- 配置 schema 的来源在 `octo-lib/config`，本仓只展示模板；若问字段完整定义，需要继续查 `octo-lib`。
