# Yazi 配置速查表

*参考文档 · 可打印 — 四个配置文件、混合机制、跨平台目录与环境变量，一张表查清。配套：[[0007-config-and-dotfiles|第 6 课]]。*

## 配置文件

| 文件 | 管什么 | 备注 |
|---|---|---|
| `yazi.toml` | 行为/功能：显示隐藏、排序、`[plugin]` 的 fetcher / previewer 等 | 段名 `[mgr]`（v25+，旧 `[manager]` 已废） |
| `keymap.toml` | 按键绑定 | 层名 `[mgr]`；键 `on = ["t","g"]` 数组 |
| `init.lua` | Lua 入口，插件 `setup()` | 插件必需，哪怕 `setup({})` 空表 |
| `theme.toml` | 配色 | 可不管，用默认 |

> [!note] 原则
> 四个文件**都可选**。yazi 自带完整默认（[shipped tag](https://github.com/sxyazi/yazi/tree/shipped/yazi-config/preset)），你只写「想改的那部分」。

## 混合机制：覆盖 vs 叠加

| 写法 | 效果 | 何时用 |
|---|---|---|
| 同名选项直接写 | **覆盖**默认 | 想改掉默认行为 |
| `prepend_*` | 插到默认**前面**（叠加） | 在默认之上加自己的（99% 场景） |
| `append_*` | 插到默认**后面**（叠加） | 同上，优先级更低 |

适用范围：**keymap、fetchers、open / icon / previewer / preloader 规则**。

```toml
# keymap.toml —— 叠加示例（不覆盖默认键）
[mgr]
prepend_keymap = [
  { on = [ "t", "g" ], run = "plugin simple-tag -- toggle-tag", desc = "切换标签" },
]

# yazi.toml —— 覆盖示例（改掉默认）
[mgr]
show_hidden = true
```

## 配置目录 & 跨平台

| 平台 | 默认目录 |
|---|---|
| Unix（WSL/Mac/Linux） | `~/.config/yazi/` |
| Windows | `%AppData%\yazi\config\` |

- **文件通用**：四平台目录里的 TOML/Lua 完全一样，无平台差异。
- **改目录**：`YAZI_CONFIG_HOME=/path yazi`（官方支持，整目录迁移）。
- **同步方案**（推荐）：在默认目录里 `git init`，`.gitignore` 排掉 `plugins/`（每机 `ya pkg add` 重装）和 `tags/`（按绝对路径存，跨机器不通用）。四台机器各自 clone 进自己的默认目录，`git pull/push` 同步。

## 新机器初始化（固定两步）

```sh
# 1. clone 配置进本机默认目录
#    Unix:  ~/.config/yazi/
#    Win:   %AppData%\yazi\config\
git clone git@github.com:你/yazi-config.git <本机默认目录>

# 2. 重装插件（plugins/ 未入库）
ya pkg add boydaihungst/simple-tag
```

## 常见坑

- `[mgr]` 不是 `[manager]`（v25+ 改名）；键用数组 `on=["t","g"]` 不是单键。
- yazi v26+ fetcher 必填 `group = "..."`，少写报 `missing field group`（simple-tag README 主示例漏写，过时）。
- `ya pkg add` 是 v25+ 命令；旧的 `ya pack -a` 已废。
- <kbd>s</kbd> 搜索需要 `fd` 或 `fdfind`（二选一）；<kbd>S</kbd> 需要 `rg`。干净系统没装则失效。

---

[[0007-config-and-dotfiles|← 第 6 课 · 配置与跨平台同步]] · **参考文档 0001** · 基于官方 Configuration 文档 v26.5.6
