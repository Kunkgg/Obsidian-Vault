# 压缩包与 opener

*Yazi 课程 · 番外（结业后按需扩展） — 解压到子目录、一键打包、看得见进度——三件事一气呵成。顺手把 opener 彻底弄懂。*

> [!tip] 本课的胜利
> 改完能做到：选中几个文件 → <kbd>c</kbd><kbd>a</kbd><kbd>a</kbd> 打成 zip；选中一个 zip → <kbd>l</kbd> 解压进**同名子目录**（不再散落一地）；解压/拷贝/移动时 → <kbd>w</kbd> 看**实时进度**。改完 `git push`，四平台同步。

## 先懂 · opener 怎么决定"按回车跑什么"

两层（和 keymap/fetcher 同套路），都在 `yazi.toml`：

- `[opener]` = 给命令**起名字**（"配方"）：`extract`、`edit`、`unpack`… 每个是数组，可按 `for` 分 OS。
- `[open]` = **匹配规则**：用 `url`（扩展名/路径 glob）或 `mime`（MIME glob）挑 opener。

语法全表见 [[reference/0002-yazi-opener-reference|opener 速查]]。这里只讲关键：yazi **默认就配好了压缩包**——我从你机器的二进制里挖出来的默认规则：

```toml
[opener] extract = [ { run = "ya pub extract --list %s", desc = "Extract here" } ]
[open]   { mime = "application/{zip,rar,7z*,tar,gzip,xz,zstd,bzip*,lzma,compress,archive,...}", use = ["extract", "reveal"] }
```

所以"按 <kbd>l</kbd>/<kbd>o</kbd> 解压"开箱就有——只是它解到**当前目录**（文件散落），且没反馈。下面三节逐一修掉。

## 痛点 ⑤ 反馈 → 按 <kbd>w</kbd>（零配置）

extract / copy / move 在 yazi 里都是**后台任务**，有实时进度。按 <kbd>w</kbd> = **任务管理器**（Task Manager）：能看到正在跑的任务、进度、完成通知。<kbd>x</kbd> 取消当前任务。

> [!note] 你之前"没反馈"多半是不知道这个键
> 不是 yazi 没进度，是进度在任务面板里。下次解压大包时按 <kbd>w</kbd> 看看——这一条不用改任何配置。

## 痛点 ① 解压到子目录 → 自定义 `unpack` opener

思路：装 `unar`（自动建"压缩包同名"子目录、全格式、跨平台），再加一个叫 `unpack` 的 opener，用 `prepend_rules` 让它**抢在默认 `extract` 前**。

**① 装 unar**（稳定工具，apt 没问题；非版本敏感，不像 yazi/fzf 那样要追新版）：

```sh
sudo apt install unar          # WSL/Debian
# brew install unar            # macOS
# Windows: 下载 unar.exe 放 PATH，或 scoop install unar
```

**② `yazi.toml` 加两段**（你目前没有 `[opener]`/`[open]` 段，新建即可）：

```toml
[opener]
unpack = [
  { run = "unar %s", desc = "解压到同名子目录", for = "unix" },
  { run = "unar %s", desc = "解压到同名子目录", for = "windows" },
]

[open]
prepend_rules = [
  { mime = "application/{zip,rar,7z*,tar,gzip,xz,zstd,bzip*,lzma,compress,archive,cpio,arj,xar,ms-cab*}", use = ["unpack", "extract", "reveal"] },
]
```

> [!warning] 为什么是 unpack 不是覆盖 extract
> 起个新名字 `unpack` + `prepend_rules`，**不删默认**。按 <kbd>l</kbd>/<kbd>Enter</kbd> 走第一个（`unpack`→子目录）；按 <kbd>o</kbd>（`open --interactive`）弹菜单，三选一：`unpack`(子目录) / `extract`(当前目录) / `reveal`(只选中不打开)。想用哪种用哪种。

> [!warning] 为什么不直接用 7zz
> 你机器上有 `7zz`，我实测 `7zz x foo.zip -ofoo/ -y` 能解到子目录。但 opener 的 `%s` 给不出"去扩展名的目录名"（要 shell 包装）；而且 `${f%.*}` 只切最后一个扩展，`foo.tar.gz` 会切成 `foo.tar/`。`unar` 一行搞定、全格式正确——多装一个小工具，换永远清爽。

## 痛点 ③ 创建压缩包 → compress.yazi 插件

**① 装插件**：

```sh
ya pkg add KKV9/compress
```

**② 确认 PATH 里有压缩器**（插件按格式调对应工具）：

