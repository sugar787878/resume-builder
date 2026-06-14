# 项目模板参考

> 本文档为 resume-builder skill 提供各就业方向的项目模板。每个模板包含完整的简历描述、技术栈、实现路径和扩展方向。

---

## 零、2026 实战项目模板（企业级）

> 以下模板基于 2025-2026 年 NLP/LLM 方向真实岗位 JD 提炼，面向面试展示而非教学。每个模板均包含可量化的简历 bullet point、最小可运行骨架和核心实现任务。

---

### Template 1: AI 面试助手 (RAG + Agent) ⭐ 最高优先级

- **项目名称:** LangChain/LangGraph + AI 智能面试助手
- **难度:** 进阶
- **技术栈:** LangChain / LangGraph + ChromaDB / Milvus + FastAPI + MCP + Docker
- **简历描述示例:**
  - 构建基于 RAG + Agent 的智能面试助手，支持多轮追问、模拟评分和面经知识库检索，服务化部署后 QPS 达 30+
  - 实现文档解析（PDF/Word）→ 智能切片（Semantic Chunking）→ 混合检索（BM25 + 向量）→ Rerank 完整管道，检索 Recall@5 达 88%
  - 基于 LangGraph 实现 Agent 多轮对话状态机，集成 MCP 协议扩展工具调用能力，对话相关性评分 > 90%
- **最小可运行骨架:**
  ```
  interview-assistant/
  ├── src/
  │   ├── parser/               # 文档解析模块
  │   │   ├── pdf_parser.py     # PDF 解析（PyMuPDF / pdfplumber）
  │   │   └── docx_parser.py    # Word 解析
  │   ├── chunker/
  │   │   └── semantic_chunk.py # 语义切片（按段落 + 语义边界）
  │   ├── retriever/
  │   │   ├── bm25_retriever.py # BM25 关键词检索
  │   │   ├── vector_retriever.py # 向量检索（ChromaDB / Milvus）
  │   │   ├── hybrid.py         # 混合检索与融合（RRF）
  │   │   └── reranker.py       # Rerank（Cross-Encoder / BGE-Reranker）
  │   ├── agent/
  │   │   ├── graph.py          # LangGraph 对话状态机
  │   │   ├── tools.py          # MCP 工具定义与注册
  │   │   └── prompts.py        # 提示词模板
  │   ├── scorer/
  │   │   └── evaluator.py      # 回答评分与反馈生成
  │   ├── api.py                # FastAPI 服务入口
  │   └── config.py             # 配置管理
  ├── data/
  │   ├── raw/                  # 原始面经文件
  │   └── processed/            # 切片后的向量库
  ├── evaluations/              # Ragas 评估脚本与结果
  ├── docker-compose.yml
  └── requirements.txt
  ```
- **核心实现任务:**
  1. 搭建 FastAPI 项目骨架，定义文档上传与问答接口契约（OpenAPI）
  2. 实现文档解析管道：PDF（PyMuPDF）+ Word（python-docx）→ 统一 Text 结构
  3. 实现语义切片：基于段落 + 滑动窗口，支持自定义 chunk_size 与 overlap
  4. 实现向量检索：选择 Embedding 模型（BGE-large-zh）→ 写入 ChromaDB → 相似度查询
  5. 实现 BM25 检索：基于 jieba 分词构建 BM25 索引，支持关键词查询
  6. 实现混合检索：RRF（Reciprocal Rank Fusion）融合 BM25 + 向量结果 → Rerank 精排
  7. 实现 Agent 对话：LangGraph 定义状态节点（检索→生成→追问判断→评分），集成 MCP 工具
  8. 实现 Ragas 评估管线：自动计算 Context Relevance / Answer Relevance / Faithfulness
- **面试加分点:**
  - 混合检索 + Rerank 的完整检索管道（而非简单的向量相似度）
  - Ragas 框架进行 RAG 质量评估（有量化指标）
  - MCP 协议标准化工具调用（2025 前沿技术）
  - 线上 Demo（HuggingFace Space / 云服务器部署）
- **参考资料:**
  - github.com/alvinxx1123/sspOffer-interview-assistant
  - LangGraph 官方文档: langchain-ai.github.io/langgraph/
  - Ragas 评估框架: docs.ragas.io

---

### Template 2: Multi-Agent 协作系统

- **项目名称:** LangGraph + MCP + Multi-Agent 协作框架
- **难度:** 进阶+
- **技术栈:** LangGraph + MCP + Function Calling + FastAPI + Redis + Docker
- **简历描述示例:**
  - 基于 LangGraph 实现多 Agent 协作框架，支持搜索、分析、写作、审核 4 类 Agent 通过 DAG 编排协同完成复杂任务
  - 集成 MCP 协议实现标准化工具调用，Agent 可按需动态发现和调用外部工具，工具调用成功率达 92%
  - 设计 Agent 间通信协议（消息队列 + 共享上下文），支持任务分解、并行执行与结果聚合，端到端任务完成率 87%
- **最小可运行骨架:**
  ```
  multi-agent-framework/
  ├── src/
  │   ├── agents/
  │   │   ├── base.py          # Agent 基类（状态、消息、工具）
  │   │   ├── search_agent.py  # 搜索 Agent（信息检索）
  │   │   ├── analysis_agent.py # 分析 Agent（数据/文本分析）
  │   │   ├── writer_agent.py  # 写作 Agent（内容生成）
  │   │   └── reviewer_agent.py # 审核 Agent（质量检查）
  │   ├── orchestrator/
  │   │   ├── graph.py         # LangGraph DAG 编排
  │   │   ├── planner.py       # 任务分解与分配
  │   │   └── aggregator.py   # 结果聚合与冲突解决
  │   ├── mcp/
  │   │   ├── tool_registry.py # MCP 工具注册中心
  │   │   ├── clients.py       # MCP 客户端封装
  │   │   └── tools/           # 自定义工具实现
  │   │       ├── web_search.py
  │   │       ├── code_executor.py
  │   │       └── data_fetcher.py
  │   ├── communication/
  │   │   └── message_bus.py   # Agent 间消息总线
  │   ├── api.py               # FastAPI 入口
  │   └── config.py
  ├── docker-compose.yml
  └── requirements.txt
  ```
- **核心实现任务:**
  1. 定义 Agent 基类：规范状态字段（messages、context、next_agent）、工具声明、执行接口
  2. 实现 Search Agent：封装 Tavily / SerpAPI 搜索，支持结构化结果提取
  3. 实现 Analysis Agent：接收搜索 Agent 结果，进行关键信息提取与对比分析
  4. 实现 Writer Agent：基于分析结果生成结构化内容（报告 / 方案 / 摘要）
  5. 实现 Reviewer Agent：对内容进行质量评分、事实核查与修改建议
  6. 实现 LangGraph 编排：DAG 定义（Search → Analysis → Writer → Reviewer），含条件分支与错误恢复
  7. 实现 MCP 工具注册与动态发现：每个 Agent 启动时注册自身工具，运行时动态调用
  8. 实现 Agent 间通信：Redis Pub/Sub 消息总线 + 共享 Context 对象
- **面试加分点:**
  - MCP 协议（Anthropic 2025 标准），展示对行业前沿的关注
  - Agent 间通信设计（非简单的顺序调用，有消息总线）
  - 错误恢复与任务重分配机制
  - 可扩展架构（新增 Agent 类型无需改动编排引擎）
- **参考资料:**
  - LangGraph Multi-Agent 示例: github.com/langchain-ai/langgraph
  - MCP 协议规范: modelcontextprotocol.io
  - CrewAI / AutoGen 作为对比参考

---

### Template 3: 企业级 RAG 知识库

- **项目名称:** FastAPI + PostgreSQL + pgvector + 企业级 RAG 知识库平台
- **难度:** 进阶
- **技术栈:** FastAPI + PostgreSQL + pgvector + Redis + MinIO + Docker Compose
- **简历描述示例:**
  - 从零构建企业级 RAG 知识库平台，支持多用户、多知识库、权限隔离与文档生命周期管理
  - 实现多路检索（向量 + 全文 + 知识图谱）→ 缓存加速（Redis）→ 流式输出（SSE）→ 用户反馈闭环完整链路
  - 生产级架构设计：Prometheus 可观测性 + Docker Compose 一键部署，QPS > 50，P99 延迟 < 500ms，支持 10GB+ 文档
- **最小可运行骨架:**
  ```
  enterprise-rag/
  ├── src/
  │   ├── api/
  │   │   ├── v1/
  │   │   │   ├── auth.py       # 认证接口
  │   │   │   ├── kb.py         # 知识库 CRUD
  │   │   │   ├── document.py   # 文档管理
  │   │   │   ├── search.py     # 检索接口
  │   │   │   └── feedback.py   # 用户反馈
  │   │   └── deps.py           # 依赖注入（DB Session、当前用户）
  │   ├── core/
  │   │   ├── config.py         # 全局配置
  │   │   ├── security.py       # JWT + RBAC 权限
  │   │   └── database.py       # PostgreSQL + pgvector 连接
  │   ├── services/
  │   │   ├── chunk_service.py  # 切片服务
  │   │   ├── embed_service.py  # 向量化服务
  │   │   ├── search_service.py # 检索编排
  │   │   └── cache_service.py  # Redis 缓存
  │   ├── models/               # SQLAlchemy 模型
  │   │   ├── user.py
  │   │   ├── knowledge_base.py
  │   │   ├── document.py
  │   │   └── chunk.py
  │   ├── stream/
  │   │   └── sse.py            # SSE 流式输出
  │   └── observability/
  │       ├── metrics.py        # Prometheus 指标
  │       └── logging.py        # 结构化日志
  ├── migrations/               # Alembic 迁移
  ├── nginx/
  │   └── nginx.conf
  ├── docker-compose.yml
  ├── Dockerfile
  └── requirements.txt
  ```
- **核心实现任务:**
  1. 搭建 FastAPI 项目结构，配置 PostgreSQL + pgvector + Redis + MinIO（docker-compose）
  2. 设计数据库模型：User、KnowledgeBase、Document、Chunk、Feedback（含 ER 图）
  3. 实现用户认证与 RBAC：注册/登录、JWT 签发、知识库级别权限隔离
  4. 实现文档管理管道：上传 → 格式检测 → 切片（Semantic + Fixed）→ 向量化 → 入 pgvector
  5. 实现多路检索：pgvector 向量检索 + PostgreSQL full-text search + Redis 缓存热点查询
  6. 实现流式输出：SSE 协议逐 token 返回生成结果，支持客户端主动断开
  7. 实现反馈闭环：用户点赞/点踩 → 记录 Bad Case → 触发切片/检索策略优化
  8. 添加可观测性：Prometheus 指标（QPS、延迟分桶、缓存命中率）+ Grafana Dashboard
- **面试加分点:**
  - 生产级架构（非 Demo），考虑了多用户、权限、缓存、可观测性
  - pgvector 而非 ChromaDB（更适合生产环境，与 PostgreSQL 深度集成）
  - SSE 流式输出（用户体验好，实际生产常用）
  - Docker Compose 一键部署（面试官可以本地跑起来）
  - 用户反馈闭环（体现了迭代优化思维，而非一次性项目）
- **参考资料:**
  - pgvector: github.com/pgvector/pgvector
  - Dify 开源 RAG 平台: github.com/langgenius/dify
  - RAGAS 评估: docs.ragas.io

---

### Template 4: 大模型微调平台

- **项目名称:** PyTorch + DeepSpeed + LoRA/QLoRA + 大模型微调训练平台
- **难度:** 高级
- **技术栈:** PyTorch + DeepSpeed + LoRA / QLoRA (PEFT) + Wandb + Gradio + vLLM
- **简历描述示例:**
  - 搭建大模型微调平台，支持 LoRA/QLoRA 微调、数据集版本管理、自动评估与一键部署，覆盖 LLaMA/Qwen 等主流基座
  - 基于 DeepSpeed ZeRO-3 实现分布式训练，支持 10B+ 参数模型在 4×A100 上高效微调，微调效率较全参提升 40%
  - 设计自动化评估管道（BLEU / ROUGE / 人工评估代理），模型导出后通过 vLLM 部署为推理服务，P99 延迟 < 200ms
