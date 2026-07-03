# 配置与跨平台同步

*Yazi 课程 · 第 6 课 — 把第 5 课写好的 3 个配置文件变成一个 git 仓库——WSL 改一处、push，Windows / Mac / Linux pull 一下就是同一套 yazi。零 symlink、零环境变量。*

> [!tip] 本课的胜利
> 把 `~/.config/yazi/` 变成一个 git 仓库并推到远端；在 Windows 上 clone 进它自己的配置目录，两端就是同一份配置。以后任何一台改了配置 → `git push`，其余机器 `git pull`。这是 mission 里「跨机器同步配置」的最省事做法。

## 先搞懂 · 4 个配置文件各管什么

yazi 的配置全是**可选**——它自带一套完整默认配置（见 [仓库 `shipped` tag](https://github.com/sxyazi/yazi/tree/shipped/yazi-config/preset)），你只写「想改的那部分」。四个文件：

| 文件 | 管什么 | 你已碰过？ |
|---|---|---|
| `yazi.toml` | 行为与功能：显示隐藏文件、排序、`[plugin]` 下的 fetcher/预览器等 | ✅ 第 5 课加了 `prepend_fetchers` |
| `keymap.toml` | 按键绑定 | ✅ 第 5 课加了 `[mgr] prepend_keymap` |
| `init.lua` | Lua 入口，给插件调 `setup()` | ✅ 第 5 课写了 `require("simple-tag"):setup({})` |
| `theme.toml` | 配色 | 本课不碰，知道有这文件即可 |

## 配置「混合」机制：prepend / append

这是第 5 课你已经在用、但还没点破的关键概念。写 `yazi.toml` / `keymap.toml` 时，**同名选项会覆盖默认**。但你想在默认之**上**叠加自己的，就用 `prepend_*` / `append_*` 把条目插到默认的前面或后面，**不抹掉默认**。

```toml
# keymap.toml —— 在默认按键之上"加"自己的（不覆盖默认）
[mgr]
prepend_keymap = [
  { on = [ "t", "g" ], run = "plugin simple-tag -- toggle-tag" },
  # ...
]
```

第 5 课的 `prepend_keymap`（加按键）和 `prepend_fetchers`（加标签徽章 fetcher）都是这个机制——所以你加了标签键，却没丢掉 <kbd>h</kbd><kbd>j</kbd><kbd>k</kbd><kbd>l</kbd> 这些默认键。官方说明它对 **keymap、fetchers、open / icon / previewer / preloader 规则**都适用。

> [!note] 一句话记忆
> 想**改掉**默认 → 直接写同名选项（覆盖）；想**在默认之上加** → 用 `prepend_*` / `append_*`。99% 的自定义用 `prepend_*`。

## 跨平台的关键

两个平台，两个默认目录（[官方 Configuration 文档已核实](https://yazi-rs.github.io/docs/configuration/overview/)）：

- **Unix**（WSL / Mac / Linux）：`~/.config/yazi/`
- **Windows**：`%AppData%\yazi\config\`（即 `C:\Users\<你>\AppData\Roaming\yazi\config\`）

> [!warning] 目录名不同，但文件完全通用
> 这两个目录里的文件都是 **TOML / Lua 纯文本，没有任何平台差异**。所以「同步」= 让四台机器的这个目录里装**同一批文件**。难点只在「目录路径不一样」——下面用 git 直接绕过它。

> [!check] 备选知识 · YAZI_CONFIG_HOME
> yazi 官方支持 `YAZI_CONFIG_HOME` 环境变量改配置目录（例：`YAZI_CONFIG_HOME=~/.config/yazi-alt yazi`）。本课**不用它**——直接在默认目录里 git，连环境变量都省了。`YAZI_CONFIG_HOME` 留作「想把 yazi 配置塞进一个统一 dotfiles 大仓库」时的备选。

## 动手 · 把配置变成 git 仓库（以 WSL 为主）

1. **进配置目录，建 git 仓库 + `.gitignore`**（排除每机本地的插件和标签数据）：

   ```sh
   cd ~/.config/yazi
   git init
   printf 'plugins/\ntags/\n' > .gitignore
   ```

   `plugins/` 是 `ya pkg add` 装的第三方插件（每机一行命令重装即可）；`tags/` 是标签数据（按绝对路径存，跨机器不通用，见下方）。入库的只有**你写的** `yazi.toml` / `keymap.toml` / `init.lua`（和可选的 `theme.toml`）。

2. **提交 + 推到远端**。先在 GitHub 建一个空仓库 `yazi-config`，然后：

   ```sh
   git add -A
   git commit -m "yazi config"
   git remote add origin git@github.com:你的用户名/yazi-config.git
   git branch -M main
   git push -u origin main
   ```

3. **Windows 上拿同一份**。Windows 的默认配置目录就是 `%AppData%\yazi\config\`，直接 clone 进去（第 5 课你已配过，先备份再覆盖）：

   ```powershell
   # PowerShell
   Move-Item "$env:APPDATA\yazi\config" "$env:APPDATA\yazi\config.bak"
   git clone git@github.com:你的用户名/yazi-config.git "$env:APPDATA\yazi\config"
   ya pkg add boydaihungst/simple-tag   # 重装插件（plugins/ 没入库）
   ```

   Mac / 干净 Linux 同理：clone 进 `~/.config/yazi/`，再 `ya pkg add boydaihungst/simple-tag`。

4. **验证两端一致**：Windows 启 yazi，<kbd>t</kbd><kbd>g</kbd> 能打标签、<kbd>t</kbd><kbd>f</kbd> 能筛选 → 配置已同步。以后 WSL 改配置 → `git push`，Windows → `cd %AppData%\yazi\config && git pull`，齐了。

> [!warning] clone 后必须重装插件
> `plugins/` 被 gitignore 了，clone 下来的新机**没有** `simple-tag`。若不跑 `ya pkg add`，`init.lua` 里的 `require("simple-tag")` 找不到插件，yazi 启动会报错。所以「clone + `ya pkg add`」是每台新机的固定两步。

## 为什么标签不跟着同步

> [!note] 复习第 5 课查实的事实
> 标签存在 `tags/` 目录里，键是**被标文件的父目录绝对路径**。WSL 是 `/home/...`、Windows 是 `C:\Users\...`——**键不同，标签不能跨机器 / 跨 OS 迁移**。所以上面 `.gitignore` 排掉 `tags/`，标签视作每机本地；dotfiles 只搬配置文件。详见 [[learning-records/0004-lesson5-tags-verified-and-storage|学习记录 0004]]。

## 跨平台注意 · 搜索依赖

> [!warning] clone 配置不够，搜索工具也要装
> <kbd>s</kbd>（按名搜索）依赖 `fd` 或 `fdfind`。这台 WSL 有 `fdfind`（yazi 自动回退）能用；Mac 用 `brew install fd`；但一台**两者都没装**的干净 Linux / 容器上 <kbd>s</kbd> 会失效——clone 了配置还得补装 `fd`。<kbd>S</kbd>（内容搜索）靠 `ripgrep`（`rg`），同理。详见 [[learning-records/0003-lesson4-s-search-fdfind-fallback|学习记录 0003]]。

> [!question] 检索练习
> 1. 想在默认按键之上「加」自己的键（不覆盖默认），`keymap.toml` 里用哪个关键字开头？
> 2. WSL 和 Windows 的 yazi 默认配置目录分别是？
> 3. 为什么 `tags/` 不入库、标签不能跨机器带走？
> 4. 在新机器 clone 完配置后，还必须跑哪条命令，标签功能才不会启动报错？

<details>
<summary>看答案</summary>

1. `prepend_`（如 `prepend_keymap`；想追加在后用 `append_`）。
2. `~/.config/yazi/`（Unix）与 `%AppData%\yazi\config\`（Windows）。
3. 标签按「被标文件的父目录绝对路径」存；WSL 的 `/home/...` 与 Windows 的 `C:\Users\...` 键不同，跨机器 / 跨 OS 不互通。
4. `ya pkg add boydaihungst/simple-tag`（`plugins/` 被 gitignore，每机要重装）。

</details>

## 下一步

下一课**自定义命令与快捷键**：在 `keymap.toml` 里绑自己的键、把常用的「组合动作」做成一个命令，让 yazi 真正长成你的工作流。今天搭好的 git 仓库会让那节课的改动自动同步到所有机器。

---

[[0006-file-tags|← 第 5 课 · 文件标签]] · **第 6 / 8 课** · [[0008-custom-commands|第 7 课 · 自定义命令与快捷键 →]]

*主源：[官方 Configuration 文档](https://yazi-rs.github.io/docs/configuration/overview/)（v26.5.6，已逐条核实：四个配置文件、`prepend_*`/`append_*` 混合机制、Unix / Windows 配置目录、`YAZI_CONFIG_HOME` 环境变量）、[默认配置（shipped tag）](https://github.com/sxyazi/yazi/tree/shipped/yazi-config/preset)。标签存储位置见 [[learning-records/0004-lesson5-tags-verified-and-storage|学习记录 0004]]。*

> [!tip] 💡 问老师
> 这节课的卡点多半在 git 远端（SSH key、仓库权限）或 Windows 上 clone 路径拼错。把 `git push` 或 Windows 启动 yazi 的报错连同现象贴我，我帮你查。两端都能 <kbd>t</kbd><kbd>g</kbd> 打标签，这节课就成了。

📋 [[reference/0001-yazi-config-reference|配置速查表（参考文档）]] —— 四个文件 + prepend/append + 目录/env 速查，可打印。
