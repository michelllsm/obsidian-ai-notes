# Lessons Learned

> Skill 执行中踩过的坑和解法。AI 遇到同类问题时先查本文件，避免重复犯错。
> 
> 格式：`- YYYY-MM-DD | 指令/场景 | 问题 → 解法`

---

## 小红书抓取

- 2026-04-15 | `-note` 小红书链接 | defuddle 对 SPA 页面无效，只拿到页脚 → 改用 web_fetch，或启动 MCP
- 2026-04-15 | `-note` 小红书链接 | opencli 浏览器插件未连接导致失败 → 不依赖 opencli，按 SKILL.md §2.7 决策树走 MCP → web_fetch
- 2026-04-10 | 小红书裸链接 | web_fetch 裸链接成功率 <30% → 必须带 xsec_token 或用 MCP

## 路径与编码

- 2026-04-14 | 创建子分类 | macOS APFS NFD 编码导致 emoji 文件夹路径找不到 → 用 NFC 规范化或 `find` + 引号
- 2026-04-13 | `-check` | `search_file` pattern 含 emoji 返回空 → 改用 `execute_command` + `find`

## Skill 配置

- 2026-04-15 | `-note` | AI 找不到 SKILL.md 路径（配置目录改名后旧路径残留） → 确保 MEMORY.md 里的路径已更新
- 2026-04-15 | Skill 文档精简 | 小红书决策逻辑写了 3 处导致不一致 → 只在 SKILL.md §2.7 维护一处，其他文件引用它

---

> **维护规则**：AI 执行指令出错或用户指出修正时，自动追加一条记录到对应分类下。