- **最小可运行骨架:**
  ```
  llm-finetune-platform/
  ├── src/
  │   ├── data/
  │   │   ├── preprocessor.py   # 数据预处理（格式标准化）
  │   │   ├── dataset.py        # PyTorch Dataset 封装
  │   │   └── version_manager.py # 数据集版本管理
  │   ├── trainer/
  │   │   ├── train.py          # 训练主脚本
  │   │   ├── lora_config.py    # LoRA 配置（rank、alpha、target_modules）
  │   │   ├── ds_config.json    # DeepSpeed ZeRO 配置
  │   │   └── callbacks.py      # Wandb 回调 + 早停
  │   ├── evaluator/
  │   │   ├── metrics.py        # BLEU / ROUGE / Perplexity
  │   │   ├── auto_eval.py      # 自动化评估管道
  │   │   └── compare.py        # 基座 vs 微调模型对比
  │   ├── deploy/
  │   │   ├── export.py         # 模型合并与导出
  │   │   ├── vllm_serve.py     # vLLM 部署脚本
  │   │   └── benchmark.py      # 推理性能压测
  │   ├── ui/
  │   │   └── app.py            # Gradio Web 界面
  │   └── config.py
  ├── configs/                   # 各模型配置文件
  │   ├── qwen2_7b_lora.yaml
  │   └── llama3_8b_qlora.yaml
  ├── docker-compose.yml
  └── requirements.txt
  ```
- **核心实现任务:**
  1. 搭建 PyTorch + Transformers + PEFT 开发环境，配置 DeepSpeed ZeRO-3
  2. 实现数据预处理管道：支持 Alpaca/ShareGPT 格式 → 统一 Chat Template → 数据集版本快照
  3. 实现 LoRA 训练脚本：加载基座模型 → 注入 LoRA Adapter → DeepSpeed 分布式训练 → Wandb 日志
  4. 实现 QLoRA 训练支持：4-bit 量化加载（bitsandbytes）→ 与 LoRA 共用训练引擎
  5. 实现自动化评估管道：训练结束后自动运行 BLEU / ROUGE / Perplexity 评估，生成对比报告
  6. 实现模型合并与导出：LoRA Adapter 合并回基座权重 → 导出为 HuggingFace 格式
  7. 实现 vLLM 推理部署：加载合并后模型 → 启动 OpenAI 兼容 API → 压测吞吐与延迟
  8. 实现 Gradio Web 界面：数据集上传 → 参数配置 → 训练监控 → 结果评估 → 模型下载
- **面试加分点:**
  - DeepSpeed ZeRO 分布式训练（展示大规模训练经验）
  - QLoRA 量化微调（降低硬件门槛，实际落地常用）
  - 自动化评估管道（非手动跑脚本，体现工程化思维）
  - vLLM 高性能推理部署（PagedAttention 技术）
  - 数据集版本管理（体现数据驱动迭代意识）
- **参考资料:**
  - PEFT (LoRA/QLoRA): github.com/huggingface/peft
  - DeepSpeed: github.com/microsoft/DeepSpeed
  - vLLM: github.com/vllm-project/vllm
  - LLaMA-Factory (参考架构): github.com/hiyouga/LLaMA-Factory

---

### Template 5: LLM 应用观测平台

- **项目名称:** FastAPI + ClickHouse + Grafana + LLM 可观测性平台
- **难度:** 进阶
- **技术栈:** FastAPI + ClickHouse + Grafana + OpenTelemetry + Redis + Docker
- **简历描述示例:**
  - 构建 LLM 应用可观测平台，实现调用链追踪、Token 消耗统计、成本核算与质量评估，支持 100+ 应用接入
  - 基于 OpenTelemetry 标准化埋点方案，无侵入接入 LangChain / OpenAI SDK，日处理 1 亿+ 条日志
  - 设计异常检测与告警规则（成本突增、延迟恶化、质量下降），通过 Grafana Alerting 推送飞书通知
- **最小可运行骨架:**
  ```
  llm-observability/
  ├── src/
  │   ├── sdk/
  │   │   ├── tracer.py        # OpenTelemetry Tracer 封装
  │   │   └── decorators.py    # @trace_llm_call 装饰器
  │   ├── collector/
  │   │   ├── api.py           # 日志接收 API（OTLP）
  │   │   └── processor.py    # 日志预处理（Token 计数、延迟分桶）
  │   ├── storage/
  │   │   ├── clickhouse.py   # ClickHouse 写入与查询
  │   │   └── schema.sql      # ClickHouse 表结构
  │   ├── analytics/
  │   │   ├── cost.py          # 成本计算引擎（按模型单价）
  │   │   ├── latency.py      # 延迟分桶统计
  │   │   └── quality.py      # LLM-as-Judge 质量评估
  │   ├── alerts/
  │   │   ├── rules.py        # 告警规则引擎
  │   │   └── notifier.py     # 飞书 Webhook 通知
  │   ├── api/
  │   │   └── query_api.py    # 查询 API（应用/时间/模型维度）
  │   └── config.py
  ├── grafana/
  │   ├── dashboards/
  │   │   ├── overview.json   # 全局概览 Dashboard
  │   │   ├── cost.json       # 成本分析 Dashboard
  │   │   └── quality.json    # 质量监控 Dashboard
  │   └── provisioning/       # Grafana 数据源自动配置
  ├── docker-compose.yml
  └── requirements.txt
  ```
- **核心实现任务:**
  1. 搭建 ClickHouse + Grafana + Redis 基础设施（docker-compose），设计日志表结构（按日期分区 + 物化视图）
  2. 实现 OpenTelemetry SDK 封装：装饰器 `@trace_llm_call`，自动捕获 model / prompt / tokens / latency / response
  3. 实现日志采集器：接收 OTLP 协议数据 → 预处理（Token 计数补偿、延迟分桶、错误分类）
  4. 实现 ClickHouse 写入管道：批量写入优化（Buffer Engine）→ 物化视图预聚合（分钟/小时/天粒度）
  5. 实现成本核算引擎：维护模型单价表（GPT-4o、Claude 3.5、DeepSeek-V4 等）× Token 用量 → 实时成本
  6. 实现查询 API：按应用、时间区间、模型、用户等维度提供聚合查询接口
  7. 搭建 Grafana Dashboard：调用量趋势、Token 消耗排行、成本趋势、延迟 P50/P99、错误率
  8. 实现告警规则引擎：成本突增 > 50%、P99 延迟 > 2s、错误率 > 5% → 飞书 Webhook 通知
- **面试加分点:**
  - OpenTelemetry 行业标准，无侵入接入（非自定义日志格式）
  - ClickHouse 列存数据库选型，展示大数据量处理意识
  - 成本可视化与优化建议（实际生产中 LLM 成本是核心痛点）
  - LLM-as-Judge 质量评估（自带评估体系，非仅关注性能指标）
  - Grafana 预配置 Dashboard 一键导入
- **参考资料:**
  - OpenTelemetry Python: opentelemetry.io/docs/languages/python/
  - ClickHouse: clickhouse.com
  - Langfuse (参考产品): github.com/langfuse/langfuse
  - Helicone (参考产品): github.com/Helicone/helicone

---

## 一、模板字段说明

每个模板统一使用以下结构：

| 字段 | 说明 |
|------|------|
| **项目名称格式** | `[技术/工具] + [业务场景]`，如 "Go + 用户积分服务" |
| **难度** | 入门 / 进阶 / 高级 |
| **简历描述示例** | 2-3条 bullet point，可直接放入简历的项目经历栏 |
| **最小可运行骨架** | 项目目录结构 + 核心文件清单，确保第一天就能跑起来 |
| **核心实现任务** | 5-8个有序任务，遵循 TDD（测试驱动开发）拆解 |
| **扩展方向** | 3-4个加分项，让项目在面试中更有竞争力 |

---

## 二、后端开发

### 模板 1：RESTful API 服务

- **项目名称:** Go/Java/Python + 通用 CRUD 管理后台
- **难度:** 入门
- **技术栈:** Go (Gin/Echo) 或 Java (Spring Boot) 或 Python (FastAPI) + MySQL + Redis + JWT
- **简历描述示例:**
  - 基于 Spring Boot 构建 RESTful API 服务，实现用户、订单、商品模块的完整 CRUD 及软删除
  - 集成 JWT 认证与 RBAC 权限控制，使用 Redis 缓存热点数据，QPS 提升 40%
  - 编写单元测试与集成测试，核心接口覆盖率达到 85%+
- **最小可运行骨架:**
  ```
  crud-service/
  ├── cmd/main.go (或 src/main/java/...)
  ├── internal/
  │   ├── handler/      # HTTP 层
  │   ├── service/      # 业务逻辑层
  │   ├── repository/   # 数据访问层
  │   ├── model/        # 数据模型
  │   └── middleware/   # 认证、日志中间件
  ├── config/           # 配置文件
  ├── migrations/       # 数据库迁移脚本
  ├── docker-compose.yml
  └── Makefile
  ```
- **核心实现任务:**
  1. 搭建项目骨架，数据库连接与自动迁移
  2. 实现 User 模块：注册、登录、JWT 签发与验证中间件
  3. 实现 RBAC：角色与权限表设计，中间件拦截校验
  4. 实现 Order 模块：创建订单、状态流转、列表分页查询
  5. 实现 Product 模块：CRUD + 库存扣减（事务）
  6. 集成 Redis 缓存：缓存商品详情，主动失效策略
  7. 编写集成测试：使用测试数据库，覆盖核心接口
  8. 编写 Dockerfile 与 docker-compose，一键启动全套环境
- **扩展方向:**
  - 添加 API 文档（Swagger/OpenAPI 自动生成）
  - 引入消息队列（RabbitMQ/Kafka）实现订单异步处理
  - 添加 Prometheus 指标暴露 + Grafana 面板
  - 实现接口限流（令牌桶/漏桶）

---

### 模板 2：分布式任务调度系统

- **项目名称:** Go/Python + 分布式任务调度平台
- **难度:** 进阶
- **技术栈:** Go + RabbitMQ/Kafka + Redis + PostgreSQL + Docker
- **简历描述示例:**
  - 设计并实现分布式任务调度系统，支持延迟任务、定时任务、优先级队列三种调度模式
  - 基于 RabbitMQ 死信队列实现延迟任务，Worker 节点支持水平扩展与心跳上报
  - 实现任务重试机制与幂等性保证，在 Worker 宕机场景下任务自动恢复
- **最小可运行骨架:**
  ```
  task-scheduler/
  ├── scheduler/         # 调度器：接收任务 → 入队
  ├── worker/            # 执行器：消费任务 → 执行 → 回调
  ├── common/
  │   ├── task.go        # 任务结构体定义
  │   ├── queue.go       # 队列抽象接口
  │   └── storage.go     # 存储抽象接口
  ├── web/               # 管理后台（可选）
  └── docker-compose.yml
  ```
- **核心实现任务:**
  1. 定义任务模型（ID、类型、参数、状态、重试次数、创建时间）
  2. 实现 RabbitMQ 连接管理与队列声明（普通队列 + 死信队列）
  3. 实现 Scheduler：接收 HTTP 任务提交，写入 DB 并入队
  4. 实现 Worker：消费消息，执行任务，更新 DB 状态
  5. 实现延迟任务：利用消息 TTL + 死信交换机
  6. 实现 Worker 心跳上报与故障检测（Redis 过期 key）
  7. 实现任务重试与幂等性（基于任务 ID + 状态机）
  8. 编写端到端测试：模拟 Worker 宕机，验证任务恢复
- **扩展方向:**
  - 支持 Cron 定时任务（集成 cron 表达式解析库）
  - 添加 Web 管理界面：任务列表、执行日志、重试按钮
  - 实现任务分片（Sharding）以支持大数据量处理
  - 接入 Prometheus + Grafana 监控队列积压与处理延迟

---

### 模板 3：实时数据处理管道

- **项目名称:** Python/Go + 实时日志分析与告警管道
- **难度:** 进阶
- **技术栈:** Python/Go + Kafka + ClickHouse + Redis + WebSocket
- **简历描述示例:**
  - 构建实时数据处理管道，从 Kafka 消费日志数据，经清洗聚合后写入 ClickHouse
  - 设计滑动窗口聚合算法，实时计算 QPS、错误率、P99 延迟等指标
  - 基于规则引擎实现异常检测，通过 WebSocket 向前端推送告警通知
