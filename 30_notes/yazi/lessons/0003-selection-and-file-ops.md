# 选择与文件操作：复制 / 剪切 / 重命名 / 新建 / 删除

*Yazi 课程 · 第 2 课 — 按键大多和 vim 一致；但 ranger / lf 的肌肉记忆有几个危险陷阱，必须先纠偏。*

> [!tip] 本课的胜利
> 纯键盘完成一次完整操作：选中几个文件 → 复制/剪切到别处粘贴 → 重命名 → 新建目录 → 安全删除。日常文件管理不再需要鼠标或 `cp`/`mv`。

## 先选，再操作

yazi 和 vim / ranger / lf 一样：先选中目标，再下命令。

| 键 | 动作 |
|---|---|
| <kbd>Space</kbd> | 选中 / 取消选中当前项 |
| <kbd>v</kbd> | 进入可视模式（区间选择，像 vim 的 <kbd>v</kbd>） |
| <kbd>V</kbd> | 可视模式（反向：取消区间内的选中） |
| <kbd>Ctrl</kbd>+<kbd>a</kbd> | 全选 |
| <kbd>Ctrl</kbd>+<kbd>r</kbd> | 反选 |
| <kbd>Esc</kbd> | 清空选择 |

选中的项左侧会有高亮标记。下面所有「复制/剪切/删除」都作用于**当前选中的集合**（没选中时作用于光标所在项）。

## 复制 / 剪切 / 粘贴

| 键 | 动作 |
|---|---|
| <kbd>y</kbd> | 复制（yank）选中项 |
| <kbd>x</kbd> | 剪切（cut）选中项 |
| <kbd>p</kbd> | 粘贴；遇同名会跳过 |
| <kbd>P</kbd> | 粘贴；遇同名会**覆盖** |
| <kbd>Y</kbd> 或 <kbd>X</kbd> | 取消当前的复制/剪切状态 |

> [!warning] ⚠ 来自 lf / ranger 的致命陷阱
> 在 **lf** 里 `d` 是「剪切」，在 **ranger** 里 `dd` 是「剪切」。但在 **yazi** 里，`d` 是「**删除（进回收站）**」！如果你照 lf/ranger 的习惯按 <kbd>d</kbd> 想剪切，文件会被删掉。
>
> **记住：yazi 的剪切是 <kbd>x</kbd>，不是 <kbd>d</kbd>。**（钩子：x 像剪刀 ✂ → cut）

> [!warning] vim 用户的注意
> vim 里 `x` 是删一个字符。yazi 里 `x` 是**剪切整个文件**——完全不同的语义。`y`（复制）和 `p`（粘贴）则和 vim 一致，可放心沿用。

## 重命名 / 新建 / 删除

| 键 | 动作 |
|---|---|
| <kbd>r</kbd> | 重命名（rename） |
| <kbd>a</kbd> | 新建；名字以 `/` 结尾则建目录 |
| <kbd>d</kbd> | 丢进回收站（可恢复） |
| <kbd>D</kbd> | **永久删除**（不可恢复，慎用） |
| <kbd>o</kbd> / <kbd>Enter</kbd> | 用默认程序打开 |
| <kbd>O</kbd> | 交互式选择用哪个程序打开 |

> [!note] 小技巧
> <kbd>a</kbd> 新建时，输入 `src/`（末尾带斜杠）就直接建目录 `src`；输入 `todo.txt` 则建文件。<kbd>d</kbd> vs <kbd>D</kbd> 的区别就是「回收站」vs「永久删除」——养成默认用 <kbd>d</kbd> 的习惯，只有确定不要了才 <kbd>D</kbd>。

## ranger ↔ lf ↔ yazi 对照

| 动作 | ranger | lf | yazi |
|---|---|---|---|
| 复制 | <kbd>y</kbd><kbd>y</kbd> | <kbd>y</kbd> | <kbd>y</kbd> |
| 剪切 | <kbd>d</kbd><kbd>d</kbd> | <kbd>d</kbd> | <kbd>x</kbd> |
| 粘贴 | <kbd>p</kbd><kbd>p</kbd> | <kbd>p</kbd> | <kbd>p</kbd> |
| 重命名 | <kbd>c</kbd><kbd>w</kbd> | <kbd>c</kbd> | <kbd>r</kbd> |
| 删除 | <kbd>d</kbd><kbd>D</kbd> | <kbd>D</kbd> | <kbd>d</kbd>/<kbd>D</kbd> |

规律：ranger 是「双键」、lf 是「单键但 d=剪切」，yazi 是「单键且 `x`=剪切 / `d`=删除」。从 lf 迁移最省力，从 ranger 迁移要改掉双键习惯。

## 动手验证（在沙盒里练，安全）

我已经在 `~/yazi-practice/` 建了几个假文件（`a.txt b.txt c.txt notes.md` + `sub/`）。在这里随便折腾：

> [!check] 跟着做
> 1. `y` 启动，导航进 `~/yazi-practice`（或先 `cd ~/yazi-practice && y`）。
> 2. 光标停在 `a.txt`，按 <kbd>Space</kbd>；移到 `b.txt`，再 <kbd>Space</kbd> —— 两个都被选中。
> 3. 按 <kbd>y</kbd> 复制；进 `sub/`（<kbd>l</kbd>），按 <kbd>p</kbd> 粘贴 → `sub` 里多了两份。
> 4. 退回上级，光标到 `c.txt`，按 <kbd>r</kbd> 改名为 `c.md`。
> 5. 按 <kbd>a</kbd>，输入 `draft/` 回车 → 新建目录 `draft`。
> 6. 选中任意一份副本，按 <kbd>d</kbd> → 进回收站（列表里消失）。想确认能恢复，去 `~/.local/share/Trash` 看。
> 7. <kbd>Esc</kbd> 清空选择。

> [!question] 检索练习 · 先想再点开（答案都是单键）
> 1. 复制（yank）选中文件？
> 2. 剪切（cut）选中文件？
> 3. 粘贴？
> 4. 丢进回收站？
> 5. 重命名？
> 6. 新建（末尾 `/` 建目录）？

<details>
<summary>看答案</summary>

1. <kbd>y</kbd>　2. <kbd>x</kbd>　3. <kbd>p</kbd>　4. <kbd>d</kbd>　5. <kbd>r</kbd>　6. <kbd>a</kbd>

两个钩子：**x 像剪刀=cut**；**d=delete=回收站**（lf 用户尤其别把 d 当剪切）。

</details>

## 下一步

单文件操作会了，下一课上「批量」：多选后**批量改名**（用 `$EDITOR` 一次改一列），以及**批量拷贝路径/文件名**到剪贴板（<kbd>c</kbd><kbd>c</kbd>/<kbd>c</kbd><kbd>d</kbd>/<kbd>c</kbd><kbd>f</kbd>/<kbd>c</kbd><kbd>n</kbd>）——这对你在项目里复制文件名/路径特别有用。

---

[[0002-core-navigation|← 第 1 课 · 核心导航]] · **第 2 / 8 课** · [[0004-bulk-rename-and-paths|第 3 课 · 批量改名与拷路径 →]]

*主源：[官方 Quick Start · Selection / File operations](https://yazi-rs.github.io/docs/quick-start/#file-operations)。*

> [!tip] 💡 问老师
> 练的时候任何不顺手都来问我——尤其 lf/ranger 习惯冲突的地方，我帮你针对性纠偏。
