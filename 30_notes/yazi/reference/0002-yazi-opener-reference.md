# Opener / Open 速查表

*参考文档 0002 · 可打印 — 「按回车时 yazi 跑什么」的全部规则。打印一张贴旁边。*

> [!note] 两层结构（一句话）
> `[opener]` 定义「能做什么菜」（命名命令）→ `[open]` 决定「什么食材走哪道菜」（匹配规则）。都写在 `yazi.toml`。

## [opener] · 命名命令

```toml
[opener]
edit    = [ { run = "${EDITOR:-vi} %s", desc = "$EDITOR", for = "unix", block = true } ]
extract = [ { run = "ya pub extract --list %s", desc = "Extract here" } ]
unpack  = [ { run = "unar %s", desc = "解压到同名子目录", for = "unix" },
            { run = "unar %s", desc = "解压到同名子目录", for = "windows" } ]
play    = [ { run = "mpv %s", orphan = true, for = "unix" } ]
```

每个 opener 是**数组**——可按 `for` 给不同 OS 不同命令（同键跨平台）。同名 key（如自定义 `extract`）**覆盖**默认。

| 字段 | 作用 |
|---|---|
| `run` | 命令。见下表模板参数 |
| `block` | 接管屏幕跑交互程序（编辑器/REPL），退出回 yazi |
| `orphan` | 后台 detach，yazi 退了也活着（播放器/GUI） |
| `desc` | 「Open with」菜单里的说明 |
| `for` | 限定 OS：`unix`(Linux+macOS) / `linux` / `macos` / `windows` |

### `run` 里的模板参数

| 写法 | 替换成 |
|---|---|
| `%s` | 所有选中文件的路径 |
| `%s1` `%s2` | 第 1、2 个选中文件 |
| `%S` `%SN` | 同上，但 URL 编码形式 |
| `%d` `%dN` | 选中文件所在目录 |
| `%%` | 字面 `%` |

## [open] · 匹配规则

```toml
[open]
prepend_rules = [
  { url = "*.json", use = "edit" },                       # 按扩展名（url 是 glob）
  { url = "*.html", use = ["open", "edit"] },             # 数组→合并：open 跑第 1 个，o 弹菜单列全部
  { mime = "video/*", use = "play" },                     # 按 MIME
  { mime = "application/{zip,rar,7z*}", use = ["unpack", "extract", "reveal"] },
]
# rules = [ ... ]   # 彻底重写（少用）
```

| 字段 | 含义 |
|---|---|
| `url` | 路径/文件名 glob，默认大小写不敏感；前缀 `\s` 变敏感 |
| `mime` | MIME 类型 glob（同上大小写规则） |
| `use` | opener 名，或名数组。数组时 `open` 跑第一个，`open --interactive`（<kbd>o</kbd>）列全部 |

## 混合机制（叠加 / 覆盖）

- `prepend_rules` / `append_rules`：在默认规则**前/后**插。yazi 用**第一条匹配**→ prepend 常用来「抢在默认前」改某类文件的行为。
- `append_rules` 里带通配符 `*` 的会**优先于**默认通配符兜底。
- `[opener]` 同名 key（如 `extract`）直接覆盖默认那个 opener。
- 同款 `prepend_*` 套路也用于：`fetchers` / `previewers` / `preloaders` / `keymap`（第 6 课讲过）。

> [!warning] 查真实 MIME
> 写 `mime` 规则前，先确认文件的实际类型：光标停文件上按 <kbd>Tab</kbd>（`spot`）→ 看到 yazi 判定的 mime，照抄。

## yazi 自带的默认 opener（从二进制核实，v26.5.6）

- `edit` → `${EDITOR:-vi} %s`（block）
- `extract` → `ya pub extract --list %s`（解压到**当前目录**；走 yazi 内建逻辑，桥接 7zz）
- `open` → 系统默认 opener（`xdg-open` / `start` / `open`）
- `reveal` → 在父目录里选中（不打开）

默认 `[open]` 已给压缩包配好：`{ mime = "application/{zip,rar,7z*,tar,gzip,xz,zstd,bzip*,lzma,compress,archive,cpio,arj,xar,ms-cab*}", use = ["extract", "reveal"] }`。所以「按 <kbd>l</kbd>/<kbd>o</kbd> 解压」开箱就有——只是它解到 **CWD**。

---

[[lessons/0009-openers-and-archives|← 第 8 课 · 压缩包与 opener]] · **参考文档 0002 · opener/open** · [[0001-yazi-config-reference|配置总速查 →]]