- **最小可运行骨架:**
  ```
  realtime-pipeline/
  ├── producer/          # 模拟日志产生器
  ├── consumer/          # 数据清洗与聚合
  ├── alert-engine/      # 规则引擎 + 告警
  ├── api-server/        # 查询 API + WebSocket 推送
  ├── config/
  └── docker-compose.yml
  ```
- **核心实现任务:**
  1. 搭建 Kafka + ClickHouse 本地环境（docker-compose）
  2. 实现日志模拟生成器：按概率分布生成正常/异常日志
  3. 实现 Consumer：消费 → 解析 → 清洗 → 批量写入 ClickHouse
  4. 设计 ClickHouse 表结构（按时间分区 + 物化视图预聚合）
  5. 实现滑动窗口聚合：每分钟计算 QPS/错误率/P99
  6. 实现规则引擎：支持阈值规则（错误率 > 5%）和突增规则
  7. 实现 API Server：查询接口 + WebSocket 实时推送告警
  8. 编写集成测试：验证端到端延迟 < 5秒
- **扩展方向:**
  - 引入 Flink/Windowing 框架替代手动滑动窗口
  - 添加告警收敛（相同告警 5 分钟内不重复发送）
  - 实现数据回放功能，支持历史数据重新处理
  - 添加 Grafana 实时面板展示管道健康状态

---

### 模板 4：微服务电商核心链路

- **项目名称:** Go/Java + 微服务电商核心链路
- **难度:** 高级
- **技术栈:** Go (go-micro/go-zero) 或 Java (Spring Cloud) + Nacos + Sentinel + Seata + Gateway
- **简历描述示例:**
  - 设计并实现微服务电商核心链路，包含用户、商品、订单、库存、支付 5 个微服务
  - 基于 Seata AT 模式实现分布式事务，保证下单-扣库存-扣款的一致性
  - 使用 Sentinel 实现熔断降级与系统保护，压测下 QPS 达 2000+ 时系统稳定运行
- **最小可运行骨架:**
  ```
  ecommerce-micro/
  ├── gateway/           # API 网关（路由、限流、鉴权）
  ├── services/
  │   ├── user/          # 用户服务
  │   ├── product/       # 商品服务
  │   ├── order/         # 订单服务
  │   ├── inventory/     # 库存服务
  │   └── payment/       # 支付服务
  ├── common/            # 公共库（DTO、错误码、工具类）
  ├── config/            # 各服务配置
  └── docker-compose.yml
  ```
- **核心实现任务:**
  1. 搭建微服务脚手架：服务注册（Nacos）、配置中心、RPC 框架
  2. 实现 User Service：注册、登录、用户信息查询
  3. 实现 Product Service：商品 CRUD、分类、搜索
  4. 实现 Inventory Service：库存查询、预占、扣减、释放
  5. 实现 Order Service：创建订单、状态机流转
  6. 实现 Payment Service：模拟支付、回调处理
  7. 实现分布式事务：下单 → 扣库存 → 扣款（Seata 全局事务）
  8. 实现 Gateway：统一鉴权、限流、路由转发
- **扩展方向:**
  - 实现分布式链路追踪（Jaeger/SkyWalking）
  - 添加缓存策略（Redis 多级缓存 + 缓存击穿防护）
  - 实现消息驱动架构：用 Kafka 替换部分同步 RPC 调用
  - 编写压测脚本（wrk/k6），输出压测报告与优化记录

---

## 三、前端开发

### 模板 1：组件库

- **项目名称:** React/Vue + 通用 UI 组件库
- **难度:** 入门
- **技术栈:** React + TypeScript + Storybook 或 Vue 3 + TypeScript + VitePress
- **简历描述示例:**
  - 基于 React + TypeScript 开发企业级 UI 组件库，包含 Button、Input、Modal、Table、Select 等 8 个组件
  - 使用 Storybook 编写组件文档与交互示例，支持 Props 面板实时调试
  - 配置 Rollup 打包，支持 ESM/CJS/UMD 三种输出格式，发布至私有 NPM 仓库
- **最小可运行骨架:**
  ```
  ui-lib/
  ├── src/
  │   ├── components/
  │   │   ├── Button/
  │   │   │   ├── index.tsx
  │   │   │   ├── Button.test.tsx
  │   │   │   └── Button.stories.tsx
  │   │   ├── Input/
  │   │   ├── Modal/
  │   │   ├── Table/
  │   │   └── Select/
  │   ├── hooks/           # 通用 hooks
  │   └── index.ts         # 统一导出
  ├── .storybook/
  ├── rollup.config.js
  └── package.json
  ```
- **核心实现任务:**
  1. 初始化项目：TypeScript + React + Storybook + 测试框架（Vitest/Jest）
  2. 实现 Button 组件：variant（primary/secondary/danger）、size、loading、disabled 状态
  3. 实现 Input 组件：受控/非受控、prefix/suffix、clearable、校验状态
  4. 实现 Modal 组件：Portal 渲染、动画、遮罩层、Esc 关闭
  5. 实现 Table 组件：列定义、排序、分页、行选择
  6. 实现 Select 组件：单选/多选、搜索过滤、虚拟列表
  7. 编写每个组件对应的 Story 和单元测试
  8. 配置打包：Rollup → ESM + CJS + UMD，CSS 提取
- **扩展方向:**
  - 添加主题系统（CSS Variables + ThemeProvider）
  - 实现 Form 组件（表单校验 + 布局）
  - 添加无障碍支持（ARIA 属性 + 键盘导航）
  - 配置 CI 自动发布（GitHub Actions → npm publish）

---

### 模板 2：数据可视化大屏

- **项目名称:** React/Vue + 数据可视化大屏
- **难度:** 进阶
- **技术栈:** React/Vue + ECharts/D3.js + WebSocket + CSS Grid/Flex
- **简历描述示例:**
  - 开发数据可视化大屏，包含实时流量、用户分布地图、销售趋势等 6 个图表模块
  - 使用 ECharts 实现动态数据更新，WebSocket 推送实时数据，渲染帧率稳定在 30fps+
  - 采用 CSS Grid 响应式布局，适配 16:9 标准大屏分辨率，支持全屏自适应缩放
- **最小可运行骨架:**
  ```
  dashboard/
  ├── src/
  │   ├── components/
  │   │   ├── LineChart/
  │   │   ├── BarChart/
  │   │   ├── PieChart/
  │   │   ├── MapChart/
  │   │   ├── GaugeChart/
  │   │   └── DataPanel/
  │   ├── hooks/
  │   │   ├── useWebSocket.ts   # WebSocket 连接管理
  │   │   └── useResize.ts      # 屏幕自适应
  │   ├── mock/                  # 模拟数据服务
  │   └── App.tsx
  └── package.json
  ```
- **核心实现任务:**
  1. 搭建项目：Vite + React/Vue + TypeScript + ECharts
  2. 设计大屏布局：CSS Grid 定义 12 列网格，6 个图表区域
  3. 实现 Mock 数据服务：模拟实时数据推送（随机游走算法）
  4. 封装 ECharts 通用组件：统一处理 option、resize、主题切换
  5. 实现折线图（实时流量） + 柱状图（销售额） + 饼图（来源分布）
  6. 实现中国地图（用户分布） + 仪表盘（完成率） + 滚动表格（最新订单）
  7. 实现 WebSocket 连接：自动重连、心跳检测、数据更新
  8. 实现全屏自适应：监听 resize + transform scale 等比缩放
- **扩展方向:**
  - 添加深色/浅色主题切换
  - 实现数据下钻（点击图表区域进入详情）
  - 添加 3D 图表（Three.js + ECharts GL）
  - 实现大屏编排工具（拖拽配置图表布局）

---

### 模板 3：低代码表单引擎

- **项目名称:** React/Vue + 低代码表单设计器
- **难度:** 进阶
- **技术栈:** React/Vue + TypeScript + 拖拽库（dnd-kit/sortablejs）+ JSON Schema
- **简历描述示例:**
  - 开发低代码表单设计器，支持拖拽编排、实时预览、条件显隐，覆盖 12 种表单项类型
  - 基于 JSON Schema 定义表单结构，实现 Schema → 组件的双向转换引擎
  - 设计插件化校验框架，支持内置规则、正则校验与自定义校验函数
- **最小可运行骨架:**
  ```
  form-engine/
  ├── src/
  │   ├── designer/        # 设计器（画布 + 组件面板 + 属性面板）
  │   ├── renderer/        # 渲染器（Schema → 真实表单）
  │   ├── components/      # 表单项组件（Input、Select、DatePicker...）
  │   ├── schema/          # Schema 类型定义与转换逻辑
  │   ├── validators/      # 校验规则注册中心
  │   └── store/           # 状态管理（Schema + 选中项 + 拖拽状态）
  └── package.json
  ```
- **核心实现任务:**
  1. 定义 JSON Schema 结构：字段类型、标签、校验规则、布局、条件显隐
  2. 实现基础表单项组件库（至少 8 种：Input、Select、DatePicker、Switch、Radio、Checkbox、Upload、RichText）
  3. 实现 Renderer：递归解析 Schema 并渲染真实表单
  4. 实现 Designer——组件面板：拖拽源，分组展示所有表单项
  5. 实现 Designer——画布：拖拽放置、排序、选中、删除
  6. 实现 Designer——属性面板：根据选中组件动态展示可配置属性
  7. 实现条件显隐引擎：根据其他字段值控制显隐/只读
  8. 实现校验框架：必填、长度、正则、自定义函数，错误信息收集
- **扩展方向:**
  - 支持表单联动（省市区三级联动、A选项影响B的可选项）
  - 实现表单模板市场（预设模板 + 一键导入）
  - 添加表单填写数据收集与导出（CSV/JSON）
  - 支持移动端自适应布局（栅格布局 + 移动端预览）

---

### 模板 4：跨端应用

- **项目名称:** React/Taro/Electron + 跨端应用
- **难度:** 高级
- **技术栈:** Taro/uni-app（小程序+H5）或 Electron + React（桌面端）或 React Native（移动端）
- **简历描述示例:**
  - 基于 Taro 开发跨端电商应用，一套代码同时编译为微信小程序、H5 和支付宝小程序
  - 封装跨端适配层，统一处理各平台 API 差异（支付、登录、定位）
  - 实现虚拟列表优化长列表渲染，首屏加载时间从 3.2s 优化至 1.1s
- **最小可运行骨架:**
  ```
  cross-platform-app/
  ├── src/
  │   ├── pages/          # 页面
  │   ├── components/     # 通用组件
  │   ├── adapters/       # 平台适配层
  │   ├── services/       # API 服务
  │   └── store/          # 状态管理
  ├── config/             # 各平台配置
  └── package.json
  ```
- **核心实现任务:**
  1. 初始化 Taro 项目，配置微信小程序 + H5 双端编译
  2. 实现平台适配工具函数（判断当前平台、条件编译）
  3. 实现跨端路由系统（统一页面跳转 API）
  4. 实现跨端请求层（统一 request，适配各平台网络 API）
  5. 实现核心页面：首页（商品列表）、详情页、购物车、个人中心
  6. 实现跨端支付流程（微信支付 + 支付宝支付适配）
  7. 实现虚拟列表组件：仅渲染可视区域，支持快速滚动
  8. 性能优化：图片懒加载、代码分包、预加载关键页面
- **扩展方向:**
  - 扩展到更多平台（字节/百度小程序、快应用）
  - 实现离线缓存策略（Service Worker + LocalStorage）
  - 添加骨架屏组件与加载动画
  - 集成性能监控埋点（页面加载时间、JS 错误上报）

---

## 四、算法/机器学习

### 模板 1：推荐系统原型

- **项目名称:** Python + 电影/商品推荐系统
- **难度:** 入门
- **技术栈:** Python + NumPy/Pandas + Scikit-learn + Flask/FastAPI
- **简历描述示例:**
  - 基于 MovieLens 数据集构建混合推荐系统，融合协同过滤与基于内容的推荐
  - 实现 User-CF 和 Item-CF 两种协同过滤算法，使用余弦相似度计算近邻
  - 设计离线评估体系（Precision@K、Recall@K、NDCG），并通过 Flask API 提供在线推荐服务
