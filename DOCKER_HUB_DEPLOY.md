## 服务器部署说明（Docker Hub 镜像）

本说明文档用于在服务器上使用 `docker-compose.yml` 直接从 Docker Hub 拉取镜像进行部署，无需本地构建镜像，所有配置通过 `.env.server` 文件管理。

---

### 1. 前置条件

- 服务器已安装 Docker 与 Docker Compose（`docker compose` 命令可用）
- 能从服务器访问 Docker Hub 镜像仓库
- 服务器已开放相关端口：80（前端）、3307（MySQL）、${SERVER_PORT}（后端，默认8080）

---

### 2. 将此项目内容拷贝或克隆到服务器

#### 2.1 创建项目目录

首先在服务器上创建一个用于存放项目的目录：

```bash
# 创建项目目录（可根据需要修改路径和用户名）
mkdir -p ~/markdown_system
cd ~/markdown_system
```

#### 2.2 从 GitHub 克隆

```bash
git clone https://github.com/MiFaZhan/markdown_system.git
cd markdown_system
```

#### 2.3 从 GitCode 克隆

如果 GitHub 访问有问题，可以使用 GitCode 仓库：

```bash
git clone https://gitcode.com/MiFaZhan/markdown_system.git
cd markdown_system
```

#### 2.4 手动上传

如果无法使用 git，可以将项目文件打包上传到服务器，然后解压：

```bash
# 解压项目文件到当前目录
tar -xzf markdown_system.tar.gz
cd markdown_system
```

#### 2.5 检查项目文件

**重要提示：** 确保项目目录下包含以下文件和文件夹：
- `docker-compose.yml` - Docker Compose 配置文件
- `.env.server` - 环境变量配置文件
- `sql/` - 数据库初始化脚本文件夹
- `sql/markdown_system.sql` - 数据库初始化脚本

---

### 3. 配置环境变量

编辑 `.env.server` 文件，修改以下配置：

```env
# 数据库配置
DB_USERNAME=markdown_user
DB_PASSWORD=your_password_here

# JWT 配置
JWT_SECRET=your_jwt_secret_here
JWT_EXPIRATION=86400000

# CORS 配置（前端访问地址，如果是同一服务器，填 http://服务器IP 或域名）
CORS_ALLOWED_ORIGINS=http://your-frontend-url

# 后端服务端口
SERVER_PORT=8080

# 文件上传配置
FILE_MAX_SIZE=104857600
```

**配置说明：**

- `DB_PASSWORD`：MySQL 数据库密码，建议使用强密码
- `JWT_SECRET`：JWT 密钥，建议使用随机字符串，至少 32 位
- `CORS_ALLOWED_ORIGINS`：允许跨域访问的前端地址，多个地址用逗号分隔，如 `http://example.com,http://localhost:3000`
- `SERVER_PORT`：后端服务暴露在宿主机的端口，默认 8080
- `FILE_MAX_SIZE`：文件上传最大大小（字节），默认 100MB（104857600）

---

### 4. 启动服务

#### 4.1 首次启动

首次启动会自动从 Docker Hub 拉取镜像并初始化数据库：

```bash
docker compose -f docker-compose.yml --env-file .env.server up -d
```

#### 4.2 重启服务

```bash
docker compose -f docker-compose.yml --env-file .env.server restart
```

#### 4.3 停止服务

```bash
docker compose -f docker-compose.yml down
```

#### 4.4 重新拉取镜像并启动

如果镜像有更新，需要重新拉取：

```bash
docker compose -f docker-compose.yml --env-file .env.server pull
docker compose -f docker-compose.yml --env-file .env.server up -d
```

---

### 5. 数据持久化与目录说明

在服务器项目目录下，以下目录会被自动创建并挂载到容器中：

- `mysql-data/`：MySQL 数据目录，对应容器内 `/var/lib/mysql`
- `uploads/`：文件上传目录，对应容器内 `/app/uploads`

即使重启或重新创建容器，数据库和文件数据仍会保留。

**重要提示：**
- 首次启动时，`sql/markdown_system.sql` 会自动导入到 MySQL 中
- 如需备份数据，请定期备份 `mysql-data/` 和 `uploads/` 目录

---

### 6. 访问服务

部署成功后，可以通过以下地址访问：

- **前端页面**：`http://服务器IP` 或 `http://域名`
- **后端 API**：`http://服务器IP:8080` 或 `http://域名:8080`
- **MySQL 数据库**：`服务器IP:3307`（仅供内部访问）

---

### 7. 确认服务是否正常启动

#### 7.1 检查容器状态

```bash
docker compose -f docker-compose.yml ps
```

所有服务的状态应该显示为 `Up`。

#### 7.2 检查后端健康状态

```bash
curl http://localhost:8080/api/health
```

如果返回正常的响应，说明后端服务运行正常。

#### 7.3 检查前端页面

```bash
curl -I http://localhost
```

如果返回 HTTP 200 状态码，说明前端服务运行正常。

---

### 8. Docker Hub 镜像仓库说明

本部署方法使用 Docker Hub 公共镜像仓库托管镜像：

- **Docker Hub 仓库地址**：`https://hub.docker.com/u/wwzxhy`
- **后端镜像**：`wwzxhy/markdown_system:backend`
- **前端镜像**：`wwzxhy/markdown_system:frontend`

镜像会自动从 Docker Hub 拉取，无需额外配置镜像仓库。

**注意：**
- 如果服务器在中国大陆，访问 Docker Hub 可能较慢，可以考虑使用阿里云镜像仓库的部署方案（参考 `ALIBABA_SERVER_DEPLOY.md`）
- 可以在服务器上配置 Docker 镜像加速器来加速拉取镜像


