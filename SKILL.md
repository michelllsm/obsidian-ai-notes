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

### 未开箱兜底拦截（强制规则）
当用户执行**任何非 `-setup` 指令**（如 `-note`、`-day` 等）时，AI 必须：
1. 检查当前工作区是否包含基础结构（如 `📅每日笔记` 或 `.obsidian` 文件夹），或全局记忆中是否已存有 Vault 路径。
2. **若判定为未初始化环境**，必须立刻拦截当前指令，并强制引导用户先执行 `-setup`：
   > ⚠️ 警告：检测到当前目录尚未初始化为笔记工作流。
   > 请先回复 `-setup` 让我帮你完成环境检查与基础骨架搭建，然后再记录笔记。

### 笔记库优先原则

```
执行优先级：
1. <vault>/obsidian-wiki-notes-skill配置/SKILL.md（用户自定义）← 最高
2. 官方 Skill 文件（系统内置）
```

---

## 1. 核心指令

### 📥 收录外部价值信息

| 指令      | 自然指令                     | 用途                                                     | 落点               |
| ------- | ------------------------ | ------------------------------------------------------ | ---------------- |
| `-note` | `note`, `主题`, `收录`       | 外部内容摘要（文章/视频/播客），按主题自动归类。**URL 触发采集时必须先执行 §2.7 信息源路由** | `📚主题笔记/<🎨主题>/` |
| `-exp`  | `exp`, `经验`, `沉淀`        | 个人经验沉淀（实践复盘/方法总结），按主题自动归类                              | `📚主题笔记/<🎨主题>/` |
| `-idea` | `idea`, `灵感`, `素材`       | 创作素材来源：别人内容中可作为输出来源的部分                                 | `📚主题笔记/💭创作灵感/` |
| `-tool` | `tool`, `工具`, `查找`, `搜索` | 工具收录/搜索/推荐：Skill/工具/技巧，联动索引                            | `🛠️tools/`      |

### 🔄 复盘输出与跟踪

| 指令 | 自然指令 | 用途 | 落点 |
|------|---------|------|------|
| `-day` | `day`, `每日`, `日报` | 每日记录：学到的、遇到的问题、明天计划 | `📅每日笔记/` |
| `-week` | `week`, `每周`, `周报` | 周复盘：汇总本周每日笔记 | `📆每周笔记/` |
| `-output` | `output`, `创作`, `输出` | 创作输出 / 多篇整合，自动归入子分类 | `📝创作输出/<🎨子分类>/` |
| `-todo` | `todo`, `任务`, `待办` | 任务跟踪：有完成标准、需持续跟进 | `📋待跟踪任务/` |

### 🧹 整理与维护

| 指令          | 自然指令                   | 用途                           | 落点                 |
| ----------- | ---------------------- | ---------------------------- | ------------------ |
| `-classify` | `classify`, `整理`, `归类` | `Clippings/` 整理：AI 逐篇判断分类并归档 | `Clippings/` → 各分类 |
| `-check`    | `check`, `检查`          | 健康检查：孤立笔记、过期内容、缺失链接          | 输出报告到对话            |

> **执行前必须读取**：每个指令的详细执行流程、Prompt 示例见 [[obsidian-wiki-notes-skill配置/references/command-reference指令参考手册.md]]
> 
> ⚠️ **规则**：执行 `-note`/`-exp`/`-idea`/`-day`/`-week`/`-output`/`-todo`/`-classify`/`-check` 指令前，**必须先 read_file 读取对应的 reference 章节**，禁止跳过直接执行。

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

### 2.2a 执行自检（强制）

AI 执行笔记创建前必须完成：**模板已读取** · **type 值合法** · **路径合规** · **文件存在性** · **YAML 完整** · **子分类 emoji**。任一项失败 → 立即停止并报告原因。

> 自检表格和失败处理细节见 [[obsidian-wiki-notes-skill配置/references/command-reference指令参考手册.md]] §附录C

### 2.2b 执行日志（强制）

