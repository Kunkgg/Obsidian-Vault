# 第 0 课完成 · 安装阻塞已解除

用户已确认 `y` wrapper 工作正常：启动 yazi → 导航进目录 → `q` 退出后 shell 成功 cd 到该目录。同时用户自行 `sudo apt remove yazi` 清掉了坏的 apt 包，现在 `yazi` 全局唯一指向 `~/.local/bin/yazi`（musl v26.5.6），无歧义。

**Implications**：安装阶段彻底结束，"更快跳转目录" 的工具链已就位（yazi 本体 + `y` cd-on-quit + 已装的 zoxide/fzf/rg/fd 可选依赖）。下一课可直接进入核心导航（`hjkl`、`gg/G`、`z`/`Z`），用户有 ranger/lf 肌肉记忆，`h/l/gg/G` 完全通用，重点放在 yazi 特有的 `z`(fzf)/`Z`(zoxide) 上。见 [[MISSION.md]] 与 [[0001-baseline-and-install-blocker]]。
