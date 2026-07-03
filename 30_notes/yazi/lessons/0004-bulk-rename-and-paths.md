# 批量改名与拷路径

*Yazi 课程 · 第 3 课 — 项目里最常用、ranger/lf 最不顺手的两件事——yazi 都做到了一键。*

> [!tip] 本课的胜利
> 选中一批文件，用编辑器一次性把它们全改名；以及把任意文件**的路径 / 文件名**一键送进剪贴板，粘到别处用。

## 批量改名：选中 + <kbd>r</kbd>

第 2 课里 <kbd>r</kbd> 是改单个文件名。当你**选中多个**文件再按 <kbd>r</kbd>，yazi 会打开你的 `$EDITOR`，把所有名字一列列出来让你改：

1. 多选若干文件（<kbd>Space</kbd> 逐个，或 <kbd>v</kbd> 可视区间）。
2. 按 <kbd>r</kbd> —— 编辑器弹出，每行一个文件名。
3. 改这些名字（**行数不能变**，只能改内容）。
4. 保存退出 → yazi 列出「将改哪些」，确认后批量生效。

> [!note] 已为你设置
> 你的 `$EDITOR` 之前没设，我已在 `.zshrc` 设成 `vim`（你熟 vim 基本操作，正好和 yazi 的 vim 风格一致）。批量改名时编辑器弹出，改完按 <kbd>Esc</kbd> 再 <kbd>:</kbd><kbd>w</kbd><kbd>q</kbd> 保存退出。想换 nano？把 `.zshrc` 里 `EDITOR=vim` 改成 `EDITOR=nano` 即可。

> [!warning] 注意
> 批量改名时**不要增删行**——yazi 按行一一对应。改完行数对不上会报错取消，不会乱动你的文件。想删掉某个文件，用 <kbd>d</kbd>，不是在编辑器里删行。

对比：ranger 要敲 `:bulkrename`，lf 得靠插件或手敲命令；yazi 就是「选中 + <kbd>r</kbd>」，最直接。

## 拷路径 / 拷文件名：<kbd>c</kbd> 前缀

<kbd>c</kbd> 是个「前缀键」——先按 <kbd>c</kbd>，再按第二个键决定拷什么（和第 1 课的 <kbd>g</kbd><kbd>g</kbd> 同一个套路）：

| 键 | 拷进剪贴板的内容 |
|---|---|
| <kbd>c</kbd> <kbd>c</kbd> | 文件完整路径 |
| <kbd>c</kbd> <kbd>d</kbd> | 所在目录路径 |
| <kbd>c</kbd> <kbd>f</kbd> | 文件名（含扩展名） |
| <kbd>c</kbd> <kbd>n</kbd> | 文件名（不含扩展名） |

> [!note] 记忆钩子
> **c** = copy；第二个字母是内容：**c**omplete path（完整路径）、**d**irectory（目录）、**f**ull name（文件名）、**n**ame no ext（去扩展名）。拷的是光标所在项（有选中时拷选中集合）。

> [!warning] WSL 剪贴板要你实测
> 你的剪贴板走 `xsel`（已验证可用）。但 xsel 写的是 Linux X 剪贴板——**能不能同步到 Windows 剪贴板**（让你在 Windows 程序里 Ctrl+V）需要你亲自测：拷一个路径，去 Windows 里粘贴看看。若不通，告诉我，我给你换 `wl-clipboard`（WSLg 原生，能同步到 Windows）。

## 预告：<kbd>;</kbd> 与 <kbd>:</kbd>

还有两个键先记一眼：<kbd>;</kbd> 对选中项**跑一条 shell 命令**（异步），<kbd>:</kbd> 同步跑（等它结束）。比如选中几个图片按 <kbd>;</kbd> 输 `convert ...` 批处理。这属于「自定义工作流」，第 6 课再细讲。

## 动手验证（沙盒）

> [!check] 跟着做
> 1. `cd ~/yazi-practice && y`。
> 2. 用 <kbd>v</kbd> 可视模式选中 `a.txt b.txt c.txt`。
> 3. 按 <kbd>r</kbd> → vim 弹出三行；改成 `a1.txt b1.txt c1.txt`，<kbd>Esc</kbd> 然后 <kbd>:</kbd><kbd>w</kbd><kbd>q</kbd> 存盘退出 → 确认改名。
> 4. 光标停在某文件，按 <kbd>c</kbd><kbd>c</kbd> 拷完整路径。
> 5. 退出 yazi（<kbd>q</kbd>），在 shell 里 `xsel -bo` 看刚拷的内容；再去 Windows 里 Ctrl+V 测同步。
> 6. 再试 <kbd>c</kbd><kbd>f</kbd>（文件名）、<kbd>c</kbd><kbd>n</kbd>（去扩展名）。

> [!question] 检索练习 · 答案都是两键
> 1. 拷文件完整路径？
> 2. 拷所在目录路径？
> 3. 拷文件名（含扩展名）？
> 4. 拷文件名（不含扩展名）？

<details>
<summary>看答案</summary>

1. <kbd>c</kbd><kbd>c</kbd>　2. <kbd>c</kbd><kbd>d</kbd>　3. <kbd>c</kbd><kbd>f</kbd>　4. <kbd>c</kbd><kbd>n</kbd>

批量改名则是另一个键：**多选 + <kbd>r</kbd>**（编辑器批量改）。

</details>

## 下一步

下一课进入**多 tab 与查找**：用 tab 并行处理多个目录（<kbd>t</kbd><kbd>t</kbd>、<kbd>1</kbd>-<kbd>9</kbd>、<kbd>[</kbd><kbd>]</kbd>），以及过滤 / 查找 / 搜索（<kbd>f</kbd>、<kbd>/</kbd>、<kbd>s</kbd>、<kbd>S</kbd>）。

---

[[0003-selection-and-file-ops|← 第 2 课 · 选择与文件操作]] · **第 3 / 8 课** · [[0005-tabs-filter-find|第 4 课 · 多 tab 与查找 →]]

*主源：[官方 Quick Start · Copy paths / Find / Search](https://yazi-rs.github.io/docs/quick-start/#copy-paths)。批量改名细节见 [Tips · Bulk rename](https://yazi-rs.github.io/docs/tips/#bulk-rename)。*

> [!tip] 💡 问老师
> 重点测两件事反馈我：① 批量改名顺不顺；② <kbd>c</kbd> 拷的路径能不能在 Windows 里 Ctrl+V。第②个不通我就给你换剪贴板方案。
