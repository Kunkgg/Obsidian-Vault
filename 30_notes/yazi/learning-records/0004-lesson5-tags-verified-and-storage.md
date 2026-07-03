# 第 5 课通过 · 标签在 WSL 与 Windows 均可用；存储位置已查实

第 5 课（simple-tag 文件标签）经用户在 **WSL 和 Windows 两端**都装好并跑通打标/筛选——这是首个跨平台验证的课，也证实 simple-tag 在 Windows（`%APPDATA%\yazi\config\`）下配置能正常加载。途中修掉一处版本漂移：yazi v26+ 给 fetcher 加了必填 `group` 字段，README 主示例漏写 → 启动报 `missing field group`；已给两条 fetcher 各补 `group = "simple-tag"`（README 注释行给的值），课程文件 0006 同步修正。

源码核实（`main.lua:549-551` + `:433`）出标签存储：**Unix/WSL** `~/.config/yazi/tags/`、**Windows** `%APPDATA%\yazi\config\tags\`；按「被标文件的**父目录绝对路径**」分目录，每目录一个 `tags.json`（`{ "文件名": ["标签字符"] }`）。

**Implications（直接决定第 6 课设计）**：标签键 = 父目录绝对路径 → 同步 `tags/` 数据目录**不会**让标签跨机器/跨 OS 迁移（WSL `/home/...` ≠ Win `C:\Users\...`，键不同；只有同 OS + 同绝对路径的两台机才一致）。故第 6 课的 dotfiles 同步**只同步配置文件**（`yazi.toml`/`keymap.toml`/`init.lua`），**不同步** `tags/`，标签视作「每机本地」。这把 RESOURCES 里"标签能否跨机器带走"的 gap 关掉。见 [[MISSION.md]]、[[0003-lesson4-s-search-fdfind-fallback]]、[[0002-lesson0-done-blocker-resolved]]。
