# 第 6 课通过 · 配置同步管道打通（WSL↔Windows）

第 6 课（配置与跨平台同步）经用户跑通：在 WSL 的 `~/.config/yazi/` 里 `git init` + push 到 GitHub，Windows 端 clone 进 `%AppData%\yazi\config\` 并 `ya pkg add boydaihungst/simple-tag` 重装插件后，两端 `t g` 打标签均可用。这把 mission 里「跨机器同步配置」的硬需求落地——且用的是**零 symlink、零环境变量**的「默认目录里 git」方案（`YAZI_CONFIG_HOME` 作为备选知识讲过、未启用）。`.gitignore` 排掉 `plugins/`（每机重装）和 `tags/`（按绝对路径存、跨机器不通用，见 [[0004-lesson5-tags-verified-and-storage]]）。

课上点破的核心概念：**`prepend_*`/`append_*` 混合机制**——同名选项覆盖默认，`prepend_*`/`append_*` 在默认之上叠加（适用 keymap/fetchers/open/icon/previewer/preloader）。这是第 5 课已经在用、但当时没讲透的东西，现已理解。

**Implications（直接决定第 7 课）**：① 同步管道已就位 → 第 7 课在 `keymap.toml` 加的任何自定义键，`git push` 后自动同步到 Windows（及将来的 Mac/Linux）。② 用户已具备读懂 `keymap.toml` 结构（`[mgr]` + `on=[...]` + `run=` + `desc=`）的能力，第 7 课可直接进入「`run` 里能写什么」——内置动作链、shell 命令、小 Lua。③ Mac / 干净 Linux 仍**未实测**（配置同步本身已验证通用，那两端只差 `ya pkg add` + 补 `fd`/`rg`，非阻塞）。见 [[MISSION.md]]、[[0004-lesson5-tags-verified-and-storage]]、[[0003-lesson4-s-search-fdfind-fallback]]。
