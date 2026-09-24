# 部署指南 - Deployment Guide

## Cloudflare Workers 部署

### 方式一：使用 Wrangler CLI（推荐）

#### 1. 安装 Wrangler

```bash
npm install -g wrangler
```

#### 2. 登录 Cloudflare

```bash
wrangler login
```

#### 3. 配置 wrangler.toml

编辑 `wrangler.toml` 文件，填入您的 Cloudflare 账户信息：

```toml
name = "v2sing"
main = "dist/worker.js"
compatibility_date = "2024-01-01"
account_id = "YOUR_CLOUDFLARE_ACCOUNT_ID"

# 可选：如果要绑定自定义域名
# routes = ["https://your-domain.com/*"]
```

#### 4. 构建项目

```bash
deno task build
```

#### 5. 部署到 Cloudflare Workers

```bash
wrangler deploy
```

#### 6. 更新部署

```bash
# 重新构建
deno task build
# 部署更新
wrangler deploy
```

---

### 方式二：使用 GitHub Actions 自动部署

#### 1. 创建 Cloudflare API Token

1. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com/)
2. 转到 "My Profile" > "API Tokens"
3. 点击 "Create Token"
4. 选择 "Edit Cloudflare Workers" 模板
5. 设置 Token 名称（例如：v2sing-deploy）
6. 复制生成的 API Token

#### 2. 配置 GitHub Secrets

在您的 GitHub 仓库中：

1. 转到 Settings > Secrets and variables > Actions
2. 点击 "New repository secret"
3. 添加以下 Secrets：
   - `CLOUDFLARE_API_TOKEN` - 您刚才生成的 API Token
   - `CLOUDFLARE_ACCOUNT_ID` - 您的 Cloudflare 账户 ID

#### 3. 触发部署

推送代码到 `master` 分支或创建新的 release tag，GitHub Actions 会自动构建并部署到 Cloudflare Workers。

您也可以手动触发：
1. 转到 Actions 标签页
2. 选择 "Cloudflare Workers Deploy" 工作流
3. 点击 "Run workflow" > "Run workflow"

---

### 方式三：直接上传构建产物

#### 1. 构建项目

```bash
deno task build
```

构建产物会生成在 `dist/worker.js` 文件中。

#### 2. 在 Cloudflare Workers 页面

1. 登录 [Cloudflare Workers](https://workers.cloudflare.com/)
2. 点击 "Create Worker" > "Upload existing Worker"
3. 上传 `dist/worker.js` 文件
4. 点击 "Deploy"

---

## 本地开发

### 启动开发服务器

```bash
deno task serve
```

这将启动一个本地开发服务器，监听在 `http://localhost:8000`，并支持热重载。

### 预览生产构建

```bash
deno task preview
```

这将构建项目并启动一个服务器来预览生产环境的代码。

---

## 配置模板

项目支持自定义配置模板。您可以：

1. 使用内置的版本化模板（在 `src/config/templates/` 目录下）
2. 创建自己的自定义模板

### 使用内置模板

```
https://your-worker.your-subdomain.workers.dev/?sub=YOUR_SUBSCRIPTION_URL&config=https://raw.githubusercontent.com/caiguguo123/v2sing/master/src/config/templates/sing-box-1.14.json
```

### 创建自定义模板

参考现有的模板文件，创建您自己的 JSON 配置文件，并使用 `{{ outbounds_tags }}` 和 `{{ outbounds }}` 占位符。

---

## 环境变量

如果需要，您可以在 Cloudflare Workers 中配置以下环境变量：

- `ENVIRONMENT` - 用于区分生产/开发环境
- `CACHE_TTL` - 缓存过期时间（秒）

在 `wrangler.toml` 中添加：

```toml
[vars]
ENVIRONMENT = "production"
CACHE_TTL = "3600"
```

---

## 问题排查

### 构建失败

确保您使用的是 Deno 2.0 或更高版本：

```bash
deno --version
```

### 部署失败

1. 检查 Cloudflare API Token 是否有效
2. 检查账户 ID 是否正确
3. 检查 wrangler.toml 配置是否正确
4. 确保 dist/worker.js 文件存在

### Worker 返回 500 错误

检查 Worker 日志：

```bash
wrangler tail
```

或者在 Cloudflare Dashboard 中查看 Worker 的日志。

---

## 更新日志

查看 [CHANGELOG.md](CHANGELOG.md) 获取详细的版本更新信息。
