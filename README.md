# Resume Builder — Claude Code Skill

基于 Claude Code 的简历编写与项目落地技能，帮助毕业生从零开始打造一份经得起面试深挖的简历。

## 核心功能

- **方向探索** — 12 个岗位方向的递进式提问，精准定位
- **简历编写** — 模块化引导，支持 Markdown + LaTeX (billryan 模板) 双格式输出
- **项目实现** — 简历上的每个项目通过 TDD 子代理真正落地
- **飞书集成** — 目标追踪、版本管理、日程提醒、消息推送
- **面试准备** — 从简历自动生成面试问题，模拟面试 + 简历反哺

## 安装

```bash
# 解压到 Claude Code skills 目录
unzip resume-builder.zip -d ~/.claude/skills/
```

或克隆本仓库：

```bash
git clone https://github.com/sugar787878/resume-builder.git ~/.claude/skills/resume-builder
```

## 使用

在 Claude Code 中输入以下任意关键词即可触发：

- "帮我写简历"
- "准备秋招"
- "没有项目经验"
- "找工作"

## 文件结构

```
resume-builder/
├── SKILL.md                            # 主指令文件
├── _meta.json                          # 元数据
└── references/
    ├── career-directions.md            # 12 方向决策树 + 简历骨架
    ├── project-templates.md            # 36 个项目模板
    ├── feishu-schema.md               # 飞书表 schema + MCP 调用
    ├── tdd-build-pattern.md           # TDD 子代理构建模式
    ├── interview-prep.md              # 面试准备模块
    └── resume-template.md             # billryan LaTeX 模板文档
```

## 覆盖方向

技术岗 + 泛互联网岗 + 游戏岗位：

后端 · 前端 · 算法/ML · 运维/DevOps · 安全 · 产品经理 · UI/UX 设计 · 数据分析 · 游戏策划 · 游戏开发 · 游戏美术 · 技术美术(TA)

## 依赖

- Claude Code
- 飞书 MCP（可选，用于目标追踪和消息推送）

## License

MIT