- **最小可运行骨架:**
  ```
  recsys/
  ├── data/               # 数据集
  ├── src/
  │   ├── cf.py           # 协同过滤算法
  │   ├── content.py      # 基于内容的推荐
  │   ├── hybrid.py       # 混合策略
  │   ├── evaluate.py     # 离线评估
  │   └── api.py          # 推荐 API 服务
  ├── notebooks/          # EDA 与实验
  └── requirements.txt
  ```
- **核心实现任务:**
  1. 加载 MovieLens 数据集，完成 EDA（评分分布、稀疏度分析）
  2. 构建用户-物品评分矩阵（稀疏矩阵存储）
  3. 实现 User-CF：计算用户相似度 → 找到 Top-K 近邻 → 预测评分
  4. 实现 Item-CF：计算物品相似度 → 基于用户历史生成推荐
  5. 实现基于内容的推荐：TF-IDF 提取电影特征 → 相似度推荐
  6. 实现混合推荐：加权融合或切换策略
  7. 实现离线评估：准备测试集，计算 Precision@K、Recall@K、NDCG
  8. 实现 Flask API：/recommend/{user_id} 接口，返回 Top-10 推荐
- **扩展方向:**
  - 引入矩阵分解（SVD/NMF）替代内存协同过滤
  - 加入冷启动处理（新用户/新物品的推荐策略）
  - 实现实时更新机制（新评分即时影响推荐结果）
  - 添加 A/B 测试框架对比不同算法效果

---

### 模板 2：NLP 文本分类服务

- **项目名称:** Python + 中文文本分类微服务
- **难度:** 进阶
- **技术栈:** Python + PyTorch/Transformers + FastAPI + Docker
- **简历描述示例:**
  - 基于 BERT 微调中文新闻文本分类模型（THUCNews 数据集），10 分类准确率达 93%+
  - 使用 ONNX Runtime 对模型推理加速，单次预测延迟从 80ms 降至 15ms
  - 通过 FastAPI 部署为微服务，支持批量预测与异步推理，QPS 达 200+
- **最小可运行骨架:**
  ```
  nlp-service/
  ├── data/               # 数据集与预处理脚本
  ├── src/
  │   ├── train.py        # 训练脚本
  │   ├── model.py        # 模型定义
  │   ├── predict.py      # 推理逻辑
  │   ├── export_onnx.py  # ONNX 导出
  │   └── api.py          # FastAPI 服务
  ├── tests/
  ├── Dockerfile
  └── requirements.txt
  ```
- **核心实现任务:**
  1. 下载 THUCNews 数据集，完成文本预处理（分词、去停用词、长度统计）
  2. 加载预训练 BERT 模型（bert-base-chinese），添加分类头
  3. 实现训练循环：DataLoader、学习率调度（warmup+linear decay）、早停
  4. 实现评估：Accuracy、F1、混淆矩阵可视化
  5. 模型导出为 ONNX 格式，验证精度无损
  6. 实现 ONNX 推理管道：tokenize → encode → inference → decode
  7. 实现 FastAPI 服务：单条预测、批量预测接口
  8. 编写 Dockerfile，构建镜像并测试容器化部署
- **扩展方向:**
  - 支持多标签分类与层次分类
  - 实现模型热更新（不停服切换模型版本）
  - 添加文本数据增强（回译、同义词替换）
  - 集成 MLflow 进行实验追踪与模型版本管理

---

### 模板 3：CV 图像处理管道

- **项目名称:** Python + 目标检测与分割服务
- **难度:** 进阶
- **技术栈:** Python + PyTorch + YOLO/MMDetection + FastAPI + OpenCV
- **简历描述示例:**
  - 基于 YOLOv8 训练自定义目标检测模型（COCO 子集 5 类别），mAP@0.5 达 85%+
  - 实现图像预处理管道（缩放、归一化、数据增强），训练过程使用 Mosaic 增强
  - 部署为实时推理服务，单张图片推理延迟 < 50ms（GPU），支持视频流检测
- **最小可运行骨架:**
  ```
  cv-pipeline/
  ├── data/               # 数据集与标注
  ├── src/
  │   ├── dataset.py      # 数据集加载与增强
  │   ├── train.py        # 训练脚本
  │   ├── detect.py       # 检测推理
  │   ├── preprocess.py   # 预处理管道
  │   └── api.py          # 推理 API
  ├── notebooks/          # 数据探索与可视化
  └── requirements.txt
  ```
- **核心实现任务:**
  1. 准备数据集：标注 5 个类别的图片（各 200+ 张），转换为 YOLO 格式
  2. 实现数据增强管道：Mosaic、随机翻转、HSV 变换
  3. 配置 YOLOv8 训练参数并启动训练
  4. 实现评估脚本：mAP 计算、PR 曲线绘制、误检分析
  5. 实现单张图片推理：预处理 → 模型推理 → NMS → 结果解析
  6. 实现视频流推理：OpenCV 读取 → 逐帧检测 → 实时绘制框
  7. 实现 FastAPI 服务：上传图片 → 返回检测结果（JSON + 标注图片）
  8. 优化推理速度：FP16 推理、批量处理、TensorRT 转换
- **扩展方向:**
  - 添加实例分割（Mask R-CNN）替代纯检测
  - 实现模型蒸馏（大模型 → 小模型）以降低推理成本
  - 添加目标跟踪（DeepSORT）实现视频中的目标 ID 追踪
  - 实现边缘端部署（ONNX → NCNN/TNN 移动端推理）

---

## 五、运维/DevOps/SRE

### 模板 1：CI/CD 管道搭建

- **项目名称:** GitHub Actions/Jenkins + 全自动 CI/CD 管道
- **难度:** 入门
- **技术栈:** GitHub Actions 或 Jenkins + Docker + 自动化测试 + 通知
- **简历描述示例:**
  - 为微服务项目搭建 GitHub Actions CI/CD 管道，实现代码推送 → 测试 → 构建 → 部署全自动化
  - 流水线包含 lint、单元测试、集成测试、Docker 镜像构建、自动部署至测试环境 5 个阶段
  - 配置分支保护规则与自动化通知（飞书/钉钉 Webhook），部署失败时自动回滚
- **最小可运行骨架:**
  ```
  cicd-pipeline/
  ├── .github/workflows/
  │   ├── ci.yml           # CI：lint + test
  │   └── cd.yml           # CD：build + deploy
  ├── scripts/
  │   ├── deploy.sh        # 部署脚本
  │   └── rollback.sh      # 回滚脚本
  ├── Dockerfile
  ├── docker-compose.yml
  └── README.md            # 管道使用说明
  ```
- **核心实现任务:**
  1. 创建 CI 工作流：代码 checkout → 安装依赖 → lint（ESLint/pylint）→ 单元测试
  2. 添加测试覆盖率检查：低于阈值（如 70%）则流水线失败
  3. 创建 CD 工作流：构建 Docker 镜像 → 推送到私有仓库
  4. 实现自动部署：SSH 连接测试服务器 → 拉取新镜像 → docker-compose up
  5. 实现部署健康检查：等待服务启动 → 调用 /health 端点验证
  6. 实现失败自动回滚：记录上一次成功版本 → 部署失败时自动恢复
  7. 配置通知：企业微信/钉钉/飞书机器人 Webhook 推送构建结果
- **扩展方向:**
  - 添加自动化安全扫描（Trivy 镜像漏洞扫描）
  - 实现蓝绿部署或金丝雀发布
  - 添加性能测试阶段（k6/JMeter 压测）
  - 实现多环境管理（dev/staging/prod 自动推进）

---

### 模板 2：Kubernetes 集群监控

- **项目名称:** Prometheus + Grafana + Kubernetes 监控体系
- **难度:** 进阶
- **技术栈:** Kubernetes + Prometheus + Grafana + Alertmanager + Loki
- **简历描述示例:**
  - 在 Kubernetes 集群上搭建 Prometheus + Grafana 全栈监控体系，覆盖集群、节点、Pod、应用四级指标
  - 使用 Prometheus Operator 管理监控资源，ServiceMonitor 自动发现应用指标
  - 配置 Alertmanager 告警规则 20+ 条（CPU > 80%、内存 > 85%、Pod 重启频繁等），接入企业微信通知
- **最小可运行骨架:**
  ```
  k8s-monitoring/
  ├── manifests/
  │   ├── prometheus/       # Prometheus 部署配置
  │   ├── grafana/          # Grafana 部署 + Dashboard JSON
  │   ├── alertmanager/     # Alertmanager 配置
  │   ├── exporters/        # Node Exporter、kube-state-metrics
  │   └── loki/             # 日志收集
  ├── dashboards/           # Grafana Dashboard JSON 导出
  ├── rules/                # Prometheus 告警规则
  └── README.md
  ```
- **核心实现任务:**
  1. 在 K8s 集群中部署 Prometheus Operator（Helm chart）
  2. 部署 Node Exporter（DaemonSet）+ kube-state-metrics 收集集群指标
  3. 配置 ServiceMonitor 自动发现应用 Pod 的 /metrics 端点
  4. 编写 Prometheus 告警规则：节点级（CPU/内存/磁盘）+ 应用级（错误率/延迟）
  5. 部署 Grafana 并导入 Dashboard：集群概览、节点详情、应用性能
  6. 部署 Alertmanager：配置告警分组、抑制、静默规则
  7. 部署 Loki + Promtail 收集容器日志
  8. 配置通知管道：Alertmanager → 企业微信 Webhook
- **扩展方向:**
  - 实现水平自动扩缩容（HPA）基于自定义指标（Prometheus Adapter）
  - 添加分布式链路追踪（Jaeger + OpenTelemetry）
  - 实现告警升级机制（5min 未确认 → 升级至电话通知）
  - 编写混沌工程测试（Chaos Mesh）验证监控告警有效性

---

### 模板 3：基础设施即代码

- **项目名称:** Terraform + Ansible + 云基础设施自动化
- **难度:** 进阶
- **技术栈:** Terraform + Ansible + AWS/阿里云 + Docker
- **简历描述示例:**
  - 使用 Terraform 编写 AWS 基础设施代码，管理 VPC、EC2、RDS、S3 等 15+ 资源
  - 结合 Ansible 实现服务器配置管理（用户、安全、Docker、应用部署），Playbook 可复用
  - 实现 Terraform 状态远程存储与锁机制（S3 + DynamoDB），支持团队协作
- **最小可运行骨架:**
  ```
  iac-project/
  ├── terraform/
  │   ├── main.tf           # 入口
  │   ├── vpc.tf            # 网络
  │   ├── ec2.tf            # 计算
  │   ├── rds.tf            # 数据库
  │   ├── variables.tf      # 变量定义
  │   └── outputs.tf        # 输出
  ├── ansible/
  │   ├── playbooks/
  │   │   ├── common.yml    # 基础配置
  │   │   ├── docker.yml    # Docker 安装
  │   │   └── app.yml       # 应用部署
  │   ├── roles/            # Ansible Roles
  │   └── inventory/        # 动态 Inventory
  └── README.md
  ```
- **核心实现任务:**
  1. 编写 Terraform VPC 模块：子网、路由表、NAT 网关、安全组
  2. 编写 Terraform EC2 模块：启动模板、Auto Scaling Group、负载均衡器
  3. 编写 Terraform RDS 模块：多可用区、自动备份、参数组
  4. 配置远程状态存储（S3 + DynamoDB 锁）
  5. 编写 Ansible common Playbook：用户管理、SSH 加固、基础软件
  6. 编写 Ansible Docker Playbook：安装 Docker + Docker Compose
  7. 编写 Ansible App Playbook：拉取镜像、启动服务、健康检查
  8. 集成 Terraform output → Ansible 动态 Inventory
- **扩展方向:**
  - 添加 Terraform Module Registry 实现模块复用
  - 集成 Atlantis 实现 Terraform PR 自动 plan/apply
  - 实现多环境管理（dev/staging/prod 用不同 tfvars）
  - 添加 Terratest 自动化测试验证基础设施正确性

---

## 六、安全

### 模板 1：Web 漏洞扫描器

