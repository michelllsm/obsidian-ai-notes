---
name: obsidian-ai-notes
description: |
  This skill enables AI agents to create, organize, and manage structured Obsidian notes 
  through natural language commands like -day, -note, -week. Designed for Chinese users 
  with English compatibility. Supports bilingual output, template-driven note generation, 
  and progressive automation.
  
  Use when: User wants to take notes in Obsidian via AI, create daily/weekly logs, 
  track tasks, or build a structured knowledge base with AI assistance.
version: 1.0.0
author: smingliang
---

# SKILL: obsidian-ai-notes

> AI 执行规范：Obsidian 笔记工作流 Skill（中文为主，英文兼容）
> 版本：1.0.0 | 更新：2026-04-07

---

## 0. When to Use This Skill / 触发条件

### Trigger Phrases / 触发词
- **Core commands / 核心指令**: `-day`, `-note`, `-week`, `-task`, `-tool`, `-output`, `-input`, `-update`
- **Chinese aliases / 中文别名**: `-每日笔记`, `-主题`, `-每周笔记`, `-待办`, `-跟踪`, `-工具`, `-创作`, `-输出`, `-灵感`, `-输入`, `-迭代`
- **Natural language / 自然语言**: "帮我记笔记", "创建每日记录", "写周复盘", "记录灵感", "跟踪任务"
- **Setup / 开箱**: "-setup", "配置笔记工作流", "初始化 obsidian"

### Do NOT Trigger / 不触发场景
- 单纯询问 Obsidian 软件使用问题（非 AI 笔记工作流）
- 要求导出/导入笔记文件（属于文件操作，非结构化笔记创建）
- 询问 Markdown 语法（属于格式问题，非工作流）

---

## 0.5 环境检测规范 / Environment Detection

**核心原则：让 AI "住在" Vault 里，而非"远程遥控"**

### 检测顺序（优先级）

```
1. 用户级 MEMORY.md 是否配置主 Vault 路径？
   └─ 是 → 使用该路径（支持跨工作空间执行）
   
2. 当前工作空间是否包含 .obsidian/ 文件夹？
   └─ 是 → 将当前工作空间视为 Vault
   
3. 环境变量 OBSIDIAN_VAULT_PATH 是否设置？
   └─ 是 → 使用该路径
   
4. 询问用户 Vault 位置
   └─ 记录到用户级 MEMORY.md，全局复用
```

### 跨工作空间执行

当用户在其他工作空间使用笔记指令时：
- AI 读取 `~/.workbuddy/memory/MEMORY.md` 获取主 Vault 路径
- 显式告知用户：「当前工作空间不是 Vault，将操作主 Vault：/path/to/vault」
- 执行指令后，笔记写入主 Vault 的对应文件夹

### 推荐路径

| 方式 | 命令 | 效果 |
|-----|------|------|
| **推荐** | `cd ~/Vault && workbuddy .` | AI 与 Vault 形成"内居"关系 |
| 次选 | 设置 `OBSIDIAN_VAULT_PATH` | 显式桥接，配置负担 |
| 避免 | 工作空间 ≠ Vault | 路径不一致，操作失败 |

### 不一致处理

如果检测到工作空间 ≠ Vault：
- 显式告知用户当前配置
- 提供选项：切换工作空间 / 配置 Vault 路径 / 继续（不推荐）

---

## 1. 核心概念

**obsidian-ai-notes** 让 AI Agent 直接操作 Obsidian 知识库，通过自然语言指令（如 `-day`、`-note`）自动创建、归类和管理结构化笔记。

**设计原则：**
- 中文为主，英文兼容
- 根据用户语言自动输出单一语言内容
- CLI 优先，文件系统兜底
- 渐进自动化（手动 → 半自动 → 全自动）

---

## 2. 语言检测与输出

### 2.1 检测层级（优先级）

1. **显式声明**（最高优先级）
   - 用户说："用中文" / "Use English"

2. **系统上下文**（如可访问）
   - `IDENTITY.md` → `language` 字段
   - `USER.md` → `preferred_language` 字段

3. **对话语境分析**
   - 用户消息的语言模式
   - 历史对话语言

4. **默认 fallback**
   - 中文（本 Skill 主要服务中文用户）

### 2.2 输出规则

**关键：根据检测到的语言，输出单一语言内容**

| 检测语言 | 输出语言 | 模板字段语言 | 文件夹名 |
|---------|---------|-------------|---------|
| 中文 (zh) | 纯中文 | 中文 | `📅每日笔记/` |
| 英文 (en) | 纯英文 | 英文 | `📅Daily/` |
| 其他 | 纯英文 | 英文 | `📅Daily/` |

**执行逻辑：**
```
检测语言 → 选择输出语言 → 用对应语言填充模板 → 生成单语笔记
```

模板文件里有双语注释（方便 AI 理解），但实际输出只保留目标语言。

---

## 3. 核心指令（精简版）

去掉 `-归类`、`-整合`，保留 8 个核心指令：

| 指令 | 英文别名 | 中文别名 | 用途 | 输出目录 |
|------|---------|---------|------|---------|
| `-day` | `-daily` | `-每日笔记` | 每日记录 | `📅每日笔记/` / `📅Daily/` |
| `-note` | `-topic` | `-主题` | 主题沉淀 | `📚主题笔记/` / `📚Topics/` |
| `-week` | `-weekly` | `-每周笔记` | 周复盘 | `📆每周笔记/` / `📆Weekly/` |
| `-task` | `-todo`, `-remind` | `-待办`, `-跟踪` | 任务跟踪 | `📋待跟踪任务/` / `📋Tasks/` |
| `-tool` | — | `-工具` | 工具卡片 | `🛠️tools/` |
| `-output` | `-out`, `-create` | `-创作`, `-输出` | 创作输出 | `📝创作输出/` / `📝Output/` |
| `-input` | `-inspiration` | `-灵感`, `-输入` | 灵感记录 | `📚主题笔记/` / `📚Topics/` |
| `-update` | `-iterate` | `-迭代` | 工作流优化 | `💡笔记工作流优化idea/` / `💡Ideas/` |

