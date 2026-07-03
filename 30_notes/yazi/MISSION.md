# Mission: Yazi 终端文件管理器

## Why
用户想要一个跨平台（WSL / Windows / Mac / Linux）、可统一配置、用键盘高速操作的终端文件管理器，替代当前 `cd`+`ls` 的低效流程。核心诉求是「快」——目录跳转、复制/移动/重命名/批量改名、批量获取文件名与路径、文件标签、跨 tab 操作，并能自定义出适合自己工作流但不复杂的命令与配置。

## Success looks like
- 装好并能在 WSL 与 Windows 上正常启动 yazi，配置（含 keymap、`y` shell wrapper、插件）跨机器同步
- 能纯键盘流畅完成：跳转目录、选中、复制/移动/重命名、批量改名（bulk rename）、把文件名/路径送进剪贴板
- 会用 tabs 与文件标签（tags）组织任务
- 能读懂 yazi 的配置/插件文档，自己加一个自定义命令或快捷键
- 操作时不依赖鼠标，ranger/lf 的肌肉记忆能迁移到 yazi 的按键约定

## Constraints
- 环境：当前这台 WSL 的 yazi 二进制因 GLIBC 版本过旧**无法运行**（需先解决安装），见 [[learning-records]]
- 跨平台一致：同一套配置要在 WSL/Windows/Mac/Linux 都能用，配置需可同步
- 用户底子：熟悉 vim 基础按键（`hjkl`、`/` 搜索等），但**不熟** vim 查找替换、register 等高级特性；轻度用过 ranger 和 lf
- 学习偏好：追求「不复杂」的配置，优先掌握常用与核心，再按需扩展（明确由用户提出）

## Out of scope
- vim 的查找替换 / register 等高级操作（用户不熟，但本次不作为主线；用到时按需点拨）
- 深入 yazi 插件开发（编写复杂 Lua 插件）——除非后续 mission 扩展
- ranger / lf 的进阶用法（只作为迁移参考，不再深入）
