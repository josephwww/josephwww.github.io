# Hexo Blog 自动部署配置

## GitHub Actions 自动部署

本项目配置了GitHub Actions来自动构建和部署Hexo博客到服务器。

### 配置步骤

#### 1. 在GitHub仓库中设置Secrets

在GitHub仓库页面，进入 `Settings` > `Secrets and variables` > `Actions`，添加以下secrets：

- `HOST`: 你的服务器IP地址或域名
- `USERNAME`: SSH登录用户名
- `DEPLOY_KEY`: 私钥内容（用于SSH连接）

#### 2. 生成SSH密钥对

在本地生成SSH密钥对：

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

#### 3. 配置服务器

1. 将公钥添加到服务器的 `~/.ssh/authorized_keys`：
   ```bash
   cat ~/.ssh/id_rsa.pub | ssh user@your-server "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
   ```

2. 确保目标目录存在：
   ```bash
   sudo mkdir -p /var/www/hexo-blog
   sudo chown $USER:$USER /var/www/hexo-blog
   ```

3. 配置Web服务器（如Nginx）指向 `/var/www/hexo-blog` 目录

#### 4. 测试部署

推送代码到main或master分支，GitHub Actions将自动：

1. 安装依赖
2. 运行 `npx hexo generate` 生成静态文件
3. 将 `public/*` 目录内容上传到服务器的 `/var/www/hexo-blog`

### 工作流程

- 当推送代码到 `main` 或 `master` 分支时触发
- 使用Ubuntu最新版本作为运行环境
- 使用Node.js 18
- 使用actions/cache@v4缓存npm依赖以加速构建
- 使用SCP上传文件到服务器

### 故障排除

1. **SSH连接失败**：检查SSH密钥配置和服务器防火墙设置
2. **权限错误**：确保目标目录存在且有正确的权限
3. **构建失败**：检查package.json中的依赖是否正确

### 手动部署

如果需要手动部署，可以运行：

```bash
npm install
npx hexo generate
# 然后手动上传public目录到服务器
``` 