---

## 4. 开箱流程（5 Phase）

### Phase 1: 环境准备
1. 检测 Obsidian 是否安装
2. 检测 obsidian-cli 是否可用
3. 不可用则声明降级为文件系统模式

### Phase 2: 语言检测
```
"开始配置笔记工作流。

请选择你的偏好语言 / Please select your preferred language:
A: 中文 (Chinese)
B: English
C: 自动检测 (Auto-detect)

或直接告诉我：'用中文' 或 'Use English'"
```

### Phase 3: 骨架初始化
根据选择的语言：
- 中文 → 创建中文文件夹名，复制中文模板逻辑
- 英文 → 创建英文文件夹名，复制英文模板逻辑

### Phase 4: 首条指令演示
用 `-day` 演示，根据语言输出对应语言内容。

### Phase 5: 扩展 Skill 推荐
展示推荐安装的扩展 Skill（见第 7 节）。

---

## 5. 文件夹结构

### 中文用户
```
<vault>/
├── 📅每日笔记/                 # -day
├── 📚主题笔记/                 # -note, -input
├── 📆每周笔记/                 # -week
├── 📋待跟踪任务/               # -task
├── 📝创作输出/                 # -output
├── 🛠️tools/                  # -tool
├── 💡笔记工作流优化idea/        # -update
└── .ai-notes/                 # Skill 配置
    ├── SKILL.md
    ├── templates/
    └── README.md
```

### 英文用户
```
<vault>/
├── 📅Daily/                    # -day
├── 📚Topics/                   # -note, -input
├── 📆Weekly/                   # -week
├── 📋Tasks/                    # -task
├── 📝Output/                   # -output
├── 🛠️Tools/                   # -tool
├── 💡Ideas/                    # -update
└── .ai-notes/
```

---

## 6. 模板策略

**模板文件特点：**
- 文件内有双语注释（方便 AI 理解字段含义）
- 实际输出时根据用户语言只保留单一语言

**示例（每日笔记模板逻辑）：**

```markdown
# {{date}}

<!-- ZH: 今日关键收获 | EN: Key Takeaways -->
## {{section_takeaways}}
- 

<!-- ZH: 问题与结论 | EN: Problems & Conclusions -->
## {{section_problems}}
1. {{field_problem}}:
   {{field_conclusion}}:

<!-- ZH: 明日动作 | EN: Tomorrow's Actions -->
## {{section_actions}}
- [ ] 
```

实际生成时：
- 中文用户 → "## 今日关键收获"、"## 问题与结论"
- 英文用户 → "## Key Takeaways"、"## Problems & Conclusions"

---

## 7. Phase 5: 扩展 Skill 推荐

完成开箱后，根据用户需求推荐安装：

| Skill | 中文效果 | EN Effect | 建议级别 |
|-------|---------|-----------|---------|
| 小红书 / Xiaohongshu | 搜索/读取小红书笔记 → 一键入库 | Search/read Xiaohongshu notes → one-click import | 推荐 |
| X (Twitter) / X采集 | 搜索推文/线程 → 归档到主题笔记 | Search tweets/threads → archive to topics | 推荐 |
| YouTube Full | 视频字幕摘要 → 自动归档 | Video transcripts → auto-archive | 推荐 |
| Agent Reach | 多平台聚合搜索 | Multi-platform aggregation | 推荐 |
| Find Skills | 发现更多能力 | Discover more capabilities | 可选 |

---

## 8. 自动化（延迟触发）

- 完成第一篇 `-day` 后：询问是否开启每日自动化
- 累计三篇笔记后：询问是否开启周复盘自动化

支持：内置定时任务 / cron / GitHub Actions

---

## 9. 自定义

用户可随时：
- 编辑 `.ai-notes/templates/` 修改模板
- 告诉 AI 新文件夹名进行迁移
- 在根目录新建文件夹添加分类

---

## 10. 多平台注意事项

- **权限**：确保 Vault 目录有读写权限
- **Unicode**：支持 emoji 文件夹名
- **离线**：本地功能正常，外部采集需联网
- **同步**：iCloud/Git 同步时等待完成再切换设备

---

## 附录：English Summary

**obsidian-ai-notes** is an AI-powered Obsidian workflow Skill designed primarily for Chinese users with English compatibility.

**Key Features:**
- Auto-detects user language preference
- Outputs content in single language (ZH or EN)
- 8 core commands: `-day`, `-note`, `-week`, `-task`, `-tool`, `-output`, `-input`, `-update`
- 5-phase setup with CLI fallback
- Extensible via additional Skills (Xiaohongshu, X/Twitter, YouTube, etc.)

**Language Detection Priority:**
1. Explicit user statement
2. System context files (IDENTITY.md / USER.md)
3. Conversation analysis
4. Default: Chinese

**Folder Naming:**
- Chinese users: `📅每日笔记/`, `📚主题笔记/`, etc.
- English users: `📅Daily/`, `📚Topics/`, etc.

See full Chinese documentation above for detailed execution logic.
