---
name: obsidian-ai-notes
description: |-
  AI-powered Obsidian workflow via natural language commands.
  10 core commands: note, exp, idea, tool, day, week, output, todo, classify, check.
  Designed for Chinese users with English compatibility.

  Use when: User wants to take notes in Obsidian, create daily/weekly logs,
  track tasks, capture ideas, or build a structured knowledge base.
  Setup: "-setup", "配置笔记工作流", "初始化 obsidian"
version: 2.0.0
author: smingliang
---

# SKILL: obsidian-ai-notes

> AI 执行规范：Obsidian 笔记工作流（中文为主，英文兼容）
> 版本：2.0.0 | 更新：2026-04-10

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
1. <vault>/obsidian-ai-notes skill配置/SKILL.md（用户自定义）← 最高
2. 官方 Skill 文件（系统内置）
```

---

## 1. 核心指令

### 📥 收录外部价值信息

| 指令      | 自然指令               | 用途                            | 落点                                |
| ------- | ------------------ | ----------------------------- | --------------------------------- |
| `-note` | `note`, `主题`, `收录` | 外部内容摘要（文章/视频/播客），自动归入子分类 | `📚主题笔记/note-overview摘要/<子分类>/`   |
| `-exp`  | `exp`, `经验`, `沉淀`  | 个人经验沉淀（实践复盘/方法总结），自动归入子分类      | `📚主题笔记/note-experience经验/<子分类>/` |
| `-idea` | `idea`, `灵感`, `素材` | 创作素材来源：别人内容中可作为输出来源的部分        | `📚主题笔记/创作灵感/`                    |
| `-tool` | `tool`, `工具`         | 工具收录：Skill/工具/技巧，联动索引 | `🛠️tools/`     |

### 🔄 复盘输出与跟踪

| 指令        | 自然指令                 | 用途                    | 落点              |
| --------- | -------------------- | --------------------- | --------------- |
| `-day`  | `day`, `每日`, `日报`  | 每日记录：学到的、遇到的问题、明天计划           | `📅每日笔记/`                         |
| `-week` | `week`, `每周`, `周报` | 周复盘：汇总本周每日笔记                  | `📆每周笔记/`                         |
| `-output` | `output`, `创作`, `输出` | 创作输出 / 多篇整合，自动归入子分类   | `📝创作输出/<子分类>/` |
| `-todo`   | `todo`, `任务`, `待办`   | 任务跟踪：有完成标准、需持续跟进      | `📋待跟踪任务/`      |

### 🧹 整理与维护

| 指令          | 自然指令                   | 用途                          | 落点                 |
| ----------- | ---------------------- | --------------------------- | ------------------ |
| `-classify` | `classify`, `整理`, `归类` | `Clippings/`整理：AI 逐篇判断分类并归档 | `Clippings/` → 各分类 |
| `-check`    | `check`, `检查`          | 健康检查：孤立笔记、过期内容、缺失链接         | 输出报告到对话            |

---

> **更详细的执行流程**见 `references/command-reference.md`

---

## 3. 全局规则

### 3.1 YAML Frontmatter（强制）

每篇笔记头部必须包含：

```yaml
---
created: YYYY-MM-DD HH:mm
modified: YYYY-MM-DD HH:mm
type: daily | topic-external | topic-personal | weekly | task | tool | output | idea
source: 原始链接（如有）
tags: [标签1, 标签2]
---
```

### 3.2 相关资源网络（自动构建）

AI 创建笔记时自动扫描并建立关联：

**扫描维度**：
1. **同主题**：内容主题相似的已有笔记
2. **同作者/来源**：同一作者或平台的其他内容
3. **核心概念**：文中反复提及的关键概念（3次+建议建概念卡片）
4. **相关工具**：文中提到的工具、平台、Skill

**输出格式**（填入 `## 相关资源`）：
```markdown
- 原文: [标题](URL)
- 同主题: [[笔记A]] | [[笔记B]]
- 同作者: [[作者的其他文章]]（如有）
- 核心概念: [[概念_xxx]] → 提及3次+建议建概念卡片
- 相关工具: [[工具名]]（如有）
```

**概念卡片触发条件**：
- 某概念在 ≥3 篇笔记中被提及
- 该概念值得独立成页（有明确定义、应用场景、关联内容）
- AI 建议创建，用户确认后执行