**笔记写入类指令**（`-note`/`-exp`/`-idea`/`-day`/`-week`/`-output`/`-todo`）完成后，输出：
```
→ 指令: -note
→ 模板: note-overview（有模板时展示）
→ 目标路径: 📚主题笔记/🧠心理学思维模型/
→ 索引已同步：📚主题笔记/index.md 新增 🧠心理学思维模型 分类（1篇）
✓ 完成：一句话概括笔记内容
```

**非写入类指令**（`-check`/`-classify`/`-tool 搜索`）完成后，直接展示执行结果摘要。

**失败时**：
```
✗ 失败：原因说明
→ 建议：下一步操作
```

**规则**：
- ❌ 不展示：自检表格、重复日志框、中间探测过程
- 索引更新行仅在实际更新了 index.md 时展示
- 整个日志块不超过 5 行

### 2.3 收录与输出分离（强制）

```
📚主题笔记/     = 输入端：外部收录 + 创作素材
📝创作输出/     = 输出端：个人原创
绝不混放。
```

### 2.4 子分类归档（强制）

`note` / `exp` / `idea` / `output` 执行时，AI 必须：

1. **扫描目标目录**下的子分类文件夹（如 `📚主题笔记/🤖AI工具/` 下的子文件夹）
2. **匹配已有子分类** → 归入
3. **无匹配** → 直接放主题根目录，**不新建子分类**

**例外**：`idea` 固定归入 `📚主题笔记/💭创作灵感/`

**文件夹 emoji 前缀（强制）**：
- 所有子分类文件夹**必须** emoji 前缀（如 `🤖AI工具/`）
- emoji 由 AI 按主题语义判断，新建时先扫描已有风格保持一致
- 仅文件夹强制，单篇文件不要求

> ⚠️ **禁止过度细分**：不要为了单篇笔记创建子分类。示例：「Claude Code技巧」应归入「🤖AI工具/」根目录，而不是新建「🤖AI工具技巧/」子文件夹。

### 2.5 Emoji 路径安全（强制）

含 emoji 的路径必须：**双引号包裹** · **前置 `export LC_ALL=en_US.UTF-8`** · **HTML 中 URL 编码不裸写**。

> 排错流程、NFC/NFD 规范化、搜索工具兼容等代码示例见 [[obsidian-wiki-notes-skill配置/references/command-reference指令参考手册.md]] §10

### 2.6 相关资源网络（自动构建）

AI 创建笔记时自动扫描并建立关联（同主题 / 同作者 / 核心概念 / 相关工具），填入模板的 `## 相关资源` 部分。

> 详细格式和概念卡片触发条件见 [[obsidian-wiki-notes-skill配置/references/command-reference指令参考手册.md]] §3

### 2.7 信息源采集规则（`-note` / `-idea`/`-exp`/`-tool` 触发时强制执行）

> ⚠️ **当用户提供的 `-note` / `-idea`/`-exp`/`-tool内容包含 URL 时，AI 必须先执行本节路由规则确定采集方式，再进行内容获取。禁止跳过本节直接猜测或凭记忆选择工具。**
>
> **错误示例**：AI 自行决定用 agent-reach 或 opencli 而未查询路由表 → ❌ 违规
>
> **正确流程**：识别到 URL → 读本节路由 → 按优先级匹配工具 → 执行采集

#### 通用采集流程（所有平台）

```
用户提供 URL → AI 执行以下步骤：

Step 0: 检查内置 Skill（available_skills）
  ├── 目标平台有对应内置 Skill？
  │   ├── ✅ 是 → use_skill 调用该 Skill → 完成
  │   └── ❌ 否 → 继续 Step 1

Step 1: 查询 Extended Toolkit 路由表
  ├── 匹配到平台首选方案 → 检测是否可用
  │   ├── ✅ 可用 → 直接调用 → 完成
  │   └── ❌ 不可用 → 继续 Step 2
  └── 无匹配平台 → 跳到 Step 3

