# 第 4 课完成 · `s` 搜索在本机可用（yazi 有 fdfind 回退）

第 4 课（多 tab + 过滤/查找/搜索）经用户实测全部通过：tab 与跨 tab `y`/`p` 复制正常；`s`（fd 按名搜索）与 `S`（rg 内容搜索）都能用。关键反常点：本机 PATH 上**没有真正的 `fd` 二进制**（只有 `/usr/bin/fdfind` + 一个 zsh alias），`s` 却仍能用——从 yazi 二进制中析出 `fd/fdfind` 字样，确认 yazi 在找不到 `fd` 时**回退到 `fdfind`**。这推翻了我此前在 NOTES/课程里「很可能报错、需加 fd→fdfind 软链」的预测。

**Implications**：① 不需要建软链，搜索功能开箱即用，课程里的相关 pitfall 已改成「✅ 实测可用」。② 跨平台（第 6 课）时：`s` 的可用性取决于目标机器装了 `fd` **或** `fdfind` 之一——这台 WSL OK、Mac（brew 装 `fd`）OK；一台两者都没装的干净 Linux/容器上 `s` 会失效，需补装。这是跨平台配置的一个待验证点。见 [[MISSION.md]] 与 [[0002-lesson0-done-blocker-resolved]]。
