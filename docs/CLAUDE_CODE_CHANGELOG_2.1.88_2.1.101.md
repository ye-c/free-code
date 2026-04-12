# Claude Code Changelog: 2.1.88 → 2.1.101

> 版本范围：2.1.88 (2026-03-28) → 2.1.101 (2026-04-10)
> 你的版本：2.1.87-dev (落后约 14 个版本)
> 数据来源：GitHub Releases、ClaudeLog、Reddit、code.claude.com/docs/en/changelog

---

## v2.1.101 (2026-04-10) — 14 项变更

### 安全修复
- **命令注入漏洞修复**：修复了 POSIX `which` fallback 中的命令注入漏洞（LSP 二进制检测路径）
- **Bash 权限绕过修复**：反斜杠转义 flag 可能被自动允许为只读导致任意代码执行
- **复合命令权限绕过修复**：修复了复合 Bash 命令在 auto/bypass-permissions 模式下绕过强制权限提示的问题
- **OTEL 敏感信息泄露修复**：`OTEL_LOG_USER_PROMPTS`/`OTEL_LOG_TOOL_DETAILS`/`OTEL_LOG_TOOL_CONTENT` 现已默认关闭

### 企业 TLS 代理
- **OS 证书存储**：默认信任 OS 证书存储，企业 TLS 代理无需额外配置

### 会话恢复
- **`--resume` 上下文丢失修复**：大会话中 loader 在死端分支而非活跃会话上锚定的问题
- **`--resume` 链恢复**：修复 subagent 消息落在主链写缝隙附近时桥接到无关 subagent 会话的问题
- **硬编码 5 分钟超时移除**：对本地 LLM 或长思考工作流的 API 超时配置现完全生效

### 新功能
- **`/team-onboarding`**：根据本地 Claude Code 使用情况生成团队新成员入职指南
- **`claude -p --resume`**：现在接受通过 `/rename` 或 `--name` 设置的会话标题
- **`/ultraplan` 和远程会话**：自动创建默认云环境，无需先进行 web 配置

### 改进
- Brief mode：Claude 响应纯文本而非结构化消息时自动重试一次
- Focus mode：Claude 写出更自包含的摘要
- 速率限制重试消息：显示具体达到的限制和重置时间
- 设置韧性：settings.json 中无法识别的 hook 事件名不再导致整个文件被忽略
- 插件 hooks：允许由托管设置强制启用的插件 hooks 在 `allowManagedHooksOnly` 设置下运行

### 杂项修复
- Web search in sub-agents 使用错误模型
- 信任对话框接受后信任功能未启用（从主目录运行时）
- 终端渲染稳定性改进
- Slash 命令建议可读性改进（长描述截断为 2 行）
- Tool hook 执行超时从 60 秒改为 10 分钟
- [VSCode] 权限请求的目标地选择器改为可点击

---

## v2.1.100 (2026-04-09) — 少量修复

### 修复
- 多个 Remote Control 问题：会话崩溃时 worktree 被移除、连接失败未记录在 transcript、本地会话的 brief mode 中出现虚假 "Disconnected" 指示器、`/remote-control` 在仅设置 `CLAUDE_CODE_ORGANIZATION_UUID` 时通过 SSH 失败

---

## v2.1.98 (2026-04-07) — 22 项变更

### 权限修复
- **false prompts 修复**：修复了 `cut -d /`、`paste -d /`、`column -s /`、`awk '{print $1}' file` 和包含 `%` 的文件名的误权限提示
- **prototype 属性名修复**：修复了与 JavaScript prototype 属性同名的权限规则导致 settings.json 被静默忽略的问题
- **agent team 权限修复**：修复了 agent team 成员使用 `--dangerously-skip-permissions` 时未继承 leader 权限模式的问题

### 安全修复
- **重定向修复**：`/dev/tcp/...` 或 `/dev/udp/...` 的重定向不再自动允许而是触发提示

### 网络修复
- **429 重试修复**：修复了服务器返回小 `Retry-After` 时在约 13 秒内耗尽所有重试的问题——指数退避现作为最小值应用
- **MCP OAuth 修复**：修复了 `oauth.authServerMetadataUrl` 配置在重启后令牌刷新时未被遵守的问题（影响 ADFS 等）
- **流式响应卡顿修复**：卡顿的流式响应超时改为回退到非流式模式

