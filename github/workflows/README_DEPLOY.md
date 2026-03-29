# 一键部署说明 (GitHub Actions -> GHCR -> VPS)

该仓库已添加一个 GitHub Actions 工作流，用于：
1.  自动构建 Docker 镜像
2.  推送至 GitHub Container Registry (GHCR)
3.  通过 SSH 部署到你的 VPS 服务器

## 使用步骤

### 1. 配置仓库 Secrets
进入仓库 **Settings → Secrets and variables → Actions → New repository secret**，添加以下 Secrets：

| Secret 名称 | 说明 | 必填 |
|-------------|------|------|
| `SERVER_HOST` | VPS 公网 IP 或域名 | ✅ |
| `SERVER_USER` | SSH 登录用户名（如 root） | ✅ |
| `SERVER_SSH_KEY` | VPS 私钥（PEM 格式，无口令） | ✅ |
| `OPENAI_API_KEY` | 你的 OpenAI API Key | ✅ |
| `SERVER_SSH_PORT` | SSH 端口（默认 22，非必填） | ❌ |
| `GHCR_PAT` | 若 GHCR 镜像设为私有，需添加具有 `read:packages` 权限的 PAT | ❌ |

### 2. 配置工作流权限
进入 **Settings → Actions → General → Workflow permissions**，选择：
- **Read and write permissions**（读取和写入权限）
- 勾选 **Allow GitHub Actions to create and approve pull requests**

### 3. 触发部署
- 推送到 `main` 分支会自动触发部署流程
- 或在 **Actions** 页面手动触发 `workflow_dispatch`

### 4. 常见问题排查
- **构建/推送失败（403 错误）**：确认工作流权限为「读取和写入」，或使用 `GHCR_PAT` 登录
- **SSH 连接失败**：确认 `SERVER_SSH_KEY` 对应的公钥已添加到 VPS `~/.ssh/authorized_keys`
- **VPS docker pull 401/403**：若 GHCR 镜像为私有，需在 Secrets 中添加 `GHCR_PAT`，并在工作流中执行 `docker login ghcr.io`