Step 2: 降级方案
  ├── web_fetch（内置，零配置）→ 完成
  └── 失败 → 提示用户提供带认证信息的完整链接

Step 3: 全部失败
  └── 告知用户当前无法获取，建议手动粘贴内容
```

#### 优先级总表

| 优先级 | 方式 | 适用场景 | 数据质量 | 零配置 |
|:---:|------|---------|:-------:|:-----:|
| **0** | 内置 Skill（use_skill） | 平台有专属 Skill 时 | ⭐⭐⭐⭐⭐ | ✅ 已装即用 |
| **1** | Extended Toolkit 首选 | 路由表中标注的方案 | ⭐⭐⭐⭐ | 视具体工具 |
| **2** | web_fetch（内置） | 所有平台的兜底 | ⭐⭐⭐（仅文字）| ✅ 永远可用 |
| **3** | agent-browser | 需要登录态的复杂操作 | ⭐⭐⭐⭐⭐ | ❌ 需配置 |

**规则：**
- 按域名匹配对应工具，不要跨 Skill 读 reference
- 对应 Skill 未安装时，降级到下一优先级
- 所有采集失败后，提示用户手动粘贴内容

#### 小红书专项（强制，直接执行不需查外部文件）

> 原则：**优先拿到 ≥90% 完整数据；降级是最后手段，不是第一反应。**

```
① 内置【小红书】Skill 可用？（检查 available_skills）
   └── ✅ 是 → use_skill "小红书" → 完成（搜索/详情/评论/互动全支持）

② 小红书 Skill 不可用 → MCP 运行中？（ps aux | grep xiaohongshu-mcp）
   └── ✅ 是 → 用 MCP → 完成（>95%完整，含图片+互动数据+评论）

③ MCP 未运行 → 检测是否已安装但未启动
   └── ✅ 已安装只是没启动 → 询问用户：
       「检测到你已安装小红书 MCP，需要启动才能获取完整数据。
        A: 帮我启动（AI 引导，约 30 秒）
        B: 跳过，先用降级方案」
       ├── 选 A → AI 启动 MCP → 回到 ② → 完成
       ├── 选 A 但启动失败 → 报告原因 → 进入 ④
       └── 选 B → 进入 ④

④ 不可用 → 降级抓取
   ├── 链接含 xsec_token → web_fetch（~50%，仅文字）→ 生成笔记
   ├── 裸链接 → web_fetch 尝试（可能只拿到部分内容）→ 生成笔记
   └── 完成后附提示（仅当从未安装 MCP 时）：
       「💡 当前仅获取到文字，缺少图片和互动数据。
        安装小红书 MCP 或使用小红书 Skill 可获完整体验。
        说"帮我安装小红书 MCP"即可，AI 全程引导约 3 分钟。」