### 终端修复
- xterm 和 VS Code 集成终端中大写字母被降为小写（kitty 键盘协议激活时）
- macOS 文本替换删除触发词而非插入替换内容

### Voice Mode 修复
- 修复了在上一条 transcript 仍在处理时重新按住 push-to-talk 键导致泄漏数十个空格字符的问题

### 内存/性能修复
- `DISABLE_AUTOUPDATER` 未完全抑制 npm registry 版本检查
- Remote Control 权限处理条目在会话生命周期内未被释放的内存泄漏
- 失败的 background subagents 未向父 agent 报告部分进度

### 杂项修复
- Prompt-type Stop/SubagentStop hooks 在长会话上失败的问题
- 反馈调查在关闭时渲染的问题
- Fullscreen 模式中悬停在 MCP tool results 上崩溃
- 全屏模式中复制包装 URL 在换行处插入空格
- `--resume` 时文件编辑 diff 在文件大于 10KB 时从 UI 消失

---

## v2.1.97 (2026-04-07) — 21 项变更

### 新功能
- **NO_FLICKER 模式下的 Focus View Toggle (`Ctrl+O`)**：显示 prompt、一行 tool 摘要（含编辑 diffstats）和最终响应
- **`refreshInterval` status line 设置**：每 N 秒重新运行 status line 命令
- **`workspace.git_worktree`**: 当当前目录在 linked git worktree 内时添加到 status line JSON 输入
- **`● N running` 指示器**：在 `/agents` 中 agent 类型旁显示有 live subagent 实例
- **Cedar 政策文件语法高亮**（`.cedar`, `.cedarpolicy`）

### 权限改进
- `--dangerously-skip-permissions` 在批准受保护路径的写入后被静默降级为 accept-edits 模式
- Bash tool 权限检查加固：收紧 env-var 前缀和网络重定向周围的检查，减少常见命令的误提示
- Accept Edits 模式：自动批准带有安全 env var 或进程包装器的文件系统命令（如 `LANG=C rm foo`, `timeout 5 mkdir out`）
- Auto mode 和 bypass-permissions 模式：自动批准沙箱网络访问提示
- 沙箱：`sandbox.network.allowMachLookup` 现已在 macOS 上生效

### 图像处理
- 粘贴和附加的图像现在压缩到与通过 Read tool 读取的图像相同的 token 预算

### Bedrock 修复
- 修复了当 `AWS_BEARER_TOKEN_BEDROCK` 或 `ANTHROPIC_BEDROCK_BASE_URL` 设置为空字符串时 Bedrock SigV4 认证失败的问题

### MCP 修复
- 修复了 MCP HTTP/SSE 连接在服务器重新连接时积累约 50 MB/hr 未释放缓冲区的问题

---

## v2.1.96 (2026-04-06)

> 信息来源有限，主要变更来自 ClaudeLog 综合列表

### 关键修复
- 修复了 startup UI freeze（多个 OAuth connector 令牌刷新）
- 修复了 Read tool 大图像在长会话中的问题
- 修复了 heredoc 命令的误权限提示
- 修复了多个实例时插件安装丢失
- 修复了 connector 重连问题
- 修复了 background agent 通知缺少输出路径
- 修复了 `/permissions` 重复条目
- 修复了 `--print` 挂起与 team agents

---

## v2.1.94 (2026-04-06) — 25 项 CLI 变更

### 新 CLI 选项
- **`--bare`**：脚本场景使用
- **`--debug` / `-d [filter]`**：调试输出，过滤器支持 `api,hooks`、`!1p,!file` 等
- **`--brief`**：brief 输出模式
- **`CLAUDE_CODE_SIMPLE=1`**：简化输出
- **`/skill-name`**：skill slash 命令支持
- **`--mcp-debug`**：已弃用，请使用 `--debug`

### 变更
- 默认 effort 从 medium → high（API key 用户）
- `/effort` 命令的行为变更
- Hook 特定输出的 `sessionTitle` 支持

