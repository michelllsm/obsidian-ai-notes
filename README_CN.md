# 📝 Obsidian AI Notes

> 通过自然语言指令驱动的 Obsidian AI 笔记工作流

**Obsidian AI Notes** 是一个 AI Skill，让你通过简单指令（如 `day`、`note`、`idea`）自动创建、归类和管理结构化的 Obsidian 笔记。

**特点：** 中文为主，英文兼容，10 个核心指令覆盖笔记全生命周期。

---

## 🚀 快速开始

```bash
# 1. 克隆仓库
git clone https://github.com/michelllsm/obsidian-ai-notes.git

# 2. 告诉 AI 助手
-setup

# 3. 跟着引导完成（5步，约5分钟）
# 4. 开始记笔记！
day
```

---

## ✨ 核心指令

### 📥 收录与记录

| 指令 | 自然指令 | 什么时候用 | Prompt 示例 | 输出目录 |
|------|---------|-----------|-------------|---------|
| `-day` | `day`、`每日`、`日报` | 一天结束时，记录今天学到的、遇到的问题、明天要做的。AI 自动读取当前对话上下文 | `-day` · `-day 今天学了 React Hooks` · `-day 只记关键事项` | `📅每日笔记/` |
| `-week` | `week`、`每周`、`周报` | 周末复盘。AI 自动读取本周所有每日笔记 | `-week` · `-week 本周专注 AI 工具链` | `📆每周笔记/` |
| `-note` | `note`、`主题`、`收录` | 收录文章/视频/播客——AI 提炼摘要。自动归入子分类 | `-note https://...` · `-note 关于 Karpathy LLM Wiki` | `📚主题笔记/<子分类>/` |
| `-idea` | `idea`、`灵感`、`素材` | 标记别人内容中可作为你未来输出来源的部分——创作素材来源 | `-idea 这篇文章的观点可以写成小红书` · `-idea 对标账号的选题角度不错` | `📚主题笔记/创作灵感/` |

### 📤 输出与跟踪

| 指令 | 自然指令 | 什么时候用 | Prompt 示例 | 输出目录 |
|------|---------|-----------|-------------|---------|
| `-output` | `output`、`创作`、`输出` | 原创内容，或整合多篇笔记为结构化总览。自动归入子分类 | `-output 写一篇关于xxx` · `-output 整合 AI 工具笔记` | `📝创作输出/<子分类>/` |
| `-task` | `task`、`任务`、`待办` | 跟踪任务：有背景、有完成标准、有进度 | `-task 完成 README 重写` · `-task 调研竞品，周五前` | `📋待跟踪任务/` |
| `-tool` | `tool`、`工具` | 记录新工具/Skill/技巧，自动更新索引文件 | `-tool Cursor 编辑器` · `-tool 小红书 Skill` | `🛠️tools/` |

### 🔄 整理与维护

| 指令 | 自然指令 | 什么时候用 | Prompt 示例 | 输出目录 |
|------|---------|-----------|-------------|---------|
| `-classify` | `classify`、`整理`、`归类` | 收件箱分类——AI 逐篇判断分类并归档 | `-classify` · `-classify 只整理最近3天` | 从 `Clippings/` 到各分类 |
| `-check` | `check`、`检查` | 健康扫描：孤立笔记、过期内容、缺失链接 | `-check` · `-check 只看主题笔记` | _(仅输出报告)_ |
| `-update` | `update`、`迭代` | 改进笔记工作流本身的点子 | `-update 加个心情字段` · `-update 建议自动标签` | `💡笔记工作流优化idea/` |

> **提示：** `-` 前缀用于加强指令识别，自然指令（不加 `-`）也能用。

---

## 📁 文件夹结构

