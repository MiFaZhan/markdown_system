## Windows 本地部署说明

本说明文档用于在 Windows 本地环境使用 Docker Desktop 部署 Markdown System，无需本地构建镜像，直接拉取镜像即可部署，所有配置通过 `.env.server` 文件管理。

---

### 1. 前置条件

- Windows 10/11 操作系统
- 已安装 Docker Desktop 并正在运行
- 能够访问 Docker Hub 或阿里云镜像仓库
- 本地端口 80、3307、8080 未被占用

---

### 2. 安装 Docker Desktop

如果尚未安装 Docker Desktop，请按以下步骤安装：

#### 2.1 下载 Docker Desktop

访问 [Docker Desktop 官网](https://www.docker.com/products/docker-desktop/) 下载适用于 Windows 的安装包。

#### 2.2 安装 Docker Desktop

1. 双击下载的安装包
2. 按照安装向导完成安装
3. 重启计算机（如需要）
4. 启动 Docker Desktop，等待其完全启动

#### 2.3 验证 Docker 安装

打开 PowerShell 或命令提示符，运行以下命令验证安装：

```powershell
docker --version
docker compose version
```

如果显示版本信息，说明安装成功。

---

### 3. 克隆项目到本地

#### 3.1 创建项目目录

在合适的位置创建项目目录，例如：

```powershell
# 在用户目录下创建项目文件夹
mkdir C:\Users\%USERNAME%\markdown_system
cd C:\Users\%USERNAME%\markdown_system
```

或者在其他位置创建：

```powershell
# 在 D 盘创建项目文件夹
mkdir D:\markdown_system
cd D:\markdown_system
```

#### 3.2 从 GitHub 克隆

```powershell
git clone https://github.com/MiFaZhan/markdown_system.git
cd markdown_system
```

#### 3.3 从 GitCode 克隆

如果 GitHub 访问有问题，可以使用 GitCode 仓库：

```powershell
git clone https://gitcode.com/MiFaZhan/markdown_system.git
cd markdown_system
```

#### 3.4 手动下载

如果无法使用 git，可以：
1. 访问 GitHub 仓库页面
2. 点击 "Code" 按钮
3. 选择 "Download ZIP"
4. 解压到项目目录
5. 重命名文件夹为 `markdown_system`

#### 3.5 检查项目文件

**重要提示：** 确保项目目录下包含以下文件和文件夹：
- `docker-compose.yml` - Docker Compose 配置文件（Docker Hub 镜像）
- `docker-compose-alibaba.yml` - Docker Compose 配置文件（阿里云镜像）
- `.env.server` - 环境变量配置文件
- `sql/` - 数据库初始化脚本文件夹
- `sql/markdown_system.sql` - 数据库初始化脚本

---

### 4. 配置环境变量

编辑 `.env.server` 文件，修改以下配置：

```env
# 数据库配置
DB_USERNAME=markdown_user
DB_PASSWORD=your_password_here

# JWT 配置
JWT_SECRET=your_jwt_secret_here
JWT_EXPIRATION=86400000

# CORS 配置（本地访问，填 http://localhost）
CORS_ALLOWED_ORIGINS=http://localhost

# 后端服务端口
SERVER_PORT=8080

# 文件上传配置
FILE_MAX_SIZE=104857600
```

**Windows 本地配置说明：**

- `DB_PASSWORD`：MySQL 数据库密码，建议使用强密码
- `JWT_SECRET`：JWT 密钥，建议使用随机字符串，至少 32 位
- `CORS_ALLOWED_ORIGINS`：本地部署建议设置为 `http://localhost`
- `SERVER_PORT`：后端服务端口，默认 8080
- `FILE_MAX_SIZE`：文件上传最大大小（字节），默认 100MB（104857600）

---

### 5. 启动服务

#### 5.1 选择部署方案

**方案一：使用 Docker Hub 镜像（推荐）**
```powershell
docker compose -f docker-compose.yml --env-file .env.server up -d
```

**方案二：使用阿里云镜像（国内用户推荐）**
```powershell
docker compose -f docker-compose-alibaba.yml --env-file .env.server up -d
```

#### 5.2 首次启动

首次启动会自动拉取镜像并初始化数据库，过程可能需要几分钟。

#### 5.3 重启服务

```powershell
# Docker Hub 方案
docker compose -f docker-compose.yml --env-file .env.server restart

# 阿里云方案
docker compose -f docker-compose-alibaba.yml --env-file .env.server restart
```

#### 5.4 停止服务

```powershell
# Docker Hub 方案
docker compose -f docker-compose.yml down

# 阿里云方案
docker compose -f docker-compose-alibaba.yml down
```

#### 5.5 重新拉取镜像并启动

如果镜像有更新，需要重新拉取：

```powershell
# Docker Hub 方案
docker compose -f docker-compose.yml --env-file .env.server pull
docker compose -f docker-compose.yml --env-file .env.server up -d

# 阿里云方案
docker compose -f docker-compose-alibaba.yml --env-file .env.server pull
docker compose -f docker-compose-alibaba.yml --env-file .env.server up -d
```

---

### 6. 数据持久化与目录说明

在项目目录下，以下目录会被自动创建并挂载到容器中：

- `mysql-data/`：MySQL 数据目录，对应容器内 `/var/lib/mysql`
- `uploads/`：文件上传目录，对应容器内 `/app/uploads`

即使重启或重新创建容器，数据库和文件数据仍会保留。

**重要提示：**
- 首次启动时，`sql/markdown_system.sql` 会自动导入到 MySQL 中
- 如需备份数据，请定期备份 `mysql-data/` 和 `uploads/` 目录

---

### 7. 访问服务

部署成功后，可以通过以下地址访问：

- **前端页面**：`http://localhost`
- **后端 API**：`http://localhost:8080`
- **MySQL 数据库**：`localhost:3307`（仅供内部访问）

---

### 8. 确认服务是否正常启动

#### 8.1 检查容器状态

```powershell
# Docker Hub 方案
docker compose -f docker-compose.yml ps

# 阿里云方案
docker compose -f docker-compose-alibaba.yml ps
```

所有服务的状态应该显示为 `Up`。

#### 8.2 检查后端健康状态

```powershell
curl http://localhost:8080/api/health
```

如果返回正常的响应，说明后端服务运行正常。

#### 8.3 检查前端页面

```powershell
curl -I http://localhost
```

如果返回 HTTP 200 状态码，说明前端服务运行正常。

---

### 9. Windows 特定配置

#### 9.1 防火墙设置

如果遇到端口访问问题，可能需要配置 Windows 防火墙：

1. 打开 Windows 防火墙设置
2. 点击"允许应用或功能通过 Windows 防火墙"
3. 添加 Docker Desktop 的防火墙例外
4. 或者手动开放端口 80、8080、3307

#### 9.2 文件路径问题

Windows 使用反斜杠 `\` 作为路径分隔符，但 Docker 容器内部使用正斜杠 `/`。环境变量配置中的路径保持使用正斜杠，Docker 会自动处理路径转换。

#### 9.3 PowerShell 执行策略

如果遇到 PowerShell 执行脚本限制，可以临时更改执行策略：

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```