### 移除
- **`/tag` 命令**（已移除）
- **`/vim` 命令**（通过 `/config` → Editor mode 切换 vim mode）

### 关键修复
- 修复了 subagent 永久性以 "Could not determine pane count" 失败的问题（tmux 窗口被杀死或重新编号后）
- 修复了 prompt-type Stop hooks 在 small fast model 返回 `ok:false` 时错误失败的问题
- 修复了流式传输将数组/对象字段作为 JSON 编码字符串发出时的工具输入验证失败
- 修复了扩展思考产生空白文本块与真实内容一起时的 API 400 错误
- 修复了插件 MCP 服务器在会话开始时卡在 "connecting" 的问题（当复制了未认证的 claude.ai connector 时）

### 性能
- Write tool diff 计算速度提升（带 tabs/`&`/`$` 的大文件快 60%）
- Linux sandbox：npm 和 native builds 现都附带 `apply-seccomp` helper，恢复 unix-socket 阻塞

---

## v2.1.92 (2026-04-04) — 来自 ClaudeLog

### 新功能
- **`forceRemoteSettingsRefresh`**：策略设置，设置为时 CLI 在启动时阻止直到远程托管设置被新鲜获取，失败则退出
- **交互式 Bedrock 设置向导**：从登录屏幕可访问，指导完成 AWS 认证、区域配置、凭证验证和模型固定
- **按模型和缓存命中的 `/cost` 细分**：订阅用户可见
- **`/release-notes`**：现为交互式版本选择器
- **Remote Control 会话名称**：现使用主机名作为默认前缀

### 性能
- Write tool diff 计算速度提升 60%（大文件，带 tabs/`&`/`$`）

---

## v2.1.91 (2026-04-03)

### 新功能
- **MCP tool 结果持久化覆盖**：通过 `_meta["anthropic/maxResultSizeChars"]` 注解（最高 500K）
- **`disableSkillShellExecution`**：禁用内联 shell 执行（skills 和 commands）
- **多行 deep links**：`claude-cli://open?q=` 支持多行
- **插件可执行文件**：`bin/` 目录支持

### 性能
- `stripAnsi` 在 Bun 上通过 `Bun.stripANSI` 加速
- Edit tool 现使用更短的 `old_string` anchors，减少输出 tokens

### 修复
- 修复了 `--resume` 在大会话上导致 prompt 缓存完全未命中
- 修复了 transcript chain 在 `--resume` 上断裂导致丢失会话历史
- 修复了 `cmd+delete` 在多个终端上的行为问题（iTerm2, kitty, WezTerm, Ghostty, Windows Terminal）
- 修复了 plan mode 在远程会话中容器重启后丢失 plan files
- 修复了 `permissions.defaultMode: "auto"` 的 JSON schema 验证
- 修复了 `/feedback` 渲染问题

---

## v2.1.90 (2026-04-02) — 52 项 CLI 变更

### 新功能
- **`/powerup`**：交互式课程，通过动画演示教授 Claude Code 功能

### 安全修复
- **PowerShell 权限检查加固**：修复了尾随 `&` 后台作业绕过、`-ErrorAction Break` 调试器挂起、archive 提取 TOCTOU、解析失败回退降级问题

### 性能
- 消除了每次 turn 时 JSON.stringify MCP tool schemas 的缓存键查找
- SSE 传输现以线性时间处理大流式帧（之前是二次方）
- 带有长对话的 SDK 会话在 transcript 写入时不再二次方变慢
- `/resume` all-projects 视图并行加载项目会话

### 关键修复
- 修复了 `PreToolUse` hooks 退出码为 2 时未正确阻止工具调用
- 修复了 auto mode 不尊重显式用户边界的问题
- 修复了 rate-limit 选项对话框在达到使用限制后无限循环重新打开导致会话崩溃
- 修复了 `--resume` 对使用 deferred tools、MCP 服务器或自定义 agents 的用户首次请求完全缓存未命中
- 修复了 `Edit`/`Write` 在 PostToolUse format-on-save hook 在连续编辑之间重写文件时失败

### 移除
- `.husky` 现为受保护目录（acceptEdits 模式）

---

## v2.1.89 (2026-04-01) — 9 项 flag 变更，52 项 CLI 变更

