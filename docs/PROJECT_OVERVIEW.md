# 项目架构概览

## 1. 项目简介

**karupeko** 是一款极简美学的健康追踪 **PWA 应用**，专注于饮食和饮水的日常记录。项目采用前后端分离架构，后端使用 FastAPI + Python 3.14，前端使用 Vue 3 + TypeScript，支持 iOS Safari 完美兼容。

### 1.1 核心模块

| 模块 | 名称 | 描述 |
|------|------|------|
| 🍽️ | MoguMogu | 饮食热量记录与追踪 |
| 💧 | GokuGoku | 饮水记录与进度追踪 |

### 1.2 设计目标

- **极简美学**：玻璃拟态设计风格，iOS 级交互体验
- **数据驱动**：实时计算热量摄入和饮水进度
- **PWA 体验**：渐进式 Web 应用，支持离线使用和原生应用体验
- **移动端优先**：针对 iOS Safari 和移动设备的优化设计
- **前后端分离**：独立部署，易于扩展
- **AI 友好**：清晰的代码结构和文档，便于 AI 辅助开发

---

## 2. 系统架构

```
┌─────────────────────────────────────────────────────────────┐
│                        前端 (Vue 3 + PWA)                    │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐  │
│  │ MoguMogu     │  │ GokuGoku     │  │   Common         │  │
│  │ Components   │  │ Components   │  │   Components     │  │
│  └──────────────┘  └──────────────┘  └──────────────────┘  │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  PWA Layer (Service Worker + Workbox + Manifest)    │   │
│  └──────────────────────────────────────────────────────┘   │
│                  ┌──────────────────────────────┐           │
│                  │      API Service Layer       │           │
│                  └──────────────────────────────┘           │
└─────────────────────────────────────────────────────────────┘
                              ↕ HTTP/REST
┌─────────────────────────────────────────────────────────────┐
│                      后端 (FastAPI)                          │
│  ┌──────────────────────────────────────────────────────┐   │
│  │                    API Router (v1)                   │   │
│  │  /api/v1/food    /api/v1/water    /api/v1/user      │   │
│  └──────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │                   Business Services                  │   │
│  │  FoodService     WaterService     UserService       │   │
│  └──────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │                  SQLAlchemy ORM Layer                │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              ↕ SQLAlchemy
┌─────────────────────────────────────────────────────────────┐
│                      数据库 (SQLite/PostgreSQL)              │
│  ┌────────────┐  ┌─────────────┐  ┌──────────────┐         │
│  │ users      │  │ food_records│  │ water_records│         │
│  └────────────┘  └─────────────┘  └──────────────┘         │
└─────────────────────────────────────────────────────────────┘
```

---

## 3. 目录结构详解

### 3.1 后端目录结构

```
backend/
├── app/
│   ├── api/                     # API 路由层
│   │   ├── v1/
│   │   │   ├── __init__.py
│   │   │   ├── food.py          # 饮食记录 API
│   │   │   ├── water.py         # 饮水记录 API
│   │   │   └── user.py          # 用户 API
│   │   └── deps.py              # 依赖注入
│   ├── core/                    # 核心配置
│   │   ├── __init__.py
│   │   ├── config.py            # 配置管理
│   │   └── security.py          # 安全认证
│   ├── db/                      # 数据库相关
│   │   ├── __init__.py
│   │   ├── base.py              # 数据库基类
│   │   ├── session.py           # 数据库会话
│   │   └── init_db.py           # 初始化数据
│   ├── models/                  # SQLAlchemy 模型
│   │   ├── __init__.py
│   │   ├── food_record.py
│   │   ├── water_record.py
│   │   └── user.py
│   ├── schemas/                 # Pydantic 模型
│   │   ├── __init__.py
│   │   ├── food.py
│   │   ├── water.py
│   │   └── user.py
│   ├── services/                # 业务逻辑层
│   │   ├── __init__.py
│   │   ├── food_service.py
│   │   ├── water_service.py
│   │   └── user_service.py
│   └── main.py                  # 应用入口
├── tests/                       # 测试文件
├── pyproject.toml               # uv 项目配置
└── requirements.txt             # 依赖列表
```

### 3.2 前端目录结构

