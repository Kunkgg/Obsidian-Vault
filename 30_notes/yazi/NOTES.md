# Teaching Notes — Yazi

## 用户偏好
- 追求"不复杂"的配置：先掌握核心与常用，再按需扩展。课程设计避免一上来堆插件/复杂 Lua。
- 跨平台一致性是硬需求：WSL / Windows / Mac / Linux 共用一套配置。涉及路径差异（`~/.config/yazi/` vs `%APPDATA%\yazi\config\`）时要点明。
- 交流用中文。
- 编辑器偏好 **vim**（明确拒绝过 nano 默认）——熟 vim 基本操作，涉及编辑器时默认 vim；`EDITOR/VISUAL=vim` 已写入 .zshrc。

## 环境备忘
- WSL: Ubuntu 22.04, GLIBC 2.35, zsh。
- **重要模式：这台机器 apt 源里的工具普遍严重过时，宁可下官方二进制也别用 apt。** 已踩坑：yazi（GLIBC 不够 → 用 musl）、fzf（apt 0.29，yazi 要 ≥0.53 → 升到 0.73.1）、zoxide（apt 0.4.3 → 升到 0.9.9）。统一做法：官方 release 二进制装到 `~/.local/bin`（`.zshrc` 已把它排 PATH 最前，自动盖住 `/usr/bin` 旧版）。
- 当前 `~/.local/bin` 里 yazi 工具链：yazi 26.5.6 (musl)、ya 26.5.6、fzf 0.73.1、zoxide 0.9.9。rg / fdfind 走系统，暂够用。
- **`s` 搜索在本机开箱可用（第 4 课已实测）**：PATH 上没有真正的 `fd` 二进制（只有 `fdfind` + zsh alias），但 yazi 二进制里含 `fd/fdfind` 回退逻辑 → 找不到 `fd` 时自动用 `fdfind`，所以 `s` 正常。**无需建软链**（推翻了我早先"很可能报错"的预测，见 [[0003-lesson4-s-search-fdfind-fallback]]）。`S`（ripgrep）用真二进制 `/usr/bin/rg`，也没问题。跨平台注意（第 6 课验证）：目标机器需装 `fd` 或 `fdfind` 之一，`s` 才能用。
- zoxide 已接进 shell（`.zshrc` 有 `zoxide init zsh`），db 在 `~/.local/share/zoxide/db.zo`，已积累若干历史目录。
- 剪贴板：`xsel` 经 WSLg **能同步到 Windows 剪贴板**（已实测 `c`⇒`c` 拷的路径可在 Windows 里 Ctrl+V），无需 wl-clipboard。
- **第 5 课配置事实（simple-tag 插件，待用户实测）**：
  - 安装：`ya pkg add boydaihungst/simple-tag`（v25+ 命令，本地 `ya pkg --help` 已核实有 `add` 子命令；旧的 `ya pack -a` 已废弃）。
  - `~/.config/yazi/` 此前**不存在**，第 5 课首次创建。需写 3 文件：`init.lua`（`require("simple-tag"):setup({})`，**必需**）、`yazi.toml`（`[plugin]` 下 `prepend_fetchers`，用 prepend 不覆盖自带 fetcher）、`keymap.toml`（`[mgr]` **不是** `[manager]`，新格式 `on=["x","y"]` 数组 + `prepend_keymap = [...]`）。
  - **yazi v26+ fetcher 必填 `group`（踩坑已修）**：第 5 课原抄 simple-tag README 主示例（无 `group`），在本机 26.5.6 启动报 `missing field group`。已修正——两条 fetcher 各加 `group = "simple-tag"`（README 注释行也给了此值，是其正确写法）。课程文件 `0006-file-tags.html` 已同步修掉错误片段。结论：**simple-tag README 此处过时，别再直抄无 `group` 的主示例**。`group` 语义=同组 fetcher 编批一起跑（见官方 yazi.toml 文档 `[plugin]` 段），simple-tag 自成一组即可。
  - **按键冲突设计（关键决策）**：simple-tag README 把所有标签动作绑 `t` 前缀，但 `t t` 是建 tab（第 4 课已验证）→ README 的 `t t k`（切换标签）会与 `t t` **撞键**（yazi 等 timeout 才决断）。故课程把切换标签改绑 **`t g`**（避开 `t t`），筛选 `t f`、清除 `t c`、显示 `t u`，全 2 键、互不冲突。`toggle-ui` 也从 README 的 `t u s` 简化成 `t u`。课程里向用户说明了与 README 的偏差。
  - **跨平台**：simple-tag README 明确声明支持 Linux/Windows/macOS（不走 mactag）→ RESOURCES 里"标签跨平台可用性"的 gap **已消除**。**标签存储位置已查实**（插件源码 `main.lua:549-551`，README 未述）：Unix/WSL `~/.config/yazi/tags/`、Windows `%APPDATA%\yazi\config\tags\`，按「被标文件的父目录绝对路径」分目录、每目录一个 `tags.json`。**关键**：键 = 父目录绝对路径 → 同步 `tags/` 数据**不会**让标签跨机器/跨 OS 迁移（WSL `/home/...` ≠ Win `C:\Users\...`，键不同）；故 dotfiles 只同步配置文件、标签视作每机本地。
- Windows 侧 simple-tag **已装并跑通**（用户第 5 课在 WSL + Windows 两端验证打标/筛选）；Mac 与干净 Linux 的独立安装/同步尚未做，第 6 课覆盖。

## 当前进度（下次会话的 resume 点）
- **已交付并经用户验证**：第 0 课（安装 + `y` wrapper）、第 1 课（导航 `hjkl`/`gg`/`G`/`z`/`Z`）、第 2 课（选择与文件操作 `y`/`x`/`p`/`r`/`a`/`d`）、第 3 课（批量改名 + 拷路径；`c`⇒`c` 拷路径经 xsel/WSLg 成功同步 Windows 剪贴板）。
- **第 4 课（多 tab 与查找）已交付并经用户验证通过**：tab（`t`⇒`t` / `1`-`9` / `[` `]` / `{` `}` / `Ctrl+c`）+ 跨 tab `y`/`x`/`p` 正常；三种找文件 `f`(过滤) / `/` `?` `n` `N`(查找) / `s` `S`(搜索) 均可用。按键按官方 Quick Start v26.5.6 核对。`s` 在本机开箱可用（yazi 有 fdfind 回退，见环境备忘与 [[0003-lesson4-s-search-fdfind-fallback]]），无需软链。沙盒 `~/yazi-practice/sub/buried_report.md` 供 `s`/`S` 演示。
- **第 5 课（文件标签 · simple-tag 插件）已交付并经用户验证（WSL + Windows 两端）**：装插件 + 建 `~/.config/yazi/` 写 3 配置（`init.lua`/`yazi.toml`/`keymap.toml`，fetcher 已修加 `group`）+ `t g` 打标 + `t f` 筛选均通过。按键刻意避开 `t t`（建 tab），用 `t g`/`t f`/`t c`/`t u`。learning record 见 [[0004-lesson5-tags-verified-and-storage]]。
- **第 6 课（配置与跨平台同步）已交付并经用户验证**：核心新知 = **`prepend_*`/`append_*` 混合机制**（第 5 课已在用、此课点破）+ **官方 `YAZI_CONFIG_HOME`**（已核实 Configuration overview v26.5.6）。同步方案「在默认目录里 `git init`」用户跑通（WSL push + Windows clone 进 `%APPDATA%\yazi\config\` + `ya pkg add` 重装插件，两端 `t g` 可用）。`.gitignore` 排 `plugins/`（每机重装）与 `tags/`（绝对路径存、跨机器不通用）。**参考文档** `reference/0001-yazi-config-reference.html` 已产出。learning record 见 [[0005-lesson6-config-dotfiles-verified]]。**未实测**：Mac / 干净 Linux（配置同步本身已验证通用，那两端只差 `ya pkg add` + 补 `fd`/`rg`，非阻塞）。
- **第 7 课（自定义命令与快捷键 · 结业）已交付并经用户验证（WSL + Windows 两端）**：`!` 开终端、`g d` 跳下载均通过，跨平台同键靠 `for` 字段。learning record 见 [[0006-lesson7-custom-keys-and-beyond]]。
- **用户结业后超越教程**（重要，详见 [[0006-lesson7-custom-keys-and-beyond]]）：参照官方维护者 dotfiles `sxyazi/dotfiles`（已加入 RESOURCES），在自家 `Kunkgg/yazi-config` 仓库里加了：① **`package.toml`**（v25+ 插件 lockfile，pin rev+hash）——这**取代**了第 6 课「gitignore plugins/ + 每机 ya pkg add」的做法，插件可声明式跨机器复现；② 额外插件 `git`/`diff`(`<C-d>`)/`smart-enter`(绑 `l`)/`smart-paste`(绑 `p`)/`mount`(`M`,unix-only)/**`folder-rules`**（用户指出是**内置功能、无需 package 安装**——曾被我误判为漏声明，已纠正）；③ `yazi.toml` 设 `linemode=size`/`show_hidden=true`/`sort_by=natural`/preview 大尺寸；④ `theme.toml` 42KB 重度主题。**实现选择**：用户把 `plugins/` **直接提交**进 git（`.gitignore` 只排 `tags/`），与 `package.toml` 并存——belt-and-suspenders，能用，更新插件时 diff 略吵（可二选一精简）。
- **待用户自查**：`<C-d>` 被 `diff` 占用，可能顶掉默认半页下滚——建议 yazi 里按 `?` 翻 keymap 确认；不用半页滚就无所谓。
- **课程状态：全 8 节（第 0-7 课）完结 🎓**，mission 所有诉求已落地。
- **番外第 8 课（压缩包与 opener）已产出，待用户实测**（`lessons/0009-openers-and-archives.html` + `reference/0002-yazi-opener-reference.html`）。用户结业后反馈三个痛点：①解压到子文件夹、③创建压缩包、⑤解压没反馈。已逐条核实解法并写进课里：
  - **⑤反馈**：按 `w`=任务管理器（零配置，多源确认；用户多半是不知道这个键）。
  - **①解压到子目录**：装 `unar`（apt 1.10.1 可装，稳定工具非版本敏感→apt 没问题）+ 自定义 `unpack` opener + `prepend_rules` 抢在默认 `extract` 前。**关键核实**：yazi 二进制内嵌默认配置里**已有** `extract = ya pub extract --list %s`（解到 CWD）+ 压缩包 `[open]` 规则——所以"按 l/o 解压"开箱即有，只是解到 CWD 且无反馈。选 `unar` 不选 `7zz` 的理由：实测 `7zz x foo.zip -ofoo/` 能子目录化，但 opener 的 `%s` 给不出"去扩展名的目录名"，且 shell `${f%.*}` 对 `.tar.gz` 只切 `.gz`→`foo.tar/`（错）；`unar` 全格式正确、自动建同名子目录。
  - **③创建压缩包**：`KKV9/compress.yazi` 插件（官方 Resources 收录，跨平台，`ya pkg add KKV9/compress`，keymap `c a a`）。**PATH 要求**：`.zip`→`zip`、`.7z`→`7z`、`.tar.gz`→`tar`。用户有 `7zz` 无 `7z`/`zip` → 课里让 `sudo apt install zip`（最通用）+ `ln -s /usr/bin/7zz ~/.local/bin/7z`（解锁 `.7z`）。
  - **opener/open 语法**：已从官方 yazi.toml 文档（v26.5.6，docsearch:version 核对）逐条核实 `[opener]`(run/block/orphan/desc/for + `%s`/`%sN`/`%d`/`%%`) 与 `[open]`(url glob/mime glob/use 名或数组、prepend/append/rules、`\s` 前缀转大小写敏感)。完整速查 → `reference/0002-yazi-opener-reference.html`。
  - **下一步**：等用户跑通三件事（建 zip、解到子目录、`w` 看进度）后写 learning-record `0007`。
- **剩余（非阻塞）**：Mac / 干净 Linux 未实测（配置同步已验证通用，那两端只差 `ya pkg install` + 补 `fd`/`rg`）。后续按需：从 awesome-yazi 挑插件、养自己的 keymap、遇问题问 GitHub Discussions。
- **页脚计数**：共 8 节（第 0-7 课），现有 7 个课程的页脚已统一刷成「/ 8 课」。
- **建议**：每课开新会话（teach 的设计用法），从本文件 resume。
