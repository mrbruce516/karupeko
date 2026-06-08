# 任务编号：TASK-001

## 任务名称
后端项目初始化

## 状态
- [ ] 待办 (backlog)
- [x] 进行中 (in-progress)
- [ ] 已完成 (done)

## 优先级
- [ ] P0 - 紧急
- [x] P1 - 高
- [ ] P2 - 中
- [ ] P3 - 低

## 关联功能模块
- 模块名称：后端基础架构
- 影响范围：后端 / 数据库 / 全栈

## 任务描述
创建 FastAPI 后端项目的基础结构，包括项目骨架、依赖配置、数据库连接、核心模块等。

当前 `backend/` 目录为空，需要从零搭建完整的后端框架。

## 实现计划
- [ ] 创建后端目录结构
- [ ] 创建 pyproject.toml 或 requirements.txt 定义依赖
- [ ] 创建 FastAPI 主应用入口 (main.py)
- [ ] 创建数据库基础配置 (db/base.py, db/session.py)
- [ ] 创建 SQLAlchemy 模型基类
- [ ] 创建 User、FoodRecord、WaterRecord 数据模型
- [ ] 创建 Pydantic Schemas (请求/响应模型)
- [ ] 创建 CRUD 工具类
- [ ] 创建 API 路由模块 (routes/)
- [ ] 创建数据库初始化脚本
- [ ] 配置 CORS 中间件
- [ ] 创建环境变量配置文件 (.env.example)
- [ ] 添加 README.md 说明文档

## 技术要点
- **Python 版本**: 3.14
- **Web 框架**: FastAPI latest
- **ORM**: SQLAlchemy 2.0
- **数据库**: SQLite (开发环境)
- **依赖管理**: uv + pip
- **异步支持**: asyncio + async/await
- **CORS**: 允许前端跨域访问

### 需要注意的边界情况
- 数据库连接的异常处理
- SQLite 自增主键的配置
- 时间字段的时区处理 (UTC 存储，本地时间展示)
- 软删除 vs 硬删除的选择

### 依赖的外部服务或模块
- fastapi
- uvicorn
- sqlalchemy
- pydantic >= 2.0
- python-dotenv
- alembic (数据库迁移)

## 验收标准
- [ ] 项目能够正常启动 (`uv run uvicorn app.main:app --reload`)
- [ ] 访问 /docs 显示 Swagger UI
- [ ] 数据库表正确创建
- [ ] 所有 API 端点可访问
- [ ] 代码符合 .cursorrules 中的规范
- [ ] 有基础的错误处理机制

## 相关文件
- 后端入口：`backend/app/main.py`
- 数据库配置：`backend/app/db/session.py`
- 数据模型：`backend/app/models/*.py`
- Schema 定义：`backend/app/schemas/*.py`
- API 路由：`backend/app/routes/*.py`
- 数据库文档：`docs/DATABASE_SCHEMA.md`
- API 文档：`docs/API_REFERENCE.md`

## 预计工作量
- 预估时间：4-6 小时
- 复杂度：中等

## 开发日志
### YYYY-MM-DD
- [待填写]

## 完成情况
- 开始日期：YYYY-MM-DD
- 完成日期：YYYY-MM-DD
- 实际负责人：@username
- 备注：后端初始化是项目的基础工作，需要严格按照设计规范执行

---
## AI 协作记录
记录与 AI 协作过程中的重要决策和代码变更