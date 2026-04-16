	# 📝 Obsidian wiki Notes

> 通过自然语言指令驱动的 Obsidian AI 笔记工作流

**Obsidian wiki Notes** 是一个 AI Skill，让你通过简单指令（如 `-day`、`-note`、`-exp`）自动创建、归类和管理结构化的 Obsidian 笔记。

**特点：** 10 个核心指令覆盖笔记全生命周期。

---

## 🚀 5分钟开箱指南

⚠️ **关键前提**：为了让 AI 能够接管你的笔记库，必须确保 **Obsidian** 和 **AI (如 WorkBuddy)** 打开的是**完全相同的文件夹**。

#### Step 1: 获取模板仓库
打开终端，将模板克隆到你的常用目录（你可以将 `My-AI-Vault` 替换为你喜欢的文件夹名字）：
```bash
# 进入文稿目录 (或你习惯的任意目录)
cd ~/Documents 
# 克隆模板并命名为 My-AI-Vault
git clone https://github.com/michelllsm/obsidian-wiki-notes.git My-AI-Vault
```
*(操作后，你的电脑里会多出一个叫 `My-AI-Vault` 的文件夹，里面已经包含了基础的模板和 AI 规则)*

#### Step 2: 激活 Obsidian 配置
1. 启动 Obsidian 软件
2. 选择 **「打开已有仓库」 (Open folder as vault)**
3. 选中刚才克隆生成的 `My-AI-Vault` 文件夹
*(这一步很重要：只要用 Obsidian 打开过一次，它就会在里面自动生成必要的 `.obsidian` 隐藏配置)*

#### Step 3: 接入 AI 工作空间
1. 启动 WorkBuddy (或你使用的其他 AI IDE)
2. 点击新建任务/打开工作空间
3. **同样选中** `My-AI-Vault` 这个文件夹
*(现在，AI 已经成功潜入你的笔记库了！)*

#### Step 4: 一键初始化
在 WorkBuddy 的对话框中，直接向 AI 发送指令：
`-setup`

AI 会自动为你进行环境检查、补充缺失文件，并带你开启智能笔记之旅！

---

## ✨ 核心指令

### 📥 收录外部价值信息

| 指令      | 自然指令             | 什么时候用                        | Prompt 示例                                          | 输出目录             |
| ------- | ---------------- | ---------------------------- | -------------------------------------------------- | ---------------- |
| `-note` | `note`、`主题`、`收录` | 收录文章/视频/播客——AI 提炼摘要。按主题自动归类  | `-note https://...` · `-note 关于 Karpathy LLM Wiki` | `📚主题笔记/<主题>/`   |
| `-exp`  | `exp`、`经验`、`沉淀`  | 记录个人经验沉淀、项目复盘、方法总结。按主题自动归类   | `-exp 项目复盘：AI笔记工作流优化` · `-exp 费曼学习法实践`             | `📚主题笔记/<主题>/`   |
| `-idea` | `idea`、`灵感`、`素材` | 标记别人内容中可作为你未来输出来源的部分——创作素材来源 | `-idea 这篇文章的观点可以写成小红书` · `-idea 对标账号的选题角度不错`       | `📚主题笔记/💭创作灵感/` |
| `-tool` | `tool`、`工具`      | 记录新工具/Skill/技巧，自动更新索引文件      | `-tool Cursor 编辑器` · `-tool 小红书 Skill`             | `🛠️tools/`      |

### 🔄 复盘输出与跟踪

| 指令        | 自然指令               | 什么时候用                            | Prompt 示例                                        | 输出目录            |
| --------- | ------------------ | -------------------------------- | ------------------------------------------------ | --------------- |
| `-day`    | `day`、`每日`、`日报`    | 一天结束时，记录聊到的、今天学到的。AI 自动读取当前对话上下文 | `-day` · `-day 今天学了 React Hooks` · `-day 只记关键事项` | `📅每日笔记/`       |
| `-week`   | `week`、`每周`、`周报`   | 周末复盘。AI 自动读取本周所有每日笔记             | `-week` · `-week 本周专注 AI 工具链`                    | `📆每周笔记/`       |
| `-output` | `output`、`创作`、`输出` | 原创内容，或整合多篇笔记为结构化总览。自动归入子分类       | `-output 写一篇关于xxx` · `-output 整合 AI 工具笔记`        | `📝创作输出/<子分类>/` |
| `-todo`   | `todo`、`任务`、`待办`   | 跟踪任务：有背景、有完成标准、有进度               | `-todo 完成 README 重写` · `-todo 调研竞品，周五前`          | `📋待跟踪任务/`      |

