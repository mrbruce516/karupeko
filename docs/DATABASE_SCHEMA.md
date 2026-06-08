# 数据库设计文档

本文档描述 karupeko 应用的数据库架构设计。

---

## 1. 数据库概述

- **开发环境**: SQLite
- **生产环境**: PostgreSQL
- **ORM 框架**: SQLAlchemy 2.0
- **Python 版本**: Python 3.14

---

## 2. ER 图

```
┌─────────────────┐
│      users      │
├─────────────────┤
│ id (PK)         │
│ username        │
│ email           │
│ password_hash   │
│ calorie_target  │
│ water_target    │
│ created_at      │
└────────┬────────┘
         │
    ┌────┴───────┐
    │            │
    ▼            ▼
┌─────────────┐ ┌─────────────────┐
│ food_records│ │ water_records   │
├─────────────┤ ├─────────────────┤
│ id (PK)     │ │ id (PK)         │
│ user_id(FK) │ │ user_id (FK)    │
│ name        │ │ name            │
│ calories    │ │ volume          │
│ meal_type   │ │ recorded_at     │
│ recorded_at │ │                 │
└─────────────┘ └─────────────────┘
```

---

## 3. 表结构详解

### 3.1 users 表 - 用户表

存储用户基本信息和每日目标设置。

| 字段名 | 类型 | 约束 | 说明 |
|--------|------|------|------|
| id | INTEGER | PRIMARY KEY, AUTOINCREMENT | 用户 ID |
| username | VARCHAR(50) | NOT NULL, UNIQUE | 用户名 |
| email | VARCHAR(100) | NOT NULL, UNIQUE | 邮箱地址 |
| password_hash | VARCHAR(255) | NOT NULL | 密码哈希值 |
| daily_calorie_target | FLOAT | DEFAULT 2000 | 每日热量目标 (kcal) |
| daily_water_target | FLOAT | DEFAULT 2000 | 每日饮水目标 (ml) |
| created_at | DATETIME | NOT NULL | 创建时间 |
| updated_at | DATETIME | NOT NULL | 更新时间 |

**SQL 定义**:

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    daily_calorie_target FLOAT DEFAULT 2000,
    daily_water_target FLOAT DEFAULT 2000,
    created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_username ON users(username);
