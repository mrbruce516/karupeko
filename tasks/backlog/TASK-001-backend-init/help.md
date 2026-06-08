# TASK-001 后端初始化 - 命令指导

本文档提供后端项目初始化过程中所需的命令和操作指导。

---

## 1. 项目设置命令

### 1.1 进入后端目录
```bash
cd backend
```

### 1.2 使用 uv 初始化 Python 项目
```bash
# 初始化 uv 项目（如果尚未初始化）
uv init

# 或者手动创建 pyproject.toml
```

### 1.3 安装依赖
```bash
# 创建虚拟环境
uv venv

# 激活虚拟环境
source .venv/bin/activate  # macOS/Linux
# 或 Windows: .venv\Scripts\activate

# 安装核心依赖
uv pip install fastapi uvicorn[standard]
uv pip install sqlalchemy pydantic python-dotenv
uv pip install alembic bcrypt
uv pip install python-multipart httpx

# 开发依赖（可选）
uv pip install pytest pytest-asyncio black ruff mypy
```

---

## 2. 目录结构创建命令

### 2.1 创建后端目录结构
```bash
cd backend

# 创建应用目录结构
mkdir -p app/{api/routes,core,db,models,schemas,services}
mkdir -p tests/{api,unit,integration}

# 创建 __init__.py 文件
touch app/__init__.py
touch app/api/__init__.py
touch app/api/routes/__init__.py
touch app/core/__init__.py
touch app/db/__init__.py
touch app/models/__init__.py
touch app/schemas/__init__.py
touch app/services/__init__.py
touch tests/__init__.py
```

### 2.2 使用 tree 查看结构
```bash
# 查看创建的目录结构
tree -L 3 --dirsfirst
```

---

## 3. 数据库相关命令

### 3.1 初始化 Alembic 迁移
```bash
cd backend

# 安装 alembic（如果尚未安装）
uv pip install alembic

# 初始化 Alembic
alembic init alembic

# 配置 alembic.ini
# 编辑 alembic/env.py 设置 target_metadata
```

### 3.2 创建迁移脚本
```bash
# 自动生成迁移（基于模型变化）
alembic revision --autogenerate -m "Initial migration"

# 应用迁移
alembic upgrade head

# 查看迁移历史
alembic history

# 回滚迁移
alembic downgrade -1
```

---

## 4. 运行和测试命令

### 4.1 启动开发服务器
```bash
cd backend

# 激活虚拟环境
source .venv/bin/activate

# 方式 1: 直接使用 uvicorn
uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# 方式 2: 使用 make 命令（如果有 Makefile）
make dev
```

### 4.2 访问 API 文档
```bash
# 在浏览器中打开以下地址：
# Swagger UI: http://localhost:8000/docs
# ReDoc: http://localhost:8000/redoc
# OpenAPI JSON: http://localhost:8000/openapi.json
```

### 4.3 测试 API 端点
```bash
# 使用 curl 测试健康检查
curl http://localhost:8000/health

# 测试创建饮食记录
curl -X POST http://localhost:8000/api/v1/food/ \
  -H "Content-Type: application/json" \
  -d '{"name": "苹果", "calories": 95, "meal_type": "加餐"}'

# 测试获取饮食记录
curl "http://localhost:8000/api/v1/food/?date=2026-06-08"
```

---

## 5. 代码质量命令

### 5.1 代码格式化
```bash
cd backend

# 使用 Black 格式化代码
uv run black app/ tests/

# 使用 Ruff 检查和修复
uv run ruff check app/ tests/ --fix
uv run ruff format app/ tests/
```

### 5.2 类型检查
```bash
# 使用 Mypy 进行类型检查
uv run mypy app/ --ignore-missing-imports
```

### 5.3 运行测试
```bash
# 运行所有测试
uv run pytest

# 运行特定测试
uv run pytest tests/api/test_food_api.py -v

# 生成覆盖率报告
uv run pytest --cov=app --cov-report=html
```

---

## 6. Git 相关命令

### 6.1 提交代码
```bash
# 查看变更
git status

# 添加文件
git add backend/app/

# 提交（遵循 Conventional Commits 规范）
git commit -m "feat(backend): 初始化后端项目结构"

# 推送到远程
git push origin main
```

### 6.2 查看提交历史
```bash
git log --oneline --graph
```

---

## 7. 故障排查命令

### 7.1 检查端口占用
```bash
# 查看 8000 端口是否被占用
lsof -i :8000

# 杀死占用端口的进程
kill -9 $(lsof -t -i:8000)
```

### 7.2 清理缓存和重建
```bash
# 删除 Python 缓存
find . -type d -name "__pycache__" -exec rm -rf {} +
find . -type f -name "*.pyc" -delete

# 删除虚拟环境重建
rm -rf .venv
uv venv
source .venv/bin/activate
uv pip install -r requirements.txt
```

### 7.3 重置数据库
```bash
# 删除 SQLite 数据库文件
rm app/data/karupeko.db

# 重新运行迁移
alembic downgrade base
alembic upgrade head
```

---

## 8. 环境变量配置

### 8.1 创建.env 文件
```bash
cd backend

# 复制示例配置
cp .env.example .env

# 编辑.env 文件，填入配置
```

### 8.2 .env 示例内容
```ini
# 应用配置
APP_NAME=karupeko
DEBUG=true
ENVIRONMENT=development

# 数据库配置
DATABASE_URL=sqlite:///./data/karupeko.db

# API 配置
API_V1_PREFIX=/api/v1
BACKEND_CORS_ORIGINS=http://localhost:5173,http://localhost:3000

# 安全配置（生产环境务必修改）
SECRET_KEY=your-secret-key-here
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
```

---

## 9. 快速启动脚本

### 9.1 一键启动脚本
```bash
#!/bin/bash
# save as: backend/scripts/start-dev.sh

set -e

echo "🚀 Starting Karupeko Backend Development Server..."

# 进入项目目录
cd "$(dirname "$0")/.."

# 激活虚拟环境
source .venv/bin/activate

# 启动服务器
uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

---

## 常用命令速查表

| 目的 | 命令 |
|------|------|
| 启动开发服务器 | `uv run uvicorn app.main:app --reload` |
| 运行测试 | `uv run pytest` |
| 格式化代码 | `uv run black app/ && uv run ruff format app/` |
| 创建迁移 | `alembic revision --autogenerate -m "description"` |
| 应用迁移 | `alembic upgrade head` |
| 安装依赖 | `uv pip install -r requirements.txt` |
| 查看 API 文档 | 浏览器访问 `http://localhost:8000/docs` |

---

## 注意事项

1. **首次运行前**确保已安装 Python 3.14+ 和 uv
2. **虚拟环境**必须先激活才能安装包
3. **数据库迁移**在每次模型变更后必须执行
4. **端口冲突**时使用 `lsof -i :8000` 检查
5. **代码规范**提交前务必运行格式化和类型检查

---

更新时间：2026-06-08