# 拓展工具包 / Extended Toolkit

---

## 1) Calling Priority / 调用优先级

| Priority / 优先级 | Description / 说明                            |
| -------------- | ------------------------------------------- |
| 1              | Installed and verified / 已安装且验证可用           |
| 2              | Recorded and verified / 已记录并验证可用            |
| 3              | Capability discovery (`find-skills`) / 能力发现 |

---

## 2) Source Routing / 信息源路由

> AI 执行采集任务时，必须查询此表匹配目标平台，按优先级调用可用方案。  
> 表中 **AI 执行指引** 列说明 AI 如何执行、需哪些配置、注意事项，确保任务可落地。

| Platform / 平台           | Primary / 首选           | Alt / 备选          | Quality / 质量说明                                                     | AI 执行指引                                                                                                                                                                                                         |
| ----------------------- | ---------------------- | ----------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 小红书 / Xiaohongshu       | xiaohongshu (MCP)      | web_fetch         | **首选**：搜索+详情+评论+互动数据，完整。**备选web_fetch**：仅公开文字，无图片/互动数据，**质量低，易失败** | **AI必须按此顺序执行**：\n1. `which xiaohongshu-mcp` → 有则直接调用（95%成功）\n2. 无MCP但有`start-mcp.sh` → 执行并扫码登录\n3. 有带`xsec_token`链接 → `web_fetch`（90%成功）\n4. 只有裸链接 → **不尝试web_fetch**，直接提示"需扫码登录小红书"\n5. web_fetch返回空/403 → 立即停止，提示安装MCP\n👉 详见《小红书数据抓取规则.md》 |
| YouTube                 | web_fetch _(内置)_       | yt-dlp (需 cookie) | **首选**：标题+描述+章节+数据+链接，**零配置成功率高**。备选：需cookie认证                     | 1. **直接**调用 `web_fetch`（无需检查其他）\n2. 仅当需下载视频时用 `yt-dlp`\n3. 失败率<5%，基本可靠                                                                                                                                                                        |
| B站 / Bilibili           | web_fetch _(内置)_       | yt-dlp (需 cookie) | **首选**：标题+描述+数据，**零配置成功率高**                                        | 同YouTube，直接 `web_fetch`                                                                                                                                                                                                         |
| 抖音 / Douyin             | web_fetch _(内置)_       | —                 | **公开内容**可抓文字，**受限内容失败率高**                                          | 1. 直接 `web_fetch`\n2. 若返回空/验证页 → 放弃，提示"抖音需特殊方案"                                                                                                                                                                                               |
| 微信公众号 / WeChat Articles | web_fetch _(内置)_       | —                 | **完全可靠**，零配置100%成功                                                 | 1. **直接** `web_fetch`\n2. 无需备选方案                                                                                                                                                                                                         |
| 飞书 / Lark               | lark-cli (MCP) | —                 | **飞书CLI优先**，需认证，配置后可靠                                           | 1. **飞书CLI优先**：检查 `lark-cli` 登录状态\n2. 未登录则提示 `lark-cli auth login`\n3. 成功后调用相应API\n4. 飞书CLI不可用时，降级到 Obsidian 文件模式                                                                                                                                    |
| Twitter/X               | web_fetch _(内置)_       | —                 | **公开推文**可抓，**登录墙内容失败**                                             | 1. 直接 `web_fetch`\n2. 遇验证页 → 提示"需登录态"                                                                                                                                                                                                         |
| 通用网页 / General Web      | web_fetch _(内置)_       | —                 | **零配置永远可用**                                                        | 直接 `web_fetch`，无脑执行                                                                                                                                                                                                         |