**output 类笔记**：在 frontmatter 标注 `references: [来源笔记]`

### 3.3 收录与输出分离（强制）

```
📚主题笔记/     = 输入端：外部收录 + 创作素材
📝创作输出/     = 输出端：个人原创

绝不混放。
```

### 3.4 主题笔记自动子分类归档

`note` / `idea` 执行时，AI 必须：
1. 分析内容所属主题
2. 扫描 `📚主题笔记/` 下已有子分类文件夹
3. 匹配到已有分类 → 归入对应子目录（如 `📚主题笔记/AI工具/`）
4. 无匹配分类 → 自动创建新子分类文件夹
5. `idea` 固定归入 `📚主题笔记/创作灵感/`

> 已有分类示例：`AI工具/`、`成长思考/`、`创作灵感/`、`产品设计/` 等，根据用户笔记库实际内容动态扩展。

### 3.5 创作输出自动子分类归档

`output` 执行时，AI 必须：
1. 分析内容主题
2. 扫描 `📝创作输出/` 下已有子分类文件夹
3. 匹配到已有分类 → 归入对应子目录
4. 无匹配分类 → 询问用户是否创建新类别

### 3.6 信息源路由（强制）

涉及外部平台采集时，AI 必须：
1. 查询 `references/拓展工具包.md` 路由表
2. 匹配到 Skill → 检测是否已安装
3. 未安装 → 提示并建议安装，不静默绕过
4. 降级顺序：专属 Skill > agent-reach > web_fetch > agent-browser

### 3.7 `-tool` 索引联动

收录工具时自动：
1. 识别类型（Skill → `💪skill技能收录.md`；工具 → `🔧tools-index.md`）
2. 追加到对应分类表格
3. 生成独立工具卡片

### 3.8 `-day` / `-week` 数据源规则

**`day` 执行时，AI 自动读取**：
1. 当前对话上下文
2. 全局记忆（`~/.workbuddy/memory/MEMORY.md`）
3. 今天已有的 `📅每日笔记/` 文件（避免重复）

**`week` 执行时，AI 自动读取**：
1. 本周所有 `📅每日笔记/` 文件
2. 当前对话上下文
3. 全局记忆

### 3.9 主题索引自动维护（借鉴 Karpathy Wiki）

每个主题子分类下维护 `index.md`，`-note` / `-idea` 执行时自动更新：

**Index 结构** (`📚主题笔记/<主题>/index.md`):
```markdown
# <主题> Index

## 📥 最近收录（最近7天）
- [[文章标题]] | 一句话摘要 | YYYY-MM-DD

## 📂 按标签
### 标签名
- [[笔记名]] | 摘要 | YYYY-MM-DD

## 🔄 状态标记
- 🟡 待整合：[[孤立笔记]]（无入链）
- 🔴 需更新：[[过期笔记]]（超过90天未更新）
```

**维护规则**：
1. **自动创建**：`-note` 在新主题下创建首篇笔记时，自动生成该主题的 `index.md`
2. **自动追加**：新笔记收录时，追加到「最近收录」顶部
3. **过期清理**：「最近收录」只保留最近7天，超期条目移到「归档」或删除
4. **状态标记**：`-check` 扫描后更新标记（孤立、过期、缺失）
5. **双向链接**：Index 中的 `[[笔记名]]` 使用 Obsidian 双向链接语法

**多主题联动**（进阶）：
- 一篇笔记涉及多个主题时，在相关主题的 index.md 中都添加引用
- 通过 index.md 快速了解某主题的知识全貌

---

## 4. 开箱流程（5 Phase）

> **详细规范**见 `references/onboarding-phases.md`

| Phase | 名称 | 关键动作 |
|-------|------|---------|
| 1 | 环境准备 | 检测 Obsidian/CLI/Node；确认语言 |
| 2 | Vault 接入 | 新建 or 接入已有知识库 |
| 3 | 骨架初始化 | 创建文件夹骨架；植入 Skill 文件 |
| 4 | 全局记忆 | 写入 MEMORY.md；试跑首条指令 |
| 5 | 扩展推荐 | 推荐并安装扩展 Skill |

### Phase 3 完成后的骨架

