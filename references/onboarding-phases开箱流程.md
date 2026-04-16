# 开箱流程详细执行规范

> 本文件是 SKILL.md `## 2. 开箱流程` 的详细执行参考。
> AI 执行开箱时**必须读取本文件**，按以下步骤逐 Phase 执行。

---

## Phase 1: 环境准备 + 语言检测

### 1.1 快速检测（优先静默执行）

1. 检测 Obsidian 桌面端是否安装（可检测则直接检测，不可检测再询问用户）。
2. 检测 `obsidian-cli` 是否可用；若不可用，检查 Node/npm 是否可用以支持自动安装。
3. 检测 **`obsidian` skill** 是否已安装（`ls ~/.workbuddy/skills/obsidian/SKILL.md` 或等效检测）。
4. 语言检测按优先级：显式声明 → 系统上下文（IDENTITY.md / USER.md）→ 对话语境 → 默认中文。
5. 优先复用环境基线文档：`references/obsidian-环境检测与开箱基线.md`

### 1.2 判定与处理规则

| 情况 | 处理 |
|------|------|
| Obsidian 已安装 | 直接进入 CLI 检测 |
| Obsidian 未安装 | 询问用户确认后下载（`https://obsidian.md/download`） |
| obsidian-cli 缺失 + npm 可用 | 询问后自动安装 |
| obsidian-cli 仍不可用 | 降级为文件系统模式，告知影响范围 |
| **obsidian skill 未安装** | **Phase 3 完成后立即安装**（必需，不可跳过） |

降级提示话术：
> 当前环境暂不支持 Obsidian CLI，将启用【文件系统直写模式】。基础创建/归档可用，高级自动化能力受限。

**CLI 降级影响范围**：

| 能力 | CLI 可用时 | 文件系统模式（降级） |
|------|----------|-------------------|
| 创建/编辑笔记 | ✅ 正常 | ✅ 正常（直接读写文件） |
| 归类/整理 | ✅ 正常 | ✅ 正常 |
| 模板应用 | ✅ 正常 | ✅ 正常 |
| 反向链接扫描 | ✅ 正常 | ✅ 正常（AI 直接扫文件名） |
| Vault 注册 | ✅ 自动注册 | ❌ 需用户在 Obsidian 中手动打开文件夹 |
| 自动打开 Obsidian 窗口 | ✅ 可用 | ❌ 不可用 |
| 打开指定笔记 | ✅ 可用 | ❌ 需手动在 Obsidian 中打开 |

> **结论**：文件系统模式下，**所有笔记创建、归类、模板、链接功能正常运行**。仅"自动打开 Obsidian 窗口/笔记"和"Vault 注册"受影响——这些在首次设置时手动操作一次即可。

### 1.3 开箱交互对话（标准引导模板）

> 要求：一次性输出，减少来回追问。

```text
开始配置 Obsidian 笔记工作流，先做 30 秒环境确认：

[环境检测结果]
- Obsidian 桌面端：{已安装 / 未检测到}
- Obsidian CLI：{可用 / 不可用}
- 运行时：Node {version 或 未安装} / npm {version 或 未安装}

[你的语言偏好]
请选择：
A: 中文（Chinese）
B: English
C: 自动检测（Auto-detect）
（也可以直接回复：用中文 / Use English）

[安装状态确认（仅在无法自动判断时提问）]
A: 已安装
B: 未安装，请帮我下载

我会按你的选择自动继续下一步（Vault 接入与结构初始化）。
```

### 1.4 Phase 1 完成判定

满足以下条件即进入 Phase 2：
- 已确认 Obsidian 状态（已安装或用户同意下载）
- 已确认 CLI 路径（可用或明确降级）
- 已确认输出语言偏好

---

## Phase 2: 工作空间校验（Vault 接入）

> 💡 基于最新的 4 步开箱指南，当用户输入 `-setup` 时，AI 应当已经在正确的 Vault 文件夹内。

### 2.1 校验当前环境
1. 检查当前工作空间绝对路径。
2. 检测是否存在 `.obsidian/` 目录（判断是否已用 Obsidian 激活过）。

### 2.2 判定与交互

**情况 A：已在正确的 Vault 中（检测到 `.obsidian/` 或用户明确在库内）**
直接静默通过，进入 Phase 3。

**情况 B：当前环境无 `.obsidian/` 配置**
向用户确认是否遗漏了前置步骤：

```text
⚠️ 检测到当前工作空间似乎不是一个已激活的 Obsidian 知识库（未找到 .obsidian 配置）。

为了避免在错误的文件夹里建笔记，请确认：
你是否已经完成了 README 中的前 3 步？
(1) `git clone` 到了目标文件夹
(2) 用 Obsidian 软件【打开过】该文件夹
(3) 将 WorkBuddy 的工作空间切换到了该文件夹

A: 已确认，当前路径就是我的笔记库，继续初始化（AI 帮你补全配置）
B: 哎呀搞错了，我先去重新打开正确的文件夹（选这个退出当前 setup）
```

