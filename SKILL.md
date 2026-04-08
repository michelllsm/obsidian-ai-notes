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

### 自动触发机制（基于用户记忆）

当 AI 读取到 `~/.workbuddy/memory/MEMORY.md` 中包含以下配置时，**自动激活**本 Skill：

```markdown
## AI Notes 工作流配置
- **主笔记库路径**: `/path/to/vault`
- **自动触发**: 当用户使用笔记指令或说"记笔记"等时，自动识别并执行
```

**自动触发场景**：
1. 用户在任何对话中使用配置的指令（如 `-day`）
2. 用户说"记笔记"、"写日报"、"记录灵感"等与笔记相关的自然语言
3. 用户要求"总结并保存"、"归档到笔记库"等涉及笔记存储的操作

AI 自动：
- 识别用户意图
- 读取 MEMORY.md 获取 Vault 路径
- 执行对应指令
- 无需用户显式提及 "obsidian-ai-notes"

### 笔记库优先原则 / Vault-First Rule

**核心规则**：AI 优先使用笔记库内的 Skill 文件

```
执行优先级：
1. <vault>/.ai-notes/SKILL.md（用户自定义版本）← 最高优先级
2. 官方 Skill 文件（系统内置）
```

**好处**：
- 用户通过自然语言修改笔记库里的 SKILL.md，即时生效
- 个性化工作流完全可控
- 官方更新不会覆盖用户修改

**更新机制**：
当官方有更新时，AI 提示用户并询问：
- A: 查看更新内容（对比差异）
- B: 合并更新（智能合并官方更新 + 保留用户修改）
- C: 跳过更新
- D: 用官方版本覆盖

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

### Phase 3: 骨架初始化 + Skill 文件植入

**Step 1: 创建笔记骨架**
根据选择的语言：
- 中文 → 创建中文文件夹名
- 英文 → 创建英文文件夹名

**Step 2: 植入 Skill 文件到笔记库** ⭐

将官方 Skill 文件复制到 `<vault>/obsidian-ai-notes skill配置/`：

```
<vault>/
└── obsidian-ai-notes skill配置/
    ├── SKILL.md              # 执行规范（用户可修改）
    ├── README.md             # 使用文档
    ├── templates/            # 模板文件
    └── update-checker.md     # 更新检查记录
```

**告知用户**：
```
📦 Skill 文件已植入笔记库！
位置：<vault>/obsidian-ai-notes skill配置/

好处：
• 直接暴露在 Obsidian 侧边栏，随时可见可改
• 你可以像改笔记一样修改 SKILL.md，AI执行时优先遵循你的笔记skill，而非官方
• 用自然语言告诉 AI "帮我修改模板"或直接修改templates，即时生效

```

**Step 3: 展示完整骨架结构**

```
📦 你的笔记库骨架已搭建完成！

<vault>/
├── 📅每日笔记/                    # -day 指令落点
│   └── YYYY-MM-DD-每日笔记.md
├── 📚主题笔记/                    # -note, -input 指令落点
│   ├── 主题分类/
│   └── 创作灵感/
├── 📆每周笔记/                    # -week 指令落点
│   └── YYYY-MM-DD-每周复盘.md
├── 📋待跟踪任务/                  # -task 指令落点
│   └── 任务名.md
├── 📝创作输出/                    # -output 指令落点
├── 🛠️tools/                      # -tool 指令落点
│   └── 工具名/
├── 💡笔记工作流优化idea/           # -update 指令落点
└── obsidian-ai-notes skill配置/   # Skill 文件（可修改）
    ├── SKILL.md
    ├── README.md
    └── templates/

好处：
✅ 所有内容都有固定归宿，不再散落各处
✅ 用指令快速创建，无需手动找位置
✅ 侧边栏结构清晰，一眼找到所需
✅ AI 理解你的组织方式，协作更顺畅
```

### Phase 4: 用户级记忆配置 + 首条指令演示

**Step 1: 询问是否配置全局记忆**

```
📍 是否配置全局记忆？

配置后，你在其他对话窗口、其他工作空间都能直接使用笔记指令，
无需手动加载 Skill，AI 会自动识别并执行。

A: 配置全局记忆（推荐）
B: 跳过，仅当前工作空间可用
```

**Step 2: 如果用户选择 A，自动配置用户记忆**

写入 `~/.workbuddy/memory/MEMORY.md`：

