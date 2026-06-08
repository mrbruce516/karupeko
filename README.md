# karupeko - 健康追踪应用

一款极简美学的饮食与饮水追踪 **PWA 应用**。

![karupeko](https://img.shields.io/badge/version-0.1.0-blue)
![Python](https://img.shields.io/badge/Python-3.14-green)
![FastAPI](https://img.shields.io/badge/FastAPI-latest-orange)

## 📱 功能特性

| 模块 | 名称 | 功能描述 |
|------|------|----------|
| 🍽️ | **MoguMogu** | 饮食热量记录与追踪，支持早餐/午餐/晚餐/加餐分类 |
| 💧 | **GokuGoku** | 饮水记录与进度追踪，可视化饮水目标达成情况 |
| 📲 | **PWA** | 渐进式 Web 应用，支持离线使用、添加到主屏幕、原生应用体验 |

## 📱 移动端优化

- **iOS Safari 兼容**：完美支持 iOS 16+ / iPadOS 16+
- **响应式设计**：适配各种移动设备屏幕尺寸
- **触摸优化**：针对触控操作的交互设计
- **添加到主屏幕**：支持 PWA 安装，无浏览器地址栏的原生体验
- **离线支持**：Service Worker 缓存策略，网络不佳时仍可正常使用

## 🏗️ 技术栈

| 层级 | 技术选型 |
|------|----------|
| **后端** | FastAPI + Python 3.14 + uv |
| **前端** | Vue 3 + TypeScript + TailwindCSS + Vite PWA |
| **数据库** | SQLite (开发) / PostgreSQL (生产) |
| **PWA** | Workbox + Manifest + Service Worker |
| **架构** | 前后端分离 |

## 🚀 快速开始

### 环境要求

- Python 3.14+
- Node.js 18+
- [uv](https://github.com/astral-sh/uv) (Python 包管理器)

### 后端启动

```bash
cd backend

# 使用 uv 创建虚拟环境并安装依赖
uv venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
uv pip install -r requirements.txt

# 启动开发服务器
uv run uvicorn app.main:app --reload --port 8000
```

API 文档访问：http://localhost:8000/docs

### 前端启动

```bash
cd frontend

# 安装依赖
npm install

# 启动开发服务器
npm run dev
```

应用访问：http://localhost:5173

### PWA 安装

在移动设备上：
1. 使用 Safari 打开应用
2. 点击分享按钮 → "添加到主屏幕"
3. 从主屏幕启动即可获得原生应用体验

## 📁 项目结构

```
karupeko/
├── backend/         # FastAPI 后端
├── frontend/        # Vue 3 前端
├── tasks/           # 📋 任务管理目录
│   ├── backlog/     # 待办任务
│   ├── in-progress/ # 进行中任务
│   └── done/        # 已完成任务
├── docs/            # 项目文档
├── prototype.html   # 原型设计参考
├── README.md        # 项目说明
└── .cursorrules     # AI 编码规范
```

详细目录结构请查看 [docs/PROJECT_OVERVIEW.md](docs/PROJECT_OVERVIEW.md)

## 📋 任务进度管理

本项目使用 `tasks/` 目录进行任务追踪，采用类似 Kanban 的看板模式：

| 状态 | 目录 | 说明 |
|------|------|------|
| ⏳ 待办 | `tasks/backlog/` | 已规划但未开始的任务 |
| 🔨 进行中 | `tasks/in-progress/` | 当前正在开发的任务 |
| ✅ 已完成 | `tasks/done/` | 已完成的任务 |

### 快速查看进度

```bash
# 查看各阶段任务数量
echo "待办：$(ls tasks/backlog/ 2>/dev/null | wc -l)"
echo "进行中：$(ls tasks/in-progress/ 2>/dev/null | wc -l)"
echo "已完成：$(ls tasks/done/ 2>/dev/null | wc -l)"
```

任务管理规范请参考 [startai.md](startai.md)。

## 📚 文档

- [项目架构概览](docs/PROJECT_OVERVIEW.md) - 系统架构和技术设计
- [API 接口文档](docs/API_REFERENCE.md) - RESTful API 详细说明
- [数据库设计](docs/DATABASE_SCHEMA.md) - 数据模型和表结构

## 🤝 开发协作

本项目使用 Cline/Cursor 等 AI 助手进行辅助开发。详细的编码规范和 AI 协作指南请参考 [.cursorrules](.cursorrules) 和 [startai.md](startai.md)。

## 📄 License

MIT License