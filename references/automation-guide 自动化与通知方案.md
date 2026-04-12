# Automation & Notifications / 自动化与通知方案

> Goal: Let users get started quickly, then gradually enable automation at the right time (avoid lengthy initial setup).
> 
> 目标：让用户先快速上手，再在合适时机渐进开启自动化（避免首次配置过长）。

---

## 1) Recommended Principle / 推荐原则 (Manual First, Then Auto)

| # | Principle / 原则 |
|---|-----------------|
| 1 | **First setup does NOT require automation** — only confirm minimum essentials (path, naming, output preference). / 首次开箱不要求配置自动化，只确认最小必要项。 |
| 2 | **After first daily note**, ask if enable "Daily Note Automation". / 写完第一篇每日笔记后，再询问是否开启每日笔记自动化。 |
| 3 | **After 3 notes accumulated**, ask if enable "Weekly Review Automation". / 累计完成 3 篇笔记后，再询问是否开启周复盘自动化。 |
| 4 | Keep "dual mode": manual always available, auto is just enhancement. / 保持"双模式并存"：手动可随时使用，自动仅作增效。 |

---

## 2) Built-in Automation / 内置自动化 (Priority)

If current platform has "built-in automation/scheduled task" capability, use it first.

若当前平台有"内置自动化/定时任务"能力，优先使用。

### 2.1 Task A: Daily Note Automation / 每日笔记自动化 (Optional)
- Name / 名称: `Daily Note Reminder/Generation` / `每日笔记提醒/生成`
- Frequency / 频率: Daily once (suggested evening) / 每日一次（建议晚间）
- Trigger / 触发动作: `-day`
- Output / 输出: `📅Daily/` or `📅每日笔记/`

### 2.2 Task B: Weekly Review Automation / 周复盘自动化 (Optional)
- Name / 名称: `Weekly Review` / `每周复盘`
- Frequency / 频率: Every Friday 22:30 (customizable) / 每周五 22:30（可改）
- Trigger / 触发动作: `-week`
- Output / 输出: `📆Weekly/` or `📆每周笔记/`

### 2.3 Notification / 通知建议
- Manual mode / 手动模式: No forced notification / 不强制通知
- Auto mode / 自动模式: Send notification after successful generation / 生成成功后发送提醒

---

## 3) Cross-Platform Alternatives / 跨平台备选

### 3.1 Option A: cron + Local Script / cron + 本地脚本 (macOS/Linux)
```cron
# Daily note at 21:30 / 每天 21:30 执行每日笔记任务
30 21 * * * /usr/bin/python3 /path/to/scripts/run_daily_note.py

# Weekly review at Friday 22:30 / 每周五 22:30 执行周复盘任务
30 22 * * 5 /usr/bin/python3 /path/to/scripts/run_weekly_review.py
```

### 3.2 Option B: GitHub Actions / GitHub Actions (Repository hosted)
```yaml
name: notes-automation
on:
  schedule:
    - cron: "30 13 * * *"   # UTC 13:30 = Beijing 21:30, Daily / 北京时间 21:30，每日
    - cron: "30 14 * * 5"   # UTC 14:30 = Beijing 22:30, Friday / 北京时间 22:30，每周五
jobs:
  run:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run notes workflow
        run: python scripts/run_notes_workflow.py
```

---

## 4) FAQ / 常见问题

**Q: Will automation affect manual recording? / 自动化会不会影响手动记录？**  
A: No. Manual and auto can run in parallel. / 不会。手动与自动可并行。

**Q: Should I configure all automation at the start? / 一开始要不要就全配自动化？**  
A: Not recommended. Get manual working first, then enable auto after triggers are met. / 不建议。先跑通手动，触发条件满足后再开启。

**Q: What if content is too long? / 内容太长怎么办？**  
A: Ensure templates only enable [Required] sections. / 确认模板只启用【必填】模块。
