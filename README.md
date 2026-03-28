# Xboard

<div align="center">

[![Telegram](https://img.shields.io/badge/Telegram-Channel-blue)](https://t.me/XboardOfficial)
![PHP](https://img.shields.io/badge/PHP-8.2+-green.svg)
![MySQL](https://img.shields.io/badge/MySQL-5.7+-blue.svg)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

</div>

## 🚀 安装

### 环境准备

安装 Docker：
```bash
curl -sSL https://get.docker.com | bash

# CentOS 系统还需执行：
systemctl enable docker
systemctl start docker
```

### 部署步骤

1. 拉取项目文件：
```bash
git clone -b compose --depth 1 https://github.com/cedar2025/Xboard
cd Xboard
```

2. 初始化并安装（推荐，使用内置 SQLite + Redis）：
```bash
docker compose run -it --rm \
    -e ENABLE_SQLITE=true \
    -e ENABLE_REDIS=true \
    -e ADMIN_ACCOUNT=admin@demo.com \
    web php artisan xboard:install
```

> ⚠️ 安装完成后请务必保存显示的管理员账号和密码。

3. 启动服务：
```bash
docker compose up -d
```

4. 访问面板：
- 默认端口：**7001**
- 访问地址：`http://服务器IP:7001`

---

## ⚙️ 配置

### 基础配置

安装目录下的 `.env` 文件包含所有核心配置项，主要包括：

| 配置项 | 说明 |
|--------|------|
| `APP_KEY` | 应用加密密钥（安装时自动生成） |
| `DB_*` | 数据库连接信息（使用 MySQL 时填写） |
| `REDIS_*` | Redis 连接信息 |
| `APP_URL` | 站点访问地址 |

修改 `.env` 后需要重启服务使配置生效：
```bash
docker compose restart
```

### 使用 MySQL 替代 SQLite

若需使用 MySQL，请先自行安装 MySQL，然后在安装时不传入 `ENABLE_SQLITE=true`，改为自定义安装：
```bash
docker compose run -it --rm web php artisan xboard:install
```
按提示填写数据库连接信息即可。

### 修改管理后台路径

在 `.env` 中修改 `ADMIN_PATH` 后，必须重启服务：
```bash
docker compose restart
```

### 配置 Nginx 反向代理

可在 Nginx 中将 7001 端口代理到 80/443 端口以使用标准域名访问。

---

## 🔄 升级

> ⚠️ 升级前请务必备份数据库，升级与迁移是不同的操作，请勿混淆。

### Docker Compose 方式升级

```bash
cd Xboard
docker compose pull && \
docker compose run -it --rm web php artisan xboard:update && \
docker compose up -d
```

> 如果提示找不到 `web` 服务，请将 `web` 替换为 `xboard`：
> ```bash
> docker compose pull && \
> docker compose run -it --rm xboard php artisan xboard:update && \
> docker compose up -d
> ```

### 版本回滚

1. 修改 `docker-compose.yaml` 中的镜像版本号为目标版本。
2. 执行：
```bash
docker compose up -d
```

---

## 🗑️ 卸载

1. 停止并删除所有容器及数据卷：
```bash
cd Xboard
docker compose down -v
```

2. 删除项目文件：
```bash
cd ..
rm -rf Xboard
```

> ⚠️ `-v` 参数会同时删除 Redis 等持久化数据卷，请确认数据已备份后再执行。
