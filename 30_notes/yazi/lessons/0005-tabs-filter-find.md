# 多 tab 与查找

*Yazi 课程 · 第 4 课 — 两个让「快」真正落地的能力——并排开几个目录，以及在任意深度一秒定位文件。*

> [!tip] 本课的胜利
> 开多个 **tab** 并行处理不同目录，还能跨 tab 复制/移动文件；以及分清**过滤 / 查找 / 搜索**三种「找文件」的方式，按场合挑最快的那个，不再靠肉眼滚列表。

## 多 tab：并排打开几个目录

一个 tab 就是一个独立的目录视图——各自导航、互不打扰。你不必为了在两个目录间搬东西而反复 `cd` 进出。

| 键 | 动作 |
|---|---|
| <kbd>t</kbd><kbd>t</kbd> | 在当前目录新建一个 tab |
| <kbd>1</kbd>-<kbd>9</kbd> | 切到第 N 个 tab |
| <kbd>[</kbd> / <kbd>]</kbd> | 上一个 / 下一个 tab |
| <kbd>{</kbd> / <kbd>}</kbd> | 与上一个 / 下一个 tab 交换位置 |
| <kbd>Ctrl</kbd>+<kbd>c</kbd> | 关闭当前 tab |

> [!note] 记忆钩子
> <kbd>t</kbd> 是 tab 前缀（和你已经熟的 <kbd>g</kbd>、<kbd>c</kbd> 前缀一个套路），所以新建是 <kbd>t</kbd><kbd>t</kbd>；数字 <kbd>1</kbd>-<kbd>9</kbd> 直达；<kbd>[</kbd><kbd>]</kbd> 翻动。新 tab 继承你**当前所在目录**，不是 home。

> [!warning] 别按错退出键
> 关掉一个 tab 是 <kbd>Ctrl</kbd>+<kbd>c</kbd>。<kbd>q</kbd> 是退出**整个 yazi**（配合 `y` wrapper 还会 cd 到当前目录）。关单个 tab 别按成 <kbd>q</kbd>。

### 跨 tab 复制 / 移动——tab 的核心价值

yazi 的剪贴板（yank / cut）是**全局的**，不局限在一个 tab 里。所以最常见的玩法是：

1. tab A：导航到源目录，<kbd>Space</kbd> 选中文件，<kbd>y</kbd>（拷）或 <kbd>x</kbd>（剪）。
2. <kbd>t</kbd><kbd>t</kbd> 开新 tab，<kbd>l</kbd> 进目标目录。
3. <kbd>p</kbd> 粘贴——文件就过来了。

第 2 课学的 <kbd>y</kbd>/<kbd>x</kbd>/<kbd>p</kbd> 原封不动，只是换了张 tab。这也是 ranger/lf 用户最容易忽略的提速点。

## 找文件的三种姿势（本课重点）

yazi 有三个看起来很像、但**范围完全不同**的「找」。搞清区别，你就知道每个场合该用哪个，不用再滚长列表：

| 键 | 干什么 | 范围 |
|---|---|---|
| <kbd>f</kbd> | 边打字边**收窄可见列表**（隐藏不匹配项） | 当前目录 |
| <kbd>/</kbd> <kbd>?</kbd> | 移动**光标**到下一个 / 上一个匹配名 | 当前目录 |
| <kbd>n</kbd> <kbd>N</kbd> | 在已找到的匹配间向后 / 向前翻 | （配合 <kbd>/</kbd>） |
| <kbd>s</kbd> | 用 **fd** 按**文件名**递归搜 | 整棵子树 |
| <kbd>S</kbd> | 用 **ripgrep** 按**文件内容**递归搜 | 整棵子树 |

### 什么时候用哪个

- 只想看当前目录里某一类（比如所有 `.txt`）→ <kbd>f</kbd>，给当前视图加个临时筛子，<kbd>Esc</kbd> 清除。
- 当前目录文件多，想跳到某个名字（如 `report` 开头）→ <kbd>/</kbd> 输入，<kbd>n</kbd>/<kbd>N</kbd> 在匹配间翻——就是 vim 里那个 <kbd>/</kbd>，**不隐藏**任何文件，只挪光标。
- 不知道文件埋在哪层子目录，按**名字**找 → <kbd>s</kbd>（fd 递归）。
- 要找「哪个文件**里写了** XXX」→ <kbd>S</kbd>（ripgrep 搜内容）。