### 新功能
- **PreToolUse hooks 的 `"defer"` 权限决策**：headless 会话可以暂停工具调用，通过 `-p --resume` 恢复并重新评估
- **`CLAUDE_CODE_NO_FLICKER=1`**：开启无闪烁 alt-screen 渲染和虚拟化回滚
- **`PermissionDenied` hook**：在 auto mode 分类器拒绝后触发，返回 `{retry: true}` 让模型重试
- **@ 提及类型建议中的命名 Subagents**
- **`MCP_CONNECTION_NONBLOCKING=true`**：`-p` 模式下跳过 MCP 连接等待

### 权限修复
- 修复了 hooks `if` 条件过滤不匹配复合命令（`ls && git push`）或带 env-var 前缀的命令（`FOO=bar git push`）

### 渲染/UI 修复
- 修复了滚动时回滚消失
- 修复了折叠搜索/阅读组徽章在重并行工具使用中重复出现
- 修复了 `notification invalidates` 未立即清除当前显示的通知
- 修复了提交后 prompt 短暂消失
- 修复了 Devanagari 和其他组合标记文本在 assistant 输出中被截断
- 修复了主屏幕终端上的渲染伪影
- 修复了点击展开悬停文本在浅色终端主题上几乎不可见

### Voice Mode 修复
- 修复了 macOS Apple Silicon 上 voice mode 未请求麦克风权限
- 修复了 Windows Terminal Preview 1.25 上 Shift+Enter 提交而非插入换行
- 修复了 voice push-to-talk 某些修饰符组合绑定未激活，Windows 上 voice mode 失败

### 历史/统计修复
- 修复了包含 CJK 或 emoji 的 prompt 历史条目在落在 `~/.claude/history.jsonl` 中 4KB 边界时被静默丢弃
- 修复了 `/stats` 排除 subagent 使用导致少算 tokens
- 修复了 `/stats` 在统计缓存格式更改时丢失超过 30 天的历史数据

### 工具修复
- 修复了 `-p --resume` 在 deferred tool 输入超过 64KB 或不存在 deferred 标记时挂起
- 修复了 `-p --continue` 未恢复 deferred tools
- 修复了 `claude-cli://` deep links 在 macOS 上未打开
- 修复了 MCP tool 错误仅在服务器返回多元素错误内容时截断为第一个内容块
- 修复了 skill reminders 和其他系统上下文在通过 SDK 发送带图像的消息时被丢弃
- 修复了 `Edit(//path/)` 和 `Read(//path/)` allow rules 检查解析后的符号链接目标
- 修复了 Edit/Write tools 在 Windows 上加倍 CRLF 并去除 Markdown 硬换行
- 修复了 `StructuredOutput` schema 缓存 bug 导致使用多个 schema 时约 50% 失败率
- 修复了大 JSON 输入在长运行会话中保留为 LRU 缓存键的内存泄漏
- 修复了从超大会话文件（超过 50MB）删除消息时崩溃
- 修复了 LSP 服务器崩溃后的僵尸状态——服务器现重启而不是失败直到会话重启

---

## v2.1.88 (2026-03-28)

### 新功能
- **`CLAUDE_CODE_NO_FLICKER=1`** 环境变量（部分功能在 2.1.89 完成）
- **`PermissionDenied` hook** 支持
- **`{retry: true}`** 返回值支持
- Named subagents 支持

### 信息来源
- Reddit r/ClaudeCode、ClaudeLog、Releasebot、code.claude.com/docs/en/changelog、newreleases.io

---

## 版本时间线

| 版本 | 日期 | 间隔 |
|------|------|------|
| 2.1.87 | 2026-03-29 | — |
| 2.1.88 | 2026-03-28 | -1 天 |
| 2.1.89 | 2026-04-01 | +3 天 |
| 2.1.90 | 2026-04-02 | +1 天 |
| 2.1.91 | 2026-04-03 | +1 天 |
| 2.1.92 | 2026-04-04 | +1 天 |
| 2.1.93 | (未公开发布) | — |
| 2.1.94 | 2026-04-06 | +2 天 |
| 2.1.95 | (未公开发布) | — |
| 2.1.96 | 2026-04-06 | 同日 |
| 2.1.97 | 2026-04-07 | +1 天 |
| 2.1.98 | 2026-04-07 | 同日 |
| 2.1.99 | (未公开发布) | — |
| 2.1.100 | 2026-04-09 | +2 天 |
| 2.1.101 | 2026-04-10 | +1 天 |

