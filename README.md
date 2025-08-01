# Cloudflare Docker 代理

一个基于 Cloudflare Workers 的 Docker 镜像代理服务，用于加速 Docker 镜像的拉取和推送。

## 功能特性

- 🚀 支持多个 Docker 镜像仓库代理
- 🌍 基于 Cloudflare Workers，全球加速
- 🔧 通过环境变量灵活配置自定义域名
- 📦 支持以下镜像仓库：
  - Docker Hub (docker.io)
  - Quay.io
  - Google Container Registry (gcr.io)
  - Kubernetes Container Registry (k8s.gcr.io, registry.k8s.io)
  - GitHub Container Registry (ghcr.io)
  - Cloudsmith
  - Amazon ECR Public

## 部署步骤

### 1. 创建 Cloudflare Workers 项目

1. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com/)
2. 进入 **Workers & Pages** 页面
3. 点击 **Create application**
4. 选择 **Create Worker**
5. 给项目起一个名字，然后点击 **Deploy**

### 2. 部署代码

1. 在 Workers 编辑器中，删除默认代码
2. 将 `_worker.js` 文件中的代码完整复制粘贴到编辑器中
3. 点击 **Save and Deploy**

### 3. 配置环境变量

1. 在 Workers 项目页面，点击 **Settings** 标签
2. 找到 **Environment Variables** 部分
3. 点击 **Add variable**
4. 添加以下环境变量：
   - **Variable name**: `CUSTOM_DOMAIN`
   - **Value**: 您的自定义域名（例如：`your-domain.com`）
5. 点击 **Save**

### 4. 使用 workers 路由优选，加速镜像站访问

1. 在 Workers 项目页面，点击 **Settings** 标签
2. 找到 **Domains & Routes** 部分
3. 点击 **Add Route**
4. 输入 `docker.your-domain.com/*`，如果你还需要拉取其它的镜像仓库，可以继续添加。
5. 支持添加多个 workers 路由，用来代理多个镜像仓库，比如 `gcr.your-domain.com/*` 等等
6. workers 路由添加完成以后，需要添加一条 cname 记录来优选 IP，比如将 `docker.your-domain.com` cname 到 `www.dynadot.com`

#### 支持的镜像仓库

| 仓库 | 代理地址格式 | 原始地址 |
|------|-------------|----------|
| Docker Hub | `docker.your-domain.com` | `registry-1.docker.io` |
| Quay.io | `quay.your-domain.com` | `quay.io` |
| Google Container Registry | `gcr.your-domain.com` | `gcr.io` |
| Kubernetes GCR | `k8s-gcr.your-domain.com` | `k8s.gcr.io` |
| Kubernetes Registry | `k8s.your-domain.com` | `registry.k8s.io` |
| GitHub Container Registry | `ghcr.your-domain.com` | `ghcr.io` |
| Cloudsmith | `cloudsmith.your-domain.com` | `docker.cloudsmith.io` |
| Amazon ECR Public | `ecr.your-domain.com` | `public.ecr.aws` |



### 如果还是不懂，看视频


## 环境变量说明

| 变量名 | 必需 | 默认值 | 说明 |
|--------|------|--------|------|
| `CUSTOM_DOMAIN` | 是 | `example.com` | 您的自定义域名，用于构建各个镜像仓库的代理地址 |

## 注意事项

1. **域名配置**：确保在 Cloudflare Workers 环境变量中正确设置了 `CUSTOM_DOMAIN`
2. **DNS 解析**：如果使用自定义域名，请确保域名已正确解析到 Cloudflare
3. **访问限制**：某些镜像仓库可能有访问限制，请遵守相关服务条款
4. **流量限制**：Cloudflare Workers 免费版有请求次数限制，请根据需要选择合适的套餐

## 故障排除

### 常见问题

1. **404 错误**：检查环境变量 `CUSTOM_DOMAIN` 是否正确设置
2. **认证失败**：某些私有镜像需要正确的认证信息
3. **拉取缓慢**：可能是网络问题，可以尝试切换不同的代理地址
4. **1101 错误**：运气不好就被风控，需要的自己试试加密混淆啥的