> [!note] 记忆钩子
> <kbd>f</kbd> = filter（收窄当前列表）；<kbd>/</kbd> 就是你 vim 里那个 <kbd>/</kbd>（当前列表里跳光标）；小写 <kbd>s</kbd> 搜**名字**、大写 <kbd>S</kbd> 搜**内容**——大写 = 更「深」，钻进文件里面去。<kbd>s</kbd>/<kbd>S</kbd> 跑起来时按 <kbd>Ctrl</kbd>+<kbd>s</kbd> 可取消。

> [!tip] ✅ 实测：<kbd>s</kbd> 在本机开箱可用
> 你这台 Ubuntu 把 fd 装成了 `fdfind`、`fd` 只是个 shell 别名——但 yazi 在找不到 `fd` 时**会自动回退到 `fdfind`**（已从 yazi 二进制确认），所以 <kbd>s</kbd> 正常工作，**无需手动建软链**。<kbd>S</kbd> 用的 `rg` 是真二进制（`/usr/bin/rg`），也没问题。*跨平台提醒*：换到一台**既没 `fd` 也没 `fdfind`** 的机器（比如干净容器），<kbd>s</kbd> 才会失效——届时装个 fd 即可。

## 动手验证（沙盒）

> [!check] 跟着做
> 1. `cd ~/yazi-practice && y`。
> 2. **tab：**<kbd>t</kbd><kbd>t</kbd> 开新 tab → <kbd>1</kbd> 回第一个 → <kbd>]</kbd> 切下一个 → <kbd>Ctrl</kbd>+<kbd>c</kbd> 关掉当前的。
> 3. **跨 tab 复制：**tab 1 选中 `a1.txt`（<kbd>Space</kbd>），<kbd>y</kbd> 拷；<kbd>t</kbd><kbd>t</kbd> 新 tab，<kbd>l</kbd> 进 `sub`，<kbd>p</kbd> 粘贴 → `sub` 里出现副本。<kbd>Ctrl</kbd>+<kbd>c</kbd> 关掉这个 tab。
> 4. **过滤：**回根目录，按 <kbd>f</kbd> 打 `txt` → 只剩 `a1.txt b1.txt`（`sub` 里的也算）；<kbd>Esc</kbd> 清除，列表复原。
> 5. **查找：**按 <kbd>/</kbd> 输 `c` → 光标跳到 `c.md`；<kbd>Esc</kbd> 退出。
> 6. **搜名字：**按 <kbd>s</kbd> 输 `report` → 应能在结果里挑出 `sub/buried_report.md`（它不在当前视图里，证明是**递归**搜）。**若报 fd 错误，记下来反馈我。**
> 7. **搜内容：**按 <kbd>S</kbd> 输 `quarterly` → ripgrep 找到 `sub/buried_report.md`（按内容命中）。

> [!question] 检索练习 · 答案都是一键
> 1. 只想给当前目录加个临时筛子、边打字边收窄可见列表？
> 2. 当前目录里，vim 风格把光标跳到下一个匹配的文件名？
> 3. 不知道埋在哪层，递归整棵子树按**文件名**搜？
> 4. 递归整棵子树，按**文件内容**搜？

<details>
<summary>看答案</summary>

1. <kbd>f</kbd>（filter）　2. <kbd>/</kbd>（find，<kbd>n</kbd>/<kbd>N</kbd> 翻）　3. <kbd>s</kbd>（fd 按名）　4. <kbd>S</kbd>（rg 按内容）

tab 那组速记：新建 <kbd>t</kbd><kbd>t</kbd> · 切换 <kbd>1</kbd>-<kbd>9</kbd> / <kbd>[</kbd><kbd>]</kbd> · 关闭 <kbd>Ctrl</kbd>+<kbd>c</kbd>。

</details>

## 下一步

下一课进入**文件标签（tags）**：用 `simple-tag.yazi` 插件给文件打标签、按标签筛选——给「跨目录管一类文件」补上组织手段。

---

[[0004-bulk-rename-and-paths|← 第 3 课 · 批量改名与拷路径]] · **第 4 / 8 课** · [[0006-file-tags|第 5 课 · 文件标签 →]]

*主源：[官方 Quick Start · Multi-tab](https://yazi-rs.github.io/docs/quick-start/#multi-tab)、[Filter / Find / Search](https://yazi-rs.github.io/docs/quick-start/#filter-files)（已按 v26.5.6 核对，与你装的版本一致）。*

> [!tip] 💡 问老师
> 本课已实测通过：<kbd>s</kbd>/<kbd>S</kbd> 搜索与跨 tab 复制都正常。任何「过滤 vs 查找 vs 搜索」分不清的地方随时问。