```

**MCP 安装引导**（用户说"帮我装"时执行）：
1. 检测架构 → 下载对应二进制（macOS ARM/Intel）
2. `chmod +x` → 运行 login 工具 → 提示用户手机扫码
3. 启动 MCP 服务 → 验证 `localhost:18060` 可用
4. 用原链接重新抓取，对比展示完整内容

> 技术细节（二进制下载、登录、失败处理）见 [[obsidian-wiki-notes-skill配置/references/小红书数据抓取规则.md]]
> 全平台路由表见 [[obsidian-wiki-notes-skill配置/references/Extended Toolkit拓展工具包.md]]

### 2.8 主题索引自动维护

> ⚠️ **执行前必须阅读**：[[obsidian-wiki-notes-skill配置/references/command-reference指令参考手册.md]] §8-9

`📚主题笔记/index.md` 是知识库的**轻量级全局视图**：

**写入端**（新增笔记后更新）：
- `-note` / `-exp` / `-idea` / `-classify` 执行后，自动追加条目
- `-check` 扫描后，更新「状态标记」区

**读取端**（执行指令前先读）：
- `-note` / `-exp` / `-idea` / `-classify` / `-output` / `-check` 执行时，**先读 index.md** 获取全局视图
- 用途：快速获取已有子分类、已有笔记列表、tags 分组、状态标记，避免全库扫描

> ⚠️ **先读后写**：凡是涉及 `📚主题笔记/` 的指令，执行前先读 index.md，执行后更新 index.md。

> Index 结构和维护规则见 [[obsidian-wiki-notes-skill配置/references/command-reference指令参考手册.md]] §3 步骤4

---

## 3. 执行后输出规范（强制）

**笔记写入类指令**（`-note`/`-exp`/`-idea`/`-day`/`-week`/`-output`/`-todo`）完成后，必须输出：

```
→ 指令: -day
→ 模板: day-template
→ 目标路径: 📅每日笔记/2026-04-16.md
→ 索引变动：📅每日笔记/index.md 追加 1 条
✓ 完成：一句话概括笔记内容
```

**输出要素**（根据实际变动输出）：
- `→ 指令`：执行的指令名
- `→ 模板`：使用的模板文件名（如有）
- `→ 目标路径`：笔记最终存放的完整路径
- `→ 索引变动`：仅当索引有实际更新时输出
  - 新增分类：`新增 🧠心理学思维模型 分类（1篇）`
  - 更新计数：`🤖AI工具 计数更新（5→6篇）`
  - 仅追加条目：`追加 1 条到最近收录`
  - 无变动：`无`（或省略此行）
- `✓ 完成`：一句话概括笔记核心内容

**工具类指令**（`-tool`/`-classify`/`-check`）完成后，输出：
```
→ 指令: -classify
→ 目标索引: 🛠️tools/skills-index.md
→ 索引变动：新增 💻开发工具 分类（1条）
✓ 完成：一句话概括操作结果
```

---

## 4. 开箱流程（5 Phase）

> ⚠️ **执行前必须阅读**：[[obsidian-wiki-notes-skill配置/references/onboarding-phases开箱流程.md]]

| Phase | 名称 | 关键动作 | 参考文档 |
|-------|------|---------|---------|
| 1 | 环境准备 | 检测 Obsidian/CLI/Node；确认语言 | 开箱流程.md §Phase 1 |
| 2 | Vault 接入 | 环境校验与接入（检查 .obsidian） | 开箱流程.md §Phase 2 |
| 3 | 骨架初始化 | 检查并补全基础文件夹 | 开箱流程.md §Phase 3 |
| 4 | 全局记忆 | 写入全局/工作空间记忆；试跑首条指令 | 开箱流程.md §Phase 4 |
| 5 | 扩展推荐 | 推荐并安装扩展 Skill/工具 | 开箱流程.md §Phase 5 |

**Vault 定位优先级**：
1. MEMORY.md 配置的主 Vault 路径
2. 当前工作空间含 .obsidian/
3. 环境变量 OBSIDIAN_VAULT_PATH
4. 询问用户 → 记录到 MEMORY.md

**语言检测**：中文 → `📅每日笔记/` | 英文 → `📅Daily/`

> ⚠️ `obsidian-wiki-notes-skill配置` 是含连字符的完整目录名，创建时必须引号包裹。

---

## 5. 更新与合并

- 官方更新以 `obsidian-wiki-notes 官方配置更新/` 送达
- 用户配置永不直接覆盖
- 用户可随时说「合并更新」手动触发

---

## 8. 经验积累（LESSONS.md）

`obsidian-wiki-notes-skill配置/LESSONS.md` 记录 Skill 执行中踩过的坑和解法。

**写入时机（强制）**：
- AI 执行指令出错（抓取失败、路径找不到、模板不匹配等）
- 用户指出执行结果不对并给出修正

**写入格式**：
```
- YYYY-MM-DD | 指令/场景 | 问题 → 解法
```

**读取时机**：
- AI 执行指令前，先读 LESSONS.md 检查是否有同类已知问题
- 避免重复犯同样的错

> ⚠️ 只记有价值的经验（非一次性偶发问题），保持精简。
