# build release

## Key Findings

- 根模块是 `github.com/Mininglamp-OSS/octo-server`，Go 版本声明为 1.25。Source: `go.mod:1-4`
- 最小本地构建命令：`go build -o octo-server .`，然后 `./octo-server --config ./configs/tsdd.yaml`。Source: `README.md:45-52`, `QUICKSTART.md:60-71`
- `go build ./...` 只编译检查所有包，不会写 runnable binary；要可运行二进制必须显式 build root main package 并 `-o octo-server .`。Source: `QUICKSTART.md:68-71`
- 一键 Docker Compose 体验栈不在本仓维护，官方 OOTB 部署收口到 `Mininglamp-OSS/octo-deployment`。Source: `README.md:60-66`, `QUICKSTART.md:19-31`, `BUILDING.md:33-39`
- 只构建本仓容器镜像可用 `make build`，Docker Hub 镜像名为 `mininglamposs/octo-server`，由 `.github/workflows/docker-publish.yml` 发布。Source: `BUILDING.md:43-52`
- 旧 `push` / `deploy` / `deploy-v2` Makefile 目标不是 canonical release surface，不应使用。Source: `BUILDING.md:57-62`
- public build 后标准 Go toolchain 会从 `proxy.golang.org` 解析依赖；private preview 阶段可能需要 cross-repo replace workaround。Source: `BUILDING.md:9-31`

## Source References

| Topic | File | Lines | Notes |
| --- | --- | --- | --- |
| Module/Go version | `go.mod` | 1-4 | Go 1.25 |
| Quickstart build | `README.md` | 45-52 | build + run |
| Go build nuance | `QUICKSTART.md` | 60-75 | `go build ./...` vs binary |
| OOTB deploy | `README.md` | 60-66 | octo-deployment |
| Docker image | `BUILDING.md` | 43-52 | make build / Docker Hub |
| Deprecated targets | `BUILDING.md` | 57-62 | do not use |
| private/public build | `BUILDING.md` | 9-31 | go.sum / proxy |

## Open Questions

- Release tag/versioning policy需继续查 GitHub Actions workflow 和 CONTRIBUTING。