### 🧹 整理与维护

| 指令 | 自然指令 | 什么时候用 | Prompt 示例 | 输出目录 |
|------|---------|-----------|-------------|---------|
| `-classify` | `classify`、`整理`、`归类` | 收件箱分类——AI 逐篇判断分类并归档 | `-classify` · `-classify 只整理最近3天` | 从 `Clippings/` 到各分类 |
| `-check` | `check`、`检查` | 健康扫描：孤立笔记、高频概念、过期内容、缺失链接 | `-check` · `-check 只看主题笔记` | _(仅输出报告)_ |

> **提示：** `-` 前缀用于加强指令识别，自然指令（不加 `-`）也能用。

### 📄 指令对应模板

每个指令生成笔记时会**强制读取对应模板**，确保格式统一。模板可自定义修改，改完立即生效。

| 指令        | 模板文件                                                   | 说明                    |
| --------- | ------------------------------------------------------ | --------------------- |
| `-note`   | [note-overview.md](obsidian-wiki-notes-skill配置/templates/note-overview.md)         | 外部内容摘要                |
| `-exp`    | [note-experience.md](obsidian-wiki-notes-skill配置/templates/note-experience.md)     | 个人经验沉淀                |
| `-idea`   | [note-overview.md](obsidian-wiki-notes-skill配置/templates/note-overview.md)         | 复用 note 模板，启用「输出方向」章节 |
| `-day`    | [daily-note每日笔记.md](obsidian-wiki-notes-skill配置/templates/daily-note每日笔记.md)       | 每日记录                  |
| `-week`   | [weekly-review每周复盘.md](obsidian-wiki-notes-skill配置/templates/weekly-review每周复盘.md) | 周复盘                   |
| `-todo`   | [todo任务.md](obsidian-wiki-notes-skill配置/templates/todo任务.md)                       | 任务跟踪（领域看板格式）          |
| `-output` | —                                                      | 自由创作，无固定模板            |
| `-tool`   | —                                                      | 表格内联定义，无独立模板          |

> 💡 **自定义模板**：直接编辑 `templates/` 下对应文件即可，或告诉 AI「帮我修改 xx 模板，加一个 xx 字段」。

---

## 📁 文件夹结构

```
<vault>/
├── 📅每日笔记/                    # -day 每日记录
├── 📚主题笔记/                    # -note / -exp（子目录按主题自动创建）
│   ├── index.md                   # 主题笔记根目录索引（自动生成）
│   └── 💭创作灵感/                  # -idea 固定落点
├── 📆每周笔记/                    # -week 周复盘
├── 📋待跟踪任务/                  # -todo 任务跟踪
├── 📝创作输出/                    # -output 创作输出（自动子分类）
├── 🛠️tools/                      # -tool 工具/网站/skill收录
│   ├── skills-index.md              # tool 自动识别skill录入表格
│   └── 🔧tools-index.md          # tool 自动识别分类录入表格
├── Clippings/                     # 插件一键剪藏收录（classify 扫描此处）
└── "obsidian-wiki-notes-skill配置"/ # ⚠️ 含空格，是一个完整目录名
    ├── SKILL.md                   # 核心执行规则
    ├── templates/                 # 笔记模板
    │   ├── note-overview.md        # -note 模板（外部内容摘要）
    │   ├── note-experience.md     # -exp 模板（个人经验沉淀）
    │   ├── todo任务.md            # -todo 模板
    │   └── ...
    ├── references/                # 参考文档
    │   └── command-reference.md
    └── README.md
```

