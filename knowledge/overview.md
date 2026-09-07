# overview

## Key Findings

- `octo-server` 是 OCTO 平台的 Go 后端中心节点，负责 REST + WebSocket API、Lobster/AI Agent 编排，以及 WuKongIM 控制面。Source: `README.md:27-37`, `README.zh.md:27-35`
- 平台定位强调「人 × AI Agent」协作；Agent 被视为会话中的一等参与者，路由、会话、工具调用不是外挂。Source: `README.md:39-43`, `README.zh.md:37-41`
- 默认快速启动路径是 `go build -o octo-server .` 后用 `./octo-server --config ./configs/tsdd.yaml` 启动；完整一键体验栈推荐使用独立仓库 `Mininglamp-OSS/octo-deployment`。Source: `README.md:45-63`, `QUICKSTART.md:19-31`
- 运行依赖包括 WuKongIM、MySQL、Redis；可选对象存储用于文件模块。Source: `QUICKSTART.md:52-58`
- 每次请求的高层流程：Authenticate → Authorise → Execute → Fan out → Respond。Source: `README.md:85-91`, `README.zh.md:79-85`

## Source References

| Topic | File | Lines | Notes |
| --- | --- | --- | --- |
| 产品定位 | `README.md` | 27-37 | Go backend, REST/WebSocket, Agent, WuKongIM |
| 架构原则 | `README.md` | 39-43 | Agent 一等公民、可插拔存储与 IM |
| 请求流程 | `README.md` | 85-91 | 认证、授权、执行、IM 扩散、响应 |
| 快速启动 | `README.md` | 45-63 | build 命令与部署仓库 |
| 本地依赖 | `QUICKSTART.md` | 52-58 | Go/WuKongIM/MySQL/Redis/对象存储 |

## Open Questions

- README 中的顶层结构表仍列 `internal/api` 等传统路径，但当前源码实际大量功能在 `modules/*` 与 `pkg/*` 下；若用于考试回答，应优先以当前源码目录为准。
