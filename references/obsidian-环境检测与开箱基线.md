# Obsidian 环境检测与开箱基线

> 目的：让 AI 在执行 Obsidian 相关任务时先走固定检测，避免重复试错。
> 适用：开箱 Phase 1 + 日常环境排查

---

## 1) 当前机器状态

> AI 首次检测后填入实际值，后续直接复用，无需反复检测。

- **Obsidian 桌面端**：{已安装 / 未安装}
- **Obsidian CLI**：{已安装 vX.Y.Z / 未安装}
- **Node.js**：{vX.Y.Z / 未安装}
- **npm**：{X.Y.Z / 未安装}
- **Vault 路径**：{用户确认的 Vault 绝对路径}
- **上次检测时间**：{YYYY-MM-DD}

---

## 2) 一次性检测命令（30 秒内）

```bash
# A. Obsidian 桌面端是否存在
osascript -e 'id of app "Obsidian"' 2>/dev/null || echo "Obsidian_NOT_INSTALLED"

# B. CLI 是否可用 + 版本
command -v obsidian-cli || echo "OBSIDIAN_CLI_NOT_FOUND"
obsidian-cli --version || true

# C. Node/NPM 运行时
node -v || echo "NODE_NOT_FOUND"
npm -v || echo "NPM_NOT_FOUND"
```

---

## 3) 开箱决策树（固定执行顺序）

1. **先检桌面端**
   - 若未安装：询问用户确认后下载官方 DMG。
2. **再检 CLI**
   - 若缺失且 npm 可用：`npm i -g obsidian-cli`
   - 若 npm 也缺失：降级为文件系统模式，告知用户。
3. **最后确认 Vault 路径（Phase 2）**
   - 检查当前工作空间是否含 `.obsidian/`（严格验证用户是否用 Obsidian 打开过库）
   - 如果没有，拦截警告，避免创建到空文件夹中。
4. **执行 smoke test**
   - 用最小命令验证 CLI 能读写 Vault。

---

## 4) 下载与安装规范

- 只用官方页面确认版本：`https://obsidian.md/download`
- macOS 下载直链格式：
  - `https://github.com/obsidianmd/obsidian-releases/releases/download/vX.Y.Z/Obsidian-X.Y.Z.dmg`
- 下载后必须校验：

```bash
hdiutil imageinfo /path/to/Obsidian-*.dmg | head
```

出现 `CUDIFDiskImage`（UDIF 镜像）即为有效安装包。

---

## 5) AI 执行加速约定

- 优先读本文件 §1 的缓存状态，不反复询问"是否装了 Obsidian/CLI"。
- 若任务失败，先做 §2 命令级复检再重试。
- 若下载异常：先换官方版本直链 → 再校验 DMG → 最后考虑网络问题。
- 复检后如状态有变，更新 §1。