**核心设计原则：**
- **收录/原创分离：** 收录内容（`📚主题笔记/`）和原创输出（`📝创作输出/`）永远分开
- **渐进式分类：** 单篇直接入库，同主题≥3篇时才建议建立子分类
- **创作素材来源：** `idea` 标记别人内容中可作为你输出来源的部分——存入 `📚主题笔记/💭创作灵感/`
- **自动子分类：** `note`、`exp`、`output` 自动分析内容主题，归入对应子文件夹
- **emoji 前缀：** 所有子分类文件夹必须使用 emoji 前缀（如 `🤖AI工具/`），单篇文件不要求
- **emoji 路径安全：** 含 emoji 的路径在 shell 命令中必须双引号包裹 + UTF-8 locale，HTML 中必须 URL 编码。详见 `references/command-reference指令参考手册.md` §10
- **智能归档：** 没有匹配的分类？AI 自动创建一个（带 emoji）

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

直接编辑 `obsidian-wiki-notes-skill配置/` 里的任何文件，或者告诉 AI：

```
帮我修改每日笔记模板，加一个"心情"字段
```

修改立即生效。

---

## 🧩 扩展 Skill 与工具

开箱完成后，可安装以下 Skill/工具 扩展能力：

| Skill/工具                                                    | 效果                   | 建议  | 首次配置                                                        |
| ----------------------------------------------------------- | -------------------- | --- | ----------------------------------------------------------- |
| **飞书CLI**                                                   | 飞书文档/表格/多维表格优先采集     | 推荐  | `npm install -g @larksuiteoapi/cli` + `lark-cli auth login` |
| **Obsidian Web Clipper**                                    | 网页一键裁剪到Obsidian收件箱   | 推荐  | [浏览器扩展](https://obsidian.md/clipper)                        |
| [xiaohongshu](https://github.com/xpzouying/xiaohongshu-mcp) | 搜索/采集小红书内容 → 自动归档到笔记 | 推荐  | 需启动 MCP + 扫码登录                                              |
| [find-skills](https://skills.sh/)                           | 发现更多实用 Skill         | 推荐  | 无                                                           |
| [self-improving-agent](https://skills.sh/)                  | 自动记录错误并持续改进          | 推荐  | 无                                                           |

> **为什么推荐列表很短？** YouTube、B站、公众号、X 等平台的信息采集通过内置工具已能获得足够质量的结果，无需额外安装 Skill。仅小红书等需要搜索/互动数据的平台，以及飞书集成场景才需要专属工具。详见 `references/Extended Toolkit拓展工具包.md`。


---

## 🔧 安装要求

- [Obsidian](https://obsidian.md/download)（免费）
- **推荐：** [Obsidian CLI](https://github.com/YakDao/obsidian-cli)（`npm install -g obsidian-cli`）

> 没有 CLI？AI 会自动切换到文件系统模式。所有笔记创建、归类、链接功能正常，仅"自动打开 Obsidian 窗口"和"Vault 注册"需要 CLI。

### 平台兼容性

本 Skill 可搭配任何支持 Skills/系统提示词的 AI 助手使用：

- **全局记忆路径**取决于你的 AI 客户端。例如 WorkBuddy 使用 `~/.workbuddy/`，其他客户端路径可能不同，在开箱 Phase 4 时按实际调整即可。
- **扩展 Skill**（如 `xiaohongshu`、`find-skills`）的安装方式因平台而异，请参考你所用平台的 Skill 市场。
- 核心笔记工作流（所有 `-day`、`-note`、`-todo` 等指令）**不依赖任何特定平台**，通用可用。

---

## 📄 许可证

[MIT](./LICENSE)

---

> **说明：** 本 Skill 设计为通用规则，可自由修改以适应你的工作流。详见 [SKILL.md](obsidian-wiki-notes-skill配置/SKILL.md) 和 [references/command-reference.md](obsidian-wiki-notes-skill配置/references/command-reference指令参考手册.md)。

**English version:** [README.md](obsidian-wiki-notes-skill配置/README.md)
