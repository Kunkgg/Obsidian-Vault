# 核心导航：`hjkl`、`gg`、`G`、`z`、`Z`

*Yazi 课程 · 第 1 课 — 好消息：八成的键和 ranger / lf / vim 一模一样。重点是 yazi 独有的两个跳转键。*

> [!tip] 本课的胜利
> 纯键盘在目录树里上下穿梭，进出自如；并且能用 <kbd>Z</kbd> 一键瞬移到你常用的项目目录。

## 四个移动键（和 vim / ranger / lf 完全一致）

| 键 | 动作 |
|---|---|
| <kbd>h</kbd> | 回到上一级父目录 |
| <kbd>l</kbd> | 进入光标所在的子目录 |
| <kbd>j</kbd> | 光标下移一项 |
| <kbd>k</kbd> | 光标上移一项 |

> [!note] 肌肉记忆迁移
> 你 ranger / lf 里这套手感**直接照搬**就行。方向键 <kbd>←</kbd><kbd>↓</kbd><kbd>↑</kbd><kbd>→</kbd> 也同样可用，但既然你熟 vim，就别退回方向键了。

## 列表内大跳

| 键 | 动作 |
|---|---|
| <kbd>g</kbd> <kbd>g</kbd> | 跳到列表最顶端（先 <kbd>g</kbd> 再 <kbd>g</kbd>） |
| <kbd>G</kbd> | 跳到列表最底端 |

## 大写 <kbd>J</kbd> / <kbd>K</kbd>：滚的是预览，不是列表

| 键 | 动作 |
|---|---|
| <kbd>K</kbd> | 在右侧**预览窗**里向上滚动 5 行 |
| <kbd>J</kbd> | 在右侧**预览窗**里向下滚动 5 行 |

> [!warning] 易混点
> 小写 <kbd>j</kbd><kbd>k</kbd> 移动的是**文件列表**里的光标；大写 <kbd>J</kbd><kbd>K</kbd> 滚动的是**预览内容**。当你预览一个长文件、想看下面的内容时，就用大写 <kbd>J</kbd>。这是 yazi 一个顺手的小设计，ranger/lf 里没有对应物。

## 两个跳转键（yazi 的杀手锏）

这是「快速跳转目录」的核心。普通移动靠 <kbd>h</kbd><kbd>l</kbd>，但跨越大目录树就得靠这两个：

| 键 | 动作 | 底层 |
|---|---|---|
| <kbd>z</kbd> | 模糊跳转：输几个字母匹配目录 | fzf（按当前目录扫描） |
| <kbd>Z</kbd> | 常用度跳转：瞬移到你最常去的目录 | zoxide（按访问频率） |
| <kbd>g</kbd> <kbd>Space</kbd> | 交互式输入路径跳转 | — |

> [!note] 刚才发生了什么
> 我发现你的 zoxide 装了却**没接进 shell**（db 是空的），所以 <kbd>Z</kbd> 本来是死的。我已经做了两件事：① 在 `.zshrc` 加了 `eval "$(zoxide init zsh)"`，于是 shell 里有了 `z` 命令；② 往 zoxide 喂了 `~`、`~/Repos`、`~/Repos/matt_skill_test` 三个真实目录，让 <kbd>Z</kbd> 现在就能用。
>
> **让它越用越聪明**：以后在 shell 里多用 `z <目录片段>` 代替 `cd`，zoxide 会记住你常去的地方，<kbd>Z</kbd> 就会把最常用的顶到最前面。

## 动手验证

> [!check] 在你的机器上跑一遍
> 1. `exec zsh`（让 zoxide 和 `y` 都生效）。
> 2. `cd ~ && y` 从家目录启动。
> 3. <kbd>l</kbd> 进入 `Repos`，再 <kbd>l</kbd> 进入 `matt_skill_test`；然后 <kbd>h</kbd> <kbd>h</kbd> 退回 家目录。
> 4. <kbd>g</kbd><kbd>g</kbd> 到顶，<kbd>G</kbd> 到底，感受大跳。
> 5. 按 <kbd>z</kbd>，输入 `matt`，回车 —— 模糊跳进 `matt_skill_test`。
> 6. 按 <kbd>Z</kbd>，看到 zoxide 列出的目录，选一个回车 —— 瞬移过去。
> 7. <kbd>q</kbd> 退出，`pwd` 应显示你最后所在的目录（第 0 课的 `y` 在收尾）。

> [!question] 检索练习 · 先想再点开
> 1. 用 fzf 模糊跳转目录，按哪个键？
> 2. 用 zoxide 按常用度跳转，按哪个键？
> 3. 进入光标所在的子目录，按哪个键？
> 4. 回到上一级父目录，按哪个键？

<details>
<summary>看答案</summary>

1. <kbd>z</kbd>　　2. <kbd>Z</kbd>　　3. <kbd>l</kbd>　　4. <kbd>h</kbd>

记忆钩子：**小写看当前目录（fzf 扫描当下），大写看全局历史（zoxide 记住你常去的）**。

</details>

## 下一步

导航搞定后，下一课进入**文件操作**：选择（<kbd>Space</kbd>、<kbd>v</kbd>）、复制/剪切/粘贴/重命名/新建/删除（<kbd>y</kbd> <kbd>x</kbd> <kbd>p</kbd> <kbd>r</kbd> <kbd>a</kbd> <kbd>d</kbd>）。

---

[[0001-install-and-y-wrapper|← 第 0 课 · 安装 + y wrapper]] · **第 1 / 8 课** · [[0003-selection-and-file-ops|第 2 课 · 选择与文件操作 →]]

*主源：[官方 Quick Start · Navigation / Find / Search](https://yazi-rs.github.io/docs/quick-start/#navigation)。zoxide 用法见 [zoxide 文档](https://github.com/ajeetdsouza/zoxide)。*

> [!tip] 💡 问老师
> 不清楚就问：比如 <kbd>z</kbd> 和 <kbd>Z</kbd> 弹出的界面怎么操作、zoxide 的「常用度」怎么算的，或者想把某个项目也喂进 zoxide。
