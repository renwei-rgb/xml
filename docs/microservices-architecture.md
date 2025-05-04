# TSS-XML-WEB 微服务架构设计文档

## 一、整体架构

### 1.1 技术栈选型

- 基础框架：Spring Boot 2.7.18
- 微服务框架：Spring Cloud Alibaba 2021.0.6.2
- 注册中心：Nacos
- 配置中心：Nacos
- 网关服务：Spring Cloud Gateway
- 服务调用：OpenFeign
- 限流熔断：Sentinel
- 分布式事务：Seata
- 消息队列：RabbitMQ
- 任务调度：XXL-Job
- 服务监控：Spring Boot Admin
- 链路追踪：SkyWalking

### 1.2 服务划分

#### 1. 网关服务 (Gateway Service)
- 服务名：tss-gateway
- 端口：9999
- 核心功能：
  - 统一入口
  - 路由转发
  - 限流控制
  - 权限验证
- 技术实现：
  - Spring Cloud Gateway
  - Sentinel 限流
  - JWT 认证

#### 2. 系统管理服务 (System Service)
- 服务名：tss-system
- 端口：7001
- 核心功能：
  - 用户管理
  - 角色权限
  - 部门管理
  - 菜单管理
  - 字典管理
  - 租户管理
- 技术实现：
  - Spring Security
  - MyBatis-Plus
  - Redis

#### 3. 认证授权服务 (Auth Service)
- 服务名：tss-auth
- 端口：7002
- 核心功能：
  - 统一认证
  - JWT token管理
  - 单点登录
  - 权限控制
- 技术实现：
  - Spring Security
  - JWT
  - Redis Token存储

#### 4. 文件服务 (File Service)
- 服务名：tss-file
- 端口：7003
- 核心功能：
  - 文件上传下载
  - MinIO/阿里OSS存储
  - 文件管理
- 技术实现：
  - MinIO Client
  - 阿里云OSS SDK
  - 文件存储策略模式

#### 5. 消息服务 (Message Service)
- 服务名：tss-message
- 端口：7004
- 核心功能：
  - 消息推送
  - 短信服务
  - 邮件服务
  - WebSocket通知
- 技术实现：
  - RabbitMQ
  - WebSocket
  - 邮件/短信SDK集成

#### 6. 任务调度服务 (Job Service)
- 服务名：tss-job
- 端口：7005
- 核心功能：
  - 定时任务
  - 任务调度
  - 任务监控
- 技术实现：
  - XXL-Job
  - Quartz
  - 分布式锁

## 二、系统架构图

```ascii
                                    客户端
                                      │
                                      ↓
                                  API网关层
                                (tss-gateway)
                                      │
            ┌──────────┬──────────┬──────────┬──────────┐
            ↓          ↓          ↓          ↓          ↓
        认证服务    系统服务    文件服务    消息服务   任务服务
      (tss-auth)  (tss-system) (tss-file) (tss-message) (tss-job)
            │          │          │          │          │
            └──────────┴──────────┴──────────┴──────────┘
                                      │
                                      ↓
                                  基础设施层
    ┌──────────┬──────────┬──────────┬──────────┬──────────┐
    │   Nacos  │  Redis   │  MySQL   │  MinIO   │ RabbitMQ │
    └──────────┴──────────┴──────────┴──────────┴──────────┘