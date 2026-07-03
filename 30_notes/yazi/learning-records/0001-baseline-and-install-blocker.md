# 学习基线 + 安装阻塞（首会话）

用户自报底子：熟悉 vim 基础按键（`hjkl`、`/` 查找等），**不熟** vim 的查找替换 / register 等高级特性；轻度过 ranger 与 lf。这意味着课程**不应**从"TUI 文件管理器是什么"或"vim 按键入门"讲起，而应直接进入 yazi 特有的按键约定与功能，并把 ranger/lf 的肌肉记忆显式映射过来。

**关键阻塞**：本机 WSL（Ubuntu 22.04, GLIBC 2.35）已通过 apt 装了 yazi `25.9.15-1`，但 v25 系列 gnu 构建要求 GLIBC ≥ 2.39，**无法运行**（上游 issue #2346，已知行为）。因此在 WSL 上的第一堂课必须先解决安装（推荐官方 musl 静态构建），否则没有任何练习反馈回路。见 [[MISSION.md]]。

**Implications**：第一课 = 让 yazi 在 WSL 跑起来 + 装 `y` wrapper（cd on quit），这是 mission 中"更快跳转目录"的前提，也是一个可立即验证的 tangible win。vim 高级特性（查找替换/register）列为 out-of-scope，用到再点拨。