- 若选 A，则自动以当前工作空间路径作为 Vault 路径，进入 Phase 3。
- 若选 B，结束 setup，等待用户重新打开工作空间。

---

## Phase 3: 骨架检查与补全

> 💡 如果用户是通过 `git clone` 开箱的，这些文件夹和文件**已经存在**。本步骤主要是为了防错，或者适配那些将本 Skill 拷入已有知识库的用户。

### 3.1 检查并补全基础文件夹
静默检查当前 Vault 下是否包含以下核心目录，缺失则自动创建（注意带有 emoji 前缀）：
- `📅每日笔记`
- `📚主题笔记`
- `📆每周笔记`
- `📋待跟踪任务`
- `📝创作输出`
- `🛠️tools`
- `Clippings`

### 3.2 展示当前骨架结构
检查完毕后，向用户确认骨架已就绪：

```text
📦 你的笔记库骨架已就绪！核心配置位于 "obsidian-wiki-notes-skill配置/" 目录。

你可以：
✅ 用指令快速创建、修改、整理文档，无需手动归类
✅ 像改笔记一样修改 SKILL.md（AI 执行时优先遵循你的笔记 skill）
✅ 可以跟 AI 自然对话修改模版，或直接手动修改 templates，即时生效
```

---

## Phase 4: 用户级记忆配置 + 首条指令演示

### Step 1: 询问是否配置全局记忆

```text
📍 是否配置全局记忆？

配置后，你在其他对话窗口、其他工作空间都能直接使用笔记指令，
无需手动加载 Skill，AI 会自动识别并执行。

A: 配置全局记忆（推荐）
B: 跳过全局，仅写入当前工作空间记忆
```

### Step 2: 写入记忆

**选 A 时**：写入全局记忆（AI 需根据当前所处平台动态决定路径）
- 若在 WorkBuddy 中：写入 `~/.workbuddy/memory/MEMORY.md`
- 若在其他 IDE/平台：写入该平台推荐的全局上下文/记忆文件路径
- ⚠️ 如果 AI 无法确定当前平台的全局路径，请退化为工作空间级记忆，并向用户说明。

```markdown
## AI Notes 工作流配置
- **主笔记库路径**: `<当前工作空间绝对路径>`
- **笔记工具**: Obsidian（本地 Markdown）
```

**选 B 时**：写入 `<当前工作空间>` 的项目级记忆（如 `.workbuddy/memory/MEMORY.md` 或 `.cursorrules` 等）

```markdown
## AI Notes 工作流配置
- **主笔记库路径**: `<当前工作空间绝对路径>`
- **笔记工具**: Obsidian（本地 Markdown）
```

> ⚠️ **选 B 不等于不写入**——必须写入工作空间级记忆，否则后续指令无法定位笔记库路径。
>
> **选 B 的影响**：在当前笔记库工作空间中，Skill 完全可用。在其他工作空间使用时，AI 无法自动定位笔记库路径，需要告知路径或改配全局记忆（随时说"帮我配置全局记忆"即可补配）。

### Step 3: 首条指令演示

**选 A 完成提示**：
```text
✅ 全局记忆已配置！

现在你可以：
• 在任何对话窗口直接使用 -day、-note 等指令
• 在其他工作空间也能操作笔记库
• 直接说"帮我记笔记"，AI 自动识别并执行

试试输入：-note obsidian-wiki-notes功能介绍以及应用场景
```

**选 B 完成提示**：
```text
✅ 工作空间记忆已配置！

当前笔记库中，所有指令完全可用。
如果以后想在其他工作空间也能直接用，随时说"帮我配置全局记忆"即可。

试试输入：-note obsidian-wiki-notes功能介绍以及应用场景
```

---

## Phase 5: 必需依赖安装 + 扩展推荐

### Step 1: 安装必需 Skill（不可跳过）

**`obsidian` skill 是笔记自动化的基础依赖**——没有它，AI 无法自动读写 Obsidian Vault、注册库、打开笔记等操作。

**检测与安装判定**：
1. 检测是否已安装 `obsidian` skill。
2. 若未安装，检查是否具备 Node.js/npm 环境。

**情况 A：有 npm 环境**
AI 必须主动安装，不等用户选择：
```text
⚠️ 检测到「obsidian」skill 未安装，这是高级自动化的基础依赖。正在为你安装...
[自动执行安装 npm 安装命令]
✅ obsidian skill 安装完成！
```

**情况 B：无 npm 环境（非技术用户）**
**必须静默跳过**，禁止尝试安装或报错：
```text
ℹ️ 当前环境未检测到 npm，将使用纯文件系统模式运行工作流。所有基础功能（笔记创建、归档、整理）完全可用。
```

