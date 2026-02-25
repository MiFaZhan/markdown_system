## 服务器部署说明

本说明文档用于在服务器上使用 `docker-compose-alibaba.yml` 直接拉取镜像进行部署，无需本地构建镜像，所有配置通过 `.env.server` 文件管理。

---

### 1. 前置条件

- 服务器已安装 Docker 与 Docker Compose（`docker compose` 命令可用）
- 能从服务器访问阿里云 Docker 镜像仓库
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
- `docker-compose-alibaba.yml` - Docker Compose 配置文件
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

**配置说明：**

- `DB_PASSWORD`：MySQL 数据库密码，建议使用强密码
- `JWT_SECRET`：JWT 密钥，建议使用随机字符串，至少 32 位
- `CORS_ALLOWED_ORIGINS`：允许跨域访问的前端地址，多个地址用逗号分隔，如 `http://example.com,http://localhost:3000`
- `SERVER_PORT`：后端服务暴露在宿主机的端口，默认 8080
- `FILE_MAX_SIZE`：文件上传最大大小（字节），默认 100MB（104857600）

---

### 4. 启动服务

#### 4.1 首次启动

首次启动会自动从阿里云镜像仓库拉取镜像并初始化数据库：

```bash
docker compose -f docker-compose-alibaba.yml --env-file .env.server up -d
```

#### 4.2 重启服务

```bash
docker compose -f docker-compose-alibaba.yml --env-file .env.server restart
```

#### 4.3 停止服务

```bash
docker compose -f docker-compose-alibaba.yml down
```

#### 4.4 重新拉取镜像并启动

如果镜像有更新，需要重新拉取：

```bash
docker compose -f docker-compose-alibaba.yml --env-file .env.server pull
docker compose -f docker-compose-alibaba.yml --env-file .env.server up -d
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

### 6. 常用操作

#### 6.1 服务管理

- 查看运行状态：

```bash
docker compose -f docker-compose-alibaba.yml ps
```

- 查看所有服务日志：

```bash
docker compose -f docker-compose-alibaba.yml logs -f
```

- 查看后端日志：

```bash
docker compose -f docker-compose-alibaba.yml logs -f backend
```

- 查看前端日志：

```bash
docker compose -f docker-compose-alibaba.yml logs -f frontend
```

- 查看数据库日志：

```bash
docker compose -f docker-compose-alibaba.yml logs -f mysql
```

#### 6.2 进入容器

- 进入后端容器：

```bash
docker exec -it markdown-backend bash
```

- 进入 MySQL 容器：

```bash
docker exec -it markdown-mysql mysql -u root -p
```

#### 6.3 数据库备份与恢复

- 备份数据库：

```bash
docker exec markdown-mysql mysqldump -u root -p密码 markdown_system > backup.sql
```

- 恢复数据库：

```bash
docker exec -i markdown-mysql mysql -u root -p密码 markdown_system < backup.sql
```

---

### 7. 验证部署

#### 7.1 查看当前环境变量配置

```bash
docker compose -f docker-compose-alibaba.yml exec backend env | grep -E "DB_|JWT_|FILE_|CORS_|SERVER_"
```

#### 7.2 访问服务

部署成功后，可以通过以下地址访问：

- **前端页面**：`http://服务器IP` 或 `http://域名`
- **后端 API**：`http://服务器IP:8080` 或 `http://域名:8080`
- **MySQL 数据库**：`服务器IP:3307`（仅供内部访问）

#### 7.3 健康检查

检查服务是否正常运行：

```bash
# 检查容器状态
docker compose -f docker-compose-alibaba.yml ps

# 检查后端健康状态
curl http://localhost:8080/api/health

# 检查前端页面
curl -I http://localhost
```

### 8. 阿里云镜像仓库说明

本部署方法使用阿里云个人容器镜像仓库托管镜像：

- **镜像仓库地址**：`crpi-6bh4ttuwodcmagh8.cn-qingdao.personal.cr.aliyuncs.com/mifazhan/markdown-system`
- **后端镜像**：`crpi-6bh4ttuwodcmagh8.cn-qingdao.personal.cr.aliyuncs.com/mifazhan/markdown-system:backend`
- **前端镜像**：`crpi-6bh4ttuwodcmagh8.cn-qingdao.personal.cr.aliyuncs.com/mifazhan/markdown-system:frontend`

镜像会自动从阿里云仓库拉取，无需额外配置 Docker Hub。

### 9. 故障排查

#### 9.1 服务无法启动

- 检查端口是否被占用：`netstat -tuln | grep -E '80|8080|3307'`
- 查看容器日志：`docker compose -f docker-compose-alibaba.yml logs`
- 检查环境变量是否正确配置

#### 9.2 数据库连接失败

- 确认 MySQL 容器已启动
- 检查数据库密码配置是否正确
- 查看后端日志中的数据库连接错误

#### 9.3 前端无法访问后端

- 检查 `CORS_ALLOWED_ORIGINS` 配置是否正确
- 确认后端服务是否正常运行
- 检查防火墙设置是否允许端口访问
