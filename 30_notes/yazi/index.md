# Yazi 终端文件管理器 · 课程笔记

> 我想要一个跨平台（WSL / Windows / Mac / Linux）、可统一配置、用键盘高速操作的终端文件管理器，替代 `cd`+`ls` 的低效流程——核心诉求是「快」。这套笔记记录了我从零到结业、再把配置跨机器同步的完整过程。（详见 [[MISSION]]）

课程已完结 🎓：8 节核心课 + 1 节番外，诉求全部落地。下面是导航。

---

## 📘 课程（按顺序）

| # | 主题 | 笔记 |
|---|---|---|
| 0 | 安装 + `y` 跳转 wrapper | [[0001-install-and-y-wrapper]] |
| 1 | 核心导航（hjkl / gg / G / z / Z） | [[0002-core-navigation]] |
| 2 | 选择与文件操作（y/x/p/r/a/d） | [[0003-selection-and-file-ops]] |
| 3 | 批量改名 + 拷路径（c 前缀） | [[0004-bulk-rename-and-paths]] |
| 4 | 多 tab + 过滤/查找/搜索 | [[0005-tabs-filter-find]] |
| 5 | 文件标签（simple-tag 插件） | [[0006-file-tags]] |
| 6 | 配置与跨平台同步（dotfiles） | [[0007-config-and-dotfiles]] |
| 7 | 自定义命令与快捷键（结业） | [[0008-custom-commands]] |
| 番外 | 压缩包与 opener | [[0009-openers-and-archives]] |

## 📋 参考速查（日后最常翻）

- [[0001-yazi-config-reference|配置速查表]] —— 四个配置文件 + prepend/append + 目录/env
- [[0002-yazi-opener-reference|opener / open 速查表]] —— 「按回车跑什么」的全部规则

## 🧠 学习记录（关键决策与非显然结论）

- [[0001-baseline-and-install-blocker|0001 · 基线与安装阻塞]]
- [[0002-lesson0-done-blocker-resolved|0002 · 第 0 课通过、阻塞解除]]
- [[0003-lesson4-s-search-fdfind-fallback|0003 · `s` 搜索的 fdfind 回退]]
- [[0004-lesson5-tags-verified-and-storage|0004 · 标签验证与存储位置]]
- [[0005-lesson6-config-dotfiles-verified|0005 · 配置同步验证]]
- [[0006-lesson7-custom-keys-and-beyond|0006 · 自定义键与结业后的自主扩展]]
- [[0007-lesson8-openers-and-compress-verified|0007 · 压缩包 / opener 验证]]

## 📁 过程文档

- [[MISSION]] —— 为什么学（mission 与成功标准）
- [[RESOURCES]] —— 高可信资源索引（官方文档、插件、社区）
- [[NOTES]] —— 教学备忘（环境踩坑、工具版本、进度）

---

## 🎨 精排 HTML 版

上面所有 MD 笔记都有带样式的 HTML 原版，收在 `html/` 子文件夹里（自包含，含 CSS）。Obsidian 不内联渲染 HTML，但**在浏览器里打开**体验最好（Tufte 风格排版、打印友好）：

- [第 0 课 HTML](html/lessons/0001-install-and-y-wrapper.html) · [配置速查 HTML](html/reference/0001-yazi-config-reference.html) · [opener 速查 HTML](html/reference/0002-yazi-opener-reference.html)

> [!tip] MD vs HTML 怎么选
> **日常查阅用 MD**（可搜、可链、进图谱、手机端可用）；**想看精排或打印用 HTML**（在文件管理器里双击，或从 Obsidian 右键「在默认程序打开」）。
