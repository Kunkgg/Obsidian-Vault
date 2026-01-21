
# 命名规范（Naming Convention）

本文件用于统一 Obsidian Vault 中 **目录、文件、链接、标签** 的命名规则。

目标不是“看起来高级”，而是：

* 降低长期维护成本
* 减少命名犹豫和反复修改
* 保证在终端 / Git / 脚本 / 多设备环境下始终稳定

> **原则一句话**：命名应当 *可预测、可搜索、可长期坚持*。

---

## 一、全局命名原则

### 1. 全小写（mandatory）

* 所有 **目录名、文件名、MOC、附件** 一律使用小写
* 避免大小写混用导致的：

  * 链接失效
  * 跨平台问题（Windows / Linux / macOS）
  * Git diff 噪音

✅ 正确：

```
python_async.md
system_design
moc_database.md
```

❌ 错误：

```
PythonAsync.md
System-Design
MocDatabase.md
```

---

### 2. 使用下划线 `_` 作为唯一分隔符

* 单词分隔 **只允许 `_`**
* 不使用：

  * 空格
  * 中划线 `-`
  * 驼峰

原因：

* `_` 在终端、URL、脚本中最稳定
* 避免 Markdown 链接转义问题

✅ 正确：

```
note_lifecycle.md
daily_attachments
moc_system_design.md
```

❌ 错误：

```
note-lifecycle.md
daily attachments
mocSystemDesign.md
```

---

### 3. 命名要“描述用途”，而不是“展示聪明”

命名的首要目标是：

> **6 个月后的自己一眼就懂**

* 优先使用直白、常见的英文词
* 避免缩写（除非行业通用）

✅ 推荐：

```
error_handling.md
index_usage.md
api_rate_limit.md
```

❌ 不推荐：

```
err_hdl.md
idx_opt.md
arl.md
```

---

## 二、目录命名规范

### 1. 顶层目录

顶层目录采用：

```
<两位数字>_<语义名>
```

例如：

```
00_system
10_inbox
20_daily
30_notes
40_projects
50_resource
60_mocs
90_archived
```

规则说明：

* 数字仅用于 **排序稳定**，不是分类逻辑
* 目录职责由 `note_lifecycle.md` 约束
* **禁止**在顶层随意新增目录

---

### 2. 子目录

子目录：

* 不使用数字
* 直接使用领域或主题名

例如：

```
30_notes/
  ├── python/
  ├── db/
  │   ├── mysql/
  │   ├── postgresql/
  │   └── sqlite/
  ├── redis/
  └── system_design/
```

---

## 三、文件命名规范

### 1. 普通笔记（notes）

格式：

```
<主题>_<限定>.md
```

示例：

```
python_async.md
python_gil.md
mysql_index_usage.md
k8s_pod_lifecycle.md
```

说明：

* 文件名应 **稳定**，避免频繁修改
* 不追求完整句子

---

### 2. Daily Note

* Daily 文件名固定为：

```
YYYY-MM-DD.md
```

例如：

```
2026-01-21.md
```

* 不允许添加任何前后缀

---

### 3. 项目文件

项目目录建议结构：

```
40_projects/project_name/
  ├── overview.md
  ├── decisions.md
  ├── progress.md
  └── notes.md
```

* `overview.md` 为必选入口
* 其余文件按需添加

---

### 4. MOC（Map of Contents）

统一格式：

```
moc_<domain>.md
```

示例：

```
moc_python.md
moc_database.md
moc_system_design.md
```

规则：

* 一个领域只允许一个 MOC
* MOC 只做 **链接与结构**，不承载长内容

---

## 四、标签（Tags）命名规范

### 1. 基本规则

* 全小写
* 使用 `/` 表示层级
* 不使用空格

示例：

```
#status/draft
#status/evergreen
#type/note
#type/project
#area/backend
```

---

### 2. 推荐标签前缀

| 前缀        | 用途   |
| --------- | ---- |
| `status/` | 笔记状态 |
| `type/`   | 笔记类型 |
| `area/`   | 知识领域 |
| `source/` | 来源   |

---

## 五、附件命名规范

### 1. 通用附件

统一存放在：

```
00_system/attachments/
20_daily/daily_attachments/
```

命名格式：

```
<日期>_<简要描述>.<ext>
```

示例：

```
2026-01-21_stack_trace.png
2026-01-21_architecture_sketch.jpg
```

---

## 六、允许的例外情况

以下情况允许轻微偏离规则：

* 外部同步内容（如 readwise）
* 自动生成文件
* 第三方导入资料

但应：

* 放在 `50_resource/` 下
* 不影响主笔记体系

---

## 七、命名检查清单（新增笔记前）

在新建文件或目录前，快速自检：

* [ ] 是否全小写？
* [ ] 是否只使用 `_` 分隔？
* [ ] 6 个月后我还能一眼看懂吗？
* [ ] 是否已经存在语义重复的笔记？

如果有犹豫：

> **优先选择“更简单”的名字。**

---

## 八、最后原则

> **一致性 > 完美**

这套命名规则的价值，在于：

* 长期不变
* 不需要思考
* 让注意力回到“内容本身”

一旦采用，除非整体重构 Vault，**不要轻易修改历史命名**。