```
frontend/
├── public/
│   ├── manifest.json            # PWA 清单文件
│   ├── icons/                   # PWA 图标
│   │   ├── icon-192.png
│   │   ├── icon-512.png
│   │   └── icon-maskable.png
│   └── favicon.ico
├── src/
│   ├── components/              # Vue 组件
│   │   ├── common/              # 通用组件
│   │   │   ├── GlassPanel.vue
│   │   │   ├── ProgressCircle.vue
│   │   │   ├── BottomSheet.vue
│   │   │   └── Toast.vue
│   │   ├── food/                # 饮食模块组件
│   │   │   ├── FoodDashboard.vue
│   │   │   ├── FoodList.vue
│   │   │   └── AddFoodForm.vue
│   │   └── water/               # 饮水模块组件
│   │       ├── WaterDashboard.vue
│   │       ├── WaterList.vue
│   │       └── AddWaterForm.vue
│   ├── composables/             # 自定义 Composables
│   │   ├── useFoodRecords.ts
│   │   ├── useWaterRecords.ts
│   │   ├── useTabNavigation.ts
│   │   └── useOfflineStatus.ts  # 离线状态检测
│   ├── services/                # API 调用层
│   │   ├── api.ts               # Axios 实例
│   │   ├── foodApi.ts
│   │   ├── waterApi.ts
│   │   └── cache.ts             # 缓存管理
│   ├── types/                   # TypeScript 类型
│   │   ├── food.ts
│   │   ├── water.ts
│   │   └── common.ts
│   ├── utils/                   # 工具函数
│   │   ├── formatters.ts
│   │   └── pwa.ts               # PWA 相关工具
│   ├── views/                   # 页面视图
│   │   └── HomeView.vue
│   ├── App.vue
│   ├── main.ts
│   ├── style.css                # TailwindCSS 样式
│   └── registerSW.ts            # Service Worker 注册
├── package.json
├── tsconfig.json
├── tailwind.config.js
├── vite.config.ts
└── pwa.config.ts                # Vite PWA 配置
```

---

## 4. 数据流向

### 4.1 饮食记录流程

```
用户操作 → AddFoodForm 组件 → foodApi.createRecord() 
    → POST /api/v1/food/ → FoodService.create() 
    → SQLAlchemy INSERT → 数据库
    → 返回 FoodRecord → 更新 FoodDashboard 状态
```

### 4.2 饮水记录流程

```
用户操作 → AddWaterForm 组件 → waterApi.createRecord()
    → POST /api/v1/water/ → WaterService.create()
    → SQLAlchemy INSERT → 数据库
    → 返回 WaterRecord → 更新 WaterDashboard 状态
```

---

## 5. 技术选型说明

### 5.1 后端选择 FastAPI 的原因

- **高性能**：基于 Starlette，性能接近 NodeJS 和 Go
- **类型安全**：原生支持 Python 类型注解
- **自动文档**：自动生成 OpenAPI/Swagger 文档
- **异步支持**：原生 async/await 支持
- **依赖注入**：内置强大的依赖注入系统

### 5.2 使用 uv 管理环境

- **极速安装**：比 pip 快 10-100 倍
- **锁定依赖**：自动生成锁定文件
- **虚拟环境管理**：内置 venv 管理功能
- **Python 版本管理**：自动下载和管理 Python 版本

### 5.3 前端选择 Vue 3 + TypeScript

- **组合式 API**：更灵活的逻辑组织方式
- **类型安全**：TypeScript 提供编译时检查
- **响应式系统**：自动依赖追踪，性能优化
- **生态系统**：丰富的第三方库支持
- **TailwindCSS**：快速构建现代化 UI

### 5.4 PWA 实现方案

- **Vite PWA Plugin**：一键生成 Service Worker 和 Manifest
- **Workbox**：强大的缓存策略和离线支持
- **iOS Safari 优化**：
  - 支持 `apple-mobile-web-app-capable`
  - 自定义 Safari 状态栏颜色
  - 触摸友好型交互设计
- **离线优先**：核心功能在无网络环境下可用
- **后台同步**：网络恢复后自动同步数据

---

## 6. 开发规范

### 6.1 代码风格

- **Python**：遵循 PEP 8，使用 Black 格式化
- **TypeScript**：遵循 StandardJS 风格
- **提交信息**：使用 Conventional Commits 格式

### 6.2 分支策略

```
main          # 生产分支
├── develop   # 开发分支
│   ├── feature/xxx  # 功能分支
│   ├── bugfix/xxx   # 修复分支
│   └── hotfix/xxx   # 紧急修复
```

---

## 7. 外部依赖

| 依赖 | 用途 |
|------|------|
| SQLAlchemy | ORM 框架 |
| Pydantic | 数据验证 |
| Uvicorn | ASGI 服务器 |
| Axios | HTTP 客户端 |
| TailwindCSS | CSS 框架 |

---

## 8. 后续规划

- [ ] 用户认证系统（JWT）
- [ ] 数据导出功能
- [ ] 统计分析图表
- [ ] 推送通知提醒
- [ ] 移动端适配优化