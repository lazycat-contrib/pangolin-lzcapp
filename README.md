# Pangolin - LazyCat App

[Pangolin](https://github.com/ca-x/pangolin)（鲮鲤）自托管 AI API 聚合网关的懒猫微服（LazyCat）打包。

## 模式

- **Docker 镜像应用（lazycat 交付）**：镜像 `ghcr.io/ca-x/pangolin` 在 CI 中复制到 `registry.lazycat.cloud` 并回写 manifest（官方商店要求 lazycat 交付模式）。
- **自动跟随上游**：每日检查上游 `ghcr.io/ca-x/pangolin` 的 semver tag（`channel: stable`），发现新版本自动更新版本号与镜像、构建 LPK、发布商店。
- **双商店发布**：官方商店（LazyCat 应用商店）+ 喵喵商店（私有）。

## 配置要点

| 项 | 值 |
| --- | --- |
| 包名 | `community.lazycat.app.pangolin` |
| 子域名 | `pangolin` |
| 数据卷 | `/lzcapp/var/pangolin` → `/data`（SQLite + DuckDB + master.key） |
| 健康检查 | `GET /api/health/live` |
| 对外地址 | `PANGOLIN_PUBLIC_URL=https://${LAZYCAT_SUBDOMAIN}.${LAZYCAT_BOX_DOMAIN}`（OIDC 必需） |

### 部署参数（首次初始化）

- `admin_email`：管理员邮箱（默认 `admin@example.com`）
- `admin_password`：管理员密码（默认随机 20 位，至少 12 位）

两者会以 `PANGOLIN_ADMIN_EMAIL` / `PANGOLIN_ADMIN_PASSWORD` 注入，应用首次启动时自动创建管理员；登录页（`/login`）已配置免密自动填充（`builtin://simple-inject-password`）。

## 结构

| 文件 | 说明 |
| --- | --- |
| `package.yml` | 包元数据 |
| `lzc-manifest.yml` | 服务与路由配置 |
| `lzc-deploy-params.yml` | 安装参数（管理员邮箱/密码） |
| `lzc-build.yml` | 构建配置 |
| `icon.png` | 图标（上游 logo） |
| `.github/lazycat-action.yml` | [lazycat-github-action](https://github.com/ca-x/lazycat-github-action) 配置（双商店 + 官方应用信息/截图） |
| `.github/workflows/lazycat.yml` | 构建 + 发布工作流 |
| `.github/screenshots/` | 官方商店截图（6 桌面 + 4 移动） |

## 所需 Secrets

| Secret | 说明 |
| --- | --- |
| `LZC_API_TOKEN` | 懒猫开放平台 PAT（官方商店发布） |
| `APPSTORE_URL` / `APPSTORE_TOKEN` | 喵喵商店 API 地址与发布令牌 |
| `APP_ID` | 可选 |
| `PRIVATE_STORE_GROUP_CODES` | 可选，私有分组码 |

## 许可

Apache-2.0（上游）