- **项目名称:** Python + 轻量级 Web 漏洞扫描器
- **难度:** 入门
- **技术栈:** Python + Requests + BeautifulSoup + SQLite
- **简历描述示例:**
  - 开发轻量级 Web 漏洞扫描器，支持 XSS、SQL 注入、命令注入、目录遍历 4 类漏洞检测
  - 基于 Payload 字典与响应特征匹配实现漏洞检测，支持 GET/POST 参数自动提取与注入
  - 设计插件化扫描架构，新漏洞类型可通过注册插件扩展
- **最小可运行骨架:**
  ```
  web-scanner/
  ├── scanner/
  │   ├── core.py         # 扫描引擎
  │   ├── crawler.py      # 爬虫：页面爬取与参数提取
  │   ├── plugins/
  │   │   ├── xss.py      # XSS 检测
  │   │   ├── sqli.py     # SQL 注入检测
  │   │   ├── cmdi.py     # 命令注入检测
  │   │   └── traversal.py # 目录遍历检测
  │   ├── payloads/       # Payload 字典
  │   └── reporter.py     # 报告生成
  ├── tests/
  │   └── test_app/       # 故意有漏洞的测试应用
  └── requirements.txt
  ```
- **核心实现任务:**
  1. 搭建脆弱测试应用（Flask 应用，包含已知漏洞端点）
  2. 实现爬虫模块：请求页面 → 解析表单与链接 → 提取参数
  3. 实现插件基类与注册机制（统一接口：check(url, params)）
  4. 实现 XSS 检测插件：注入 Payload → 检查反射
  5. 实现 SQL 注入检测插件：注入 Payload → 检查数据库错误特征
  6. 实现命令注入与目录遍历检测插件
  7. 实现扫描报告生成（HTML/JSON 格式，包含漏洞详情与修复建议）
  8. 对测试应用运行完整扫描，验证所有漏洞被检出
- **扩展方向:**
  - 添加登录态支持（Cookie/Session 保持）
  - 实现并发扫描（线程池/异步）提升扫描速度
  - 添加 WAF 绕过 Payload（编码、大小写变换）
  - 集成 Nuclei 模板兼容（读取社区漏洞模板）

---

### 模板 2：CTF 解题工具集

- **项目名称:** Python + CTF 常用工具集
- **难度:** 进阶
- **技术栈:** Python + Crypto 库 + 自定义脚本 + CLI 界面
- **简历描述示例:**
  - 开发 CTF 解题工具集，覆盖 Crypto（古典密码、RSA 攻击）、Misc（隐写分析）、Reverse（基础反编译辅助）三类
  - 实现 RSA 常见攻击工具：分解 N、低加密指数、共模攻击、维纳攻击
  - 实现图片隐写分析工具：LSB 提取、文件尾附加检测、StegSolve 同类功能
- **最小可运行骨架:**
  ```
  ctf-toolkit/
  ├── ctfkit/
  │   ├── cli.py            # 命令行入口
  │   ├── crypto/
  │   │   ├── classical.py  # 古典密码（凯撒、维吉尼亚、Base64 家族）
  │   │   └── rsa.py        # RSA 攻击
  │   ├── misc/
  │   │   ├── stego.py      # 隐写分析
  │   │   └── forensics.py  # 取证工具
  │   └── reverse/
  │       └── helper.py     # 辅助脚本
  ├── tests/
  └── requirements.txt
  ```
- **核心实现任务:**
  1. 搭建 CLI 框架（Click/argparse），子命令分组
  2. 实现古典密码模块：凯撒（自动破解）、维吉尼亚（卡西斯基测试）、Base64/32/16 家族编解码
  3. 实现 RSA——因数分解：YAFU 集成、Fermat 分解、Pollard p-1
  4. 实现 RSA——低加密指数攻击（广播攻击 + Coppersmith）
  5. 实现 RSA——维纳攻击（连分数）
  6. 实现隐写分析：PNG LSB 提取、图片元数据解析（Exif）
  7. 实现文件取证：文件头识别、strings 增强、binwalk 简化版
  8. 编写单元测试（用已知 CTF 题目验证工具正确性）
- **扩展方向:**
  - 添加 Web 题辅助工具（JWT 破解、SQLi 盲注脚本模板）
  - 实现自动化解题流水线（输入题目文件 → 尝试多个工具 → 输出结果）
  - 添加流量分析模块（PCAP 解析、协议还原）
  - 集成在线工具 API（Factordb、CyberChef）

---

### 模板 3：日志安全分析平台

- **项目名称:** Python/Go + 日志安全分析平台
- **难度:** 进阶
- **技术栈:** Python/Go + Elasticsearch/Kibana + 规则引擎 + Web 界面
- **简历描述示例:**
  - 构建日志安全分析平台，支持 Web 服务器日志、系统日志、防火墙日志的集中采集与分析
  - 实现基于规则的实时告警引擎，内置 OWASP Top 10 相关的 30+ 条检测规则
  - 使用 Elasticsearch 聚合分析实现攻击来源 IP 统计、攻击趋势可视化
- **最小可运行骨架:**
  ```
  log-security/
  ├── collector/          # 日志采集器（Filebeat 替代）
  ├── parser/             # 日志解析器（多格式支持）
  ├── engine/
  │   ├── rules/          # 检测规则（YAML 定义）
  │   └── matcher.py      # 规则匹配引擎
  ├── web/                # 管理界面
  └── docker-compose.yml
  ```
- **核心实现任务:**
  1. 搭建 Elasticsearch + Kibana 环境（docker-compose）
  2. 实现日志采集器：监听文件变化 → 发送至 ES
  3. 实现日志解析器：支持 Apache/Nginx、Syslog、IPTables 三种格式
  4. 设计检测规则 DSL（YAML 格式）：条件表达式 + 阈值 + 时间窗口
  5. 实现规则匹配引擎：解析规则 → 匹配日志 → 触发告警
  6. 编写内置规则：SQL 注入探测、XSS 探测、暴力破解检测、端口扫描检测
  7. 实现告警聚合：相同来源 5 分钟内合并为一条告警
  8. 实现简单 Web 界面：告警列表、趋势图（Kibana 嵌入）、规则管理
- **扩展方向:**
  - 引入机器学习异常检测（Isolation Forest 检测异常访问模式）
  - 添加威胁情报集成（对接 AbuseIPDB 查询 IP 信誉）
  - 实现 SOAR 自动响应（检测到攻击 → 自动封禁 IP）
  - 添加用户行为分析（UEBA）检测内部威胁

---

## 七、产品经理

### 模板 1：完整 PRD 文档

- **项目名称:** 某业务场景 + 产品需求文档（PRD）
- **难度:** 入门
- **核心交付物:** 一份 15-30 页的结构化 PRD 文档（可放入作品集）
- **简历描述示例:**
  - 独立完成「XX 功能/产品」的完整 PRD 文档，包含市场分析、用户画像、功能规格与验收标准
  - 编写 15 个用户故事，使用用例图 + 流程图描述核心业务流程
  - 定义 6 个核心指标（OEC），设计数据埋点方案与产品上线后评估计划
- **PRD 建议结构:**
  ```
  prd-document/
  ├── 01-背景与目标.md        # 业务背景、问题定义、产品目标
  ├── 02-市场分析.md          # 竞品分析、市场机会、差异化定位
  ├── 03-用户画像.md          # 用户画像 × 3、用户场景、用户旅程地图
  ├── 04-功能规格.md          # 功能列表、优先级（MoSCoW）、详细用例
  ├── 05-用户故事.md          # 15+ 用户故事（作为…我想要…以便…）
  ├── 06-流程图.md            # 核心业务流程图（Draw.io/Figma）
  ├── 07-数据指标.md          # OEC/北极星指标、埋点方案、AB 实验设计
  ├── 08-验收标准.md          # 功能验收条件、边界条件、异常场景
  └── 09-原型链接.md          # 低保真/高保真原型链接
  ```
- **核心实现任务:**
  1. 选定业务场景与产品方向，明确要解决的具体问题
  2. 调研至少 3 款竞品，输出竞品功能对比矩阵
  3. 创建 2-3 个用户画像，描述其特征、目标与痛点
  4. 绘制用户旅程地图（As-Is → To-Be）
  5. 编写功能列表与优先级排序（MoSCoW 方法）
  6. 编写 15+ 用户故事，标注验收标准（Acceptance Criteria）
  7. 绘制核心流程图与用例图
  8. 定义数据指标体系与埋点方案
- **扩展方向:**
  - 使用 Figma/Axure 制作高保真交互原型
  - 组织 3-5 人用户访谈并输出可用性测试报告
  - 编写产品上线后复盘报告（含数据分析与改进建议）
  - 制作产品宣讲 PPT（面向"投资人"或"内部评审"）

---

### 模板 2：竞品分析报告

- **项目名称:** 某赛道 + 竞品深度分析报告
- **难度:** 进阶
- **核心交付物:** 一份 20-40 页的多维度竞品分析报告
- **简历描述示例:**
  - 对「XX 赛道」5 款核心竞品进行深度分析，覆盖功能、体验、商业模式、用户口碑四个维度
  - 构建竞品功能对比矩阵（50+ 功能点），识别差异化机会与空白市场
  - 输出 SWOT 分析与产品策略建议，被采纳的 3 条建议已进入产品路线图
- **建议结构:**
  ```
  competitive-analysis/
  ├── 01-行业概述.md          # 赛道规模、增长趋势、关键玩家
  ├── 02-竞品概览.md          # 5 款竞品基本信息与定位
  ├── 03-功能对比矩阵.md      # 功能点覆盖度矩阵
  ├── 04-用户体验对比.md      # 核心流程体验走查（截图 + 分析）
  ├── 05-商业模式对比.md      # 定价、付费转化、收入模型
  ├── 06-用户口碑分析.md      # App Store/应用商店评分、用户评价关键词
  ├── 07-SWOT分析.md          # 每款竞品 SWOT
  └── 08-策略建议.md          # 差异化机会、产品建议、风险提示
  ```
- **核心实现任务:**
  1. 确定分析赛道与竞品名单（3-5 款）
  2. 收集竞品基础信息（公司、融资、团队、用户量）
  3. 逐产品走查核心流程，截图记录关键页面
  4. 构建功能对比矩阵（至少覆盖 30 个功能维度）
  5. 分析各竞品商业模式（定价策略、付费转化路径）
  6. 收集并分析用户评价（语义分析关键词）
  7. 输出 SWOT 分析表格
  8. 提炼差异化策略建议（至少 3 条可行建议）
- **扩展方向:**
  - 添加定量数据（SimilarWeb/七麦数据 估算的 DAU/下载量）
  - 制作竞品动态追踪表（飞书多维表格，持续更新）
  - 使用波特五力模型或价值曲线进行更深入的行业分析
  - 输出一份向高管汇报用的 5 页 PPT 版本

---

### 模板 3：A/B 测试方案设计

- **项目名称:** 某功能模块 + A/B 测试设计与分析
- **难度:** 进阶
- **核心交付物:** A/B 测试设计方案 + 模拟数据分析 + 结论报告
- **简历描述示例:**
  - 为「XX 功能改版」设计 A/B 测试方案，包含假设设定、样本量计算、分流策略与评估指标
  - 基于历史数据计算所需样本量（显著性 95%、统计功效 80%），预计实验周期 14 天
  - 使用 Python 模拟实验数据并完成统计分析（T 检验 + 置信区间），输出实验结论与上线建议
- **建议结构:**
  ```
  ab-test-design/
  ├── 01-实验背景与假设.md    # 业务背景、核心假设（H0/H1）
  ├── 02-实验设计.md          # 变量定义、分流策略、样本量计算
  ├── 03-指标体系.md          # 主要指标、次要指标、护栏指标
  ├── 04-数据模拟.py          # Python 脚本：模拟 A/B 组数据
  ├── 05-统计分析.md          # 检验方法、P 值、置信区间、效应量
  └── 06-实验结论.md          # 结论、建议、潜在风险
  ```
- **核心实现任务:**
  1. 选定实验场景，明确对照组/实验组的差异（单变量）
  2. 编写实验假设（H0: 无差异 / H1: 实验组优于对照组）
  3. 基于历史数据计算所需最小样本量（power analysis）
  4. 设计分流策略（用户 ID Hash → 分组，确保随机性）
  5. 定义指标体系：主要指标（如转化率）、次要指标、护栏指标
  6. 编写 Python 脚本模拟实验数据（正态分布 + 效应量）
  7. 执行统计分析：正态性检验 → T 检验/Mann-Whitney → 置信区间
  8. 输出实验结论与业务建议