- `.zip` → 需要 `zip`：`sudo apt install zip`（最通用，推荐）
- `.7z` → 需要 `7z`：你有 `7zz` 没 `7z`，`ln -s /usr/bin/7zz ~/.local/bin/7z`（`.local/bin` 已在 PATH 最前，见第 0 课）
- `.tar.gz` → `tar`+`gzip`，你都有，免装

**③ `keymap.toml` 的 `[mgr]` 下，已有 `prepend_keymap` 数组里追加**：

```toml
{ on = [ "c", "a", "a" ], run = "plugin compress", desc = "打包选中文件" },
```

用法：选中文件 → <kbd>c</kbd><kbd>a</kbd><kbd>a</kbd> → 输归档名（留空=用文件名/父目录名）→ 输扩展名决定格式（`zip`/`7z`/`tar.gz`…，默认 zip）→ 回车。

进阶（同前缀，按需加）：`c a p` 加密码、`c a l` 压缩级别、`c a h` 加密文件头（7z/rar）。`c` 前缀与默认的 `c`⇒`c`(拷路径) 不冲突——按 `c` 停顿会弹出所有 `c` 开头的分支。

## 动手 · 三件事打通

> [!check] 跟着做（WSL）
> 1. `sudo apt install unar zip` + `ya pkg add KKV9/compress` + `ln -s /usr/bin/7zz ~/.local/bin/7z`
> 2. 编辑 `~/.config/yazi/yazi.toml`：加上面 `[opener]` + `[open]` 两段。
> 3. 编辑 `~/.config/yazi/keymap.toml`：`prepend_keymap` 数组里加 `c a a` 那行。
> 4. **重启 yazi**（<kbd>q</kbd> 再进）。
> 5. **测创建**：选 2 个文件 → <kbd>c</kbd><kbd>a</kbd><kbd>a</kbd> → 输名字 `test` → 格式 `zip` → 回车，得 `test.zip`。
> 6. **测解压**：选中 `test.zip` → 按 <kbd>l</kbd> → 应出现 `test/` 子目录，里面是那 2 个文件（不是散落当前目录）。按 <kbd>o</kbd> 能看到 unpack/extract/reveal 三选项。
> 7. **测进度**：找个大压缩包解压，按 <kbd>w</kbd> → 看实时进度，<kbd>x</kbd> 取消。
> 8. 都 OK：`cd ~/.config/yazi && git add -A && git commit -m "openers + compress plugin" && git push`，Windows `git pull` + `ya pkg install` 同步。

> [!question] 检索练习
> 1. 想让某类文件"按回车跑自定义命令"，要在 yazi.toml 写哪两段？
> 2. `prepend_rules` 给压缩包 `use = ["unpack","extract"]`——按 <kbd>l</kbd> 跑哪个？按 <kbd>o</kbd> 呢？
> 3. 解压大包时想看进度、想取消，各按什么键？
> 4. 同样解压 `report.tar.gz`，`unar` 会建什么目录？shell 的 `${f%.*}` 会建什么？

<details>
<summary>看答案</summary>

1. `[opener]` 定义命令（命名 + run），`[open]` 写规则（url/mime → use）。
2. <kbd>l</kbd> 跑第一个 `unpack`（解到子目录）；<kbd>o</kbd> 弹菜单列全部（unpack/extract/reveal 三选一）。
3. <kbd>w</kbd> 看进度，<kbd>x</kbd> 取消。
4. `unar` → `report/`（正确）；`${f%.*}` → `report.tar/`（只切了 `.gz`，错了）——这就是选 unar 的理由。

</details>

---

[[0008-custom-commands|← 第 7 课 · 自定义命令与快捷键]] · **番外 · 压缩包与 opener** · [[reference/0002-yazi-opener-reference|opener 速查 →]]

*主源：[官方 yazi.toml 文档](https://yazi-rs.github.io/docs/configuration/yazi/)（v26.5.6，`[opener]`/`[open]` 全字段已核实）+ [compress.yazi README](https://github.com/KKV9/compress.yazi)（跨平台、格式表、keymap、PATH 要求）。默认 opener/规则从本机 yazi 二进制内嵌配置核实。`w`=任务管理器见 yazi 键盘参考。*

> [!tip] 💡 问老师
> 卡点常见在两处：① `unar`/`zip` 没装好（`which unar zip` 确认）；② `c a a` 按了没反应（多半是 `ya pkg add` 后没重启 yazi，或 `prepend_keymap` 数组少写逗号）。把现象和你的 `yazi.toml`/`keymap.toml` 贴我，我帮你查。