**Routing Rule / 路由规则**：
1. Match platform → check if primary skill installed and ready
2. 首选可用 → 直接调用
3. 首选**需配置**（如扫码登录/API Key）→ **提示用户完成配置**，不绕过
4. 首选未安装 → 检查 web_fetch 是否为该平台的合格替代（见上表"质量说明"）
5. web_fetch 是合格替代 → 直接用，无需安装额外 Skill
6. web_fetch 不足 → 建议安装首选 Skill 并说明原因
7. No match → `web_fetch`

**设计原则**：
- **零配置优先**：能用内置 `web_fetch` 获得足够质量的信息，就不要求用户额外安装/配置
- **坦诚告知**：`web_fetch` 无法覆盖的能力（如小红书搜索、视频字幕提取），明确说明并建议安装专属 Skill
- **不凑合**：降级方案质量不达标时，宁可提示用户配置/安装，也不用半残方案
- **AI 可执行性**：每条路由均提供明确步骤与检查点，确保 AI 自动执行时不卡住

---

## 3) Standard Skill Table / 拓展工具包

| Skill | Level / 建议级别 | Purpose / 主要用途 | Route / 路由平台 | Source / 安装来源 | Setup / 首次配置 |
|-------|-----------------|-------------------|----------------|-----------------|-----------------|
| [obsidian-wiki-notes](https://github.com/michelllsm/obsidian-wiki-notes) | Required / 必安装 | Core note workflow / 笔记工作流核心 | — | 当前 Skill 包 | 执行 `-setup` |
| [obsidian](https://skills.sh/) | Required / 必安装 | Note automation / 笔记读写自动化 | — | skills.sh | 无 |
| [Tasks](https://publish.obsidian.md/tasks/) | Recommended / 推荐 | Task checkbox & filter / 任务打勾+筛选+看板 | — | Obsidian 社区插件 | 开箱时引导安装 |
| [Obsidian Web Clipper](https://obsidian.md/clipper) | Recommended / 推荐 | Web clipping to Obsidian / 网页裁剪到收件箱 | 通用网页 | Obsidian 官方浏览器插件 | 浏览器安装插件即可 |
| [xiaohongshu](https://github.com/xpzouying/xiaohongshu-mcp) | Recommended / 推荐 | Xiaohongshu content / 小红书搜索+详情+互动数据 | 小红书 | GitHub | 需 `start-mcp.sh` 启动 + **扫码登录** |
| [yt-dlp](https://github.com/yt-dlp/yt-dlp) | Optional / 可选 | Video download & subtitle / 视频下载+字幕提取 | YouTube/B站 | `brew install yt-dlp` | 需 `--cookies-from-browser` 认证 |

> **为什么精简了？** 大量平台（YouTube、B 站、公众号、Twitter 等）的信息采集通过内置 `web_fetch` 已能获得足够质量的结果（标题+描述+章节+数据），无需额外安装 Skill。只有需要**搜索能力**（小红书）或**视频字幕提取**（yt-dlp）的场景才真正需要专属工具。

---

## 4) Legend / 图例

| Level / 级别 | Meaning / 含义 |
|--------------|---------------|
| **Required** / 必安装 | Essential for core functionality / 核心功能必需 |
| **Recommended** / 推荐安装 | Significantly enhances workflow / 显著提升工作流 |
| **Optional** / 可选安装 | Nice to have for specific needs / 特定场景有用 |

---

## 5) Maintenance / 维护说明

- This file maintains general capabilities, routing rules, and recommendation levels.
- 本文件维护通用能力、路由规则与建议级别。
- Personal installation status is detected at runtime (`ls ~/.workbuddy/skills/`), not hardcoded here.
- 个人安装状态运行时自动检测，不在此文件写死。
- When `-tool` command adds a new Skill with platform routing, update Section 2 accordingly.
- 当 `-tool` 指令收录新 Skill 且涉及平台路由时，同步更新第 2 节。
- **跨引用更新**：若其他 Skill 文档（如 `README.md`、任务模板）引用此表，请同步调整名称与结构，确保术语一致（如"Skill Installation Table"→"拓展工具包"）。