```text
<vault>/
├── 📅每日笔记/                    # day
├── 📚主题笔记/                    
│   ├── note-overview摘要/         # note 外部内容摘要（多篇同主题时建子分类）
│   │   ├── AI工具/               # 同主题≥3篇时自动创建
│   │   ├── 健康/
│   │   └── ...
│   ├── note-experience经验/       # exp 个人经验沉淀（多篇同主题时建子分类）
│   │   ├── AI工具/
│   │   └── ...
│   └── 创作灵感/                  # idea 固定落点
├── 📆每周笔记/                    # week
├── 📋待跟踪任务/                  # todo
├── 📝创作输出/                    # output（自动创建子分类）
├── 🛠️tools/                      # tool
├── Clippings/                   # 收件箱（classify 扫描）
└── obsidian-ai-notes skill配置/
    ├── SKILL.md
    ├── templates/
    │   ├── topic-overview.md      # note 模板
    │   ├── topic-experience.md    # exp 模板
    │   ├── todo任务.md            # todo 模板
    │   └── ...
    ├── references/
    │   └── command-reference.md
    └── README.md
```

---

## 5. Vault 定位与跨空间执行

```
Vault 路径检测优先级：
1. MEMORY.md 配置的主 Vault 路径
2. 当前工作空间含 .obsidian/
3. 环境变量 OBSIDIAN_VAULT_PATH
4. 询问用户 → 记录到 MEMORY.md
```

---

## 6. 语言检测

| 检测语言 | 输出 | 文件夹名 |
|---------|------|---------|
| 中文 | 纯中文 | `📅每日笔记/` |
| 英文 | 纯英文 | `📅Daily/` |

---

## 7. 更新与合并

- 官方更新以 `obsidian-ai-notes 官方配置更新/` 送达
- 用户配置永不直接覆盖
- 用户可随时说「合并更新」手动触发

---

## 附录：与 Karpathy LLM Wiki 的对比与借鉴

### 架构对应

| Karpathy    | obsidian-ai-notes      | 说明           |
| ----------- | ---------------------- | ------------ |
| Raw Sources | Clippings/             | 原始裁剪，只读不写    |
| Wiki        | 📚主题笔记/                | AI 维护的知识层    |
| Schema      | SKILL.md               | 控制协议，侧边栏可编辑  |
| Ingest      | `-note` / `-idea`      | 收录 / 素材标记    |
| Query       | `-output`              | 查询沉淀，可触发多篇整合 |
| Lint        | `-check`               | 健康检查         |
| Index       | `📚主题笔记/<主题>/index.md` | 主题级索引（新增）    |

### 核心差异

| 维度            | Karpathy Wiki      | obsidian-ai-notes     |
| ------------- | ------------------ | --------------------- |
| **目标用户**      | 技术用户，喜欢 DIY        | 代码小白，开箱即用             |
| **配置方式**      | 工具清单 + prompt，用户装配 | 封装成 Skill，自动化配置       |
| **Ingest 模板** | 无固定模板，LLM 自由生成     | 结构化模板（可自定义）           |
| **索引层级**      | 全局 index.md        | 主题级 index.md（每个子分类独立） |
| **近期收录**      | 保留全部历史             | 只保留最近7天（轻量）           |
| **上手门槛**      | 需要理解工具链            | 5 分钟跑通，零配置            |

### 借鉴的核心理念

1. **渐进式编译**：每次 `-note` 不只是创建单篇笔记，而是更新整个主题的知识网络（索引、双向链接）
2. **知识复利**：`-output` 多篇整合时，产生的洞察可以写回主题笔记，形成复利
3. **用户只读，LLM 维护**：主题 index.md 由 AI 自动维护，用户通过 `-check` 查看状态
4. **健康检查**：模仿 lint，输出结构化问题报告 + 修复建议

### 为什么保留模板？

Karpathy 方案**无固定模板**，每次 ingest 由 LLM 自由生成摘要。这很灵活，但也有问题：
- 风格不一致，每次生成可能不同
- 需要更强的 LLM 才能稳定输出
- 用户难以预期输出格式

**obsidian-ai-notes 选择结构化模板**（可自定义）：
- 一致性：同类型笔记格式统一
- 可预期：用户知道会得到什么
- 可定制：修改 `templates/` 即可自定义
- 轻量：模板即 prompt，不增加复杂度

两种方案各有优劣，Karpathy 更灵活，obsidian-ai-notes 更可控。
