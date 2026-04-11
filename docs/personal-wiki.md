# 个人知识库 Wiki 全览

---

## 概览
位置： `~/.claude/personal-vault/`
定位： 私人知识库，与工作vault和研究vault物理隔离，防止OpenClaw Agent访问私人内容
状态： 架构初始化完成（2026-04-06），内容录入

---

## 目录结构

personal-vault/
├── CLAUDE.md          ← 完整工作流规范（本库的"宪法"）
├── .gitignore         ← 排除 raw/chat, raw/docs, wiki/private, .obsidian/
├── raw/               ← 原始素材区（只读，LLM 不修改）
│   ├── chat/
│   │   ├── wechat/    ← 微信导出
│   │   ├── claude/    ← Claude 对话导出
│   │   └── chatgpt/   ← ChatGPT JSON 导出/
│   ├── voice/         ← 语音转写
│   ├── screenshots/   ← 截图
│   ├── handwritten/   ← 手写照片
│   ├── docs/          ← PDF/扫描件
│   ├── clips/         ← 网页剪藏
│   └── misc/          ← 未分类
├── wiki/              ← LLM 维护的结构化 Markdown
│   ├── index.md       ← 全局索引（LLM 每次先读这个）
│   ├── log.md         ← 操作日志
│   ├── journal/       ← 日记（YYYY-MM-DD.md）
│   ├── ideas/         ← 想法（slug.md，状态驱动）
│   ├── people/        ← 人物档案
│   ├── chatlogs/      ← 聊天记录摘要
│   ├── projects/      ← 个人项目/目标
│   ├── concepts/      ← 概念页
│   ├── private/       ← 私密文档
│   ├── sources/       ← 来源摘要
│   ├── outputs/       ←

---

# 工作流（CLAUDE.md 定义的8个工作流）

| 工作流 | 触发场景 | 核心动作 |
|--------|----------|----------|
| chat-ingest | 处理微信原始记录 | 提取决定/待办/重要信息 → 更新人物页 → 更新 index + log |
| ai-chat-ingest | 处理 Claude/ChatGPT 导出 | 识别主题 → 写 chatlog → 提取可复用 prompt/方法 → 更新 concepts |
| idea-capture | 新想法 | 以 seed 状态录入，growing 才进索引 |
| idea-review | 定期复盘 | 审查 seed/growing，建议合并/升级/归档 |
| journal-ingest | 日记录入 | 原汁原味转写，不做 AI 分析 |
| query | 查询知识库 | 先查 index.md → 读相关页 → 重要答案存 outputs/ |
| people-update | 发现人物信息 | 创建或更新人物档案 |
| lint | 维护 | 查孤立页、断链、超30天未更新的 seed 想法 |

---

## 模板工具箱（5个）

| 模板 | 对应目录 | 关键字段 |
|------|----------|----------|
| tpl-journal.md | wiki/journal/ | date, mood, tags, people |
| tpl-idea.md | wiki/ideas/ | date, status(seed/growing/done/archive), tags, type |
| tpl-person.md | wiki/people/ | name, relation, met, platforms |
| tpl-chatlog.md | wiki/chatlogs/ | date, platform, participants, message_count |
| tpl-project.md | wiki/projects/ | date, status, deadline, tags |

---

## 规范与约束

- 隐私边界： `wiki/private/` 内容仅在明确查询时提及，不主动引用
- 不跨库： 永远不与其他两个 vault 交叉引用
- 不删页： 过期内容归档，不删除
- 不改 raw/： 原始素材只读
- 引用格式： 统一用 `[[slug]]` 内链
- 双维护： 每次操作后同步更新 `index.md` 和 `log.md`

---

# 一个Prompt和工具清单实现Karpathy本地知识库

我把工具清单搞出来，和prompt一起直接复制发给你的 Claude Code / Codex 就能开搭。

## ⚠️ 架构：就三层

① 原始资料层（只读，LLM 不改）
你的论文、文章、笔记、截图、网页剪藏

② Wiki 层（LLM 写和维护，你只看）
结构化的 Markdown 文件：摘要、卡片、索引、综述，全部自动生成

③ Schema 层（告诉 LLM 怎么干活）
一个 `CLAUDE.md` 或 `AGENTS.md`，写清楚目录结构、命名规范、工作流程

---

# 工具清单：按层拆解

## 资料采集

