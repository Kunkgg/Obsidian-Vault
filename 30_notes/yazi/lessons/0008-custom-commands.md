# 自定义命令与快捷键

*Yazi 课程 · 第 7 课（结业） — 把 yazi 长成你的形状——绑自己的键、串自己的动作、跑自己的 shell 命令，还跨平台同键。这是最后一节课。*

> [!tip] 本课的胜利
> 加两个你会一直用的键：<kbd>!</kbd>「在这开终端」（WSL 开 zsh、Windows 开 PowerShell，同键）和 <kbd>g</kbd><kbd>d</kbd>「跳到下载目录」。改完 `git push`，Windows `pull` 一下就同步——第 6 课搭好的管道直接受益。做完这节课，你就具备**全套 yazi 自定义能力**。

## 先搞懂 · 一条键的三件套

每条自定义键就三个字段，你在第 5 课已经写过一条：

```toml
{ on = [ "t", "g" ], run = "plugin simple-tag -- toggle-tag", desc = "切换标签" }
```

| 字段 | 含义 |
|---|---|
| `on` | 按什么键——单键 `["!"]` 或组合 `["g","d"]` 或 `<C-a>` |
| `run` | **做什么**——本课的核心，下面展开 |
| `desc` | 说明文字，按 <kbd>?</kbd> 或键前缀停顿时会显示 |

## `run` 能装三种东西

| 类型 | 样子 | 你见过？ |
|---|---|---|
| ① 内置动作 | `cd ~/Downloads`、`copy path`、`hidden toggle` | 第 5 课的 fetcher 算半个 |
| ② `shell` 外部命令 | `shell -- trash-put %s` | 本课重点 |
| ③ `plugin` Lua | `plugin simple-tag -- toggle-tag` | ✅ 第 5 课 |

> [!note] 自定义命令的本质 · 串联
> `run` 可以是**一个动作字符串**，也可以是**动作数组**：`run = ["act1", "act2"]`——一个键依次跑多个动作。大部分需求一个动作就够；想「先 X 再 Y」就写数组。这就是 yazi 里「自定义命令」的全部秘密。

## 重点 · `shell` 动作（最有威力）

用 `shell` 在 yazi 里直接跑系统命令，文件路径用模板参数代入：

| 参数 | 替换成 |
|---|---|
| `%h` | 光标悬停文件的路径 |
| `%s` | 所有选中文件的路径 |
| `%s1` `%s2` | 第 1、2 个选中文件 |
| `%d` | 选中文件所在的目录 |
| `%%` | 字面 `%` 本身 |

三种执行模式：

- `--block`：**接管屏幕**跑交互程序（开终端、`git log`、`htop`），程序退出后回 yazi。
- `--orphan`：**后台**跑，detached——yazi 退了它也活着（开个 GUI 程序）。
- `--interactive`：弹输入框让你**手输**命令（默认 <kbd>:</kbd> 就是这个）。

> [!warning] 用 -- 免转义
> 命令里带 `%s` 时，推荐用 `--`（end-of-options）写法：`shell -- trash-put %s`，比 `shell "trash-put %s"` 少一层引号转义、干净得多。官方文档也这么建议。

## 跨平台同键 · `for`（直击你的跨平台需求）

用 `for` 把一条键限定到某个 OS；不写就是全平台。取值：`"unix"`（Linux+macOS+Android）、`"linux"`、`"macos"`、`"windows"`、`"android"`。同一个 `on` 配不同 `for`，就能**同键不同命令**：

```toml
[mgr]
prepend_keymap = [
  { on = [ "!" ], run = 'shell "$SHELL" --block', desc = "在这开终端", for = "unix" },
  { on = [ "!" ], run = 'shell "pwsh" --block',   desc = "在这开终端", for = "windows" },
]
```

> [!warning] Windows 那行
> Windows 没有 `$SHELL`，所以显式写 `pwsh`（PowerShell 7）。没装 pwsh 就换成 `powershell`（Windows 自带的 Windows PowerShell）。macOS 不单列——它属于 `unix`，`$SHELL` 就是你的 zsh，第一条就覆盖了。

## 书签 · `cd` + `g` 系列

<kbd>g</kbd> 是默认的「跳转」前缀键（按一下会弹出已绑定的目标）。加自己的书签就是给 `g` 配 `cd` 动作：

```toml
{ on = [ "g", "d" ], run = "cd ~/Downloads", desc = "跳到下载", for = "unix" },
{ on = [ "g", "d" ], run = 'cd "D:\\Downloads"',  desc = "跳到下载", for = "windows" },
```

Windows 路径的反斜杠在 TOML 里要写成 `\\`（转义）；或用 `cd D:` 直接切盘。

## 动手 · 加这 2 个键

