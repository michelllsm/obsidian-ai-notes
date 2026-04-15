---
name: obsidian-wiki-notes
description: |-
  AI-powered Obsidian workflow via natural language commands.
  10 core commands: note, exp, idea, tool, day, week, output, todo, classify, check.
  Designed for Chinese users with English compatibility.

  Use when: User wants to take notes in Obsidian, create daily/weekly logs,
  track tasks, capture ideas, or build a structured knowledge base.
  Setup: "-setup", "配置笔记工作流", "初始化 obsidian"
version: 2.2.0
author: smingliang
---

# SKILL: obsidian-wiki-notes

> AI 执行规范：Obsidian 笔记工作流（中文为主，英文兼容）
> 版本：2.2.0 | 更新：2026-04-14

---

## 0. 触发条件

### 触发词
- **核心指令**: `-note`, `-exp`, `-idea`, `-tool`, `-day`, `-week`, `-output`, `-todo`, `-classify`, `-check`
- **自然指令**: `note`, `exp`, `idea`, `tool`, `day`, `week`, `output`, `todo`, `classify`, `check`, `主题`, `经验`, `灵感`, `工具`, `每日`, `每周`, `任务`, `整理`, `检查`
- **自然语言**: "帮我记笔记", "创建每日记录", "写周复盘", "记录灵感", "跟踪任务"
- **开箱**: `-setup`, "配置笔记工作流", "初始化 obsidian"

### 笔记库优先原则

```
执行优先级：
1. <vault>/obsidian-wiki-notes skill配置/SKILL.md（用户自定义）← 最高
2. 官方 Skill 文件（系统内置）
```

---

## 1. 核心指令

### 📥 收录外部价值信息

| 指令 | 自然指令 | 用途 | 落点 |
|------|---------|------|------|
| `-note` | `note`, `主题`, `收录` | 外部内容摘要（文章/视频/播客），按主题自动归类 | `📚主题笔记/<🎨主题>/` |
| `-exp` | `exp`, `经验`, `沉淀` | 个人经验沉淀（实践复盘/方法总结），按主题自动归类 | `📚主题笔记/<🎨主题>/` |
| `-idea` | `idea`, `灵感`, `素材` | 创作素材来源：别人内容中可作为输出来源的部分 | `📚主题笔记/💭创作灵感/` |
| `-tool` | `tool`, `工具`, `查找`, `搜索` | 工具收录/搜索/推荐：Skill/工具/技巧，联动索引 | `🛠️tools/` |

### 🔄 复盘输出与跟踪

| 指令 | 自然指令 | 用途 | 落点 |
|------|---------|------|------|
| `-day` | `day`, `每日`, `日报` | 每日记录：学到的、遇到的问题、明天计划 | `📅每日笔记/` |
| `-week` | `week`, `每周`, `周报` | 周复盘：汇总本周每日笔记 | `📆每周笔记/` |
| `-output` | `output`, `创作`, `输出` | 创作输出 / 多篇整合，自动归入子分类 | `📝创作输出/<🎨子分类>/` |
| `-todo` | `todo`, `任务`, `待办` | 任务跟踪：有完成标准、需持续跟进 | `📋待跟踪任务/` |

### 🧹 整理与维护

| 指令 | 自然指令 | 用途 | 落点 |
|------|---------|------|------|
| `-classify` | `classify`, `整理`, `归类` | `Clippings/` 整理：AI 逐篇判断分类并归档 | `Clippings/` → 各分类 |
| `-check` | `check`, `检查` | 健康检查：孤立笔记、过期内容、缺失链接 | 输出报告到对话 |

> **每个指令的详细执行流程、Prompt 示例**见 [[command-reference指令参考手册]]

---

## 2. 全局规则

### 2.1 YAML Frontmatter（强制）

每篇笔记头部必须包含 YAML frontmatter，**type 值必须与模板一一对应**：

| type 值            | 对应指令      | 对应模板                                   |
| ----------------- | --------- | -------------------------------------- |
| `note-overview`   | `-note`   | `templates/note-overview.md`           |
| `note-experience` | `-exp`    | `templates/note-experience.md`         |
| `daily`           | `-day`    | `templates/daily-note每日笔记.md`          |
| `weekly`          | `-week`   | `templates/weekly-review每周复盘.md`       |
| `todo`            | `-todo`   | `templates/todo任务.md`                  |
| `output`          | `-output` | —                                      |
| `tool`            | `-tool`   | —                                      |
| `idea`            | `-idea`   | `templates/note-overview.md`（启用输出方向章节） |

