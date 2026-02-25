# Markdown System 部署仓库

## 项目简介

Markdown System 是一个现代化的前后端分离 Markdown 文档管理平台，提供项目管理、文件树导航、实时编辑、文档分享等完整功能。本仓库专门用于存放系统的部署配置文件和数据库脚本。



## 系统介绍

Markdown System 旨在为个人和团队提供便捷的文档管理和协作平台。系统采用前后端分离架构设计，具有良好的扩展性和可维护性。

### 主要特性

**项目管理**
- 支持创建多个独立项目，每个项目拥有独立的文件空间
- 项目图标支持emoji表情，方便视觉识别
- 项目列表提供卡片和列表两种视图切换
- 内置搜索和筛选功能，快速定位项目
- 完整的回收站机制，支持删除后恢复



![image-20260225161318351](D:\MyCode\GIT\markdown_system\images\image-20260225161318351.png)



![image-20260225162022845](D:\MyCode\GIT\markdown_system\images\image-20260225162022845.png)



**文档编辑**
- 基于 Vditor 的强大 Markdown 编辑器
- 实时大纲导航，快速跳转到文档任意位置
- 图片拖拽上传和粘贴上传，编辑体验流畅

![image-20260225170054990](D:\MyCode\GIT\markdown_system\images\image-20260225170054990.png)

**文件组织**
- 树形目录结构，支持无限层级嵌套
- 拖拽移动文件和文件夹，整理文件更方便
- 批量删除和恢复操作，提升管理效率
- 支持创建文件夹和 Markdown 文件
- 文件重命名功能

![image-20260225170010942](D:\MyCode\GIT\markdown_system\images\image-20260225170010942.png)

**文档分享**
- 支持分享项目、文件夹、单个文件
- 可选密码保护，确保文档安全
- 可设置分享过期时间，灵活控制访问权限
- 独立的分享页面，无需登录即可访问
- 分享链接管理，随时取消或更新



![image-20260225161854158](D:\MyCode\GIT\markdown_system\images\image-20260225161854158.png)

![image-20260225170155443](D:\MyCode\GIT\markdown_system\images\image-20260225170155443.png)



![image-20260225161838637](D:\MyCode\GIT\markdown_system\images\image-20260225161838637.png)

![image-20260225170220105](D:\MyCode\GIT\markdown_system\images\image-20260225170220105.png)



**用户体验**
- 多标签页编辑，同时打开多个文件
- 标签页支持拖拽排序，自由管理工作区
- 右键菜单快捷操作（关闭、关闭其他、全部关闭）
- 标签页状态持久化，刷新后不丢失
- 响应式设计，桌面端和移动端均有良好体验
- 亮色/暗色主题切换，支持跟随系统主题

**权限管理**
- 基于 JWT 的无状态认证
- 用户注册和登录功能
- 角色权限系统（RBAC），支持细粒度权限控制
- 用户管理功能，管理员可管理所有用户
- AOP 注解鉴权，权限校验更优雅

**数据安全**
- 用户级别项目隔离，确保数据安全
- 项目和节点逻辑删除，可恢复
- 密码加密存储，使用 Spring Security Crypto
- 分享链接可设置密码和过期时间

### 适用场景

- 个人知识库管理
- 团队文档协作
- 技术文档编写
- 笔记整理
- 博客内容管理

## 系统架构

本系统采用前后端分离架构，包含以下独立仓库：

### 核心仓库

| 仓库 | 地址 | 说明 |
|------|------|------|
| **前端仓库** | [MiFaZhan/markdown_system_frontend](https://github.com/MiFaZhan/markdown_system_frontend) | Vue 3 + Element Plus 前端应用 |
| **后端仓库** | [MiFaZhan/markdown_system_backend](https://github.com/MiFaZhan/markdown_system_backend) | Spring Boot 后端服务 |
| **部署仓库** | [MiFaZhan/markdown_system](https://github.com/MiFaZhan/markdown_system) | 本仓库，包含部署配置 |

## 技术栈

### 前端技术栈

| 技术 | 版本 | 说明 |
|------|------|------|
| Vue | 3.3.4 | 渐进式框架，提供响应式数据绑定和组件化开发 |
| Vue Router | 4.2.4 | 官方路由管理器，实现单页应用导航 |
| Pinia | 2.1.6 | 新一代状态管理库，替代 Vuex |
| Element Plus | 2.3.9 | 基于 Vue 3 的组件库，提供丰富的 UI 组件 |
| Vditor | 3.11.2 | 一款浏览器端的 Markdown 编辑器 |
| Vite | 4.4.5 | 新一代前端构建工具，快速的开发体验 |
| Sass | 1.66.1 | CSS 预处理器，支持变量、嵌套等特性 |
| mobile-drag-drop | 3.0.0-rc.0 | 移动端拖拽支持库 |

### 后端技术栈

| 技术 | 版本 | 说明 |
|------|------|------|
| Spring Boot | 3.3.5 | 核心框架，简化 Spring 应用开发 |
| Java | 17 | 开发语言，LTS 长期支持版本 |
| MyBatis-Plus | 3.5.7 | MyBatis 增强工具，简化 CRUD 操作 |
| MySQL | 8.0+ | 关系型数据库，存储应用数据 |
| JWT (jjwt) | 0.12.6 | JSON Web Token 实现，用于认证授权 |
| Spring Security Crypto | - | 密码加密工具，使用 BCrypt 算法 |
| MapStruct | 1.5.5 | 对象映射框架，类型安全的转换 |
| Lombok | - | 代码简化工具，自动生成 getter/setter 等 |
| Spring AOP | - | 面向切面编程，用于权限校验等横切关注点 |
| Validation | - | 参数校验，基于 JSR-303 规范 |

## 快速导航

- 📖 [前端 README](https://github.com/MiFaZhan/markdown_system_frontend/blob/main/README.md) - 前端项目详细说明
- 📖 [后端 README](https://github.com/MiFaZhan/markdown_system_backend/blob/main/README.md) - 后端项目详细说明
- 🚀 [服务器部署文档](ALIBABA_SERVER_DEPLOY.md) - Docker Compose 部署教程

## 仓库内容

```
markdown_system/
├── sql/
│   └── markdown_system.sql      # 数据库初始化脚本
├── docker-compose-alibaba.yml   # Docker Compose 配置（使用阿里云镜像）
├── .env.server                  # 服务器环境变量配置
├── ALIBABA_SERVER_DEPLOY.md     # 详细部署文档
└── README.md                    # 本文件
```

## 部署说明

本仓库提供了基于 Docker Compose 的一键部署方案，使用阿里云容器镜像仓库托管镜像，无需本地构建镜像，直接拉取即可部署。

详细的部署教程请查看：[服务器部署说明](ALIBABA_SERVER_DEPLOY.md)

## 许可证

MIT License
