# 📝 Obsidian LLM Notes

> AI-powered Obsidian workflow via natural language commands

**Obsidian LLM Notes** is an AI Skill that lets you create, organize, and manage structured Obsidian notes through simple commands like `day`, `note`, `idea`.

**Features:** Chinese-first, English-compatible, 10 core commands covering the full note lifecycle.

---

## 🚀 Quick Start

```bash
# 1. Clone the repo
git clone https://github.com/michelllsm/obsidian-wiki-notes.git

# 2. Tell your AI assistant
-setup

# 3. Follow the guided setup (5 phases, ~5 min)
# 4. Start taking notes!
day
```

---

## ✨ Core Commands

### 📥 Capture External Value

| Command | Natural Commands | When to use | Prompt Example | Output |
|---------|-----------------|-------------|----------------|--------|
| `-note` | `note`, `主题`, `收录` | External content summary (article/video/podcast). Auto-sorted by topic | `-note https://...` · `-note 关于 Karpathy LLM Wiki` | `📚Topics/<topic>/` |
| `-exp` | `exp`, `经验`, `沉淀` | Personal experience summary (practice review/method). Auto-sorted by topic | `-exp 完成xxx项目复盘` · `-exp 搭建AI工作流经验` | `📚Topics/<topic>/` |
| `-idea` | `idea`, `灵感`, `素材` | Mark content from others as creative material — potential source for your future output | `-idea 这篇文章的观点可以写成小红书` · `-idea 对标账号的选题角度不错` | `📚Topics/💭创作灵感/` |
| `-tool` | `tool`, `工具`, `查找`, `搜索` | 收录/搜索/推荐工具：Skill/工具/技巧，联动索引 | `-tool 收录 Cursor编辑器` · `-tool 搜索 用户体验` · `-tool 推荐 用户旅程图工具` | `🛠️tools/` |

### 🔄 Review, Output & Track

| Command | Natural Commands | When to use | Prompt Example | Output |
|---------|-----------------|-------------|----------------|--------|
| `-day` | `day`, `每日`, `日报` | Daily log: learnings, problems, tomorrow's plan. AI reads current conversation context | `-day` · `-day 今天学了 React Hooks` · `-day 只记关键事项` | `📅Daily/` |
| `-week` | `week`, `每周`, `周报` | Weekly review. AI auto-reads all daily notes from this week | `-week` · `-week 本周专注 AI 工具链` | `📆Weekly/` |
| `-output` | `output`, `创作`, `输出` | Original content or merge multiple notes into overview. Auto-sorted into sub-category | `-output 写一篇关于xxx` · `-output 整合 AI 工具笔记` | `📝Output/<sub>/` |
| `-todo` | `todo`, `任务`, `待办` | Track a task with status, context, and completion criteria | `-todo 完成 README 重写` · `-todo 调研竞品，周五前` | `📋Tasks/` |

### 🧹 Organize & Maintain

| Command | Natural Commands | When to use | Prompt Example | Output |
|---------|-----------------|-------------|----------------|--------|
| `-classify` | `classify`, `整理`, `归类` | Triage inbox — AI sorts each clipping to the right folder | `-classify` · `-classify 只整理最近3天` | from `Clippings/` |
| `-check` | `check`, `检查` | Health scan: orphan notes, stale content, missing links | `-check` · `-check 只看主题笔记` | _(report only)_ |

> **Tip:** The `-` prefix strengthens command recognition. Natural commands without `-` also work — AI understands by context.

### 📄 Command → Template Mapping

Each command **reads its template before generating content**, ensuring consistent formatting. Templates are fully customizable.

| Command | Template | Description |
|---------|----------|-------------|
| `-note` | [note-overview.md](templates/note-overview.md) | External content summary |
| `-exp` | [note-experience.md](templates/note-experience.md) | Personal experience review |
| `-idea` | [note-overview.md](templates/note-overview.md) | Reuses note template with "output direction" section enabled |
| `-day` | [daily-note每日笔记.md](templates/daily-note每日笔记.md) | Daily log |
| `-week` | [weekly-review每周复盘.md](templates/weekly-review每周复盘.md) | Weekly review |
| `-todo` | [todo任务.md](templates/todo任务.md) | Task tracking (domain kanban format) |
| `-output` | — | Free-form creation, no fixed template |
| `-tool` | — | Inline table format, no separate template |

> 💡 **Customize:** Edit any file in `templates/` directly, or tell your AI: "Modify the daily note template to add a mood field."

---

## 📁 Folder Structure