```
<vault>/
├── 📅每日笔记/                    # day
├── 📚主题笔记/                    # note（自动子分类）、idea
│   ├── AI工具/                    # note 自动创建
│   ├── 创作灵感/                  # idea（创作素材来源）
│   └── .../                       # 动态扩展
├── 📆每周笔记/                    # week
├── 📋待跟踪任务/                  # task
├── 📝创作输出/                    # output（自动子分类）
├── 🛠️tools/                      # tool
│   ├── 💪skill-index.md           # tool 自动更新
│   └── 🔧tools-index.md          # tool 自动更新
├── 💡笔记工作流优化idea/           # update
├── Clippings/                   # 收件箱 + 原始档案（classify 扫描此处）
└── obsidian-ai-notes skill配置/
    ├── SKILL.md                   # 核心执行规则
    ├── templates/                 # 笔记模板
    ├── references/                # 参考文档
    │   └── command-reference.md
    └── README.md
```

**核心设计原则：**
- **收录/原创分离：** 收录内容（`📚主题笔记/`）和原创输出（`📝创作输出/`）永远分开
- **创作素材来源：** `idea` 标记别人内容中可作为你输出来源的部分——存入 `📚主题笔记/创作灵感/`
- **自动子分类：** `note` 和 `output` 自动分析内容主题，归入对应子文件夹
- **智能归档：** 没有匹配的分类？AI 自动创建一个

---

## 🌍 语言支持

| 语言 | 文件夹示例 | 输出语言 |
|------|-----------|---------|
| 🇨🇳 中文 | `📅每日笔记/`、`📚主题笔记/` | 纯中文 |
| 🇺🇸 English | `📅Daily/`、`📚Topics/` | Pure English |

---

## 🔄 更新与自定义

### 更新

```bash
git pull origin main
```

你的自定义**始终受保护**——AI 智能合并更新，不会覆盖你的修改。

### 自定义

直接编辑 `obsidian-ai-notes skill配置/` 里的任何文件，或者告诉 AI：

```
帮我修改每日笔记模板，加一个"心情"字段
```

修改立即生效。

---

## 🧩 扩展 Skill

开箱完成后，可安装以下 Skill 扩展能力：

| Skill | 效果 | 建议 | 首次配置 |
|-------|------|------|---------|
| [xiaohongshu](https://github.com/xpzouying/xiaohongshu-mcp) | 搜索/采集小红书内容 → 自动归档到笔记 | 推荐 | 需启动 MCP + 扫码登录 |
| [find-skills](https://skills.sh/) | 发现更多实用 Skill | 推荐 | 无 |
| [self-improving-agent](https://skills.sh/) | 自动记录错误并持续改进 | 推荐 | 无 |

> **为什么推荐列表很短？** YouTube、B站、公众号、X 等平台的信息采集通过内置工具已能获得足够质量的结果，无需额外安装 Skill。仅小红书等需要搜索/互动数据的平台才需要专属 Skill。详见 `references/拓展工具包.md`（已重命名为"拓展工具包"）。

---

## 🔧 安装要求

- [Obsidian](https://obsidian.md/download)（免费）
- **推荐：** [Obsidian CLI](https://github.com/YakDao/obsidian-cli)（`npm install -g obsidian-cli`）

> 没有 CLI？AI 会自动切换到文件系统模式。所有笔记创建、归类、链接功能正常，仅"自动打开 Obsidian 窗口"和"Vault 注册"需要 CLI。

### 平台兼容性

本 Skill 可搭配任何支持 Skills/系统提示词的 AI 助手使用：

- **全局记忆路径**取决于你的 AI 客户端。例如 WorkBuddy 使用 `~/.workbuddy/`，其他客户端路径可能不同，在开箱 Phase 4 时按实际调整即可。
- **扩展 Skill**（如 `xiaohongshu`、`find-skills`）的安装方式因平台而异，请参考你所用平台的 Skill 市场。
- 核心笔记工作流（所有 `-day`、`-note`、`-task` 等指令）**不依赖任何特定平台**，通用可用。

---

## 📄 许可证

[MIT](./LICENSE)

---

> **说明：** 本 Skill 设计为通用规则，可自由修改以适应你的工作流。详见 [SKILL.md](SKILL.md) 和 [references/command-reference.md](references/command-reference.md)。

**English version:** [README.md](README.md)
