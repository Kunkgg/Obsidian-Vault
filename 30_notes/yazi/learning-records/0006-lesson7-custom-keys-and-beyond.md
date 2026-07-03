# 第 7 课通过 · 结业；用户参照官方 dotfiles 走到课程之外

第 7 课（自定义命令与快捷键，结业课）经用户在 **WSL 和 Windows 两端**验证：`!`「在这开终端」（unix `$SHELL` / windows `pwsh`，靠 `for` 字段同键不同命令）与 `g d`「跳到下载」均可用。至此 mission 里「能自定义命令/快捷键」的诉求落地，**全 8 节课程（第 0-7 课）完结 🎓**。

用户结业后**主动走到课程之外**——参照官方维护者自己的 dotfiles [`sxyazi/dotfiles`](https://github.com/sxyazi/dotfiles/tree/main/yazi)（已加入 RESOURCES），在自家 `Kunkgg/yazi-config` 仓库里加了一整套：

- **`package.toml`（v25+ 插件 lockfile）**——每条依赖 pin `rev` + `hash`。这**取代**了第 6 课教的「gitignore `plugins/` + 每机 `ya pkg add` 重装」做法：插件从「每机手动装」升级为「声明式、可复现」。是第 6 课 plugin 可移植性故事的进化。
- **额外插件**（多来自官方 `yazi-rs/plugins`）：`git`（文件列表显 git 状态徽章）、`diff`（`<C-d>` 对比选中与悬停文件）、`smart-enter`（绑 `l`：进目录或打开文件）、`smart-paste`（绑 `p`：粘进悬停目录或 cwd）、`mount`（`M`，`for="unix"`）、`folder-rules`（**用户指出是 yazi 内置功能、无需 package 安装**——我一度误判为「`package.toml` 漏声明」，已纠正）。`init.lua`：`require("git"):setup{order=0}` / `require("simple-tag"):setup({})` / `require("folder-rules"):setup()`。
- **`yazi.toml`**：`linemode=size`、`show_hidden=true`、`sort_by=natural`、`[preview]` 大尺寸 + `wrap=yes`。
- **`theme.toml`**（42KB）重度主题定制；`[flavor] deps=[]`（走 theme.toml 不走 flavor 包）。
- **实现选择**：用户把整个 `plugins/` **直接提交**进 git（`.gitignore` 只排 `tags/`），与 `package.toml` 并存——belt-and-suspenders，跨机器 clone 即可用；代价是更新插件时 git diff 略吵（可二选一精简：要么只留 `package.toml` + gitignore `plugins/` 靠 `ya pkg install` 复现，要么只提交 `plugins/` 去掉 `package.toml`）。当前两套都在，能用，非 bug。

**Implications**：① 课程主线已收尾，后续是「养 keymap + 按需挑插件」的长期阶段，不再是结构化授课。② `package.toml` 是比第 6 课更优的插件可移植方案，已记入 NOTES/RESOURCES 供将来参考。③ **待用户自查的小点**：`<C-d>` 被 `diff` 占用——若默认 `<C-d>` 是半页下滚（建议在 yazi 里按 `?` 翻 keymap 自查），则已被 `prepend_keymap` 顶掉；不用半页滚就无所谓，要用就把 diff 换个键。④ Mac / 干净 Linux 仍未实测（非阻塞，配置同步已验证通用）。见 [[MISSION.md]]、[[0005-lesson6-config-dotfiles-verified]]、[[0004-lesson5-tags-verified-and-storage]]。
