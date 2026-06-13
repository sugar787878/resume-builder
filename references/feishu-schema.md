# 飞书 MCP 集成参考文档

> 本文档为 resume-builder 技能提供飞书多维表格（Bitable）、消息推送、日历集成等能力的完整参考。
> 所有 MCP 工具均通过 `mcp__feishu-mcp__*` 命名空间调用。

---

## 目录

1. [表格 Schema（完整的字段定义）](#1-表格-schema完整的字段定义)
2. [MCP 调用模式（可复制的调用示例）](#2-mcp-调用模式可复制的调用示例)
3. [推送与日历策略](#3-推送与日历策略)
4. [会话状态管理](#4-会话状态管理)
5. [错误处理模式](#5-错误处理模式)

---

## 1. 表格 Schema（完整的字段定义）

resume-builder 使用 Base 中的 3 张数据表来管理求职目标、简历版本和项目经历。

### 1.1 目标表（Goals Table）

记录所有求职相关的阶段性目标、日常任务和学习里程碑。

```
表名: 目标表
默认视图名: 全部目标
```

**字段定义（按顺序）：**

| 序号 | 字段名             | 类型   | ui_type       | 属性                                                         |
|------|--------------------|--------|---------------|--------------------------------------------------------------|
| 1    | 目标名             | 1      | Text          | 必填，索引字段                                               |
| 2    | 类型               | 3      | SingleSelect  | options: 里程碑, 日常任务, 学习目标, 面试准备                 |
| 3    | 状态               | 3      | SingleSelect  | options: 未开始, 进行中, 已完成, 已逾期                      |
| 4    | 截止日             | 5      | DateTime      | date_formatter: "yyyy-MM-dd HH:mm"                           |
| 5    | 日程ID             | 1      | Text          | 飞书日历事件 ID，用于关联日程                                |
| 6    | 优先级             | 3      | SingleSelect  | options: 高, 中, 低                                          |
| 7    | 关联简历版本       | 1      | Text          | 关联的简历版本记录 ID                                        |
| 8    | 备注               | 1      | Text          | 多行文本，补充说明                                           |

**MCP 创建调用示例：**

```json
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>"
  },
  "data": {
    "table": {
      "name": "目标表",
      "default_view_name": "全部目标",
      "fields": [
        {
          "field_name": "目标名",
          "type": 1,
          "ui_type": "Text"
        },
        {
          "field_name": "类型",
          "type": 3,
          "ui_type": "SingleSelect",
          "property": {
            "options": [
              { "name": "里程碑" },
              { "name": "日常任务" },
              { "name": "学习目标" },
              { "name": "面试准备" }
            ]
          }
        },
        {
          "field_name": "状态",
          "type": 3,
          "ui_type": "SingleSelect",
          "property": {
            "options": [
              { "name": "未开始" },
              { "name": "进行中" },
              { "name": "已完成" },
              { "name": "已逾期" }
            ]
          }
        },
        {
          "field_name": "截止日",
          "type": 5,
          "ui_type": "DateTime"
        },
        {
          "field_name": "日程ID",
          "type": 1,
          "ui_type": "Text"
        },
        {
          "field_name": "优先级",
          "type": 3,
          "ui_type": "SingleSelect",
          "property": {
            "options": [
              { "name": "高" },
              { "name": "中" },
              { "name": "低" }
            ]
          }
        },
        {
          "field_name": "关联简历版本",
          "type": 1,
          "ui_type": "Text"
        },
        {
          "field_name": "备注",
          "type": 1,
          "ui_type": "Text"
        }
      ]
    }
  }
}
```

---

### 1.2 简历版本表（Resume Versions Table）

存储每次简历修改的完整快照，支持版本回溯和变更追踪。

```
表名: 简历版本表
默认视图名: 全部版本
```

**字段定义（按顺序）：**

| 序号 | 字段名             | 类型   | ui_type       | 属性                                                         |
|------|--------------------|--------|---------------|--------------------------------------------------------------|
| 1    | 日期               | 5      | DateTime      | date_formatter: "yyyy-MM-dd HH:mm"，索引字段                 |
| 2    | 完整内容           | 1      | Text          | 多行文本，存储简历 Markdown 全文                              |
| 3    | 变更摘要           | 1      | Text          | 多行文本，本次修改的简要说明                                  |
| 4    | 方向标签           | 4      | MultiSelect   | options: 后端, 前端, 算法, 运维, 安全, 产品, 设计, 数据分析, 游戏策划, 游戏开发, 游戏美术, 技术美术 |
| 5    | 目标公司梯队       | 1      | Text          | 如 "T0", "T1", "T2" 或具体公司名                              |
| 6    | 文件名             | 1      | Text          | 本地 Markdown 文件名，如 "resume-v3-backend.md"               |

**MCP 创建调用示例：**

```json
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>"
  },
  "data": {
    "table": {
      "name": "简历版本表",
      "default_view_name": "全部版本",
      "fields": [
        {
          "field_name": "日期",
          "type": 5,
          "ui_type": "DateTime"
        },
        {
          "field_name": "完整内容",
          "type": 1,
          "ui_type": "Text"
        },
        {
          "field_name": "变更摘要",
          "type": 1,
          "ui_type": "Text"
        },
        {
          "field_name": "方向标签",
          "type": 4,
          "ui_type": "MultiSelect",
          "property": {
            "options": [
              { "name": "后端" },
              { "name": "前端" },
              { "name": "算法" },
              { "name": "运维" },
              { "name": "安全" },
              { "name": "产品" },
              { "name": "设计" },
              { "name": "数据分析" },
              { "name": "游戏策划" },
              { "name": "游戏开发" },
              { "name": "游戏美术" },
              { "name": "技术美术" }
            ]
          }
        },
        {
          "field_name": "目标公司梯队",
          "type": 1,
          "ui_type": "Text"
        },
        {
          "field_name": "文件名",
          "type": 1,
          "ui_type": "Text"
        }
      ]
    }
  }
}
```

---

### 1.3 项目表（Projects Table）

记录简历中涉及的每个项目，跟踪项目构建进度和技术栈信息。

```
表名: 项目表
默认视图名: 全部项目
```

**字段定义（按顺序）：**

| 序号 | 字段名             | 类型   | ui_type       | 属性                                                         |
|------|--------------------|--------|---------------|--------------------------------------------------------------|
| 1    | 项目名             | 1      | Text          | 必填，索引字段                                               |
| 2    | 关联简历条目       | 1      | Text          | 多行文本，简历中该项目对应的 STAR/情境-任务-行动-结果 描述    |
| 3    | 技术栈             | 4      | MultiSelect   | options: Go, Java, Python, Rust, C++, C#, JavaScript, TypeScript, React, Vue, Angular, Unity, Unreal, Blender, Houdini, MySQL, PostgreSQL, Redis, MongoDB, Kafka, Docker, K8s, 其他 |
| 4    | 总任务数           | 2      | Number        | formatter: 整数，默认值 0                                     |
| 5    | 已完成任务         | 2      | Number        | formatter: 整数，默认值 0                                     |
| 6    | 状态               | 3      | SingleSelect  | options: 未开始, 进行中, 已完成, 暂停                          |
| 7    | 仓库路径           | 1      | Text          | 本地 Git 仓库绝对路径                                         |
| 8    | 构建模式           | 3      | SingleSelect  | options: 自动, 手动, 混合                                     |
| 9    | 关联目标           | 1      | Text          | 关联的目标表记录 ID                                           |

**MCP 创建调用示例：**

```json
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>"
  },
  "data": {
    "table": {
      "name": "项目表",
      "default_view_name": "全部项目",
      "fields": [
        {
          "field_name": "项目名",
          "type": 1,
          "ui_type": "Text"
        },
        {
          "field_name": "关联简历条目",
          "type": 1,
          "ui_type": "Text"
        },
        {
          "field_name": "技术栈",
          "type": 4,
          "ui_type": "MultiSelect",
          "property": {
            "options": [
              { "name": "Go" },
              { "name": "Java" },
              { "name": "Python" },
              { "name": "Rust" },
              { "name": "C++" },
              { "name": "C#" },
              { "name": "JavaScript" },
              { "name": "TypeScript" },
              { "name": "React" },
              { "name": "Vue" },
              { "name": "Angular" },
              { "name": "Unity" },
              { "name": "Unreal" },
              { "name": "Blender" },
              { "name": "Houdini" },
              { "name": "MySQL" },
              { "name": "PostgreSQL" },
              { "name": "Redis" },
              { "name": "MongoDB" },
              { "name": "Kafka" },
              { "name": "Docker" },
              { "name": "K8s" },
              { "name": "其他" }
            ]
          }
        },
        {
          "field_name": "总任务数",
          "type": 2,
          "ui_type": "Number"
        },
        {
          "field_name": "已完成任务",
          "type": 2,
          "ui_type": "Number"
        },
        {
          "field_name": "状态",
          "type": 3,
          "ui_type": "SingleSelect",
          "property": {
            "options": [
              { "name": "未开始" },
              { "name": "进行中" },
              { "name": "已完成" },
              { "name": "暂停" }
            ]
          }
        },
        {
          "field_name": "仓库路径",
          "type": 1,
          "ui_type": "Text"
        },
        {
          "field_name": "构建模式",
          "type": 3,
          "ui_type": "SingleSelect",
          "property": {
            "options": [
              { "name": "自动" },
              { "name": "手动" },
              { "name": "混合" }
            ]
          }
        },
        {
          "field_name": "关联目标",
          "type": 1,
          "ui_type": "Text"
        }
      ]
    }
  }
}
```

---

## 2. MCP 调用模式（可复制的调用示例）

### 2.1 前置：获取 Base 和 Table 信息

#### 列出 Base 中所有表

```json
// 调用: mcp__feishu-mcp__bitable_v1_appTable_list
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>"
  },
  "params": {
    "page_size": 20
  }
}
```

**返回示例：**
```json
{
  "items": [
    { "table_id": "tblXXXXXX1", "name": "目标表" },
    { "table_id": "tblXXXXXX2", "name": "简历版本表" },
    { "table_id": "tblXXXXXX3", "name": "项目表" }
  ]
}
```

#### 列出表中所有字段

```json
// 调用: mcp__feishu-mcp__bitable_v1_appTableField_list
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>",
    "table_id": "<TABLE_ID>"
  },
  "params": {
    "page_size": 50
  }
}
```

**返回示例（目标表）：**
```json
{
  "items": [
    { "field_id": "fldAAAA01", "field_name": "目标名", "type": 1 },
    { "field_id": "fldAAAA02", "field_name": "类型", "type": 3 },
    { "field_id": "fldAAAA03", "field_name": "状态", "type": 3 },
    { "field_id": "fldAAAA04", "field_name": "截止日", "type": 5 },
    { "field_id": "fldAAAA05", "field_name": "日程ID", "type": 1 },
    { "field_id": "fldAAAA06", "field_name": "优先级", "type": 3 },
    { "field_id": "fldAAAA07", "field_name": "关联简历版本", "type": 1 },
    { "field_id": "fldAAAA08", "field_name": "备注", "type": 1 }
  ]
}
```

---

### 2.2 创建记录（Create Records）

#### 在目标表中创建一条里程碑

```json
// 调用: mcp__feishu-mcp__bitable_v1_appTableRecord_create
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>",
    "table_id": "<目标表_TABLE_ID>"
  },
  "data": {
    "fields": {
      "目标名": "完成简历初稿",
      "类型": "里程碑",
      "状态": "未开始",
      "截止日": 1718236800000,
      "优先级": "高",
      "备注": "包括教育背景、工作经历、项目经历三个模块"
    }
  },
  "params": {
    "user_id_type": "open_id"
  }
}
```

**时间戳说明：** 飞书 DateTime 字段使用毫秒级 Unix 时间戳。例如 `1718236800000` 对应 `2024-06-13 00:00:00 UTC+8`。可使用以下方式转换：
- JavaScript: `new Date("2024-06-13").getTime()`
- Python: `int(datetime(2024, 6, 13).timestamp() * 1000)`

#### 在简历版本表中创建一条记录

```json
// 调用: mcp__feishu-mcp__bitable_v1_appTableRecord_create
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>",
    "table_id": "<简历版本表_TABLE_ID>"
  },
  "data": {
    "fields": {
      "日期": 1718236800000,
      "完整内容": "# 张三\n\n## 教育背景\n- 北京大学 计算机科学 本科 2020-2024\n\n## 工作经历\n...",
      "变更摘要": "初版简历，完成三个核心模块",
      "方向标签": ["后端", "算法"],
      "目标公司梯队": "T1",
      "文件名": "resume-v1-backend-algorithm.md"
    }
  },
  "params": {
    "user_id_type": "open_id"
  }
}
```

#### 在项目表中创建一条记录

```json
// 调用: mcp__feishu-mcp__bitable_v1_appTableRecord_create
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>",
    "table_id": "<项目表_TABLE_ID>"
  },
  "data": {
    "fields": {
      "项目名": "分布式缓存系统",
      "关联简历条目": "设计并实现了一个基于 Redis 的分布式缓存系统，支持 10 万 QPS，P99 延迟 < 2ms...",
      "技术栈": ["Go", "Redis", "Kafka", "Docker", "K8s"],
      "总任务数": 12,
      "已完成任务": 5,
      "状态": "进行中",
      "仓库路径": "/home/user/projects/dist-cache",
      "构建模式": "混合",
      "关联目标": "<目标表_RECORD_ID>"
    }
  },
  "params": {
    "user_id_type": "open_id"
  }
}
```

---

### 2.3 查询记录（Search Records）

#### 查询所有进行中的目标（按优先级排序）

```json
// 调用: mcp__feishu-mcp__bitable_v1_appTableRecord_search
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>",
    "table_id": "<目标表_TABLE_ID>"
  },
  "data": {
    "filter": {
      "conjunction": "and",
      "conditions": [
        {
          "field_name": "状态",
          "operator": "is",
          "value": ["进行中"]
        }
      ]
    },
    "sort": [
      {
        "field_name": "优先级",
        "desc": false
      }
    ],
    "field_names": ["目标名", "状态", "优先级", "截止日", "备注"]
  },
  "params": {
    "page_size": 50
  }
}
```

**优先级排序说明：** 飞书对单选字段按选项顺序排序。"高/中/低" 的选项顺序决定了排序结果。如果需要精确控制，可以在创建选项时按优先级从高到低排列。

#### 查询即将到期的目标（截止日在未来 3 天内）

```json
// 调用: mcp__feishu-mcp__bitable_v1_appTableRecord_search
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>",
    "table_id": "<目标表_TABLE_ID>"
  },
  "data": {
    "filter": {
      "conjunction": "and",
      "conditions": [
        {
          "field_name": "截止日",
          "operator": "isGreater",
          "value": ["<NOW_TIMESTAMP_MS>"]
        },
        {
          "field_name": "状态",
          "operator": "isNot",
          "value": ["已完成"]
        }
      ]
    },
    "field_names": ["目标名", "截止日", "状态", "优先级"]
  },
  "params": {
    "page_size": 50
  }
}
```

> **注意：** `isGreater` 的是"大于"，对于截止日字段，需要传入当前时间戳来查询未来到期的记录。飞书目前不直接支持"在未来 N 天内"这种相对时间过滤，建议在客户端侧对返回结果做二次过滤（`截止日 > now && 截止日 < now + 3 * 86400 * 1000`）。

#### 查询特定方向标签的简历版本

```json
// 调用: mcp__feishu-mcp__bitable_v1_appTableRecord_search
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>",
    "table_id": "<简历版本表_TABLE_ID>"
  },
  "data": {
    "filter": {
      "conjunction": "and",
      "conditions": [
        {
          "field_name": "方向标签",
          "operator": "contains",
          "value": ["后端"]
        }
      ]
    },
    "sort": [
      {
        "field_name": "日期",
        "desc": true
      }
    ],
    "field_names": ["日期", "变更摘要", "方向标签", "文件名"],
    "automatic_fields": true
  },
  "params": {
    "page_size": 20
  }
}
```

#### 查询所有进行中项目的进度

```json
// 调用: mcp__feishu-mcp__bitable_v1_appTableRecord_search
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>",
    "table_id": "<项目表_TABLE_ID>"
  },
  "data": {
    "filter": {
      "conjunction": "and",
      "conditions": [
        {
          "field_name": "状态",
          "operator": "is",
          "value": ["进行中"]
        }
      ]
    },
    "field_names": ["项目名", "总任务数", "已完成任务", "技术栈", "构建模式"]
  },
  "params": {
    "page_size": 50
  }
}
```

---

### 2.4 更新记录（Update Records）

#### 更新目标状态为"已完成"

```json
// 调用: mcp__feishu-mcp__bitable_v1_appTableRecord_update
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>",
    "table_id": "<目标表_TABLE_ID>",
    "record_id": "<RECORD_ID>"
  },
  "data": {
    "fields": {
      "状态": "已完成",
      "备注": "已完成：2024-06-13，比计划提前2天"
    }
  }
}
```

#### 更新项目进度（增加已完成任务数）

```json
// 调用: mcp__feishu-mcp__bitable_v1_appTableRecord_update
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>",
    "table_id": "<项目表_TABLE_ID>",
    "record_id": "<RECORD_ID>"
  },
  "data": {
    "fields": {
      "已完成任务": 8,
      "状态": "进行中"
    }
  }
}
```

#### 批量更新逾期目标状态

```json
// 步骤 1: 先查询所有已逾期的目标
// 调用: mcp__feishu-mcp__bitable_v1_appTableRecord_search
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>",
    "table_id": "<目标表_TABLE_ID>"
  },
  "data": {
    "filter": {
      "conjunction": "and",
      "conditions": [
        {
          "field_name": "截止日",
          "operator": "isLess",
          "value": ["<NOW_TIMESTAMP_MS>"]
        },
        {
          "field_name": "状态",
          "operator": "is",
          "value": ["进行中"]
        }
      ]
    }
  }
}

// 步骤 2: 对查询到的每条记录，逐个调用 update 接口
// 将状态改为"已逾期"
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>",
    "table_id": "<目标表_TABLE_ID>",
    "record_id": "<OVERDUE_RECORD_ID>"
  },
  "data": {
    "fields": {
      "状态": "已逾期"
    }
  }
}
```

---

### 2.5 记录 ID 格式说明

飞书 Bitable 记录 ID (`record_id`) 格式为 `recuXXXXXX`（17 位字符串）。每次创建记录成功后，响应的 `record_id` 用于后续更新和关联。

**跨表关联最佳实践：**
- 目标表 → 简历版本表：在目标表的"关联简历版本"字段存储简历版本表的 `record_id`
- 项目表 → 目标表：在项目表的"关联目标"字段存储目标表的 `record_id`
- 避免使用 `field_name` 做关联（名称可能重复或变更），始终使用 `record_id`

---

## 3. 推送与日历策略

### 3.1 消息推送模式

使用 `mcp__feishu-mcp__im_v1_message_create` 发送消息，支持以下接收对象：
- 用户（通过 `open_id` / `union_id` / `user_id`）
- 群聊（通过 `chat_id`）

#### 3.1.1 文本消息 — 进度提醒

```json
// 调用: mcp__feishu-mcp__im_v1_message_create
{
  "params": {
    "receive_id_type": "open_id"
  },
  "data": {
    "receive_id": "<USER_OPEN_ID>",
    "msg_type": "text",
    "content": "{\"text\":\"📋 简历构建进度报告\\n\\n本周完成：\\n✅ 更新后端简历版本（v3）\\n✅ 完善分布式缓存项目描述\\n✅ 完成2道算法面试题\\n\\n进行中：\\n🔄 微服务架构项目 — 完成度 60%\\n🔄 系统设计面试准备 — 完成度 40%\\n\\n即将到期：\\n⏰ 投递字节跳动 — 截止日 2024-06-15（还有2天）\"}"
  }
}
```

**消息内容格式说明：**
- `content` 字段是 JSON 字符串，需要二次序列化
- 文本消息结构：`{"text": "消息正文"}`
- 支持 `\\n` 换行，不支持 Markdown 渲染（富文本请用 `post` 或 `interactive` 类型）

#### 3.1.2 卡片消息 — 结构化进度卡片

```json
// 调用: mcp__feishu-mcp__im_v1_message_create
{
  "params": {
    "receive_id_type": "open_id"
  },
  "data": {
    "receive_id": "<USER_OPEN_ID>",
    "msg_type": "interactive",
    "content": "{\"config\":{\"wide_screen_mode\":true},\"header\":{\"title\":{\"tag\":\"plain_text\",\"content\":\"📋 简历构建进度\"},\"template\":\"blue\"},\"elements\":[{\"tag\":\"div\",\"fields\":[{\"is_short\":true,\"text\":{\"tag\":\"lark_md\",\"content\":\"**进行中目标**\\n3 个\"}},{\"is_short\":true,\"text\":{\"tag\":\"lark_md\",\"content\":\"**简历版本**\\n5 个\"}},{\"is_short\":true,\"text\":{\"tag\":\"lark_md\",\"content\":\"**进行中项目**\\n2 个\"}},{\"is_short\":true,\"text\":{\"tag\":\"lark_md\",\"content\":\"**本周新增**\\n1 版简历\"}}]},{\"tag\":\"hr\"},{\"tag\":\"div\",\"text\":{\"tag\":\"lark_md\",\"content\":\"⏰ **即将到期**\\n- 投递字节跳动（T0梯队）：**2024-06-15**\\n- 算法刷题目标100道：**2024-06-20**\"}},{\"tag\":\"action\",\"actions\":[{\"tag\":\"button\",\"text\":{\"tag\":\"plain_text\",\"content\":\"查看详情\"},\"type\":\"primary\",\"url\":\"https://your-feishu-bitable-url\"}]}]}"
  }
}
```

**卡片消息 (interactive) 要点：**
- 使用飞书卡片 JSON 模板语法
- Header `template` 可选值：`blue` / `wathet` / `turquoise` / `green` / `yellow` / `orange` / `red` / `carmine` / `violet` / `purple` / `indigo` / `grey`
- 支持按钮跳转、多列布局、分割线等组件
- 卡片消息请求体大小限制：30 KB（含卡片数据）
- 详细组件文档：https://open.feishu.cn/document/uAjLw4CM/ukzMukzMukzM/feishu-cards/card-components

#### 3.1.3 推送时机策略

| 时间节点             | 消息类型   | 内容                                                         |
|----------------------|------------|--------------------------------------------------------------|
| 每天早上 9:07        | 卡片消息   | 今日待办汇总 + 今日到期提醒                                  |
| 截止日前 3 天        | 文本消息   | "⏰ 提醒：目标「XXX」将在 3 天后（2024-06-16）到期"          |
| 截止日当天（9:30）   | 文本消息   | "⏰ 目标「XXX」今天到期！请及时处理"                          |
| 逾期后（每天 10:00） | 文本消息   | "⚠️ 目标「XXX」已逾期 N 天，状态已自动更新为「已逾期」"      |
| 里程碑完成时         | 文本消息   | "🎉 里程碑「XXX」已完成！下一个目标：YYY"                     |
| 每周五 17:07         | 卡片消息   | 本周总结：完成数 / 进展中数 / 下周计划                       |

**实现方式：** resume-builder 技能在每次启动时，扫描 目标表 中所有即将到期/已逾期的记录，按上述策略触发消息推送。由于飞书目前不支持通过 MCP 直接操作日历 API，日历提醒通过消息推送 + 目标表的"截止日"字段驱动。

### 3.2 日历集成策略

飞书日历 API 目前不在 MCP 工具的覆盖范围内（MCP 工具仅包含 Bitable / IM / Docx / Wiki / Drive），因此日历集成分两个层级：

#### 第一层：目标表驱动（主要）

目标表本质就是一个轻量日历：
- "截止日"字段承载时间轴
- "日程ID"字段预留飞书日历关联（用户手动在飞书日历中创建事件后，将事件 ID 回填到此字段）
- skill 启动时查询 `截止日 >= now` 的记录，构建自己的提醒队列

#### 第二层：飞书日历（手动补充）

用户可手动在飞书日历 App 中：
1. 为目标创建日历事件，设置提醒时间
2. 复制事件 ID
3. 将事件 ID 填入目标表的"日程ID"字段

这样目标表就和飞书原生日历建立了双向链接——日历负责弹窗提醒，目标表负责进度追踪。

#### 备选方案：消息提醒替代日历

如果用户不需要日历的原生弹窗提醒，完全可以用消息推送替代：
- skill 扫描截止日 → 计算提醒时间 → 发送飞书消息
- 消息在飞书 App 内以红点通知的形式出现
- 优点：无需手动操作日历，全自动

---

## 4. 会话状态管理

### 4.1 设计思路

resume-builder 是一个跨会话运行的技能。每次用户启动对话，skill 需要知道：
- 上次进行到哪一步了
- 当前活跃的目标有哪些
- 最新的简历版本是什么

目标表同时充当"会话断点存储"：skill 将运行状态写入目标表的特定记录中（类型设为"里程碑"），下次启动时读取。

### 4.2 状态写入（Session End）

每次会话结束或阶段性完成时，写入一条状态记录：

```json
// 调用: mcp__feishu-mcp__bitable_v1_appTableRecord_create
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>",
    "table_id": "<目标表_TABLE_ID>"
  },
  "data": {
    "fields": {
      "目标名": "[SYSTEM] 会话断点 — 2024-06-13 14:30",
      "类型": "里程碑",
      "状态": "进行中",
      "优先级": "中",
      "备注": "当前阶段: 项目经历优化\n简历方向: 后端(T1)\n活跃目标数: 5\n待处理项目: 分布式缓存系统(60%), 微服务架构(40%)\n下次提醒: 2024-06-14 09:00"
    }
  }
}
```

**备注字段的约定格式（结构化元数据）：**
```
当前阶段: <阶段名>
简历方向: <方向标签>(<目标梯队>)
活跃目标数: <N>
待处理项目: <项目名>(<进度%>), ...
下次提醒: <ISO Datetime>
```

### 4.3 状态恢复（Session Start）

每次 skill 启动时的恢复流程：

```
1. 查询最近 7 天内创建的里程碑记录
   ↓
2. 筛选带 "[SYSTEM] 会话断点" 前缀的记录
   ↓
3. 解析最新一条记录的"备注"字段，提取结构化元数据
   ↓
4. 根据元数据恢复：
   - 当前阶段 → 确定 resume-builder 入口流程
   - 简历方向 → 加载对应方向标签的简历版本
   - 活跃目标数 → 查询当前进行中的目标
   - 待处理项目 → 查询对应项目的进度
   ↓
5. 向用户报告上次进度，询问是否继续
```

**查询会话断点的 MCP 调用：**

```json
// 调用: mcp__feishu-mcp__bitable_v1_appTableRecord_search
{
  "path": {
    "app_token": "<YOUR_BASE_TOKEN>",
    "table_id": "<目标表_TABLE_ID>"
  },
  "data": {
    "filter": {
      "conjunction": "and",
      "conditions": [
        {
          "field_name": "目标名",
          "operator": "contains",
          "value": ["[SYSTEM] 会话断点"]
        },
        {
          "field_name": "类型",
          "operator": "is",
          "value": ["里程碑"]
        }
      ]
    },
    "sort": [
      {
        "field_name": "截止日",
        "desc": true
      }
    ],
    "field_names": ["目标名", "备注", "截止日"],
    "automatic_fields": true
  },
  "params": {
    "page_size": 5
  }
}
```

### 4.4 本地 JSON 回退格式

当 MCP 不可用时（网络问题、令牌过期等），resume-builder 回退到本地 JSON 文件存储。文件路径：

```
~/resume-builder/state.json
```

**JSON 格式：**

```json
{
  "last_session": "2024-06-13T14:30:00+08:00",
  "current_phase": "项目经历优化",
  "resume_direction": "后端",
  "target_tier": "T1",
  "active_goals": [
    {
      "name": "完成简历初稿",
      "status": "进行中",
      "deadline": "2024-06-15T23:59:00+08:00",
      "priority": "高"
    },
    {
      "name": "投递字节跳动",
      "status": "未开始",
      "deadline": "2024-06-20T23:59:00+08:00",
      "priority": "高"
    }
  ],
  "projects_in_progress": [
    {
      "name": "分布式缓存系统",
      "progress": 0.6,
      "repo_path": "/home/user/projects/dist-cache"
    }
  ],
  "last_resume_version_id": "recuXXXXXX01",
  "next_reminder": "2024-06-14T09:00:00+08:00"
}
```

**同步策略：**
- MCP 可用时：双写（飞书 + 本地 JSON），飞书为主数据源
- MCP 不可用时：仅写本地 JSON，下次 MCP 恢复时同步差异到飞书
- 差异检测：比较本地 `last_session` 与飞书最新断点记录的日期

---

## 5. 错误处理模式

### 5.1 调用超时 / 网络错误

飞书 MCP 调用超时默认 30 秒。在网络不稳定或飞书服务波动时，按以下策略处理：

```
┌─ MCP 调用
│    ├── 成功 → 返回结果
│    ├── 超时/网络错误 → 重试 1 次（间隔 2 秒）
│    │    ├── 重试成功 → 返回结果
│    │    └── 重试失败 → 回退到本地 JSON
│    └── 业务错误 → 见 5.3
```

**实现伪代码：**

```python
async def feishu_call_with_retry(tool_name, params):
    for attempt in range(2):  # 原始请求 + 1 次重试
        try:
            result = await call_mcp(tool_name, params)
            return result
        except TimeoutError:
            if attempt == 0:
                log.warning(f"MCP 调用超时，第 1 次重试: {tool_name}")
                await sleep(2)
            else:
                log.error(f"MCP 调用超时，已重试 1 次，回退到本地: {tool_name}")
                return fallback_to_local(tool_name, params)
        except NetworkError:
            if attempt == 0:
                log.warning(f"MCP 网络错误，第 1 次重试: {tool_name}")
                await sleep(3)  # 网络错误多等 1 秒
            else:
                log.error(f"MCP 网络错误，已重试 1 次，回退到本地: {tool_name}")
                return fallback_to_local(tool_name, params)
```

### 5.2 表不存在 → 初始化流程

当调用 `search` 或 `create record` 失败，错误消息包含"table not found"或类似信息时，触发初始化流程：

```
1. 调用 appTable_list 获取当前 Base 的表列表
2. 检查是否需要创建表：
   ├── 目标表 不存在 → 调用 appTable_create（按 1.1 Schema）
   ├── 简历版本表 不存在 → 调用 appTable_create（按 1.2 Schema）
   └── 项目表 不存在 → 调用 appTable_create（按 1.3 Schema）
3. 每创建完一张表，调用 appTableField_list 验证字段完整性
4. 如果字段不完整：
   - 当前 MCP 工具不支持通过 API 给已有表添加字段
   - 向用户报告需要手动在飞书界面中添加缺失字段
   - 提供缺失字段的详细定义
5. 初始化完成后继续原始请求
```

**表初始化检测伪代码：**

```python
async def ensure_tables_exist(app_token):
    tables = await call_mcp("bitable_v1_appTable_list", {"path": {"app_token": app_token}})
    table_names = {t["name"] for t in tables.get("items", [])}

    required = {
        "目标表": GOALS_TABLE_DEFINITION,
        "简历版本表": RESUME_VERSIONS_TABLE_DEFINITION,
        "项目表": PROJECTS_TABLE_DEFINITION,
    }

    for name, schema in required.items():
        if name not in table_names:
            log.info(f"表「{name}」不存在，开始创建...")
            await call_mcp("bitable_v1_appTable_create", {
                "path": {"app_token": app_token},
                "data": schema
            })
            log.info(f"表「{name}」创建完成")
```

### 5.3 权限错误

飞书 MCP 返回的权限错误通常表现为：
- HTTP 403 / 401
- 错误消息中包含 "access denied" / "no permission" / "permission"
- 特定操作（如写入、删除）失败

**常见原因和解决方案：**

| 错误模式                               | 原因                             | 用户操作                                                      |
|----------------------------------------|----------------------------------|---------------------------------------------------------------|
| 所有 Bitable 操作失败                   | Base 未共享给应用                | 在飞书 Base 设置中将应用添加为协作者（编辑权限）              |
| 只能读不能写                           | Base 权限为"仅查看"             | 将应用权限升级为"可编辑"或"可管理"                            |
| IM 消息发送失败                         | 应用未在目标用户的可用范围内      | 在飞书开放平台 → 应用 → 安全设置中配置可用范围               |
| IM 消息发送到群聊失败                   | 机器人不在群聊中或无发言权限     | 将机器人添加到群聊，或在群设置中开启机器人发言               |
| 创建表失败                              | Base 数量达到上限                | 清理无用 Base，或使用现有 Base                                |
| Document/Drive 操作失败                 | 文档未共享给应用                 | 使用 `drive_v1_permissionMember_create` 添加应用为协作者      |

**处理模式：**

```python
async def handle_permission_error(error):
    msg = str(error).lower()
    if "no permission" in msg or "access denied" in msg:
        return {
            "type": "permission_error",
            "user_action": (
                "飞书权限不足。请检查：\n"
                "1. Base 是否已共享给当前应用（编辑权限）\n"
                "2. 应用可用范围是否包含你的账号\n"
                "3. 如需发消息到群聊，机器人是否在群内"
            ),
            "fallback": "已切换到本地 JSON 模式，数据保存在本地 checkpoint.json"
        }
    return {"type": "unknown_error", "message": str(error)}
```

### 5.4 数据字段校验

向飞书写入数据前，需校验字段值是否符合 Schema：

```python
FIELD_VALIDATORS = {
    "目标表": {
        "类型": lambda v: v in ["里程碑", "日常任务", "学习目标", "面试准备"],
        "状态": lambda v: v in ["未开始", "进行中", "已完成", "已逾期"],
        "优先级": lambda v: v in ["高", "中", "低"],
        "截止日": lambda v: isinstance(v, int) and v > 0,  # 毫秒时间戳
    },
    "项目表": {
        "状态": lambda v: v in ["未开始", "进行中", "已完成", "暂停"],
        "构建模式": lambda v: v in ["自动", "手动", "混合"],
        "技术栈": lambda v: isinstance(v, list) and len(v) > 0,
        "总任务数": lambda v: isinstance(v, int) and v >= 0,
        "已完成任务": lambda v: isinstance(v, int) and v >= 0,
    },
}

def validate_fields(table_name, fields):
    validators = FIELD_VALIDATORS.get(table_name, {})
    errors = []
    for field_name, value in fields.items():
        if field_name in validators:
            if not validators[field_name](value):
                errors.append(f"字段 '{field_name}' 的值 '{value}' 不合法")
    if errors:
        raise ValueError(f"字段校验失败: {'; '.join(errors)}")
```

### 5.5 重试与回退流程图

```
┌─────────────────────────────────┐
│ MCP 操作请求                      │
└─────────────┬───────────────────┘
              ↓
       ┌──────┴──────┐
       │  网络/超时？  │── 是 ──→ 重试(1次)
       └──────┬──────┘              ↓
              │ 否            ┌──────┴──────┐
              ↓               │  重试成功？   │
       ┌──────┴──────┐       └──┬───────┬──┘
       │  权限错误？   │         │ 是     │ 否
       └──┬───────┬──┘         ↓        ↓
          │ 是    │ 否     返回结果   回退本地
          ↓       ↓
    提示用户   ┌──┴───────┐
    操作修复   │ 表不存在？ │
              └──┬───────┬──┘
                 │ 是     │ 否
                 ↓        ↓
             初始化流程  返回业务错误
```

---

## 附录 A：飞书字段类型速查

| 类型编号 | 类型名         | 说明                                         | 值格式                       |
|----------|----------------|----------------------------------------------|------------------------------|
| 1        | Text           | 多行文本，支持 `\\n` 换行                    | `"string"`                   |
| 2        | Number         | 数字                                         | `123` / `3.14`               |
| 3        | SingleSelect   | 单选，值必须匹配已有选项                      | `"选项名"`                   |
| 4        | MultiSelect    | 多选，值为选项名数组                          | `["选项A", "选项B"]`         |
| 5        | DateTime       | 日期时间，毫秒级 Unix 时间戳                  | `1718236800000`              |
| 7        | Checkbox       | 复选框                                       | `true` / `false`             |
| 11       | User           | 人员，值为 open_id / union_id / user_id      | `"ou_xxxxx"`                 |
| 13       | PhoneNumber    | 电话号码                                     | `"13800138000"`              |
| 15       | Url            | 超链接，JSON 对象 `{"link":"...","text":"..."}` | `{"link":"url","text":"text"}` |
| 17       | Attachment     | 附件，需先上传获取 attachment token           | `[{"file_token":"..."}]`     |
| 18       | Link           | 单向关联，值为关联表的 record_id             | `"recuXXXXXX"`              |
| 21       | DuplexLink     | 双向关联，值为关联表的 record_id             | `"recuXXXXXX"`              |

---

## 附录 B：环境变量与配置

resume-builder 技能期望以下环境变量或 Claude Code 配置：

| 变量/配置项                    | 说明                                 | 示例值                         |
|--------------------------------|--------------------------------------|--------------------------------|
| `FEISHU_BASE_TOKEN`            | 飞书 Base 的唯一标识 (app_token)      | `BPCnxxxxxxxxxxxxx`            |
| `FEISHU_USER_OPEN_ID`          | 用户 Open ID，用于消息推送接收方      | `ou_xxxxxxxxxxxxxxxxxx`        |
| `FEISHU_TABLE_ID_GOALS`        | 目标表的 table_id（初始化后自动填充）  | `tblxxxxxxxxxxxxx`             |
| `FEISHU_TABLE_ID_VERSIONS`     | 简历版本表的 table_id                 | `tblxxxxxxxxxxxxx`             |
| `FEISHU_TABLE_ID_PROJECTS`     | 项目表的 table_id                     | `tblxxxxxxxxxxxxx`             |
| `FEISHU_CHAT_ID`               | 可选，推送消息的目标群聊 ID           | `oc_xxxxxxxxxxxxxxxxxx`       |
| LOCAL_CHECKPOINT_PATH          | 本地断点文件路径                      | `.claude/skills/resume-builder/state/checkpoint.json` |

**获取方式：**
- `app_token`：打开飞书 Base，URL 中 `base/` 后的字符串
- `table_id`：打开具体数据表，URL 中 `table/` 后的字符串，或通过 `appTable_list` 获取
- `open_id`：通过 `contact_v3_user_batchGetId` 用手机号或邮箱查询
- `chat_id`：通过 `im_v1_chat_list` 获取群聊列表，或新建群聊后从返回结果获取

---

## 附录 C：Base 初始化一键脚本（概念）

以下为 resume-builder 初始化 Base 的概念流程，实际使用时拆分为多个 MCP 调用：

```
1. 用户提供 app_token 和 open_id
2. skill 调用 appTable_list，检查 3 张表是否存在
3. 缺少的表按 Section 1 的 Schema 创建
4. 获取所有表的 table_id 和 field_id 映射
5. 写入一条初始里程碑："[SYSTEM] resume-builder 初始化完成"
6. 将 table_id 映射缓存到本地配置文件
7. 向用户发送初始化完成消息
```

完成初始化后，resume-builder 即可正常使用全部飞书集成功能。