| 工具 | 干嘛的 | 门槛 |
|------|--------|------|
| Obsidian Web Clipper | 浏览器一键把网页变成 Markdown | 装插件就行 |
| Jina Reader（r.jina.ai） | 任意 URL → 干净 Markdown, LLM 直接能读 | 免费 API |
| Zotero | 学术党必备，论文管理 + 一键导出 BibTeX | 免费 |
| Readwise | 把 Kindle / 微信读书 / Pocket 的标注自动同步到本地 | 付费 |
| MarkDownload | 另一个网页转 Markdown 的浏览器扩展 | 免费 |
| 微信输入法语音转文字 | 随手录想法，复制到笔记里 | 自带 |

## 知识库 IDE（你看 wiki 用的）

| 工具 | 干嘛的 | 门槛 |
|------|--------|------|
| Obsidian | 本地 Markdown 编辑器，图谱视图能看知识连接 | 免费，核心推荐 |
| obsidian-cli | 让 Agent 通过命令行操作 Obsidian | `brew install obsidian-cli` |
| Obsidian Dataview 插件 | 在笔记上跑查询，比如按标签列出所有论文 | 装插件 |
| Obsidian Marp 插件 | Markdown 直接生成幻灯片 | 装插件 |
| Logseq | Obsidian 的替代品，大纲式组织，也是本地 Markdown | 免费 |

## LLM Agent（帮你写和维护 wiki 的）

| 工具 | 特点 | 适合谁 |
|------|------|--------|
| Claude Code | 最稳，读写本地文件无障碍，适合单人 wiki | 有 API key 就行 |
| Codex (OpenAI) | 适合顺序执行，配合 AGENTS.md | 类似 |
| OpenClaw | 开源本地 Agent，支持多 Agent 编排 + 浏览器自动化 | 折腾党 |
| Cursor / Windsurf | 代码编辑器但也能操作 Markdown 文件 | 已经在用的 |
| Aider | 开源命令行 AI 编程助手，也能管 Markdown 仓库 | 极客 |

## 搜索和索引

| 工具 | 干嘛的 | 门槛 |
|------|--------|------|
| qmd | 本地 Markdown 语义搜索，BM25 + 向量混合 | `npm install -g @tobilu/qmd` |
| Ollama + Embedding 模型 | 本地跑向量化，配合 qmd 使用 | 需要 8G+ 内存 |
| ripgrep (rg) | 极速全文搜索，Agent 的 grep 神器 | `brew install ripgrep` |
| fzf | 模糊搜索，快速定位文件 | `brew install fzf` |

---

# 复制这段给你的 Agent

把上面和工具清单和下面prompt直接发给你的ClaudeCode 或 Codex：

```text
你是我的个人知识库管理员。请帮我在本地搭建一个 LLM Wiki 系统。

## 架构
三个目录：
- raw/      → 原始资料（你只读不改）
- wiki/     → 你生成并维护的 Markdown 知识库
- CLAUDE.md → 工作规范（我们一起写）

## Wiki 结构
wiki/
├── index.md       ← 所有页面的目录（每页一行：链接 + 一句话摘要）
├── log.md         ← 操作日志（## [日期] 操作类型 | 标题）
├── concepts/      ← 概念页（每个重要概念一个 .md）
├── entities/      ← 实体页（人物、项目、公司、工具）
├── sources/       ← 每个原始资料的摘要页
└── outputs/       ← 查询产出（综述、对比表、分析）

## 核心工作流

### 摄入（Ingest）
当我把新文件放进 raw/ 并告诉你处理时：
1. 读原文，和我讨论要点
2. 在 sources/ 写一页摘要
3. 更新 index.md
4. 更新所有相关的 concepts/ 和 entities/ 页面
5. 在 log.md 追加记录
一个资料可能涉及 10-15 个页面的更新。

### 查询（Query）
当我问问题时：
1. 先读 index.md 找到相关页面
2. 深入阅读这些页面
3. 综合回答，引用具体页面
4. 如果回答有价值，存为 outputs/ 下的新页面，更新 index.md

### 健康检查（Lint）
定期检查：
- 页面之间有没有矛盾
- 有没有孤立页面（没有任何链接指向它）
- 有没有提到但还没建页面的概念
- 有没有过时信息可以用新资料更新

## 规范
- 所有文件用 Markdown
- 页面之间用 [[wiki-links]] 互相引用
- 每个页面开头有 YAML frontmatter（tags, date, sources）
- index.md 按类别组织，每次摄入都更新
- log.md 每条格式：## [YYYY-MM-DD] ingest/query/lint | 标题

先帮我创建这个目录结构和一个空的 index.md + log.md。
然后阅读工具清单并根据我的本地电脑信息，选择适配的工具。
最后告诉我怎么开始，采取问答式协同工作。