- **扩展方向:**
  - 实现多臂老虎机（MAB）实验与固定样本量 A/B 的对比
  - 添加 AA 测试验证分流系统的均匀性
  - 制作 A/B 测试计算器工具（输入参数 → 自动计算样本量）
  - 设计一个实验平台的后端数据模型（如何存实验配置、分流结果、指标）

---

## 八、UI/UX 设计

### 模板 1：设计系统

- **项目名称:** 某产品/场景 + 设计系统（Design System）
- **难度:** 入门
- **核心交付物:** Figma 设计文件 + 组件规范文档 + 前端 Storybook（可选）
- **简历描述示例:**
  - 独立构建一套设计系统，包含颜色、字体、间距、图标及 8 个核心组件（Button、Input、Card、Modal、Table、Tabs、Toast、Avatar）
  - 在 Figma 中创建组件变体（Variants）与自动布局（Auto Layout），支持多状态切换
  - 编写组件使用规范文档，包含交互状态、尺寸、使用场景与 Do's/Don'ts
- **最小可交付结构:**
  ```
  design-system/
  ├── figma-file.fig         # Figma 设计文件
  ├── spec-docs/
  │   ├── 01-colors.md       # 色彩系统（主色、辅助色、语义色）
  │   ├── 02-typography.md   # 字体层级（H1-H6、Body、Caption）
  │   ├── 03-spacing.md      # 间距系统（4px 基准网格）
  │   ├── 04-icons.md        # 图标规范（线宽、尺寸、网格）
  │   └── 05-components/     # 各组件规范
  │       ├── button.md
  │       ├── input.md
  │       └── ...
  └── README.md              # 设计系统概述与使用指南
  ```
- **核心实现任务:**
  1. 定义设计 Token：颜色（Primary/Secondary/Neutral/Semantic）、字体层级、间距梯度
  2. 创建 Figma Color Styles 与 Text Styles 库
  3. 设计图标系统（至少 20 个常用图标，24×24 网格）
  4. 设计 Button 组件：3 种 Variant × 4 种 Size × 5 种 State
  5. 设计 Input、Card、Modal 组件（各含变体与状态）
  6. 设计 Table、Tabs、Toast、Avatar 组件
  7. 编写每个组件的规范文档（含使用场景、代码片段、Do's/Don'ts）
  8. 制作设计系统概览页面（Figma 单页展示所有组件）
- **扩展方向:**
  - 前端开发实现部分组件（React/Vue），验证设计与代码一致性
  - 添加深色模式（Dark Mode）的色彩系统
  - 实现 Figma Token 导出插件（JSON → CSS Variables）
  - 添加无障碍规范（对比度检查、焦点环、ARIA 标注）

---

### 模板 2：用户研究 + 交互重设计

- **项目名称:** 某产品 + 用户体验研究与交互重设计
- **难度:** 进阶
- **核心交付物:** 用户研究报告 + 交互重设计方案 + 可用性测试报告
- **简历描述示例:**
  - 对「XX 产品」核心流程进行用户体验研究，招募 8 名用户完成可用性测试与深度访谈
  - 通过任务完成率、操作时间、SUS 量表定量评估当前体验，识别 12 个可用性问题
  - 输出交互重设计方案（含线框图与高保真原型），二次测试 SUS 评分从 62 提升至 78
- **建议结构:**
  ```
  ux-redesign/
  ├── 01-研究计划.md          # 研究目标、方法、招募标准、访谈提纲
  ├── 02-现状评估.md          # 启发式评估（Nielsen 10 原则）走查结果
  ├── 03-用户测试报告.md      # 任务完成率、操作时间、SUS 评分、关键发现
  ├── 04-用户旅程图.md        # As-Is 用户旅程（标注痛点与情绪曲线）
  ├── 05-问题优先级矩阵.md    # 影响度 × 严重度 矩阵
  ├── 06-重设计方案.md        # 线框图（低保真 → 高保真）+ 设计说明
  └── 07-验证测试报告.md      # 二次可用性测试结果对比
  ```
- **核心实现任务:**
  1. 选取一个产品的核心流程（如电商下单、App 注册、内容发布）
  2. 编写用户研究计划：目标、方法、招募标准（3-5 人即可）
  3. 执行启发式评估：依 Nielsen 10 原则逐页走查并记录问题
  4. 设计可用性测试任务（5-8 个核心任务）与访谈提纲
  5. 执行用户测试（可远程/面对面），记录操作过程与反馈
  6. 分析测试数据：计算任务完成率、时间、SUS 评分
  7. 输出重设计线框图（Figma）+ 交互说明
  8. 制作 Before/After 对比页面与设计总结
- **扩展方向:**
  - 使用眼动追踪或热力图工具加强分析说服力
  - 执行卡片分类法（Card Sorting）优化信息架构
  - 制作交互原型（Figma Prototype/Principle）模拟真实操作
  - 编写设计系统级别的交互规范（动效时长、缓动曲线、手势）

---

### 模板 3：多端适配设计规范

- **项目名称:** 某产品 + 多端适配设计规范
- **难度:** 进阶
- **核心交付物:** Responsive Web 规范 + 移动端 Native 适配指南
- **简历描述示例:**
  - 制定某产品 PC Web、iPad、Mobile Web、iOS、Android 五端设计适配规范
  - 定义断点策略（320/768/1024/1440）与栅格系统，确保核心流程在多端体验一致
  - 编写移动端触控交互规范（最小点击区域 44pt、手势冲突规避、横屏适配）
- **建议结构:**
  ```
  multi-platform-design/
  ├── 01-断点与栅格系统.md    # 响应式断点、栅格列数、间距定义
  ├── 02-PC-Web规范.md       # 桌面端布局、导航、弹窗规范
  ├── 03-Mobile-Web规范.md   # 移动端 H5 布局、手势、键盘适配
  ├── 04-iOS适配规范.md      # iOS 安全区域、导航栏、HIG 对齐
  ├── 05-Android适配规范.md  # Material Design 对齐、返回手势、 fragmentation
  ├── 06-组件映射表.md       # 同一组件在 5 端的形态对照
  └── 07-典型页面适配方案.md  # 首页、详情页、列表页的多端布局方案
  ```
- **核心实现任务:**
  1. 定义响应式断点与栅格系统（参考 Bootstrap/Tailwind 改进）
  2. 设计 PC Web 端核心页面布局（导航方式、内容区宽度、侧边栏）
  3. 设计 Mobile Web 端适配方案（底部导航、抽屉菜单、触控友好）
  4. 设计 iOS 端适配要点（Safe Area、导航栏样式、HIG 组件选用）
  5. 设计 Android 端适配要点（Material Design 组件、返回手势、状态栏）
  6. 制作组件映射表（同一功能在各端的 UI 形态对照）
  7. 选取 3 个典型页面输出多端布局对比图
  8. 编写多端适配检查清单（给设计师和开发使用的自查表）
- **扩展方向:**
  - 添加平板端（iPad/Android Tablet）的布局策略
  - 制作 Figma 多端组件库（同一组件支持切换端预览）
  - 编写可折叠屏设备的适配指南
  - 添加国际化/本地化设计规范（RTL 语言、多语言文案长度适配）

---

## 九、数据分析

### 模板 1：业务指标看板

- **项目名称:** SQL + BI 工具 + 业务指标看板
- **难度:** 入门
- **技术栈:** SQL + Metabase/Superset/Tableau + Python（数据预处理）
- **简历描述示例:**
  - 基于业务数据库设计并搭建多维度业务指标看板，覆盖用户增长、活跃、留存、转化 4 大模块
  - 编写 20+ 条 SQL 查询构建核心指标（DAU/MAU、留存率、LTV、转化漏斗）
  - 使用 Metabase 搭建可视化看板，设置每日自动刷新与关键指标预警
- **最小可运行骨架:**
  ```
  business-dashboard/
  ├── data/
  │   └── generate_mock.py   # 模拟业务数据生成
  ├── sql/
  │   ├── user_growth.sql    # 用户增长指标
  │   ├── retention.sql      # 留存分析
  │   ├── funnel.sql         # 转化漏斗
  │   └── revenue.sql        # 收入/LTV
  ├── dashboards/
  │   └── dashboard.json     # Metabase/Superset 导出
  └── README.md
  ```
- **核心实现任务:**
  1. 使用 Python 生成模拟业务数据（用户表、订单表、访问日志表）
  2. 将数据导入 PostgreSQL/MySQL
  3. 编写用户增长 SQL：日新增、日活（DAU）、周活（WAU）、月活（MAU）
  4. 编写留存 SQL：次日/7日/30日留存率（Cohort 分析）
  5. 编写转化漏斗 SQL：访问→注册→下单→支付 各环节转化率
  6. 编写收入 SQL：ARPU、ARPPU、LTV 估算
  7. 搭建 Metabase/Superset 连接数据库
  8. 创建可视化图表并组装为综合看板
- **扩展方向:**
  - 添加 RFM 用户分层模型
  - 实现预警机制（指标异常时自动发送通知）
  - 添加同比/环比自动计算与展示
  - 实现看板自动化：Python 脚本定期刷新数据并导出 PDF 报告

---

### 模板 2：用户行为分析报告

- **项目名称:** Python + 用户行为深度分析
- **难度:** 进阶
- **技术栈:** Python + Pandas/NumPy + Matplotlib/Seaborn + Scipy
- **简历描述示例:**
  - 对 10 万条用户行为日志进行深度分析，包含行为路径分析、用户分群与流失预测
  - 使用桑基图可视化用户行为路径，识别出高转化路径与高流失节点
  - 基于 K-Means 将用户分为 4 个群体，针对高价值低活跃群体提出 3 条运营策略
- **最小可运行骨架:**
  ```
  user-behavior-analysis/
  ├── data/
  │   └── generate_logs.py    # 模拟行为日志
  ├── notebooks/
  │   ├── 01-eda.ipynb        # 探索性分析
  │   ├── 02-path_analysis.ipynb  # 行为路径分析
  │   ├── 03-segmentation.ipynb   # 用户分群
  │   └── 04-churn_analysis.ipynb # 流失分析
  ├── src/
  │   ├── preprocessing.py    # 数据清洗
  │   ├── path_analysis.py    # 路径分析
  │   └── clustering.py       # 聚类
  └── report/
      └── analysis_report.md  # 综合分析报告
  ```
- **核心实现任务:**
  1. 编写行为日志模拟生成器（包含页面访问、点击、转化事件）
  2. 数据清洗：去重、异常值处理、时间窗口切分
  3. 探索性分析：用户量趋势、活跃时段分布、设备/渠道分布
  4. 行为路径分析：构建用户会话 → 提取路径序列 → 桑基图可视化
  5. 漏斗分析：关键转化路径的逐层流失率
  6. 用户分群：特征工程 → K-Means 聚类 → 轮廓系数评估 → 群体画像描述
  7. 流失分析：定义流失 → 特征对比（留存 vs 流失用户）→ 相关性分析
  8. 编写综合分析报告：关键发现 + 数据支撑 + 业务建议
- **扩展方向:**
  - 添加 RFM 模型做用户价值分层
  - 实现用户生命周期分析（Cohort × LT）
  - 使用 Markov Chain 预测用户下一步行为概率
  - 搭建自动化用户分群 Pipeline（定时运行并输出分群结果）

---

### 模板 3：预测模型

- **项目名称:** Python + 时间序列预测 / 分类预测模型
- **难度:** 进阶
- **技术栈:** Python + Scikit-learn/Prophet + Statsmodels + FastAPI
- **简历描述示例:**
  - 构建某业务指标（销售额/用户量）的预测模型，对比 ARIMA、Prophet 与 LSTM 三种方法
  - 使用 Prophet 模型实现 30 天预测，MAPE 控制在 12% 以内
  - 通过 FastAPI 将模型部署为预测服务，支持手动输入参数与自动定时预测
