# 让 Yazi 跑起来，并用 `y` 跳转目录

*Yazi 课程 · 第 0 课 — 安装只是一次性动作；`y` wrapper 才是你以后每天都会用的东西。*

> [!tip] 本课的胜利
> 敲 <kbd>y</kbd> 启动 yazi，导航进某个目录，按 <kbd>q</kbd> 退出 —— 你的 shell 已经自动 `cd` 到了那个目录。从此 yazi 就是你的「可视化的 `cd`」。

## 我们装了什么

官方把 yazi 编译成两套 Linux 二进制：**GNU**（依赖系统 glibc）和 **Musl**（静态链接，不依赖 glibc）。你的 WSL 是 Ubuntu 22.04，自带 **glibc 2.35**，而新版 yazi 的 GNU 构建要求 glibc ≥ 2.39 —— 所以之前 apt 装的那个一跑就崩（`GLIBC_2.39 not found`）。

解法：用 **musl** 构建，它把 C 库打包进二进制，在任何 Linux 上都能跑。我们把 `v26.5.6` 的 musl 版装到了 `~/.local/bin/`：

- `yazi` —— 文件管理器本体
- `ya` —— 命令行助手，以后装插件、补全全靠它

> [!tip] 已清理
> 原先坏的 apt 包（`/usr/bin/yazi`）已用 `sudo apt remove yazi` 删除。现在 `yazi` 全局唯一指向 `~/.local/bin/` 里的 musl 版，无歧义。

## `y` wrapper：退出即跳转

这是本课的核心。默认情况下，从 yazi 退出**不会**改变你 shell 的工作目录 —— 你用 ranger / lf 大概也踩过这点：浏览了半天，退出后还在原地。yazi 用一个小技巧解决了它：

1. 启动时给 yazi 一个临时文件路径（`--cwd-file`）；
2. 退出时，yazi 把「你最后所在的目录」写进这个文件；
3. shell 读出来，替你 `cd` 过去。

下面这个函数把三步打包好，我已经追加进了你的 `~/.zshrc`：

```sh
function y() {
	local tmp="$(mktemp -t "yazi-cwd.XXXXXX")" cwd
	command yazi "$@" --cwd-file="$tmp"
	IFS= read -r -d '' cwd < "$tmp"
	[ "$cwd" != "$PWD" ] && [ -d "$cwd" ] && builtin cd -- "$cwd"
	command rm -f -- "$tmp"
}
```

注意里面是 `command yazi`（避免函数递归调用自己），以及最后只在新目录与当前不同、且确实存在时才 `cd`。

## 动手验证（你的反馈回路）

> [!check] 跟着做一遍
> 1. 让新配置生效：`exec zsh`（或 `source ~/.zshrc`）。
> 2. 敲 <kbd>y</kbd> 启动 yazi。
> 3. 用 <kbd>l</kbd> 进入一个子目录（比如 `Repos`），再 <kbd>l</kbd> 进一层。
> 4. 按 <kbd>q</kbd> 退出。
> 5. 敲 `pwd` —— 应该显示你刚才所在的那个目录。✅
>
> 再试一次，但这次用 <kbd>Q</kbd>（大写）退出：`pwd` 应**不变**。这就是「想跳就 <kbd>q</kbd>，不想跳就 <kbd>Q</kbd>」。

> [!question] 检索练习 · 先别看答案
> 退出 yazi 时，想让 shell 跟着跳到所在目录，按哪个键？不想跳呢？

<details>
<summary>看答案</summary>

想跳按 <kbd>q</kbd>；不想跳按 <kbd>Q</kbd>。这个差别来自 wrapper 函数本身——它总是 `cd`，但 yazi 在按 <kbd>Q</kbd> 时不会写出新的 cwd，于是 `$PWD` 保持不变。

</details>

## 下一步

yazi 已经能用、也能跳转了。下一课我们正式练**核心导航**（<kbd>h</kbd><kbd>j</kbd><kbd>k</kbd><kbd>l</kbd>、<kbd>g</kbd><kbd>g</kbd>、<kbd>G</kbd>、<kbd>z</kbd>、<kbd>Z</kbd>），并把你在 ranger / lf 里的肌肉记忆迁移过来。

---

**第 0 / 8 课** · 下一篇：[[0002-core-navigation|第 1 课 · 核心导航 →]]

*主源（推荐阅读）：[官方 Quick Start · Shell wrapper & Keybindings](https://yazi-rs.github.io/docs/quick-start/)。安装细节见 [Installation](https://yazi-rs.github.io/docs/installation/)。*

> [!tip] 💡 问老师
> 有任何不清楚的地方，直接问我（你的 agent 老师）——比如想搞懂 wrapper 函数里某一行的意思，或者 musl/glibc 的细节。