> [!check] 跟着做（WSL）
> 1. 编辑 `~/.config/yazi/keymap.toml`，在 `[mgr]` 下已有的 `prepend_keymap` 数组里**追加**这 4 条（`!` 各 OS 一条、`g d` 各 OS 一条；Windows 路径按你机器改）。
> 2. **重启 yazi**（<kbd>q</kbd> 退出再进）。
> 3. 验证：在某目录按 <kbd>!</kbd> → 进入当前目录的 shell，`exit` 回 yazi；按 <kbd>g</kbd><kbd>d</kbd> → 跳到下载目录。
> 4. `cd ~/.config/yazi && git add -A && git commit -m "custom keys" && git push`
> 5. Windows 上：`cd %AppData%\yazi\config && git pull`，重启 yazi，按 <kbd>!</kbd> 应开 PowerShell。

> [!warning] 改键的原理
> yazi 选**第一个匹配**的键执行，而 `prepend_keymap` 优先级高于默认。所以「改掉某个默认键」= 用 `prepend_keymap` 把同一个 `on` 绑到新动作。想**彻底废掉**某个默认键又不重写整个 keymap，用虚拟动作 `noop`：`{ on = ["g","c"], run = "noop" }`。

## 进阶锦囊（知道有这些就行，不强制做）

- **组合键**：`<C-a>`=Ctrl+a、`<A-j>`=Alt+j、`<S-k>`=Shift+k。macOS 没 Alt 键，得在终端把 Option 映射成 Alt。
- **复制路径统一用 `/`**：`copy path --separator=unix`，Windows 上复制出来的也是正斜杠路径。
- **扁平视图**（连子目录一起看）：`run = 'search_do --via=fd --args="-d 3"'`。
- **多行脚本**：复杂 shell 用 TOML 的三引号 `'''…'''` 写成多行。
- 想要更强的书签 / 模糊跳转，去 [awesome-yazi](https://github.com/AnirudhG07/awesome-yazi) 挑插件，别自己从零写 Lua。

> [!question] 检索练习
> 1. 想让一个键依次跑两个动作，`run` 怎么写？
> 2. 「在这开终端、接管屏幕」，用哪个动作加哪个模式？
> 3. 同一个键在 Windows 上跑不同命令，用什么字段限定平台？
> 4. 想废掉默认的 `g c` 但不重写整个 keymap，`run` 写什么？

<details>
<summary>看答案</summary>

1. 写成数组：`run = ["act1", "act2"]`。
2. `shell "$SHELL" --block`（`--block` 接管屏幕，`$SHELL` 换成你要的 shell）。
3. `for = "windows"`（Unix 用 `"unix"`）。
4. `noop`：`{ on = ["g","c"], run = "noop" }`。

</details>

## 🎓 结业 · 你现在会了

装好并跨平台同步 → 纯键盘导航 → 选中/复制/移动/重命名 → 批量改名 + 拷路径 → 多 tab + 三种查找 → 文件标签 → 配置 dotfiles 同步 → **自定义命令与快捷键**。mission 里的每一条诉求都有了落点。

往哪走：

- **按需挑插件**：[awesome-yazi](https://github.com/AnirudhG07/awesome-yazi)（模糊跳转、预览增强、更强书签……）。记得 `ya pkg add` 装完要 `git` 处理一下——`plugins/` 被 gitignore 了，每机重装。
- **遇问题问社区**：[GitHub Discussions](https://github.com/sxyazi/yazi/discussions)（维护者亲自回）或 [r/yazi](https://www.reddit.com/r/yazi/)。配置疑难、跨平台差异都能问。
- **养自己的 keymap**：每次发现「这个操作我天天做、却要点好几下」，就给它绑个键、push。几周后 yazi 就完全是你的了。

所有课程文件都在 `~/.config/yazi/` 的 git 仓库里，跨机器跟着你走。想回看，[[0001-yazi-config-reference|配置速查表]]打印一张贴旁边。

---

[[0007-config-and-dotfiles|← 第 6 课 · 配置与跨平台同步]] · **第 7 / 8 课 · 结业 🎓** · 课程全 8 节完结

*主源：[官方 keymap.toml 文档](https://yazi-rs.github.io/docs/configuration/keymap/)（v26.5.6，已逐条核实：`run` 字符串/数组、`shell` 全部模板参数与 `--block`/`--orphan`/`--interactive`/`--`、`for` 取值、`cd` 书签、`noop`、键位记法 `<C-a>`）。跨平台 `!` 开终端的思路参考 [这篇日文实践](https://zenn.dev/kabec_dev/articles/9deed65b6b6519)（已转译为 v26 格式）。*

> [!tip] 💡 问老师
> 结业课，卡点多半在 Windows 的 `pwsh` 有没有装、或 TOML 路径反斜杠没转义。把 <kbd>!</kbd> 或 <kbd>g</kbd><kbd>d</kbd> 按了没反应的现象（连同你的 `keymap.toml`）贴我，我帮你查。两边都能用，这门课就圆满了。