- **最小可运行骨架:**
  ```
  prediction-model/
  ├── data/
  ├── notebooks/
  │   ├── 01-eda.ipynb          # 时间序列探索
  │   ├── 02-arima.ipynb        # ARIMA 建模
  │   ├── 03-prophet.ipynb      # Prophet 建模
  │   └── 04-model_compare.ipynb # 模型对比
  ├── src/
  │   ├── train.py              # 训练脚本
  │   ├── predict.py            # 推理
  │   └── api.py                # API 服务
  └── requirements.txt
  ```
- **核心实现任务:**
  1. 获取或生成时间序列数据（建议使用真实公开数据：股价、气温、零售额）
  2. 时间序列 EDA：趋势、季节性、周期性、异常值检测
  3. 实现 ARIMA 模型：平稳性检验 → 差分 → ACF/PACF 定阶 → 拟合
  4. 实现 Prophet 模型：调参（季节性、变点、假期效应）
  5. 模型对比：MAE、MAPE、RMSE 评估，残差分析
  6. 实现滚动预测：每次预测未来 7 天，每天更新
  7. 实现 FastAPI 服务：/predict?days=30 返回预测值与置信区间
  8. 编写模型监控方案（数据漂移检测、预测误差告警）
- **扩展方向:**
  - 实现 LSTM/Transformer 深度学习预测模型
  - 添加外部变量（天气、促销、节假日特征）
  - 实现自动模型选择（AutoML：自动对比多种模型并选最优）
  - 构建预测结果的可视化 Dashboard

---

## 十、游戏方向

### 模板 1（策划）：游戏系统设计文档

- **项目名称:** 某游戏类型 + 核心系统设计文档
- **难度:** 入门
- **核心交付物:** 系统设计文档 + 数值表 + 流程图
- **简历描述示例:**
  - 完成一款 RPG 游戏的「战斗系统」完整设计文档，包含伤害公式、属性成长、技能机制
  - 使用 Excel 构建数值模型，模拟 1-60 级属性成长曲线与战斗平衡性（DPS 误差 < 5%）
  - 绘制系统流程图（Draw.io），清晰表达战斗流程、状态机与边缘情况处理
- **建议结构:**
  ```
  game-design-doc/
  ├── 01-系统概述.md          # 系统定位、设计目标、参考游戏
  ├── 02-核心机制.md          # 核心玩法循环、规则详解
  ├── 03-数值设计.xlsx        # 属性公式、成长曲线、平衡性模拟
  ├── 04-流程图/              # 系统流程、状态机图
  ├── 05-UI原型/              # 界面布局草图
  ├── 06-边界情况.md          # 异常场景处理
  └── 07-验收标准.md          # 可玩性检查清单
  ```
- **核心实现任务:**
  1. 选定游戏类型与要设计的系统（战斗/经济/养成/社交）
  2. 调研 3 款同类游戏的该系统实现方式
  3. 编写系统概述：定位、设计目标、玩家体验目标
  4. 详细描述核心机制（规则、公式、流程）
  5. 使用 Excel 构建数值模型（公式 + 曲线图 + 模拟数据）
  6. 绘制系统流程图与状态机图
  7. 设计 UI 界面草图（手绘或 Figma）
  8. 编写边界情况处理与验收标准
- **扩展方向:**
  - 添加数值模拟脚本（Python 蒙特卡洛模拟战斗 1000 次验证平衡性）
  - 制作可交互的数值调参工具（修改参数 → 实时看曲线变化）
  - 撰写玩家体验分析（心流曲线、目标梯度、损失厌恶应用）
  - 输出一份面向团队的宣讲 PPT

---

### 模板 2（策划）：关卡设计原型

- **项目名称:** 某游戏类型 + 关卡设计白盒原型
- **难度:** 进阶
- **核心交付物:** 白盒关卡文件 + 设计说明 + 玩法验证结果
- **简历描述示例:**
  - 使用 UE5/Unity 白盒工具设计 FPS 游戏单人关卡，包含战斗、探索、解谜三种玩法节奏
  - 编写关卡设计文档，定义关卡流程图（Beat Chart）、敌人配置与资源点位分布
  - 组织 5 人内部试玩，收集反馈并迭代 3 版，优化掩体位置与敌人 AI 巡逻路径
- **建议结构:**
  ```
  level-design/
  ├── project/                # Unity/UE 白盒关卡工程
  ├── docs/
  │   ├── 01-关卡概述.md      # 关卡主题、设计目标、情感曲线
  │   ├── 02-关卡流程图.md    # Beat Chart：探索→战斗→解谜→高潮
  │   ├── 03-敌人配置表.md    # 敌人类型、数量、位置、触发条件
  │   ├── 04-资源点位.md      # 武器/道具/掩体分布
  │   └── 05-玩法验证.md      # 试玩反馈与迭代记录
  └── screenshots/            # 关卡截图
  ```
- **核心实现任务:**
  1. 确定关卡主题、美术风格参考与设计目标
  2. 绘制关卡俯视图（Top-down Layout）与 Beat Chart
  3. 使用白盒搭建关卡基本结构（地形、建筑、路径）
  4. 配置敌人类型、数量、出生点与 AI 巡逻路径
  5. 放置武器、道具、掩体、交互物件
  6. 实现关卡脚本（触发器、事件、目标提示）
  7. 自测核心玩法循环，调整难度曲线
  8. 组织试玩 → 收集反馈 → 迭代优化（至少 2 轮）
- **扩展方向:**
  - 使用蓝图/可视化脚本实现完整关卡逻辑
  - 制作关卡俯瞰视频（Flythrough 录像）
  - 设计多难度版本（简单/普通/困难 敌人配置差异）
  - 添加 Speedrun 路线设计（高级技巧 + 隐藏捷径）

---

### 模板 3（开发）：Unity 2D/3D 小游戏

- **项目名称:** Unity + 完整可玩游戏
- **难度:** 入门
- **技术栈:** Unity + C# + 2D/3D 基础功能
- **简历描述示例:**
  - 独立使用 Unity 开发一款 2D 平台跳跃游戏，包含角色控制、物理碰撞、敌人 AI、UI 系统
  - 实现 5 个完整关卡，包含移动平台、尖刺、巡逻敌人 3 种机制
  - 使用对象池优化 Bullet/Enemy 频繁创建销毁，帧率稳定在 60FPS
- **最小可运行骨架:**
  ```
  unity-game/
  ├── Assets/
  │   ├── Scripts/
  │   │   ├── Player/
  │   │   │   ├── PlayerController.cs
  │   │   │   └── PlayerHealth.cs
  │   │   ├── Enemy/
  │   │   │   ├── EnemyAI.cs
  │   │   │   └── EnemySpawner.cs
  │   │   ├── Systems/
  │   │   │   ├── GameManager.cs
  │   │   │   ├── ObjectPool.cs
  │   │   │   └── UIManager.cs
  │   │   └── Level/
  │   │       ├── Checkpoint.cs
  │   │       └── MovingPlatform.cs
  │   ├── Scenes/            # 菜单、关卡1-5
  │   ├── Prefabs/           # 预制体
  │   └── Art/               # 美术资源（可用免费素材）
  └── ProjectSettings/
  ```
- **核心实现任务:**
  1. 创建 Unity 项目，导入免费 2D/3D 素材（Kenney/Asset Store）
  2. 实现角色控制：移动、跳跃、动画状态机
  3. 实现物理系统：重力、碰撞检测、地面检测
  4. 实现敌人 AI：巡逻路线、检测范围、追击/返回状态
  5. 实现 UI 系统：主菜单、血条、得分、暂停菜单、结算界面
  6. 搭建 5 个关卡场景（难度渐进）
  7. 实现对象池：管理子弹、敌人、特效的复用
  8. 构建发布：PC 可执行文件 + WebGL 在线试玩
- **扩展方向:**
  - 添加音效与背景音乐系统
  - 实现存档系统（PlayerPrefs → JSON 文件）
  - 添加排行榜功能（本地 + 在线分数提交）
  - 实现关卡编辑器（在 Unity Editor 中可视化编辑关卡）

---

### 模板 4（开发）：游戏框架/工具

- **项目名称:** Unity/UE + 编辑器工具/游戏框架
- **难度:** 进阶
- **技术栈:** Unity Editor 扩展 + C# 或 UE Editor 扩展 + C++/Blueprint
- **简历描述示例:**
  - 开发 Unity 编辑器工具「关卡批量生成器」，支持从 JSON/CSV 数据自动生成关卡场景
  - 实现可视化节点编辑器，策划可通过连线配置对话树与任务流程
  - 将工具封装为 Unity Package，供团队 10+ 人使用，关卡制作效率提升 60%
- **最小可运行骨架:**
  ```
  game-tool/
  ├── Assets/
  │   ├── Editor/
  │   │   ├── LevelGenerator/
  │   │   │   ├── LevelGeneratorWindow.cs   # Editor Window
  │   │   │   ├── LevelGeneratorLogic.cs    # 生成逻辑
  │   │   │   └── LevelDataImporter.cs      # 数据导入
  │   │   └── NodeEditor/
  │   │       ├── NodeEditorWindow.cs
  │   │       ├── Nodes/
  │   │       │   ├── DialogueNode.cs
  │   │       │   └── QuestNode.cs
  │   │       └── Connection.cs
  │   └── Runtime/
  │       └── NodePlayer.cs                # 运行时播放
  └── README.md
  ```
- **核心实现任务:**
  1. 创建 Editor Window：菜单入口、窗口布局、基本 UI（IMGUI/UI Toolkit）
  2. 实现关卡数据导入：读取 JSON/CSV → 解析为关卡数据结构
  3. 实现关卡生成逻辑：根据数据自动放置地形、道具、敌人
  4. 实现节点编辑器——画布：绘制背景、网格
  5. 实现节点编辑器——节点：创建、拖拽、连线、删除
  6. 实现节点编辑器——运行时：根据节点图执行对话/任务逻辑
  7. 添加 Undo/Redo 支持（Unity Undo 系统）
  8. 打包为 Unity Package，编写使用文档
- **扩展方向:**
  - 添加关卡预览功能（在 Editor 中模拟运行关卡）
  - 实现批量资源处理工具（批量重命名、格式转换、图集打包）
  - 扩展节点系统支持更多类型（技能树、AI 行为树）
  - 实现自动化测试工具（自动运行关卡并收集性能数据）

---

### 模板 5（美术）：风格化角色管线

- **项目名称:** 某风格 + 完整角色制作管线
- **难度:** 进阶
- **核心交付物:** 角色模型 + 贴图 + 引擎内展示 + 制作过程文档
- **简历描述示例:**
  - 完成一个风格化（Stylized）游戏角色的完整制作管线：概念设计 → 高模 → 低模 → UV → 贴图 → 引擎渲染
  - 使用 ZBrush 雕刻高模（30M 面），Maya/Blender 拓扑低模（15K 三角面），Substance Painter 制作 PBR 贴图
  - 在 Unity/UE 中完成材质设置、光照调试与最终渲染展示
- **建议交付结构:**
  ```
  character-pipeline/
  ├── 01-概念设计/           # 参考图、剪影探索、配色方案
  ├── 02-高模/               # ZBrush 截图或 Marmoset 渲染
  ├── 03-低模/               # 拓扑结果、UV 布局
  ├── 04-贴图/               # BaseColor、Normal、Roughness、Metallic
  ├── 05-引擎展示/           # Unity/UE 截图、360 度旋转视频
  └── 06-制作文档.md         # 制作流程、技术细节、遇到的问题与解决
  ```
- **核心实现任务:**
  1. 收集参考素材并确定美术风格方向
  2. 绘制角色概念草图或剪影（奠定比例与轮廓）
  3. ZBrush/Blender 雕刻高模（注重形体、褶皱、细节）
  4. Maya/Blender 拓扑低模（控制面数、边线流向）
  5. 拆分 UV（合理利用空间，重要区域给更多分辨率）
  6. Substance Painter 制作 PBR 贴图（烘焙 AO/Curvature/Thickness 辅助）
  7. 导入引擎（Unity/UE），设置材质球与光照环境
  8. 输出最终渲染（静态图 + 360 度旋转视频）
- **扩展方向:**
  - 添加骨骼绑定与简单动画（Idle/Walk/Run）
  - 实现手绘风格贴图（不依赖 PBR，纯 Diffuse 手绘）
  - 制作角色换装系统（模块化部件 + 材质切换）
  - 输出 Marmoset Toolbag 实时查看文件