CREATE INDEX idx_users_email ON users(email);
```

### 3.2 food_records 表 - 饮食记录表

存储用户的饮食热量记录。

| 字段名 | 类型 | 约束 | 说明 |
|--------|------|------|------|
| id | INTEGER | PRIMARY KEY, AUTOINCREMENT | 记录 ID |
| user_id | INTEGER | NOT NULL, FOREIGN KEY | 用户 ID (关联 users.id) |
| name | VARCHAR(100) | NOT NULL | 食物名称 |
| calories | FLOAT | NOT NULL, CHECK > 0 | 热量值 (kcal) |
| meal_type | VARCHAR(20) | 餐次类型 |
| recorded_at | DATETIME | NOT NULL | 记录时间 |

**餐次类型枚举**:
- `早餐` - Breakfast
- `午餐` - Lunch
- `晚餐` - Dinner
- `加餐` - Snack

**SQL 定义**:

```sql
CREATE TABLE food_records (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    user_id INTEGER NOT NULL,
    name VARCHAR(100) NOT NULL,
    calories FLOAT NOT NULL CHECK (calories > 0),
    meal_type VARCHAR(20),
    recorded_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

CREATE INDEX idx_food_records_user_id ON food_records(user_id);
CREATE INDEX idx_food_records_recorded_at ON food_records(recorded_at);
CREATE INDEX idx_food_records_user_date ON food_records(user_id, DATE(recorded_at));
```

### 3.3 water_records 表 - 饮水记录表

存储用户的饮水记录。

| 字段名 | 类型 | 约束 | 说明 |
|--------|------|------|------|
| id | INTEGER | PRIMARY KEY, AUTOINCREMENT | 记录 ID |
| user_id | INTEGER | NOT NULL, FOREIGN KEY | 用户 ID (关联 users.id) |
| name | VARCHAR(100) | NOT NULL | 饮水来源描述 |
| volume | FLOAT | NOT NULL, CHECK > 0 | 饮水量 (ml) |
| recorded_at | DATETIME | NOT NULL | 记录时间 |

**SQL 定义**:

```sql
CREATE TABLE water_records (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    user_id INTEGER NOT NULL,
    name VARCHAR(100) NOT NULL,
    volume FLOAT NOT NULL CHECK (volume > 0),
    recorded_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

CREATE INDEX idx_water_records_user_id ON water_records(user_id);
CREATE INDEX idx_water_records_recorded_at ON water_records(recorded_at);
CREATE INDEX idx_water_records_user_date ON water_records(user_id, DATE(recorded_at));
```

---

## 4. SQLAlchemy 模型

### 4.1 User 模型

```python
from datetime import datetime
from sqlalchemy import Column, Integer, String, Float, DateTime
from sqlalchemy.orm import relationship
from app.db.base import Base


class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    username = Column(String(50), unique=True, nullable=False, index=True)
    email = Column(String(100), unique=True, nullable=False, index=True)
    password_hash = Column(String(255), nullable=False)

    # 目标设置
    daily_calorie_target = Column(Float, default=2000.0)
    daily_water_target = Column(Float, default=2000.0)

    # 时间戳
    created_at = Column(DateTime, default=datetime.utcnow, nullable=False)
    updated_at = Column(
        DateTime, default=datetime.utcnow, onupdate=datetime.utcnow, nullable=False
    )

    # 关系
    food_records = relationship("FoodRecord", back_populates="user", cascade="all, delete-orphan")
    water_records = relationship("WaterRecord", back_populates="user", cascade="all, delete-orphan")
```

### 4.2 FoodRecord 模型

```python
from datetime import datetime
from sqlalchemy import Column, Integer, String, Float, DateTime, ForeignKey
from sqlalchemy.orm import relationship
from app.db.base import Base


class FoodRecord(Base):
    __tablename__ = "food_records"

    id = Column(Integer, primary_key=True, index=True)
    user_id = Column(Integer, ForeignKey("users.id", ondelete="CASCADE"), nullable=False)
    name = Column(String(100), nullable=False)
    calories = Column(Float, nullable=False)
    meal_type = Column(String(20))  # 早餐/午餐/晚餐/加餐
    recorded_at = Column(DateTime, default=datetime.utcnow, nullable=False)

    # 关系
    user = relationship("User", back_populates="food_records")

    # 约束
    __table_args__ = (
        {"sqlite_autoincrement": True},
    )
```

### 4.3 WaterRecord 模型

```python
from datetime import datetime
from sqlalchemy import Column, Integer, String, Float, DateTime, ForeignKey
from sqlalchemy.orm import relationship
from app.db.base import Base


class WaterRecord(Base):
    __tablename__ = "water_records"

    id = Column(Integer, primary_key=True, index=True)
    user_id = Column(Integer, ForeignKey("users.id", ondelete="CASCADE"), nullable=False)
    name = Column(String(100), nullable=False)
    volume = Column(Float, nullable=False)
    recorded_at = Column(DateTime, default=datetime.utcnow, nullable=False)

    # 关系
    user = relationship("User", back_populates="water_records")
```

---

## 5. 常用查询示例

### 5.1 获取用户某日的饮食记录

```python
from sqlalchemy import func
from app.models import FoodRecord

def get_daily_food_records(session, user_id: int, date: str):
    return (
        session.query(FoodRecord)
        .filter(
            FoodRecord.user_id == user_id,
            func.date(FoodRecord.recorded_at) == date
        )
        .order_by(FoodRecord.recorded_at.desc())
        .all()
    )
```

### 5.2 计算用户某日总热量摄入

```python
from sqlalchemy import func
from app.models import FoodRecord

def get_daily_calories(session, user_id: int, date: str) -> float:
    result = (
        session.query(func.sum(FoodRecord.calories))
        .filter(
            FoodRecord.user_id == user_id,
            func.date(FoodRecord.recorded_at) == date
        )
        .scalar() or 0.0
    )
    return result
```

### 5.3 按餐次类型分组统计

```python
from sqlalchemy import func
from app.models import FoodRecord

def get_calories_by_meal_type(session, user_id: int, date: str) -> dict:
    results = (
        session.query(
            FoodRecord.meal_type,
            func.sum(FoodRecord.calories)
        )
        .filter(
            FoodRecord.user_id == user_id,
            func.date(FoodRecord.recorded_at) == date
        )
        .group_by(FoodRecord.meal_type)
        .all()
    )
    return {meal_type or "未知": calories for meal_type, calories in results}
```

### 5.4 获取用户某日饮水总量

```python
from sqlalchemy import func
from app.models import WaterRecord

def get_daily_water_volume(session, user_id: int, date: str) -> float:
    result = (
        session.query(func.sum(WaterRecord.volume))
        .filter(
            WaterRecord.user_id == user_id,
            func.date(WaterRecord.recorded_at) == date
        )
        .scalar() or 0.0
    )
    return result
```

---

## 6. 数据库初始化

```python
# app/db/init_db.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from app.db.base import Base
from app.models import User
from app.core.security import get_password_hash

def init_db(engine):
    """初始化数据库，创建所有表"""
    Base.metadata.create_all(bind=engine)

    # 创建默认用户（开发环境）
    SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
    session = SessionLocal()

    if not session.query(User).first():
        default_user = User(
            username="admin",
            email="admin@karupeko.local",
            password_hash=get_password_hash("admin123"),
            daily_calorie_target=2000.0,
            daily_water_target=2000.0
        )
        session.add(default_user)
        session.commit()

    session.close()
```

---

## 7. 迁移策略

项目使用 Alembic 进行数据库迁移管理。

```bash
# 初始化 Alembic
cd backend
uv pip install alembic
alembic init alembic

# 生成迁移脚本
alembic revision --autogenerate -m "Initial migration"

# 应用迁移
alembic upgrade head
```

---

## 8. 备份与恢复

### 8.1 SQLite 备份

```bash
# 备份数据库
cp karupeko.db karupeko.db.backup.$(date +%Y%m%d)

# 恢复数据库
cp karupeko.db.backup.20260608 karupeko.db
```

### 8.2 PostgreSQL 备份

```bash
# 备份
pg_dump -U postgres karupeko > backup.sql

# 恢复
psql -U postgres karupeko < backup.sql