> ⚠️ **禁止使用未定义的 type 值**（如 topic-external、topic-personal 等旧值）。

### 2.2 模板强制读取（强制）

**AI 执行任何笔记创建指令时，必须先读取对应模板文件，再按模板结构生成内容。**

```
执行流程（不可跳过）：
1. 识别指令 → 查上方 type 表 → 确定模板文件
2. 读取 templates/<模板文件>.md → 获取完整结构
3. 按模板结构逐节填充内容
4. 禁止凭记忆或自由发挥生成结构
```

> ⚠️ **不读模板就写笔记 = 违规**。即使 AI "记得"模板结构，也必须每次重新读取。

### 2.3 收录与输出分离（强制）

```
📚主题笔记/     = 输入端：外部收录 + 创作素材
📝创作输出/     = 输出端：个人原创
绝不混放。
```

### 2.4 子分类归档（强制）

`note` / `exp` / `idea` / `output` 执行时，AI 必须：
1. 分析内容主题 → 扫描目标目录下已有子分类文件夹
2. 匹配到已有分类 → 归入；无匹配 → 新建
3. `idea` 固定归入 `📚主题笔记/💭创作灵感/`

**文件夹 emoji 前缀（强制）**：
- 所有子分类文件夹**必须** emoji 前缀（如 `🤖AI工具/`）
- emoji 由 AI 按主题语义判断，新建时先扫描已有风格保持一致
- 仅文件夹强制，单篇文件不要求

### 2.5 Emoji 路径安全（强制）

文件夹名含 emoji 时，**文件操作、shell 命令、HTML 生成** 必须遵守以下规则：

**文件操作**：
- 所有含 emoji 的路径必须用**双引号包裹**（shell/脚本/工具参数均适用）
- 禁止对 emoji 路径使用 glob 通配符展开，改用 `find` + 引号 或逐个指定
- 读写文件前先用 `ls` / `test -d` 验证路径存在，失败时立即用 NFC 规范化重试

**Shell 命令**：
- 所有含 emoji 路径的 shell 命令必须前置 `export LC_ALL=en_US.UTF-8`（确保 locale 支持多字节字符）
- macOS APFS 使用 NFD 编码，部分工具期望 NFC。**遇到路径找不到时**，用 Python 规范化：
  `python3 -c "import unicodedata,sys; print(unicodedata.normalize('NFC',sys.argv[1]))" "路径"`
- `grep` / `find` / `rg` 搜索含 emoji 的模式时，改用 `-F`（固定字符串）而非正则

**HTML / Markdown 生成**：
- HTML 文件中引用含 emoji 的路径时，emoji 必须用 **HTML 实体编码**（如 `📚` → `&#128218;`）或 **URL 编码**（`%F0%9F%93%9A`），不得裸写 emoji 字面量到 `href` / `src` 属性
- Markdown 文件内可直接使用 emoji 字面量（Obsidian 原生支持）
- 生成 `<meta charset="UTF-8">` 是 HTML 文件的**必选项**

**搜索工具兼容**：
- AI 调用 `search_file` / `search_content` 时，pattern 或 path 参数**不要包含 emoji**
- 改为先定位父目录（不含 emoji），再用 `list_dir` / `read_file` 逐层进入
- 若必须搜索 emoji 文件夹内容，用 `execute_command` + `find`/`ls` 替代

> 详细排错流程和代码示例见 [[command-reference指令参考手册]] §10

### 2.6 相关资源网络（自动构建）

AI 创建笔记时自动扫描并建立关联（同主题 / 同作者 / 核心概念 / 相关工具），填入模板的 `## 相关资源` 部分。

> 详细格式和概念卡片触发条件见 [[command-reference指令参考手册]] §3

### 2.7 信息源路由（强制）

涉及外部平台采集时：
1. 查询 `references/Extended Toolkit拓展工具包.md` 路由表
2. 匹配到 Skill → 检测是否已安装 → 未安装则提示
3. 降级顺序：专属 Skill > agent-reach > web_fetch > agent-browser

> 小红书采集有专项执行规则，见 [[小红书数据抓取规则]]。

### 2.8 `-tool` 索引联动

**三种模式：**
1. **收录模式**：` -tool 收录 [工具名]` - 收录新工具到索引
2. **搜索模式**：` -tool 搜索 [关键词]` - 在tools文件夹中检索工具
3. **推荐模式**：` -tool 推荐 [需求描述]` - 联网推荐合适工具