---

## 分类汇总（2.1.88 → 2.1.101）

### 安全相关
- 命令注入漏洞（LSP binary detection）
- Bash 权限绕过（反斜杠转义 flag）
- 复合命令绕过权限提示
- OTEL 敏感信息泄露
- PowerShell 4 个关键漏洞补丁
- `/dev/tcp` 和 `/dev/udp` 重定向修复

### 权限系统
- PreToolUse hook 支持 `"defer"` 决策
- PermissionDenied hook + `{retry: true}`
- 原型属性名导致 settings.json 被忽略
- `disableSkillShellExecution` 设置
- `sandbox.filesystem.allowWrite` 绝对路径修复

### 会话恢复（--resume）
- 大会话上下文丢失（loader 锚定错误分支）
- subagent 链桥接到无关会话
- 硬编码 5 分钟超时移除
- `-p --resume` 接受会话标题
- `-p --continue` 恢复 deferred tools
- prompt 缓存未命中修复

### 性能优化
- Write tool diff 计算 60% 提升
- `stripAnsi` Bun 加速
- SSE 传输线性时间处理
- 对话历史写入不再二次方变慢
- `/resume` all-projects 并行加载
- 滚动性能（yoga-layout → TypeScript 实现）

### 渲染/UI
- `CLAUDE_CODE_NO_FLICKER=1`
- Focus view toggle (Ctrl+O)
- 终端回滚消失修复
- 幽灵字符修复
- 复制选择未触发修复
- Ghostty/Kitty/WezTerm 终端退出后键盘模式问题
- Windows 行内响应流式传输禁用

### MCP 改进
- `_meta["anthropic/maxResultSizeChars"]` 覆盖（最高 500K）
- `MCP_CONNECTION_NONBLOCKING=true`
- OAuth CIMD/SEP-991 支持
- MCP HTTP/SSE 缓冲区泄漏修复（50 MB/hr）
- stdio MCP 服务器进程在退出后徘徊修复

### 内存/资源泄漏
- 长会话内存泄漏（虚拟滚动条历史副本）
- Remote Control 权限处理条目未释放
- 大 JSON 输入保留为 LRU 缓存键
- 工具结果文件从未清理
- MCP 连接缓冲区未释放

### 企业功能
- OS CA 证书存储信任（默认）
- `forceRemoteSettingsRefresh` 策略设置
- 交互式 Bedrock 设置向导
- `/team-onboarding` 命令
- `/ultraplan` 自动云环境创建

### 新命令/设置
- `/powerup` — 交互式课程
- `/team-onboarding` — 团队入职指南
- `/ultraplan` — 远程会话自动云环境
- `refreshInterval` status line 设置
- `workspace.git_worktree` status line 字段
- `● N running` 在 `/agents` 中
- Cedar 政策文件语法高亮
- `--bare` 标志
- `--debug/-d [filter]` 替代 `--mcp-debug`

### 已移除
- `/tag` 命令
- `/vim` 命令

---

## 本地开发参考

如果你想在本地实现这些功能或修复，以下是关键实现区域：

1. **权限系统**：`settings.json` hook 评估逻辑、`PreToolUse` / `PermissionDenied` hooks
2. **会话恢复**：`--resume` loader 的分支锚定逻辑、`cc log` 对话历史读写
3. **TLS/证书**：`CLAUDE_CODE_CERT_STORE` 环境变量处理、OS 证书存储读取
4. **MCP**：`_meta["anthropic/maxResultSizeChars"]` 注解处理、`MCP_CONNECTION_NONBLOCKING`
5. **内存泄漏**：虚拟滚动条的消息列表管理、LRU 缓存实现
6. **沙箱**：`apply-seccomp` helper、文件系统 allow rules 解析
7. **渲染**：`CLAUDE_CODE_NO_FLICKER` 的 alt-screen 渲染器实现
