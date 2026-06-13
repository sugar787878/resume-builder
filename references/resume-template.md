# billryan/resume LaTeX 模板参考文档

> 本文档为 resume-builder 技能的参考文件，详细记录了 billryan/resume 模板的结构、命令和与技能模块的映射关系。
> 技能在 Phase 2（简历编写）完成后，若用户选择 LaTeX 输出，将依据本文档生成 `.tex` 文件。

---

## 一、模板概述

**billryan/resume** 是一个基于 XeLaTeX 的中文简历模板，GitHub 仓库地址 [https://github.com/billryan/resume](https://github.com/billryan/resume)。

| 属性 | 说明 |
|------|------|
| 编译器 | XeLaTeX |
| 语言 | 中文为主，英文为辅 |
| 页数 | 单页（默认），内容精简优雅 |
| 开源协议 | MIT License |
| Stars | 11.4k+ |
| 分支 | `master` 为英文模板，`zh_CN` 为中文模板 |
| 评价 | 中文 LaTeX 简历领域最受欢迎的开源模板 |

### 为什么要用这个模板？

- **排版精美**：基于 `resume.cls` 自定义文档类，字体大小、间距、线条都经过精心调校，视觉效果专业。
- **ATS 友好**：纯文本 LaTeX 编译，生成的是标准 PDF，内容可复选可搜索，通过 ATS（Applicant Tracking System）解析。
- **中文支持完善**：内置 Adobe 中文字体配置（Adobe Song Std / Adobe Heiti Std / Adobe Kaiti Std），也可使用系统字体。
- **易于定制**：所有样式集中在 `resume.cls`，内容写在 `.tex`，结构清晰。支持带/不带照片版本。
- **业界认可**：国内互联网、金融、咨询等行业技术岗求职者广泛使用，面试官熟悉该格式。

---

## 二、文件结构

```
resume/
├── resume.cls                          # 文档类（核心样式定义，所有格式化逻辑）
├── resume-zh_CN.tex                    # 中文模板范例（常用入口文件）
├── resume.tex                          # 英文模板范例
├── resume_photo.tex                    # 带照片版本
├── zh_CN-Adobefonts_external.sty       # 外部中文字体样式（调用系统已安装的 Adobe 字体）
├── zh_CN-Adobefonts_internal.sty       # 系统中文字体样式（调用常见系统字体，如 SimSun）
├── linespacing_fix.sty                 # 行间距修复宏包
└── fonts/                              # 字体文件目录（存放 .ttf/.otf 字体文件）
```

### 关键文件说明

| 文件 | 作用 | 是否需要修改 |
|------|------|-------------|
| `resume.cls` | 定义页面布局、字体、颜色、节标题格式、列表样式等 | 否，直接使用 |
| `resume-zh_CN.tex` | 用户填写简历内容的入口文件 | 是，按用户信息修改 |
| `zh_CN-Adobefonts_external.sty` | 字体配置，指定中文字体映射 | 酌情，若系统无对应字体需切换 |
| `linespacing_fix.sty` | 解决 XeLaTeX 中文字体行间距过大问题 | 否，直接使用 |

---

## 三、核心命令速查表

以下命令均来自 `resume.cls` 的定义，在 `.tex` 文件中直接使用。

### 3.1 个人信息类

| 命令 | 语法 | 说明 |
|------|------|------|
| `\name{}` | `\name{张三}` | 姓名，居中显示，使用 `\Huge` 字号 + small-caps 样式 |
| `\basicInfo{}` | `\basicInfo{\email{...} \phone{...} \github{...}}` | 基本信息栏，居中，各元素用分隔符分开 |
| `\email{}` | `\email{zhangsan@example.com}` | 邮箱，带 FontAwesome 信封图标 |
| `\phone{}` | `\phone{(+86) 138-0000-0000}` | 电话，带 FontAwesome 电话图标 |
| `\github{}` | `\github[username]{https://github.com/username}` | GitHub，带 FontAwesome 图标，可选参数为显示文本 |
| `\linkedin{}` | `\linkedin[display]{https://linkedin.com/in/xxx}` | LinkedIn，带 FontAwesome 图标，可选参数为显示文本 |
| `\homepage{}` | `\homepage{https://me.example.com}` | 个人主页/博客，带 FontAwesome 地球图标 |

### 3.2 节标题类

| 命令 | 语法 | 说明 |
|------|------|------|
| `\section{}` | `\section{教育背景}` | 一级标题，带底部横线，中文使用黑体/粗体 |
| `\datedsubsection{}` | `\datedsubsection{北京大学}{2021.09 -- 2025.06}` | 左侧标题 + 右侧日期，使用 `\textbf` 加粗 |
| `\role{}` | `\role{实习生}{后端开发}` | 角色行，第一个参数为角色名（默认斜体），第二个参数为描述，用竖线分隔 |
| `\datedline{}` | `\datedline{项目名称}{2023.01 -- 2023.06}` | 带日期的一行文本，左侧标题 + 右侧日期 |

### 3.3 列表与附加项

| 环境/命令 | 语法 | 说明 |
|-----------|------|------|
| `itemize` | `\begin{itemize}\item 第一条\end{itemize}` | 标准列表，`\item` 前导符号为实心圆点 |
| `enumerate` | `\begin{enumerate}\item 第一项\end{enumerate}` | 有序列表，`\item` 前导数字 |
| `\resumeItem{}` | `\resumeItem{描述内容}` | 自定义列表项（某些版本中可用，非标准命令） |

### 3.4 排版控制类

| 命令 | 说明 |
|------|------|
| `\noindent` | 取消段落首行缩进 |
| `\vspace{-0.5em}` | 负垂直间距，用于压缩列表与标题之间的空白 |
| `\small` | 小号字体 |
| `\textbf{}` | 加粗 |

---

## 四、Skill 模块 → LaTeX 命令映射

技能在 Phase 2 收集的用户简历信息，按照以下映射转换为 LaTeX 命令。

```
技能模块              →  LaTeX 命令 / 环境
══════════════════════════════════════════════════════════════
个人信息 (姓名)        →  \name{<用户姓名>}

个人信息 (联系方式)    →  \basicInfo{
                            \email{<邮箱>} \textperiodcentered{}
                            \phone{<电话>} \textperiodcentered{}
                            \github[<用户名>]{<链接>}
                          }

求职意向               →  通常不单独列出，可附加在 \basicInfo{} 内或
                          在 \section{其他} 中注明

教育背景               →  \section{教育背景}
                          \datedsubsection{<学校名称>}{<起止时间>}
                          \role{<学位：学士/硕士/博士>}{<专业名称>}
                          可选：\begin{itemize}
                            \item 主修课程: ...
                            \item GPA: 3.9/4.0, 排名 5/120
                          \end{itemize}

实习经历               →  \section{实习/项目经历}
                          \datedsubsection{<公司名称>}{<起止时间>}
                          \role{<职位名称>}{<部门/方向>}
                          \begin{itemize}
                            \item 用 STAR 法则描述工作内容
                            \item 量化成果（提升XX%, 优化XX）
                            \item 技术栈标签：Python / Go / React
                          \end{itemize}

项目经历               →  \datedsubsection{<项目名称>}{<起止时间>}
                          \role{<角色：负责人/核心开发>}{<技术栈>}
                          \begin{itemize}
                            \item 项目简介（一句话）
                            \item 个人职责与贡献
                            \item 技术难点与解决方案
                            \item 量化成果（用户量、性能指标）
                          \end{itemize}

技术栈 / 技能           →  \section{IT 技能}
                          \begin{itemize}
                            \item \textbf{编程语言:} Python, Go, Java
                            \item \textbf{后端框架:} Django, Gin, Spring Boot
                            \item \textbf{数据库:} MySQL, Redis, MongoDB
                            \item \textbf{工具与平台:} Docker, K8s, Linux
                          \end{itemize}

获奖情况               →  \section{获奖情况}
                          \begin{itemize}
                            \item 国家奖学金, 2023
                            \item ACM-ICPC 区域赛金牌, 2022
                            \item 全国大学生数学建模竞赛一等奖, 2021
                          \end{itemize}

语言能力 / 证书 / 其他  →  \section{其他}
                          \begin{itemize}
                            \item \textbf{英语:} CET-6 6xx, 流利阅读技术文档
                            \item \textbf{证书:} AWS Solutions Architect Associate
                            \item \textbf{兴趣爱好:} 开源贡献, 技术博客
                          \end{itemize}
```

### 映射注意事项

1. **时间格式统一**：起止时间使用 `2024.01 -- 2024.06` 格式（四位年份.两位月份，短横线前后加空格）。
2. **经历排序**：按时间倒序排列（最近的在前），与简历惯例一致。
3. **STAR 原则**：每个 item 应包含 Situation / Task / Action / Result 要素，用数字量化成果。
4. **技术栈格式化**：技能部分建议使用 `\textbf{类别:}` 加粗类别名，后面跟具体技能，分值清晰。
5. **页数控制**：默认模板仅容纳单页内容。如果内容过多，适当精简描述或压缩行间距（使用 `\vspace{}`）。

---

## 五、完整中文模板范例

以下为 `resume-zh_CN.tex` 的完整示例内容，所有个人信息为占位符。技能生成 `.tex` 文件时应以此结构为基础，替换占位符为用户真实数据。

```latex
%!TEX TS-program = xelatex
%!TEX encoding = UTF-8 Unicode

\documentclass{resume}
\usepackage{zh_CN-Adobefonts_external} % 简体中文支持
% \usepackage{zh_CN-Adobefonts_internal} % 若系统中无 Adobe 字体，改用此项
\usepackage{linespacing_fix} % 行间距修复
\usepackage{cite}

\begin{document}
\pagenumbering{gobble} % 抑制页码

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%       个人信息
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\name{张三}
\basicInfo{
  \email{zhangsan@example.com} \textperiodcentered{}
  \phone{(+86) 138-0000-0000} \textperiodcentered{}
  \github[zhangsan]{https://github.com/zhangsan}
}

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%       教育背景
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\section{教育背景}

\datedsubsection{北京大学}{2021.09 -- 2025.06}
\role{学士}{计算机科学与技术}
\begin{itemize}
  \item 主修课程：数据结构、计算机网络、操作系统、数据库原理、机器学习
  \item GPA: 3.8/4.0, 专业排名前 10\%
\end{itemize}

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%       实习 / 项目经历
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\section{实习 / 项目经历}

\datedsubsection{字节跳动}{2024.03 -- 2024.06}
\role{后端开发实习生}{抖音电商 - 商品平台}
\begin{itemize}
  \item 参与商品搜索服务重构，将搜索响应时延从 120ms 优化至 45ms（P99），日均承载 5000 万次请求
  \item 设计并实现商品属性倒排索引，支撑多条件组合筛选场景，查询 QPS 提升 3 倍
  \item 使用 Go + Kitex 框架开发 3 个微服务模块，完成从接口定义到上线的全流程，代码测试覆盖率 92\%
\end{itemize}

\datedsubsection{分布式 KV 存储系统}{2023.09 -- 2024.01}
\role{核心开发者}{Go / gRPC / Raft}
\begin{itemize}
  \item 基于 Raft 共识算法实现强一致性分布式 KV 存储，支持 Leader 选举、日志复制和快照压缩
  \item 设计 MVCC 多版本并发控制机制，实现事务隔离级别 Snapshot Isolation
  \item 使用 gRPC 构建客户端与服务端通信，支持 Put/Get/Delete/Scan 操作
  \item 完成 20+ 单元测试和集成测试，在 5 节点集群上通过 Jepsen 线性一致性验证
\end{itemize}

\datedsubsection{校园二手交易平台}{2023.03 -- 2023.06}
\role{全栈开发}{Vue.js / Django / MySQL}
\begin{itemize}
  \item 作为 4 人团队负责人，基于 Django REST Framework 构建后端 API，Vue3 构建前端 SPA
  \item 实现商品发布、搜索、私信、订单管理等核心功能，接入阿里云 OSS 图片存储
  \item 使用 Redis 做热点数据缓存和分布式 Session，支撑 2000+ 注册用户日常使用
\end{itemize}

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%       IT 技能
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\section{IT 技能}
\begin{itemize}
  \item \textbf{编程语言:} Go, Python, Java, C/C++, JavaScript
  \item \textbf{后端框架:} Gin, Kitex, Django, Spring Boot, gRPC
  \item \textbf{数据库与中间件:} MySQL, Redis, MongoDB, Elasticsearch, Kafka
  \item \textbf{云原生与工具:} Docker, Kubernetes, Linux, Git, CI/CD (GitHub Actions)
\end{itemize}

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%       获奖情况
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\section{获奖情况}
\begin{itemize}
  \item 国家奖学金, 教育部, 2023
  \item ACM-ICPC 亚洲区域赛银牌, 2022
  \item 全国大学生数学建模竞赛省级一等奖, 2022
\end{itemize}

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%       其他
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\section{其他}
\begin{itemize}
  \item \textbf{英语能力:} CET-6 620, 能够流畅阅读英文技术文档和论文
  \item \textbf{开源贡献:} 参与 go-redis 开源项目, 提交 3 个 PR 被合并
  \item \textbf{技术博客:} 在个人博客累计发布 15 篇技术文章
\end{itemize}

\end{document}
```

---

## 六、编译指南

### 方式一：Overleaf（推荐，零门槛）

Overleaf 是在线 LaTeX 编辑器，无需本地安装任何软件。

1. 注册/登录 [https://www.overleaf.com](https://www.overleaf.com)
2. 创建新项目 → 上传文件
3. 上传以下文件到项目中：
   - `resume.cls`（文档类）
   - `zh_CN-Adobefonts_external.sty`（字体样式）
   - `zh_CN-Adobefonts_internal.sty`（备用字体样式）
   - `linespacing_fix.sty`（行间距修复）
   - 用户的 `.tex` 文件（由技能生成）
4. 点击左上角 Menu → Compiler → 选择 **XeLaTeX**
5. 点击 Recompile → 预览 PDF → 下载

> **注意**：Overleaf 默认不含 Adobe 中文字体。若编译报错字体缺失，将 `.tex` 中的 `\usepackage{zh_CN-Adobefonts_external}` 替换为 `\usepackage{zh_CN-Adobefonts_internal}`，使用系统中文字体。

### 方式二：本地编译

#### Windows
1. 下载并安装 [TeX Live](https://www.tug.org/texlive/) 或 [MikTeX](https://miktex.org/)
2. 安装完成后，打开命令行确认 `xelatex --version` 可用
3. 进入 `.tex` 文件所在目录，执行：
```bash
xelatex resume-zh_CN.tex
```
4. 如需刷新引用（通常简历不需要多次编译），再执行一次即可

#### macOS
```bash
# 安装 MacTeX（约 4GB，包含完整 TeX Live）
brew install --cask mactex

# 编译
xelatex resume-zh_CN.tex
```

#### Linux (Ubuntu/Debian)
```bash
# 安装必要包
sudo apt install texlive-xetex texlive-latex-extra texlive-lang-chinese

# 编译
xelatex resume-zh_CN.tex
```

### 常见编译问题

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| `! LaTeX Error: File 'resume.cls' not found.` | 缺少文档类文件 | 确保 `resume.cls` 与 `.tex` 在同一目录 |
| `! Package fontspec Error: The font "Adobe Song Std" cannot be found.` | 系统中无 Adobe 字体 | 改用 `zh_CN-Adobefonts_internal` 或安装对应字体 |
| `! LaTeX Error: File 'FontAwesome' not found.` | 缺少 FontAwesome 宏包 | 安装 `texlive-fonts-extra`（Linux）或 TeX Live 完整版 |
| 中文显示为空白或乱码 | 编译器未选择 XeLaTeX | 确认使用 XeLaTeX 而非 pdfLaTeX |
| 内容超出单页 | 内容过多 | 精简 item 描述，或使用 `\vspace{-0.3em}` 压缩间距 |

---

## 七、如何在 Phase 2 中使用

### 7.1 触发时机

当技能完成 Phase 2 所有模块的编写后（个人信息、教育背景、实习/项目经历、技能、获奖、其他全部完成），向用户展示如下选项：

```
简历编写完成！你希望以什么格式输出？

A. Markdown — 纯文本，方便后续编辑和版本对比
B. LaTeX (billryan 模板) — 精美排版，适合投递和打印
C. 两者都要 — Markdown + LaTeX
```

### 7.2 LaTeX 生成流程

如果用户选择 B 或 C（包含 LaTeX），按以下步骤操作：

1. **读取本文档**：获取模板结构和命令映射关系。
2. **内容映射**：将已收集的用户简历数据，按照第四章的映射表，逐模块转换为 LaTeX 命令。
3. **写入 .tex 文件**：
   - 文件路径：`C:/Users/Administrator/resume-builder/resume-<求职方向>.tex`
   - 文件结构：参照第五章的完整范例，替换占位符为真实数据。
4. **复制依赖文件**：从技能目录中复制 `resume.cls`、字体样式文件、`linespacing_fix.sty` 到同一输出目录。这些文件的源路径为：
   - `C:/Users/Administrator/.claude/skills/resume-builder/templates/resume.cls`
   - `C:/Users/Administrator/.claude/skills/resume-builder/templates/zh_CN-Adobefonts_internal.sty`
   - `C:/Users/Administrator/.claude/skills/resume-builder/templates/linespacing_fix.sty`
5. **提供编译指引**：向用户说明编译方式（优先推荐 Overleaf），并附上第六章的编译步骤。

### 7.3 内容适配原则

- **长度控制**：根据内容多少调整 `\vspace{}` 和列表项数量，确保不超过单页。
- **排序规则**：经历按时间倒序，最近的在最前。
- **STAR 量化**：确保每个 item 有具体数字（百分比、绝对数值），避免空洞描述。
- **一致性**：日期格式、标点符号（全角/半角）、列表项结尾标点统一。
- **技术栈精简**：技能列表控制为 4-5 个大类，每类不超过 8 项具体技能。
- **无占位符残留**：生成前确认所有 `<>` 包裹的占位符已被真实数据替换。

### 7.4 输出确认

生成 `.tex` 文件后，向用户展示：

```
已生成 LaTeX 简历文件：
  resume-<方向>.tex  →  C:/Users/Administrator/resume-builder/resume-<方向>.tex
  resume.cls         →  (已复制到同目录)

编译方式：
  推荐使用 Overleaf（在线，无需安装）
  1. 打开 https://www.overleaf.com
  2. 上传上述目录中的所有文件
  3. 编译器选择 XeLaTeX → 编译即可

  或本地编译（需安装 TeX Live）：
  xelatex resume-<方向>.tex
```
```

---

## 附录：resume.cls 关键样式参数

以下为 `resume.cls` 中的核心样式参数，供技能自定义修改时参考（正常使用时无需修改）。

| 参数 | 默认值 | 说明 |
|------|--------|------|
| 纸张大小 | A4 | `\LoadClass[11pt,a4paper]{article}` |
| 页边距 | 上下左右 = 1.2cm | `\RequirePackage[margin=1.2cm]{geometry}` |
| 正文字号 | 11pt | 在 `\LoadClass` 中定义 |
| `\name` 字号 | `\Huge`（约 25pt） | 姓名字号 |
| `\section` 字号 | `\Large`（约 17pt） | 节标题字号 |
| `\section` 线上间距 | 0.4pt 线条 | `\textcolor{lightgray}{\rule{\textwidth}{0.4pt}}` |
| 行间距 | `\linespread{1.2}` | 全局行间距倍数 |
| 链接颜色 | `\definecolor{links}{HTML}{2A1B81}` | 超链接颜色（深蓝色） |
| 节标题底部间距 | `-\parsep` | 标题与内容之间的间距 |