**自动联动：**
- Skill → `🛠️tools/skills-index.md`
- 其他工具 → `🛠️tools/🔧tools-index.md`
- 生成独立工具卡片

**搜索流程：**
1. 检索本地 `🛠️tools/` 文件夹所有文件
2. 匹配工具名称、功能描述、标签
3. 无结果时提供联网推荐选项

> 表格模板格式和防错要求见 [[command-reference指令参考手册]] §7

### 2.9 `-day` / `-week` 数据源

- **day**：自动读取当前对话 + 全局记忆 + 今日已有笔记
- **week**：自动读取本周所有每日笔记 + 当前对话 + 全局记忆

> ⚠️ day/week 执行时除了读取数据源，**仍然必须先 read_file 读取对应模板文件**（见 §2.2），按模板结构生成内容。

### 2.10 主题索引自动维护

`📚主题笔记/index.md` 是知识库的**轻量级全局视图**，兼具读取与写入双重角色：

**写入端**（新增笔记后更新）：
- `-note` / `-exp` / `-idea` / `-classify` 执行后，自动追加条目到 index.md
- `-check` 扫描后，更新 index.md 的「状态标记」区

**读取端**（执行指令前先读）：
- `-note` / `-exp` / `-idea` / `-classify` / `-output` / `-check` 执行时，**先读 index.md** 获取全局视图
- 用途：快速获取已有子分类、已有笔记列表、tags 分组、状态标记，避免全库扫描

> ⚠️ **先读后写**：凡是涉及 `📚主题笔记/` 的指令，执行前先读 index.md，执行后更新 index.md。

> Index 结构和维护规则见 [[command-reference指令参考手册]] §3 步骤4

---

## 3. 开箱流程（5 Phase）

> 必须先阅读**详细规范** [[onboarding-phases开箱流程]]。
> Phase 1 环境检测基线见 [[obsidian-环境检测与开箱基线]]。

| Phase | 名称       | 关键动作                      |     |
| ----- | -------- | ------------------------- | --- |
| 1     | 环境准备     | 检测 Obsidian/CLI/Node；确认语言 |     |
| 2     | Vault 接入 | 新建 or 接入已有知识库             |     |
| 3     | 骨架初始化    | 创建文件夹骨架；植入 Skill 文件       |     |
| 4     | 全局记忆     | 写入 MEMORY.md；试跑首条指令       |     |
| 5     | 扩展推荐     | 推荐并安装扩展 Skill/工具          |     |

### Phase 3 骨架

> ⚠️ `obsidian-wiki-notes skill配置` 是含空格的完整目录名，创建时必须引号包裹。

```text
<vault>/
├── 📅每日笔记/                    # -day
├── 📆每周笔记/                    # -week
├── 📚主题笔记/                    # -note / -exp
│   ├── index.md                   # 统一索引（自动维护）
│   ├── <🎨子主题>/                # 动态归类（emoji前缀强制）
│   └── 💭创作灵感/                # -idea 固定落点
├── 📝创作输出/                    # -output（自动子分类）
├── 📋待跟踪任务/                  # -todo
├── 🛠️tools/                      # -tool 工具/网站/skill收录
│   ├── skills-index.md              # tool 自动识别skill录入表格
│   └── 🔧tools-index.md          # tool 自动识别分类录入表格
├── Clippings/                     # 剪藏收件箱（-classify 扫描）
└── "obsidian-wiki-notes skill配置"/
    ├── SKILL.md
    ├── templates/
    └── references/
```

---

## 4. Vault 定位

```
路径检测优先级：
1. MEMORY.md 配置的主 Vault 路径
2. 当前工作空间含 .obsidian/
3. 环境变量 OBSIDIAN_VAULT_PATH
4. 询问用户 → 记录到 MEMORY.md
```

---

## 5. 语言检测

| 检测语言 | 输出 | 文件夹名 |
|---------|------|---------|
| 中文 | 纯中文 | `📅每日笔记/` |
| 英文 | 纯英文 | `📅Daily/` |

---

## 6. 自动化

首篇 `-day` 后建议日自动化，三篇笔记后建议周自动化。

> 定时任务配置与通知方案见 [[automation-guide 自动化与通知方案]]。

---

## 7. 更新与合并

- 官方更新以 `obsidian-wiki-notes 官方配置更新/` 送达
- 用户配置永不直接覆盖
- 用户可随时说「合并更新」手动触发
