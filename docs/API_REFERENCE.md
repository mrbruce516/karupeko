# API 接口文档

本文档描述 karupeko 应用的所有 RESTful API 接口。

**Base URL**: `http://localhost:8000/api/v1`

---

## 目录

- [饮食记录 API (Food)](#饮食记录-api-food)
- [饮水记录 API (Water)](#饮水记录-api-water)
- [用户 API (User)](#用户-api-user)
- [错误响应](#错误响应)

---

## 饮食记录 API (Food)

### 1.1 创建饮食记录

**POST** `/food/`

创建一条新的饮食记录。

**请求体**:

```json
{
  "name": "美式拿铁",
  "calories": 150,
  "meal_type": "早餐"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| name | string | 是 | 食物名称，最多 100 字符 |
| calories | float | 是 | 热量值 (kcal)，必须大于 0 |
| meal_type | string | 否 | 餐次类型：早餐/午餐/晚餐/加餐 |

**成功响应** (201 Created):

```json
{
  "id": 1,
  "user_id": 1,
  "name": "美式拿铁",
  "calories": 150,
  "meal_type": "早餐",
  "recorded_at": "2026-06-08T08:30:00Z"
}
```

### 1.2 获取饮食记录列表

**GET** `/food/`

获取指定日期的所有饮食记录。

**查询参数**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| date | string | 是 | 日期，格式：YYYY-MM-DD |

**成功响应** (200 OK):

```json
[
  {
    "id": 1,
    "user_id": 1,
    "name": "美式拿铁",
    "calories": 150,
    "meal_type": "早餐",
    "recorded_at": "2026-06-08T08:30:00Z"
  },
  {
    "id": 2,
    "user_id": 1,
    "name": "全麦三明治",
    "calories": 320,
    "meal_type": "午餐",
    "recorded_at": "2026-06-08T12:15:00Z"
  }
]
```

### 1.3 获取单条饮食记录

**GET** `/food/{record_id}`

根据 ID 获取单条饮食记录。

**成功响应** (200 OK):

```json
{
  "id": 1,
  "user_id": 1,
  "name": "美式拿铁",
  "calories": 150,
  "meal_type": "早餐",
  "recorded_at": "2026-06-08T08:30:00Z"
}
```

### 1.4 更新饮食记录

**PUT** `/food/{record_id}`

更新指定的饮食记录。

**请求体**:

```json
{
  "name": "卡布奇诺",
  "calories": 180
}
```

**成功响应** (200 OK):

```json
{
  "id": 1,
  "user_id": 1,
  "name": "卡布奇诺",
  "calories": 180,
  "meal_type": "早餐",
  "recorded_at": "2026-06-08T08:30:00Z"
}
```

### 1.5 删除饮食记录

**DELETE** `/food/{record_id}`

删除指定的饮食记录。

**成功响应** (204 No Content)

### 1.6 获取每日饮食汇总

**GET** `/food/summary/{date}`

获取指定日期的饮食汇总统计。

**查询参数**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| date | string | 是 | 日期，格式：YYYY-MM-DD |

**成功响应** (200 OK):

```json
{
  "date": "2026-06-08",
  "total_calories": 580,
  "target_calories": 2000,
  "remaining_calories": 1420,
  "progress_percent": 29.0,
  "record_count": 2,
  "by_meal_type": {
    "早餐": 320,
    "午餐": 260,
    "晚餐": 0,
    "加餐": 0
  }
}
```

---

## 饮水记录 API (Water)

### 2.1 创建饮水记录

**POST** `/water/`

创建一条新的饮水记录。

**请求体**:

```json
{
  "name": "温开水",
  "volume": 350,
  "recorded_at": "2026-06-08T08:00:00Z"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| name | string | 是 | 饮水来源，最多 100 字符 |
| volume | float | 是 | 饮水量 (ml)，必须大于 0 |
| recorded_at | datetime | 否 | 记录时间，默认当前时间 |

**成功响应** (201 Created):

```json
{
  "id": 1,
  "user_id": 1,
  "name": "温开水",
  "volume": 350,
  "recorded_at": "2026-06-08T08:00:00Z"
}
```

### 2.2 获取饮水记录列表

**GET** `/water/`

获取指定日期的所有饮水记录。

**查询参数**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| date | string | 是 | 日期，格式：YYYY-MM-DD |

**成功响应** (200 OK):

```json
[
  {
    "id": 1,
    "user_id": 1,
    "name": "温开水",
    "volume": 350,
    "recorded_at": "2026-06-08T08:00:00Z"
  },
  {
    "id": 2,
    "user_id": 1,
    "name": "气泡水",
    "volume": 250,
    "recorded_at": "2026-06-08T14:00:00Z"
  }
]
```

### 2.3 获取单条饮水记录

**GET** `/water/{record_id}`

根据 ID 获取单条饮水记录。

**成功响应** (200 OK):

```json
{
  "id": 1,
  "user_id": 1,
  "name": "温开水",
  "volume": 350,
  "recorded_at": "2026-06-08T08:00:00Z"
}
```

### 2.4 更新饮水记录

**PUT** `/water/{record_id}`

更新指定的饮水记录。

**请求体**:

```json
{
  "name": "矿泉水",
  "volume": 500
}
```

**成功响应** (200 OK):

```json
{
  "id": 1,
  "user_id": 1,
  "name": "矿泉水",
  "volume": 500,
  "recorded_at": "2026-06-08T08:00:00Z"
}
```

### 2.5 删除饮水记录

**DELETE** `/water/{record_id}`

删除指定的饮水记录。

**成功响应** (204 No Content)

### 2.6 获取每日饮水汇总

**GET** `/water/summary/{date}`

获取指定日期的饮水汇总统计。

**查询参数**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| date | string | 是 | 日期，格式：YYYY-MM-DD |

**成功响应** (200 OK):

```json
{
  "date": "2026-06-08",
  "total_volume": 1000,
  "target_volume": 2000,
  "remaining_volume": 1000,
  "progress_percent": 50.0,
  "record_count": 3
}
```

---

## 用户 API (User)

### 3.1 获取当前用户信息

**GET** `/user/me`

获取当前登录用户的信息。

**成功响应** (200 OK):

```json
{
  "id": 1,
  "username": "bruce",
  "email": "bruce@example.com",
  "daily_calorie_target": 2000,
  "daily_water_target": 2000,
  "created_at": "2026-06-01T00:00:00Z"
}
```

### 3.2 更新用户信息

**PUT** `/user/me`

更新当前用户的信息。

**请求体**:

```json
{
  "daily_calorie_target": 2200,
  "daily_water_target": 2500
}
```

**成功响应** (200 OK):

```json
{
  "id": 1,
  "username": "bruce",
  "email": "bruce@example.com",
  "daily_calorie_target": 2200,
  "daily_water_target": 2500,
  "created_at": "2026-06-01T00:00:00Z"
}
```

---

## 错误响应

所有 API 在发生错误时返回统一的错误格式：

```json
{
  "detail": "错误描述信息"
}
```

### 常见错误码

| HTTP 状态码 | 说明 |
|------------|------|
| 400 | 请求参数错误 |
| 401 | 未授权（需要登录） |
| 403 | 禁止访问 |
| 404 | 资源不存在 |
| 422 | 数据验证失败 |
| 500 | 服务器内部错误 |

### 错误示例

**422 验证错误**:

```json
{
  "detail": [
    {
      "loc": ["body", "calories"],
      "msg": "ensure this value is greater than 0",
      "type": "value_error.number.not_gt"
    }
  ]
}
```

**404 资源不存在**:

```json
{
  "detail": "Record not found"
}
```

---

## 附录：请求示例

### cURL 示例

```bash
# 创建饮食记录
curl -X POST http://localhost:8000/api/v1/food/ \
  -H "Content-Type: application/json" \
  -d '{"name": "苹果", "calories": 95, "meal_type": "加餐"}'

# 获取今日饮食记录
curl http://localhost:8000/api/v1/food/?date=2026-06-08

# 获取每日汇总
curl http://localhost:8000/api/v1/food/summary/2026-06-08
```

### Python 示例

```python
import httpx

async def create_food_record():
    async with httpx.AsyncClient() as client:
        response = await client.post(
            "http://localhost:8000/api/v1/food/",
            json={
                "name": "香蕉",
                "calories": 105,
                "meal_type": "加餐"
            }
        )
        return response.json()