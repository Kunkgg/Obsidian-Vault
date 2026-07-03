# 文件标签（simple-tag 插件）

*Yazi 课程 · 第 5 课 — 给文件打个单字符标记，一键筛出所有同标记的文件——不靠目录结构、不靠改名，就能把「一类文件」聚到眼前。*

> [!tip] 本课的胜利
> 装好 `simple-tag` 插件，给文件打标签、按标签筛当前目录，让带某个标记的文件瞬间浮上来。这是 mission 里「跨目录管一类文件」的最轻手段。

## 先搞懂：标签在 yazi 里怎么工作

- 核心 yazi **没有内置标签**（不像 macOS Finder）。标签靠插件实现。
- `simple-tag` 的模型极轻：**一个标签 = 一个字符**。你给文件打上字符 `1`，它就有标签 `1`；再按 `1` 就取消。一个文件可同时挂多个标签字符。
- 它**明确支持 Linux / Windows / macOS**（README 声明，不走 macOS 原生标签），正合你跨平台的需求。
- 它**没有默认快捷键**——插件只提供一堆「动作」，键位由你挑。这正契合「不复杂」：本课只绑 **4 个**最常用的。

> [!warning] 本课第一次碰配置文件
> 前 5 课都是开箱即用的按键；这节课要写配置。好在只需一次性建好 yazi 的配置目录、写 3 个小文件。位置：**Linux/macOS** `~/.config/yazi/`，**Windows** `%APPDATA%\yazi\config\`（你这台 WSL 目前还没有这目录，下面会建）。

## 一次性配置（3 步）

### 第 1 步 · 建目录 + 装插件

```sh
mkdir -p ~/.config/yazi
ya pkg add boydaihungst/simple-tag
```

`ya pkg add` 是 yazi v25+ 的插件安装命令（旧的 `ya pack -a` 已废弃）。装完插件文件落在 `~/.config/yazi/plugins/simple-tag.yazi/`。

### 第 2 步 · 写 3 个配置文件（都是新建）

**① `~/.config/yazi/init.lua`** —— 插件初始化：

```lua
require("simple-tag"):setup({})
```

这个 `setup` 调用是**必需**的，哪怕给个空表——不写它插件不工作。

**② `~/.config/yazi/yazi.toml`** —— 让标签徽章显示在文件列表里：

```toml
[plugin]
prepend_fetchers = [
  { id = "simple-tag", url = "*",  run = "simple-tag", group = "simple-tag" },
  { id = "simple-tag", url = "*/", run = "simple-tag", group = "simple-tag" },
]
```

fetcher 负责渲染标签徽章。用 `prepend_fetchers`（追加在前）而**不是** `fetchers`——后者会**覆盖**掉 yazi 自带的 fetcher（比如显示 git 状态那个）。每个条目末尾的 `group = "simple-tag"` 是 **yazi v26+ 的必填项**：它把同组的 fetcher 编成一批一起跑，simple-tag 用自己的组名、不跟内置 fetcher 混。*少写它启动就报 `missing field group`*——simple-tag README 的主示例漏了这个字段（过时写法，别直抄），是它注释行里才给了 `group = "simple-tag"`。

**③ `~/.config/yazi/keymap.toml`** —— 绑定 4 个动作：

```toml
[mgr]
prepend_keymap = [
  { on = [ "t", "g" ], run = "plugin simple-tag -- toggle-tag", desc = "切换标签（弹窗后按一个字符）" },
  { on = [ "t", "f" ], run = "plugin simple-tag -- filter",     desc = "按标签筛选当前目录（弹窗后按字符）" },
  { on = [ "t", "c" ], run = "plugin simple-tag -- clear",      desc = "清除选中文件的全部标签" },
  { on = [ "t", "u" ], run = "plugin simple-tag -- toggle-ui",  desc = "切换徽章显示：图标/字符/隐藏" },
]
```

> [!warning] 两个最容易踩的坑
> **① `[mgr]` 不是 `[manager]`。** yazi v25 起把文件列表这层从 `[manager]` 改名成 `[mgr]`，按键也从 `on = "x"` 单键改成 `on = ["x","y"]` 数组。你装的是 v26，**必须**用新格式 `[mgr]` + 数组。网上老教程里的 `[[manager.prepend_keymap]]` 是过时写法，别抄。
>
> **② 我把标签键定在 `t g` / `t f` 等，刻意避开 `t t`。** 因为你第 4 课已验证 <kbd>t</kbd><kbd>t</kbd> = 新建 tab——README 里写的 `t t k`（切换标签）会跟它**撞键**（按完 `t t` yazi 要等一下才决定你是要建 tab 还是继续按 `k`）。所以我把切换标签挪到 `t g`（**t**ag / **g**），干净利落。你若更喜欢 README 原版的 `t t k`，照抄也行，只是要忍受那点延迟。

### 第 3 步 · 重启 yazi

退出当前 yazi（<kbd>q</kbd>），重新进，配置才生效。

## 核心按键（就 4 个）

| 键 | 动作 |
|---|---|
| <kbd>t</kbd><kbd>g</kbd> | **切换标签**：弹提示窗，按一个字符给当前/选中文件打上或取消该标签 |
| <kbd>t</kbd><kbd>f</kbd> | **按标签筛选**：弹提示窗，按一个字符，当前目录只剩带该标签的文件 |
| <kbd>t</kbd><kbd>c</kbd> | **清除**选中文件的全部标签 |
| <kbd>t</kbd><kbd>u</kbd> | **切换徽章显示**：图标 → 标签字符 → 隐藏（循环） |

> [!note] 记忆钩子
> 所有标签动作都是 <kbd>t</kbd> 前缀，第二键是动作首字母：**t**oggle 用 <kbd>g</kbd>、**f**ilter 用 <kbd>f</kbd>、**c**lear 用 <kbd>c</kbd>、**u**i 用 <kbd>u</kbd>。最常用就一个 <kbd>t</kbd><kbd>g</kbd>——「给这文件标一下」。

> [!warning] 筛选范围 = 当前目录
> <kbd>t</kbd><kbd>f</kbd> 筛的是**当前目录**里带某标签的文件（和第 4 课的 <kbd>f</kbd> 过滤同范围），**不递归**子目录。想跨目录找带标签的文件，得逐层进——这是 simple-tag 当前的局限，记住。*若按 <kbd>t</kbd><kbd>g</kbd> 却弹出新 tab*，说明你这台把建 tab 绑在了单键 <kbd>t</kbd> 上（而非 <kbd>t</kbd><kbd>t</kbd>），告诉我，我换个前缀。

## 动手验证（沙盒）

> [!check] 跟着做
> 1. 先完成上面「一次性配置」3 步，`cd ~/yazi-practice && y` 进沙盒。
> 2. **打标签：**光标停在 `a1.txt`，按 <kbd>t</kbd><kbd>g</kbd>，再按 <kbd>1</kbd> → 文件名旁应冒出徽章。没看到？按 <kbd>t</kbd><kbd>u</kbd> 把显示切到「字符」模式（图标→字符→隐藏循环），就能看见 `1`。
> 3. **第二个文件同标签：**移到 `c.md`，同样 <kbd>t</kbd><kbd>g</kbd> → <kbd>1</kbd>。
> 4. **筛选：**按 <kbd>t</kbd><kbd>f</kbd> → <kbd>1</kbd> → 当前目录只剩 `a1.txt` 和 `c.md`（都带标签 `1`）。<kbd>Esc</kbd> 退出筛选。
> 5. **多标签：**给 `b1.txt` 打 <kbd>t</kbd><kbd>g</kbd> → <kbd>2</kbd>。现在 <kbd>t</kbd><kbd>f</kbd>→<kbd>1</kbd> 不含它，<kbd>t</kbd><kbd>f</kbd>→<kbd>2</kbd> 只有它。
> 6. **清除：**选中 `a1.txt`，<kbd>t</kbd><kbd>c</kbd> → 它的标签全没了。

> [!tip] 若没生效
> 徽章没出现 / <kbd>t</kbd><kbd>g</kbd> 没反应，八成是配置没读到或 `[mgr]` 写错。核对 `~/.config/yazi/` 下三个文件的**文件名和内容**，确认重启过 yazi。把现象记下来反馈我。

> [!question] 检索练习 · 答案都是两键
> 1. 给当前文件打上 / 取消一个标签（弹窗后按字符）？
> 2. 只看当前目录里带某标签的文件？
> 3. 把选中文件的所有标签一次清掉？
> 4. 切换标签徽章怎么显示（图标/字符/隐藏）？

<details>
<summary>看答案</summary>

1. <kbd>t</kbd><kbd>g</kbd>（toggle，再按标签字符）　2. <kbd>t</kbd><kbd>f</kbd>（filter，再按字符）　3. <kbd>t</kbd><kbd>c</kbd>（clear）　4. <kbd>t</kbd><kbd>u</kbd>（ui）

一句话：**t 前缀 + 动作首字母**（g/f/c/u）。

</details>

## 下一步

下一课**配置与跨平台同步**：把今天碰到的 `yazi.toml` / `keymap.toml` / `init.lua` 讲透，并用一套 dotfiles（git + symlink）让 WSL / Windows / Mac / Linux 共用同一份配置——也顺便**实测标签到底存在哪个文件里**、能不能跨机器带走。

---

[[0005-tabs-filter-find|← 第 4 课 · 多 tab 与查找]] · **第 5 / 8 课** · [[0007-config-and-dotfiles|第 6 课 · 配置与跨平台同步 →]]

*主源：[simple-tag.yazi README](https://github.com/boydaihungst/simple-tag.yazi)（插件全部行为与按键选项的出处，已据此自配 4 键）、[官方 Keymap 文档](https://yazi-rs.github.io/docs/configuration/keymap/)（`[mgr]` 层与 `prepend_keymap` 格式）。安装命令 `ya pkg add` 已在本机 `ya pkg --help` 核实。*

> [!tip] 💡 问老师
> 这节课第一次写配置，最容易卡在 `[mgr]` 拼错或忘了 `init.lua`。配好后按 <kbd>t</kbd><kbd>g</kbd>→<kbd>1</kbd> 能看到徽章就成了。任何「按了没反应」的现象，连同三个配置文件内容一起发我，我帮你查。