> 💡 `obsidian` skill 提供：Vault 自动注册、笔记读写自动化、打开指定笔记等底层能力。
> 没有它，`obsidian-wiki-notes` 仍可通过文件系统直写工作，但体验会打折扣。

### Step 2: 推荐扩展安装

推荐安装列表见 [[obsidian-wiki-notes-skill配置/references/Extended Toolkit拓展工具包.md]]，或使用以下默认列表：

| Skill/插件             | 级别     | 用途                               | 首次配置                    |
| -------------------- | ------ | -------------------------------- | ----------------------- |
| **obsidian**         | **必需** | **Obsidian 自动化底层能力（Step 1 已安装）** | 无                       |
| Tasks                | 推荐     | 任务 checkbox 打勾 + 筛选 + 看板         | Obsidian 社区插件安装         |
| Obsidian Web Clipper | 推荐     | 网页裁剪到 Obsidian 收件箱               | 浏览器安装插件                 |
| lark-cli             | 推荐     | 飞书文档/表格/多维表格内容采集                 | 需 `lark-cli auth login` |
| xiaohongshu          | 推荐     | 小红书搜索+详情+互动数据                    | 需启动 MCP + 扫码登录          |
| find-skills          | 推荐     | 发现更多实用 Skill                     | 无                       |
| self-improving-agent | 推荐     | 自动记录错误并持续改进                      | 无                       |

> **为什么列表比较短？** YouTube、B站、公众号、X 等平台的信息采集通过内置工具已能获得足够质量的结果，无需额外安装 Skill。详见 `references/Extended Toolkit拓展工具包.md`。

**补充**：如需统一的多平台采集方案，可考虑安装Agent Reach（支持16+平台），但它与当前专精方案互为补充而非替代。

---

### 飞书CLI安装（推荐）

**用途**：飞书文档/表格/多维表格内容采集（`-note`飞书链接时优先使用）

**安装命令**：
```bash
# 方式1：npm安装
npm install -g @larksuiteoapi/cli

# 方式2：macOS Homebrew
brew install lark-cli

# 方式3：官方GitHub下载
https://github.com/larksuite/oapi-sdk-cli/releases
```

**首次配置**：
```bash
lark-cli config init
lark-cli auth login
```

---

### Obsidian Web Clipper安装（推荐）

**用途**：网页内容一键裁剪到Obsidian收件箱（`Clippings/`）

**安装方式**：
- **Chrome/Edge**: [Chrome Web Store](https://chromewebstore.google.com/detail/obsidian-web-clipper/cnjifjpddelmedmihgijeibhnjdfpjhn)
- **Firefox**: [Firefox Add-ons](https://addons.mozilla.org/firefox/addon/obsidian-web-clipper/)
- **Safari**: 通过 Mac App Store 搜索 "Obsidian Web Clipper"

**配置步骤**：
1. 安装浏览器扩展
2. 点击扩展图标 → 设置 → 配置Obsidian Vault路径
3. 设置默认保存位置为 `Clippings/`
4. 测试裁剪任意网页

---

### Obsidian Tasks 插件安装（推荐）

**用途**：任务 checkbox 增强——打勾自动记录完成日期，支持按优先级/截止日筛选和看板视图，与 `-todo` 指令的领域看板配合使用。

**安装步骤**：
1. 打开 Obsidian → 设置 → 第三方插件 → 关闭安全模式
2. 点击「浏览」→ 搜索 **Tasks**
3. 安装并启用
4. 无需额外配置，安装即可用

> 💡 安装后，`📋待跟踪任务/` 中的 checkbox 会自动支持打勾、日期追踪和筛选功能。

---

**交互话术**：
```text
🚀 基础笔记工作流已就绪！

为了让工作流发挥最大威力，强烈推荐安装以下扩展工具与 Skills：

【1. 外部扩展工具（需要手动或 CLI 配置）】
• 飞书 CLI：支持高质量飞书文档采集（npm 安装）
• Obsidian Web Clipper：官方浏览器一键剪藏插件
• Obsidian Tasks：Obsidian 社区强大的任务看板插件

【2. 核心提效 Skills（可直接让 AI 一键安装）】
• find-skills：发现更多实用的 AI 技能
• self-improving-agent：让 AI 自动记录错误并持续自我进化

请选择（可多选）：
A: 帮我用 npm 安装并配置飞书 CLI
B: 给我 Web Clipper / Tasks 的安装指引
A: 帮我一键安装推荐的 Skills/工具（强烈推荐！）
D: 暂时跳过，直接开始记笔记！
```

选 D 时：
```text
好的，随时说"帮我安装 XX Skill"或"配置飞书CLI"即可。
```