---

### 模板 6（TA）：Shader 效果库

- **项目名称:** Unity/UE + Shader 视觉效果集
- **难度:** 进阶
- **核心交付物:** 3+ Shader 文件 + 效果对比 + 技术分解文档
- **简历描述示例:**
  - 开发 Shader 视觉效果集，包含溶解、全息投影、水面、轮廓描边、卡通渲染 5 个效果
  - 基于 Shader Graph/HLSL/GLSL 实现，每个效果提供可调节参数（颜色、强度、方向等）
  - 撰写技术分解文档，详细说明每个效果的数学原理、渲染管线位置与性能优化方案
- **最小可交付结构:**
  ```
  shader-library/
  ├── unity-project/         # Unity 工程
  │   ├── Assets/Shaders/
  │   │   ├── Dissolve.shader
  │   │   ├── Hologram.shader
  │   │   ├── Water.shader
  │   │   ├── Outline.shader
  │   │   └── Toon.shader
  │   └── Assets/Scenes/     # 展示场景
  └── docs/
      ├── 01-溶解效果.md     # 原理（噪声+clip）、算法、参数说明
      ├── 02-全息投影.md
      └── ...
  ```
- **核心实现任务:**
  1. 搭建 Shader 测试场景（模型 + 光照 + 相机）
  2. 实现溶解效果：噪声纹理 + alpha 裁剪 + 边缘发光
  3. 实现全息投影效果：顶点偏移（扫描线）+ 菲涅尔边缘光 + 扫描线纹理
  4. 实现水面效果：顶点动画（Gerstner 波）+ 法线扰动 + 折射/反射
  5. 实现轮廓描边：背面膨胀法 + 后处理深度法 两种方案
  6. 实现卡通渲染：色阶化漫反射 + 轮廓线 + 高光简化
  7. 编写效果对比文档（Everyday 渲染 vs 自定义 Shader）
  8. 撰写技术分解文档（每个 Shader 核心算法 + 性能分析）
- **扩展方向:**
  - 添加后处理特效（Bloom、Color Grading、Motion Blur 的自定义实现）
  - 实现 shader 变体管理（Keyword 控制功能开关以减少分支）
  - 适配移动端（优化指令数、纹理采样次数）
  - 制作 Shader 参数调节面板（MaterialPropertyBlock + 自定义 Inspector）

---

### 模板 7（TA）：程序化生成工具

- **项目名称:** Houdini/Blender + 程序化生成工具
- **难度:** 进阶
- **核心交付物:** Houdini HDA 或 Blender Geometry Nodes 文件 + 生成结果展示 + 文档
- **简历描述示例:**
  - 使用 Houdini 开发程序化建筑生成工具，输入地块轮廓自动生成多样化的建筑模型
  - 设计模块化规则：立面风格、窗户密度、屋顶类型、阳台概率 4 个维度可参数化控制
  - 导出为 Houdini Engine 可在 Unity/UE 中实时调用，提升关卡美术生产效率
- **建议交付结构:**
  ```
  procedural-tool/
  ├── houdini-project/        # Houdini 工程文件 (.hip)
  │   ├── hda/                # 导出的 HDA 文件
  │   └── otls/               # 自定义节点
  ├── unity-project/          # Unity 集成示例（可选）
  ├── outputs/                # 生成结果截图/视频
  └── docs/
      ├── 01-系统设计.md      # 规则设计思路、模块拆解
      ├── 02-参数说明.md      # 各参数含义与效果对照
      └── 03-使用指南.md      # 在引擎中的使用方式
  ```
- **核心实现任务:**
  1. 确定程序化生成目标（建筑/植被/道路/地形）
  2. 在 Houdini 中搭建基础生成流程（输入 → 处理 → 输出）
  3. 实现核心生成逻辑：几何体分割、随机化、约束检查
  4. 暴露参数到 HDA 接口（浮点、整数、Toggle、下拉菜单）
  5. 实现立面/样式变化逻辑（多种模块随机组合）
  6. 测试不同参数组合的生成结果多样性
  7. 导出 HDA 并在 Unity/UE 中加载测试
  8. 编写使用文档与参数调优指南
- **扩展方向:**
  - 添加碰撞检测（避免生成物重叠）
  - 实现 LOD 自动生成（远近不同精度）
  - 扩展到更多生成类型（道路网络 + 建筑 + 植被联动）
  - 使用 Blender Geometry Nodes 实现相同功能（熟练掌握两套工具）

---

## 十一、跨方向组合模板

### 模板 1（全栈）：前端 + 后端完整应用

- **项目名称:** React/Vue + Go/Node/Python + 全栈 Web 应用
- **难度:** 进阶
- **技术栈:** React/Vue + Go/Node/Python + PostgreSQL + Docker
- **简历描述示例:**
  - 全栈独立开发「XX 管理平台」，前端 React + TypeScript，后端 Go + Gin，数据库 PostgreSQL
  - 实现用户认证（JWT + Refresh Token）、RBAC 权限、文件上传、数据导出等完整功能
  - 使用 Docker Compose 部署全套环境，编写 60+ 接口文档（OpenAPI），前后端测试覆盖率 80%+
- **最小可运行骨架:**
  ```
  fullstack-app/
  ├── frontend/            # React/Vue 项目
  │   ├── src/
  │   │   ├── pages/       # 页面组件
  │   │   ├── components/  # 通用组件
  │   │   ├── api/         # API 请求封装
  │   │   └── store/       # 状态管理
  │   └── package.json
  ├── backend/             # Go/Node/Python 项目
  │   ├── handler/         # HTTP 层
  │   ├── service/         # 业务逻辑
  │   ├── repository/      # 数据访问
  │   └── middleware/      # 中间件
  ├── docker-compose.yml
  └── Makefile
  ```
- **核心实现任务:**
  1. 搭建后端骨架：路由、中间件、数据库连接
  2. 实现后端用户模块：注册、登录、JWT
  3. 实现后端业务 CRUD（选取与职位相关的业务模型）
  4. 搭建前端骨架：路由、状态管理、API 层
  5. 实现前端登录页 + 首页布局
  6. 实现前端业务页面（列表 + 表单 + 详情）
  7. 前后端联调，修复跨域、认证、数据格式问题
  8. Docker Compose 编排 + 编写 README 说明
- **扩展方向:**
  - 添加 WebSocket 实现实时通知
  - 实现文件上传（OSS/本地存储 + 缩略图）
  - 添加 E2E 测试（Cypress/Playwright）
  - 部署至云服务器并绑定域名（展示在线 Demo）

---

### 模板 2（游戏全栈）：Unity + 后端服务

- **项目名称:** Unity + Go/Node + 联网游戏
- **难度:** 高级
- **技术栈:** Unity + C# + Go/Node 后端 + WebSocket/TCP + Redis + MySQL
- **简历描述示例:**
  - 开发 Unity 联网多人游戏 Demo，后端使用 Go 实现房间匹配、状态同步与排行榜服务
  - 基于 WebSocket 实现客户端-服务端实时通信，使用帧同步方案保证多人一致性
  - 服务端实现玩家匹配队列、房间管理与数据持久化，支持 20+ 并发房间
- **最小可运行骨架:**
  ```
  online-game/
  ├── unity-client/          # Unity 工程
  │   ├── Assets/Scripts/
  │   │   ├── Network/
  │   │   │   ├── WebSocketClient.cs
  │   │   │   └── MessageHandler.cs
  │   │   ├── Game/
  │   │   │   ├── GameManager.cs
  │   │   │   └── PlayerController.cs
  │   │   └── UI/
  │   └── Assets/Scenes/
  ├── game-server/           # Go/Node 后端
  │   ├── handler/
  │   │   ├── match.go       # 匹配逻辑
  │   │   ├── room.go        # 房间管理
  │   │   └── game.go        # 游戏逻辑
  │   └── main.go
  └── docker-compose.yml
  ```
- **核心实现任务:**
  1. 搭建 WebSocket 服务端（Go: gorilla/websocket 或 Node: ws）
  2. 实现服务端消息协议（JSON/Protobuf）与消息路由
  3. 实现客户端 WebSocket 连接管理（连接、重连、心跳）
  4. 实现匹配系统：玩家加入队列 → 凑齐人数 → 创建房间
  5. 实现房间管理：创建、加入、离开、解散
  6. 实现游戏状态同步（帧同步或状态同步，选一种）
  7. 实现简单玩法（如多人贪吃蛇、坦克大战）验证同步正确性
  8. 实现排行榜服务（Redis Sorted Set + API）
- **扩展方向:**
  - 实现 UDP/KCP 低延迟传输替代 WebSocket
  - 添加服务端游戏逻辑校验（防作弊）
  - 实现观战模式
  - 实现匹配天梯（ELO 评分系统）

---

### 模板 3（数据驱动产品）：数据分析 + 产品设计

- **项目名称:** 数据驱动 + 产品功能迭代方案
- **难度:** 进阶
- **核心交付物:** 数据分析报告 + 产品优化 PRD + 效果预估
- **简历描述示例:**
  - 对「XX 产品」进行数据驱动的功能优化，通过漏斗分析识别注册转化瓶颈
  - 提出 3 项优化方案，基于历史数据预估转化率可提升 15-20%
  - 设计 A/B 测试方案验证优化效果，定义核心指标与统计检验方法
- **建议结构:**
  ```
  data-driven-product/
  ├── 01-现状分析.md          # 当前漏斗数据、用户分群、竞品基准
  ├── 02-问题诊断.md          # 数据异常点、用户反馈、体验走查
  ├── 03-优化方案.md          # 方案列表、优先级、效果预估
  ├── 04-AB测试设计.md        # 实验设计、样本量、指标
  └── 05-模拟数据/            # Python 脚本：模拟优化前后数据
      └── simulate.py
  ```
- **核心实现任务:**
  1. 选取一个产品的核心流程并定义漏斗指标
  2. 使用 Python 生成当前状态的模拟漏斗数据
  3. 分析漏斗各环节转化率与流失原因假设
  4. 设计 3 个优化方案，描述方案细节与预期影响
  5. 模拟优化后数据（调整各环节转化率参数）
  6. 计算优化方案的预期收益（转化率提升 × 用户量 × 单用户价值）
  7. 设计 A/B 测试方案验证核心假设
  8. 输出综合报告：数据 → 洞察 → 方案 → 预估 → 验证计划
- **扩展方向:**
  - 添加 ROI 计算（开发成本 vs 预期收益）
  - 制作交互式数据看板（Streamlit/Gradio）
  - 编写用户分群策略（特征 + 行为 + 价值 三维分群）
  - 输出产品路线图建议（短期/中期/长期优化方向）

---

## 十二、模板使用指南

### 如何选择合适的模板？

1. **匹配求职方向:** 选择与你投递岗位最相关的 2-3 个方向
2. **难度金字塔:** 建议组合 = 1 入门 + 1 进阶 + 1 高级（如果没有高级，2 进阶也可）
3. **技术栈对齐:** 优先选择与目标公司技术栈重叠的模板
4. **时间估算:**
   - 入门级项目：1-2 周（兼职）
   - 进阶项目：2-4 周（兼职）
   - 高级项目：4-8 周（兼职）

### 如何使用模板？

1. **复制骨架:** 复制模板中的目录结构，初始化 Git 仓库
2. **按任务执行:** 按 5-8 个核心实现任务顺序推进，每完成一个任务做一次 commit
3. **TDD 优先:** 先写测试（如果你选的模板涉及编码），再写实现
4. **记录过程:** 每个任务完成后在 README 中添加进度记录与关键决策
5. **面试准备:** 准备好「为什么选这个技术」「遇到什么困难」「如何解决」的回答

### 如何让项目在简历上更出彩？

1. **有数据:** 性能提升了 X%、覆盖了 Y 个用例、支持了 Z 个并发
2. **有 Demo:** 部署一个在线版本或提供可运行的 Docker 命令
3. **有文档:** README 中写清楚背景、架构、运行方式、技术选型理由
4. **有测试:** 测试覆盖率说明你关心代码质量
5. **有 CI/CD:** GitHub Actions Badge 展示自动化程度