```
<vault>/                            对应指令：
├── 📅每日笔记/                    # day
├── 📚主题笔记/                    # -note / -exp（子目录按主题自动创建）
│   ├── index.md                   # 主题笔记根目录索引（自动生成）
│   └── 💭创作灵感/                  # -idea 固定落点
├── 📆每周笔记/                    # week
├── 📋待跟踪任务/                  # todo
├── 📝创作输出/                    # output（自动创建子分类）
├── 🛠️tools/                      # tool
│   ├── skills-index.md               # tool 自动写入
│   └── 🔧tools-index.md          # tool 自动写入
├── Clippings/                     # 收件箱（classify 扫描此处）
└── "obsidian-wiki-notes skill配置"/ # ⚠️ 含空格，是一个完整目录名
    ├── SKILL.md                   # 核心执行规范
    ├── templates/                 # 笔记模板（可以自己改）
    │   ├── note-overview.md        # note 模板（外部内容摘要）
    │   ├── note-experience.md     # exp 模板（个人经验沉淀）
    │   ├── todo任务.md            # todo 模板
    │   └── ...
    ├── references/                # 详细指令文档、开箱、拓展工具包（AI看的）
    └── README.md                  # 详细指令文档、开箱、拓展工具包（用户看的）
```

**Key design principles:**
- **Collected/Original Separation:** Collected content (`📚Topics/`) and original output (`📝Output/`) are always separate
- **Creative Material:** `idea` marks other people's content as potential source for your output — saved to `📚Topics/💭创作灵感/`
- **Auto Sub-categories:** `note` and `output` auto-analyze content theme and sort into sub-folders
- **Emoji Prefix:** All sub-category folders must use emoji prefix (e.g. `🤖AI工具/`). Individual note files don't require it
- **Emoji Path Safety:** Paths containing emoji must be double-quoted in shell commands with UTF-8 locale, and URL-encoded in HTML. See `references/command-reference指令参考手册.md` §10
- **Smart Archiving:** No matching category? AI creates a new one (with emoji)

---

## 🌍 Language Support

| Language     | Folder Example          | Output       |
| ------------ | ----------------------- | ------------ |
| 🇨🇳 中文      | `📅每日笔记/`, `📚主题笔记/`    | 纯中文          |
| 🇺🇸 English | `📅Daily/`, `📚Topics/` | Pure English |

---

## 🔄 Updates & Customization

### How to Update

```bash
git pull origin main
```

Your customizations are **always protected** — AI merges updates intelligently without overwriting your changes.

### How to Customize

Edit any file in `obsidian-wiki-notes skill配置/` directly, or tell your AI:

```
Help me modify the daily note template to add a "mood" field
```

Changes take effect immediately.

---

## 🧩 Extension Skills & Tools

After setup, install these to supercharge your workflow:

| Skill/Tool | What it does | Level | Setup |
|------------|-------------|-------|-------|
| **lark-cli** | Feishu/Lark CLI for priority content collection from docs/sheets | Recommended | `npm install -g @larksuiteoapi/cli` + `lark-cli auth login` |
| **Obsidian Web Clipper** | One-click web clipping to Obsidian inbox | Recommended | [Browser Extension](https://obsidian.md/clipper) |
| [xiaohongshu](https://github.com/xpzouying/xiaohongshu-mcp) | Search & collect Xiaohongshu (RedNote) content → auto-archive | Recommended | MCP start + QR login |
| [find-skills](https://skills.sh/) | Discover new Skills from the marketplace | Recommended | None |
| [self-improving-agent](https://skills.sh/) | Auto-record errors and improve over time | Recommended | None |

> **Why so few?** Most platforms (YouTube, Bilibili, WeChat Articles, X, etc.) work great with built-in web fetching — no extra Skill needed. Only platforms requiring search/interaction data (like Xiaohongshu) or Feishu integration need dedicated tools. See `references/Extended Toolkit拓展工具包.md` for the full routing table.

### Quick Install

```bash
# Feishu CLI (recommended for Feishu content)
npm install -g @larksuiteoapi/cli
lark-cli auth login

# Obsidian Web Clipper (browser extension)
# Chrome/Edge: https://chromewebstore.google.com/detail/obsidian-web-clipper/cnjifjpddelmedmihgijeibhnjdfpjhn
# Firefox: https://addons.mozilla.org/firefox/addon/obsidian-web-clipper/
```

---

## 🔧 Requirements

- [Obsidian](https://obsidian.md/download) (Free)
- **Recommended:** [Obsidian CLI](https://github.com/YakDao/obsidian-cli) (`npm install -g obsidian-cli`)

> No CLI? AI automatically falls back to filesystem mode. All core note creation, filing, and linking features work normally. Only "auto-open Obsidian window" and "Vault registration" require CLI.

### Platform Compatibility

This Skill is designed to work with any AI assistant that supports Skills/system prompts. Some notes:

- **Global memory path** depends on your AI client. For example, WorkBuddy uses `~/.workbuddy/`, other clients may differ. Adjust the path in Phase 4 of setup accordingly.
- **Extension Skills** (like `xiaohongshu`, `find-skills`) may have different installation methods depending on your AI platform. Check your platform's skill marketplace.
- The core note workflow (all `-day`, `-note`, `-task` etc. commands) works with **any AI assistant** — no platform-specific dependencies.

---

## 📄 License

[MIT](./LICENSE)

---

> **Note:** This Skill is designed as general rules. Feel free to modify to fit your workflow. See [SKILL.md](obsidian-wiki-notes%20skill配置/SKILL.md) for the AI execution spec, and [references/command-reference.md](command-reference指令参考手册.md) for detailed command reference.

**中文版:** [README_CN.md](README_CN.md)