```markdown
## AI Notes 工作流配置
- **主笔记库路径**: `<当前工作空间绝对路径>`
- **笔记工具**: Obsidian（本地 Markdown）
- **可用指令**: `-day`, `-note`, `-week`, `-task`, `-tool`, `-output`, `-input`, `-update`
- **自动触发**: 当用户使用以上指令，或说"记笔记"、"写日报"、"记录灵感"、"跟踪任务"等时，自动识别为笔记操作意图并调用 obsidian-ai-notes Skill
- **跨对话支持**: 任何工作空间、任何对话窗口均可直接调用
- **执行优先级**: 优先使用笔记库内 .ai-notes/SKILL.md，其次官方 Skill
```

**Step 3: 告知用户好处+: 首条指令演示**

```
✅ 全局记忆已配置！

现在你可以：
• 在任何对话窗口直接使用 -day、-note 等指令
• 在其他工作空间（如项目文件夹）也能操作笔记库
• 直接说"帮我记笔记"，AI 自动识别并执行
• 无需每次手动加载 Skill
• 修改 <vault>/.ai-notes/SKILL.md 即时生效

试试输入：-note obsidian-ai-notes功能介绍以及应用场景
```

### Phase 5: 扩展 Skill 推荐

**AI 与用户对话**：

```
🚀 基础笔记工作流已就绪！

想让你的笔记系统更强大吗？推荐安装以下扩展 Skill，
安装后，AI 更顺畅地可以采集更多平台的信息源：

┌─────────────────────────┬──────────┬──────────────────────────────────────────────┐
│ Skill                   │ 级别     │ 用途                                         │
├─────────────────────────┼──────────┼──────────────────────────────────────────────┤
│ obsidian-ai-notes       │ 必需     │ 核心笔记工作流（当前已安装）                  │
│ obsidian                │ 必需     │ Obsidian 自动化操作                          │
│ xiaohongshu             │ 推荐     │ 小红书内容采集与整理                         │
│ youtube-full            │ 推荐     │ YouTube 完整工作流（下载/转录/总结）          │
│ video-summary           │ 推荐     │ 多平台视频总结（B站/抖音/YouTube/本地等）     │
│ agent-reach             │ 推荐     │ AI互联网访问（Twitter/X、Reddit、B站、抖音等16+平台）│
│ agent-browser           │ 推荐     │ 浏览器自动化（网页测试、截图、数据提取）       │
│ find-skills             │ 推荐     │ 发现更多实用 Skill                           │
│ self-improving-agent    │ 推荐     │ 自动记录错误并持续改进                       │
└─────────────────────────┴──────────┴──────────────────────────────────────────────┘

📍 是否现在安装扩展 Skills？

A: 安装推荐 Skills
B: 跳过，以后再说
```

**如果用户选择 A**：
执行安装命令，并告知安装进度和完成后的使用方法。

**如果用户选择 B：
```
好的，已跳过扩展 Skill 安装。

随时可以通过以下方式安装：
• 说 "帮我安装小红书 Skill"
• 或运行：find-skills xiaohongshu
```


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
└── obsidian-ai-notes skill配置/  # Skill 配置（可修改）
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
└── obsidian-ai-notes skill配置/
```

---

## 6. 模板策略

### 6.1 模板优先级（覆盖机制）

AI 按以下顺序查找模板：

```
1. obsidian-ai-notes skill配置/templates/（用户自定义）
   └─ 存在 → 使用用户模板（不会被更新覆盖）
   
2. templates/（官方模板）
   └─ 使用官方模板
```

**好处**：更新 Skill 时，用户的个性化模板安全保留。

### 6.2 模板文件特点
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

### 6.3 自定义模板方法

```bash
# 1. 复制官方模板到用户目录
cp templates/daily-note每日笔记.md .ai-notes/user/templates/

# 2. 修改用户目录下的模板
# 编辑 .ai-notes/user/templates/daily-note每日笔记.md

# 3. 下次使用 -day 时，AI 自动使用你的自定义模板
```

---

## 8. 自动化（延迟触发）

- 完成第一篇 `-day` 后：询问是否开启每日自动化
- 累计三篇笔记后：询问是否开启周复盘自动化

支持：内置定时任务 / cron / GitHub Actions

---

## 9. 自定义

用户可随时：
- 编辑 `obsidian-ai-notes skill配置/templates/` 修改模板
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
