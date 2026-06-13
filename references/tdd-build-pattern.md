# TDD 子代理构建模式参考

> 本文件定义了 resume-builder skill 在实施简历项目时使用的 TDD 子代理协作模式。
> 当用户简历中包含可实施的项目条目时，skill 按本文档定义的流程将其落地为真实代码。

---

## 目录

1. [项目拆解模式](#1-项目拆解模式)
2. [子代理派发模板](#2-子代理派发模板)
3. [规格审查清单](#3-规格审查清单)
4. [代码质量审查清单](#4-代码质量审查清单)
5. [按项目类型的 TDD 策略](#5-按项目类型的-tdd-策略)
6. [手动模式交接](#6-手动模式交接)
7. [混合模式协调](#7-混合模式协调)

---

## 1. 项目拆解模式

### 1.1 核心原则

将一条简历条目拆解为可独立实施、可独立测试的 TDD 任务。每个任务对应一个子代理的派发单元。

**拆解规则：**

| 规则 | 说明 |
|------|------|
| 单一可测单元 | 每个任务对应一个可独立运行测试的单元 |
| 依赖优先 | 基础模块（数据模型、工具函数）排在前面 |
| 自底向上 | 先核心逻辑，再接口层，最后集成 |
| 5-10 个任务 | 太少则粒度太粗，太多则协调成本过高 |
| 每个任务有明确产物 | 创建/修改哪些文件、运行什么测试命令、预期输出是什么 |

### 1.2 拆解示例

**简历条目：**
> "使用 Go 开发了分布式任务调度系统，支持优先级队列和失败重试"

**拆解结果：**

```
Task 1/6: Task 数据结构与验证
  目标: 定义 Task struct，实现字段验证逻辑
  文件: internal/model/task.go, internal/model/task_test.go
  测试: go test ./internal/model/ -run TestTaskValidation
  依赖: 无

Task 2/6: 优先级队列 (heap)
  目标: 基于 container/heap 实现任务优先级队列
  文件: internal/queue/priority.go, internal/queue/priority_test.go
  测试: go test ./internal/queue/ -run TestPriorityQueue
  依赖: Task 1 (引用 Task 类型)

Task 3/6: 任务调度器核心
  目标: 实现调度器主循环：从队列取任务 → 执行 → 记录结果
  文件: internal/scheduler/scheduler.go, internal/scheduler/scheduler_test.go
  测试: go test ./internal/scheduler/ -run TestScheduler
  依赖: Task 1, Task 2

Task 4/6: 失败重试机制
  目标: 实现指数退避重试，最大重试次数限制
  文件: internal/scheduler/retry.go, internal/scheduler/retry_test.go
  测试: go test ./internal/scheduler/ -run TestRetry
  依赖: Task 3

Task 5/6: REST API 接口
  目标: 实现 POST /tasks 和 GET /tasks/:id 两个端点
  文件: internal/api/handler.go, internal/api/handler_test.go
  测试: go test ./internal/api/ -run TestHandlers
  依赖: Task 3, Task 4

Task 6/6: 集成测试 + Docker Compose
  目标: 端到端验证：发请求 → 等待调度 → 检查结果
  文件: docker-compose.yml, integration/api_test.go
  测试: go test ./integration/ -tags=integration
  依赖: Task 1-5
```

### 1.3 任务描述模板

每个拆解后的任务应包含以下字段：

```
Task N/M: [任务名称]
  难度: easy | medium | hard
  模式: auto | manual | suggest（初始建议，用户可覆盖）
  目标: [一句话描述要构建什么]
  创建文件: [精确路径列表]
  修改文件: [精确路径列表]
  测试文件: [精确测试路径列表]
  测试命令: [可复制粘贴的测试命令]
  预期输出: [PASS / 具体断言]
  依赖: [前置任务列表，无则写"无"]
  约束:
    - [技术约束，如"不引入第三方库"]
    - [范围约束，如"只实现 HTTP 层，不碰数据库"]
```

### 1.4 拆解前检查

在开始拆解前，确认以下信息已就绪：

- [ ] 项目名称和技术栈已明确
- [ ] 目标代码库目录已确定（新建或已有仓库）
- [ ] 编程语言和主要框架已确定
- [ ] 用户对自动/手动模式的偏好已确认
- [ ] 飞书项目表已创建（用于跟踪任务状态）

---

## 2. 子代理派发模板

### 2.1 标准派发格式

向子代理派发实现任务时，使用以下统一模板：

```
Goal: [用一句话描述要构建什么]

Context:
  这是 "[项目名称]" 的第 N/M 个任务。
  已完成的任务: [列举已完成任务及其产出]
  当前任务依赖: [列举依赖的任务及其关键接口/类型]

Files:
  Create: [精确路径，每行一个]
  Modify: [精确路径，每行一个]
  Test: [精确测试路径]

Constraints:
  - 严格遵循 TDD：先写失败测试，再写最小实现使其通过
  - 保持简单，YAGNI 原则（你不会需要它）
  - 不要修改其他任务的文件
  - [语言特定的约束，如 "使用标准库，不引入第三方依赖"]

Test Command:
  [可复制粘贴的测试命令]

Expected Output:
  [PASS 或更具体的描述]

Verify:
  运行 [测试命令] 并确认所有测试通过
```

### 2.2 不同类型项目的派发变体

**后端 API 服务（Go/Python/Node.js）：**
```
Constraints 补充:
  - 接口定义在前（interface），实现放在后
  - 错误不要静默吞掉，用 fmt.Errorf 包装上下文
  - HTTP 处理器只做参数绑定和响应序列化，业务逻辑放 service 层
```

**前端组件（React/Vue）：**
```
Constraints 补充:
  - 组件渲染用 @testing-library（不测试实现细节）
  - 状态管理逻辑可单独单测
  - 不写快照测试（snapshot tests 维护成本高）
```

**CLI 工具：**
```
Constraints 补充:
  - flag/arg 解析和业务逻辑严格分离
  - stdout/stderr 通过接口注入以便测试
  - 集成测试用真实临时文件
```

**数据管道：**
```
Constraints 补充:
  - 每个 transform 函数必须是纯函数
  - source/sink 通过接口抽象
  - 端到端测试使用小样本数据集
```

### 2.3 子代理类型选择

| 任务类型 | 推荐子代理类型 | 原因 |
|---------|---------------|------|
| 标准代码实现 | `general-purpose` | 通用实现能力，覆盖面广 |
| 复杂业务逻辑 | `general-purpose` | 需要多文件协调和上下文理解 |
| 算法/数据结构 | `general-purpose` | 需要正确的算法实现和边界处理 |
| 配置文件/脚本 | `general-purpose` | 直接可用 Bash 工具验证 |
| 代码审查 | `code-review` skill | 专业审查能力 |
| 调试/修复 | `general-purpose` | 需要诊断和修复能力 |

> **注意：** `claude-code-guide` 子代理仅用于回答关于 Claude Code 功能的问题，不可用于代码实现。

---

## 3. 规格审查清单

每个任务实现完成后，由 spec-reviewer 子代理进行审查。审查必须覆盖以下 5 项：

### 3.1 审查清单

```
1. ✅ 范围符合性
   问: 代码是否只做了任务要求的事情，没有额外扩展？
   反例: Task 3 要求实现调度器，但代码顺便实现了持久化层
   正例: 代码严格实现了调度循环，持久化留给后续任务

2. ✅ 测试是否测试行为
   问: 测试是否验证了可观测的行为，而不是内部实现细节？
   反例: 测试检查了某个私有方法的调用次数
   正例: 测试验证了给定输入后，公开接口返回了预期输出

3. ✅ 与前序任务集成正确
   问: 新代码是否正确引用了前序任务的类型和接口？
   反例: 自己重新定义了 Task 类型而不是引用 Task 1 的定义
   正例: import 了 Task 1 的包，使用其导出的类型

4. ✅ 错误情况覆盖
   问: 测试是否覆盖了至少 2 种错误/边界情况？
   反例: 只测试了 happy path
   正例: 测试了空输入、无效参数、依赖失败等场景

5. ✅ API/接口一致性
   问: 导出的函数签名、类型命名是否与项目整体风格一致？
   反例: 项目用 PascalCase 但这任务用了 snake_case
   正例: 命名风格、包结构、错误处理方式与已有代码对齐
```

### 3.2 审查结果处理

| 审查结论 | 处理方式 |
|---------|---------|
| 全部通过 | 标记任务为 completed，进入下一任务 |
| 1-2 项未通过 | 派发 fix 子代理，针对性修复后重新审查 |
| 3 项以上未通过 | 标记任务为 blocked，需要重新实现 |

### 3.3 审查报告格式

```
## Spec Review: Task N "[任务名称]"

### 审查结果
| 检查项 | 结果 | 说明 |
|--------|------|------|
| 范围符合性 | ✅/❌ | [具体说明] |
| 测试行为 | ✅/❌ | [具体说明] |
| 集成正确 | ✅/❌ | [具体说明] |
| 错误覆盖 | ✅/❌ | [具体说明] |
| 接口一致 | ✅/❌ | [具体说明] |

### 问题详情
[如果有 ❌，详细描述问题及修复建议]

### 结论
APPROVED / NEEDS_FIX / REJECTED
```

---

## 4. 代码质量审查清单

规格审查通过后，进行代码质量审查。审查 5 个维度：

### 4.1 审查清单

```
1. ✅ DRY（不重复）
   问: 是否有重复的逻辑块或常量？
   反例: 三个文件中各写了一遍相同的错误处理逻辑
   修复: 提取公共函数或使用 middleware

2. ✅ 命名
   问: 变量、函数、类型命名是否清晰、符合语言习惯？
   反例: data, tmp, process() — 含义模糊
   正例: taskPayload, retryCount, scheduleNext()

3. ✅ 错误处理
   问: 错误是否被正确处理，没有被静默吞掉？
   反例: _ = file.Close() 或 fmt.Println(err) 后继续执行
   正例: 错误向上传递并包装上下文信息

4. ✅ 无死代码
   问: 是否有被注释掉的代码块、从未调用的函数？
   反例: /* TODO: remove this later */ 后面残留的旧实现
   正例: 代码干净，没有注释掉的段落

5. ✅ 注释质量
   问: 注释解释的是"为什么"而不是"做了什么"？
   反例: // loop through items — 冗余，代码本身已说明
   正例: // 使用二分查找而非线性扫描，因为 items 已排序且数量可能上万
```

### 4.2 自动检查工具

| 语言 | 推荐工具 | 用途 |
|------|---------|------|
| Go | `go vet`, `staticcheck` | 静态分析 |
| Python | `ruff`, `mypy` | 格式+类型检查 |
| TypeScript | `eslint`, `tsc --noEmit` | 类型+风格检查 |
| Rust | `cargo clippy`, `cargo fmt --check` | lint+格式 |

### 4.3 质量审查结果处理

质量审查发现问题时，按严重程度分级处理：

| 级别 | 条件 | 处理 |
|------|------|------|
| BLOCKER | 重复逻辑 > 10 行、错误被吞掉、命名误导 | 必须修复后才能合并 |
| WARNING | 轻微重复（≤ 5 行）、注释可改进 | 记录问题但允许通过 |
| INFO | 命名可优化但不影响理解 | 仅记录，不阻塞 |

---

## 5. 按项目类型的 TDD 策略

### 5.1 后端 API 服务

**实施顺序：**

```
Phase 1: 数据模型 + 验证测试
  - 定义 struct/model + 序列化/反序列化
  - 测试字段验证（必填、格式、范围）
  - 测试 JSON 序列化往返

Phase 2: Service 层 + Mock 测试
  - 定义 interface（Repository/Client）
  - 使用 mock 实现测试业务逻辑
  - 覆盖正常流程 + 各依赖失败场景

Phase 3: HTTP Handler + 集成测试
  - 使用 httptest 测试路由和中间件
  - 测试请求参数校验、响应状态码
  - 测试错误响应的统一格式

Phase 4: 端到端测试
  - 启动真实服务（或 TestMain 中初始化）
  - 发送真实 HTTP 请求验证完整链路
  - 可选：Docker Compose 环境测试
```

**Go 后端示例目录结构：**

```
project/
├── internal/
│   ├── model/          # Phase 1: 数据模型
│   │   ├── task.go
│   │   └── task_test.go
│   ├── service/        # Phase 2: 业务逻辑
│   │   ├── scheduler.go
│   │   └── scheduler_test.go
│   ├── api/            # Phase 3: HTTP 层
│   │   ├── handler.go
│   │   └── handler_test.go
│   └── repository/     # Phase 2: 数据访问接口
│       ├── interface.go
│       └── mock.go
├── integration/        # Phase 4: 集成测试
│   └── api_test.go
├── go.mod
└── main.go
```

### 5.2 前端组件/应用

**实施顺序：**

```
Phase 1: 组件渲染测试
  - 测试组件挂载后 DOM 输出
  - 测试 props 变化时重新渲染
  - 不测试内部 state（那是实现细节）

Phase 2: 状态管理测试
  - 如果是独立状态逻辑（composable/hook），单独测试
  - 测试 action/mutation/reducer 的纯逻辑部分
  - 不测试框架的响应式系统本身

Phase 3: 交互测试
  - 模拟用户点击、输入、提交
  - 测试事件回调是否被正确调用
  - 测试 UI 状态变化（loading → success/error）

Phase 4: 集成测试（Mock API）
  - 使用 MSW (Mock Service Worker) 拦截请求
  - 测试完整用户流程：加载列表 → 创建条目 → 看到新条目
```

**React 示例关键约定：**

```
- 使用 @testing-library/react（不用 enzyme）
- 查询元素用 getByRole/getByLabelText（不用 getByTestId 除非必要）
- 用 userEvent（不用 fireEvent，除非需要特殊事件）
- 不测试 CSS 类名或样式
- 每个测试代表一个用户故事
```

### 5.3 游戏原型（Unity/Godot）

**核心策略：将游戏逻辑与引擎 API 分离。**

```
Phase 1: 核心机制单元测试（纯逻辑，不依赖 MonoBehaviour）
  - 伤害计算公式
  - 状态机转换逻辑
  - 资源管理/冷却计算
  - 所有逻辑放在独立的纯 C# 文件中测试

Phase 2: 场景集成测试
  - 创建最小场景，挂载组件
  - 通过 Unity Test Framework 或 Godot GUT 运行
  - 测试 GameObject 生命周期中的行为

Phase 3: 可玩性检查清单
  - 玩家可以完成核心循环吗？
  - 边界情况：角色死亡、资源耗尽、暂停/恢复
  - 输入响应延迟是否可接受？
```

**分离模式：**

```
Scripts/
├── Core/                  # Phase 1: 纯逻辑，可独立单测
│   ├── CombatSystem.cs
│   ├── StateMachine.cs
│   └── Tests/
│       ├── CombatSystemTest.cs
│       └── StateMachineTest.cs
├── Gameplay/              # Phase 2: 依赖引擎的组件
│   ├── PlayerController.cs
│   └── EnemyAI.cs
└── Integration/           # Phase 3: 场景测试
    └── CombatSceneTest.cs
```

### 5.4 CLI 工具/脚本

```
Phase 1: 输入解析测试
  - 测试 flag/arg 的各种组合
  - 测试无效输入的错误提示
  - 测试默认值

Phase 2: 核心逻辑测试
  - 业务逻辑独立于 CLI 框架单测
  - 输入/输出通过接口注入
  - 测试边界条件和错误路径

Phase 3: 输出格式化测试
  - 测试不同输出格式（table, JSON, plain text）
  - 测试输出内容与预期一致

Phase 4: 集成测试（真实文件系统）
  - 创建临时目录和文件
  - 运行完整的 CLI 命令
  - 验证文件输出和退出码
```

### 5.5 数据管道

```
Phase 1: Transform 函数测试
  - 每个 transform 是纯函数
  - 测试各种输入 → 预期输出
  - 测试边界值（空数据、null、大数值）

Phase 2: Source/Sink 适配器测试
  - Source: 能正确连接并读取数据
  - Sink: 能正确写入并确认写入成功
  - 使用测试替身（test double）隔离外部依赖

Phase 3: 端到端管道测试
  - 使用小样本测试数据集（5-10 条记录）
  - 验证 source → transform → sink 完整链路
  - 测试失败恢复和断点续传
```

---

## 6. 手动模式交接

当用户为某个任务选择手动模式时，skill 生成一份详尽的手动实施指南。

### 6.1 交接模板

```markdown
# 手动实施指南：Task N "[任务名称]"

## 概述
- 项目：[项目名称]
- 任务：N/M
- 难度：[easy/medium/hard]
- 预计时间：[估算]

## 前置准备
- [ ] 确认开发环境：[语言版本、工具]
- [ ] 确认依赖已安装：[列表]
- [ ] 阅读前序任务产出：[文件列表]

## 实施步骤

### Step 1: [步骤名称]
**创建文件**: `path/to/file.go`

**代码草图:**
```go
// 关键结构/函数签名，不提供完整实现
type Task struct {
    ID       string
    Name     string
    Priority int
}

func NewTask(name string, priority int) (*Task, error) {
    // TODO: 实现验证逻辑
    // 1. name 不能为空
    // 2. priority 必须在 1-10 之间
}
```

**运行测试确认:**
```bash
go test ./path/to/ -run TestNewTask -v
# 预期: FAIL — 测试先于实现，这是正常的
```

### Step 2: [步骤名称]
...

## 需要编写的测试用例

| # | 测试名称 | 输入 | 预期输出 |
|---|---------|------|---------|
| 1 | TestNewTaskValid | name="report", priority=5 | Task{...}, nil |
| 2 | TestNewTaskEmptyName | name="", priority=5 | nil, error |
| 3 | TestNewTaskInvalidPriority | name="ok", priority=0 | nil, error |
| 4 | TestNewTaskPriorityTooHigh | name="ok", priority=11 | nil, error |

## 验证命令
```bash
# 全部测试通过后运行
go test ./... -v

# 预期输出: 所有测试 PASS
```

## 完成后
1. 将代码推送到分支: `feature/task-N-short-name`
2. 在飞书项目表中将任务标记为 `completed`
3. 回复 "Task N done" 以触发下一步骤
```

### 6.2 手动模式标记

在飞书项目表中：
- `mode` 字段设为 `manual`
- `status` 字段保持 `in_progress`
- `guide_generated` 字段打勾
- `assigned_to` 字段填写用户 ID

### 6.3 手动模式超时

- 手动任务默认 72 小时超时
- 超时后飞书发送提醒消息
- 用户可回复 "skip" 跳过，或 "extend" 延长时间

---

## 7. 混合模式协调

### 7.1 自动/手动判定逻辑

每个任务在拆解阶段会根据复杂度启发式给出模式建议：

```
建议自动 (auto) 的条件:
  - 涉及文件数 ≤ 3
  - 逻辑复杂度低（单一职责，无复杂算法）
  - 无外部依赖（数据库、第三方 API）
  - 任务类型为：配置、脚手架、简单 CRUD

建议手动 (manual) 的条件:
  - 涉及文件数 > 3
  - 需要深入理解业务领域
  - 涉及 UI/UX 决策
  - 需要访问用户无法代理的敏感凭证
  - 用户明确偏好手动实现
```

**用户的模式选择优先级高于建议。** 查看飞书项目表中的 `mode_override` 字段。

### 7.2 自动任务失败处理

```
自动任务执行流程:
  1. 派发子代理实现
  2. spec-review 审查
     ├── PASS → 标记 completed
     └── FAIL → 进入修复流程
           ↓
  3. 修复流程:
     第 1 次失败: 自动派发 fix 子代理，提供审查反馈
     第 2 次失败: 标记为 blocked，生成手动实施指南
                  通知用户并提供选项：
                  a) 切换到手动模式
                  b) 重新描述需求后重试
                  c) 跳过此任务
```

### 7.3 手动 + 自动成果合并

当项目中同时存在手动和自动完成的任务时：

```
合并检查清单:
  1. [ ] 命名风格一致：手动代码的命名与自动代码对齐
  2. [ ] 导入路径正确：手动代码正确引用了自动代码的包/模块
  3. [ ] 测试覆盖：手动代码包含对应测试
  4. [ ] 项目编译通过：完整项目可以编译
  5. [ ] 全部测试通过：./... 范围测试全部 PASS
  6. [ ] 无导入冲突：go mod tidy / npm install 无报错
```

**合并流程：**

```
1. 用户完成手动任务 → 代码推送到分支
2. Skill 检测到分支更新 → 触发合并审查
3. 运行合并检查清单
   ├── 全部通过 → 自动合并到主分支，标记任务 completed
   └── 发现问题 → 生成冲突报告，通知用户修复
```

### 7.4 任务状态机

```
                    ┌─────────┐
                    │ pending │
                    └────┬────┘
                         │
               ┌─────────┼─────────┐
               ▼         ▼         ▼
          ┌────────┐ ┌────────┐ ┌─────────┐
          │  auto  │ │manual  │ │ skipped │
          └───┬────┘ └───┬────┘ └─────────┘
              │          │
              ▼          ▼
     ┌────────────┐ ┌─────────────┐
     │ in_progress│ │guide_sent   │
     └─────┬──────┘ └──────┬──────┘
           │               │
     ┌─────┼─────┐         │ (用户提交代码)
     ▼     ▼     ▼         ▼
 ┌──────┐┌──────┐┌──────────┐
 │PASS  ││FAIL  ││ review   │
 │done  ││retry ││pending   │
 └──────┘└──┬───┘└────┬─────┘
            │          │
      ┌─────┼─────┐    │
      ▼     ▼     ▼    │
  ┌──────┐┌──────┐┌──────┐
  │PASS  ││FAIL  ││      │
  │done  ││→manual│      │
  └──────┘└──────┘      │
            │           │
            ▼           ▼
       ┌──────────┐ ┌──────────┐
       │ manual   │ │completed │
       │ guide    │ └──────────┘
       │ generated│
       └──────────┘
```

### 7.5 飞书同步策略

每完成一个任务（自动或手动），立即同步到飞书项目表：

| 同步时机 | 同步内容 |
|---------|---------|
| 任务开始 | status → in_progress, started_at → now |
| 自动通过 | status → completed, completed_at → now, tests_passed → N |
| 自动失败 | status → blocked, error_log → 简要错误描述 |
| 手动指南生成 | status → manual, guide_url → 指南文件路径 |
| 手动完成 | status → completed, completed_at → now, merged_by → user |
| 任务跳过 | status → skipped, skip_reason → 用户提供的理由 |

---

## 附录 A：常见问题

### Q: 一个项目应该拆成多少个任务？
**A:** 5-10 个。少于 5 个说明单个任务太粗，子代理容易超出范围；多于 10 个说明拆得太细，协调成本过高。小型 CLI 工具可以少到 3 个，大型全栈项目可以多到 15 个。

### Q: 子代理失败了怎么办？
**A:** 先看失败原因。如果是测试写对了但代码有 bug → 自动修复一次。如果是理解偏差（做了超出范围的事）→ 生成手动指南。如果是环境问题（依赖安装失败）→ 人工介入。

### Q: 用户中途想改变技术栈怎么办？
**A:** 已实施的任务保持不变（投入了真实工作），未开始的任务重新评估。如果改动太大（如 Go → Rust），建议开新项目而不是改建。

### Q: 如何保证不同子代理的代码风格一致？
**A:** (1) 拆解时定义好公共类型和接口；(2) 代码质量审查会检查一致性；(3) 合并前运行 lint 工具统一格式。

---

## 附录 B：任务拆解反例

**反面示例：拆得太粗**

```
Task 1/2: 实现后端
  文件: 全部
  测试: 全部
  依赖: 无
```
问题：任务过大，子代理无法高质量完成，测试覆盖难以保证。

**反面示例：拆得太细**

```
Task 1/15: 定义 Task.ID 字段
Task 2/15: 定义 Task.Name 字段
Task 3/15: 定义 Task.Priority 字段
...
```
问题：每个任务没有独立测试价值，协调成本远超实现成本。

---

> 本文档为 resume-builder skill 的内部参考，定义了从简历条目到可运行代码的完整 TDD 实施模式。
> 所有子代理派发和行为均需遵循本文档定义的流程和约定。
