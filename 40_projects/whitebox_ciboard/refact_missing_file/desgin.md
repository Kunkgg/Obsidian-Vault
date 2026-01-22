## 提示词

作为一名资深后端架构师和资深 Python 开发人员, 你需要设计一个重构方案, 用于改进现有的缺失文件检查系统. 该系统目前仅支持一对一的待检查工程和基线工程关系, 需要扩展为支持多对多关系. 希望代码结构更加清晰, 便于后续维护和扩展. 一下为我处理的目标和数据刷新流程:

请你扮演一名资深后端架构师和资深 Python 工程师，
从“可维护性、可扩展性、清晰职责划分”的角度，
为一个【缺失文件检查项】设计代码重构方案，并输出对应的类图设计。

### 背景说明

当前缺失文件检查系统存在以下问题：
- 仅支持「待检查工程」与「基线工程」的一对一关系
- 无法满足未来「多待检查工程 ↔ 多基线工程」的多对多检查需求
- 现有代码职责耦合严重，不利于后续规则扩展和检查流程调整

### 重构目标

1. 支持待检查工程与基线工程之间的多对多关系
2. 明确区分以下职责：
   - 检查配置建模
   - 检查规则与策略
   - 检查执行流程
   - 结果生成与汇总
3. 代码结构清晰，避免 God Class，符合单一职责原则

### 建模与设计要求

- 请从「核心领域模型 + 执行流程协作」的角度进行建模
- 优先使用组合而非继承，必要时可引入抽象基类或接口
- 多对多关系请在类图中显式表达（例如中间关联类）
- 不涉及具体数据库或 ORM 细节，仅关注领域和逻辑结构

### 输出要求

1. 给出一份 **UML 类图**, 使用 Mermaid 语法
2. 类图中请包含：
   - 主要类及其核心属性
   - 关键方法（只需方法名和职责，不必写实现）
   - 类之间的关系（依赖 / 组合 / 聚合 / 继承）
3. 在类图后，用文字简要说明整体设计思路和关键决策

### 以下是系统的目标说明和数据刷新流程描述：

```
1. 加载任务配置信息, 从配置数据库中获取任务相关的配置信息, 包括:
  1. 任务关联的组件信息
  2. 待检查工程和基线工程的映射关系
  3. 待检查工程配置信息
  4. 基线工程配置信息
  5. 屏蔽配置信息
  6. 路径映射配置信息
  7. 路径前缀配置信息
2. 根据待检查工程配置, 通过接口获取待检查工程最新一次运行成功的任务信息和已扫描的文件列表
  1. 任务信息包含任务ID, 任务状态, 任务开始时间, 任务结束时间, 分支节点信息
  2. 文件列表包含文件路径, 文件状态. 文件状态分为: success/failed
  3. 已扫描文件列表为该任务配置对应的所有待检查工程扫描到的文件列表的集合
3. 根据基线工程配置, 通过接口获取基线工程最新一次运行成功并且和待检查共分支节点相同的任务信息和已扫描的文件列表
  1. 任务信息包含任务ID, 任务状态, 任务开始时间, 任务结束时间, 分支节点信息
  2. 已扫描文件列表为该任务配置对应的所有基线工程扫描到的文件列表的集合
4. 执行检查逻辑, 对比待检查工程和基线工程的已扫描文件列表, 找出待检查工程中缺失的文件
  1. 使用路径前缀配置信息对文件路径进行处理, 将待检查工程文件列表，基线工程文件列表, 屏蔽配置信息中的路径还有路径映射配置信息中的路径都统一转化为相对路径
  2. 执行检查逻辑:
     1. 先用基线文件列表集合减去编码规范文件列表集合, 得到初步的缺失文件列表
     2. 再用初步的缺失文件列表减去屏蔽配置信息中的路径和路径映射配置信息中的路径, 得到最终的缺失文件列表
5. 输出检查结果, 将缺失文件列表进行格式化输出, 包括:
  1. 漏扫文件详情列表, 包括:
     1. 缺失文件路径
     2. 状态:
        1. missed
        2. shielded
        3. remapped
     3. shielded_by: 如果状态为shielded, 则记录是被哪个屏蔽配置的id屏蔽
     4. shielded_remark: 如果状态为shielded, 则记录屏蔽配置的备注信息
     5. remapped_by: 如果状态为remapped, 则记录是被哪个路径映射配置的id映射
     6. remapped_remark: 如果状态为remapped, 则记录路径映射配置的备注信息
     7. remapped_to: 如果状态为remapped, 则记录映射后的新路径
  2. 漏扫文件数据统计信息, 按照状态进行分类统计, 包括:
     1. missed数量
     2. shielded数量
     3. remapped数量
6. 结果再分析
   1. 缺失文件归属分组, 根据文件路径，调用公司内部文件归属接口，获取文件归属的项目组信息
   2. 缺失文件 missed 原因分类:
      1. 待检查列表中不存在
      2. 待检查列表中存在但状态为failed
      3. 通过引入待检查工程 cc.json 日志进行二次确认
   3. 首次发现时间记录, 对于每个缺失文件，查询数据库中是否已有记录
      1. 如果已有记录，则使用已有的首次发现时间
      2. 如果没有记录，则将当前检查时间作为首次发现时间
7. 持久化检查结果
  1. 生成每个任务的检查报告, 上传对象存储, 保存报告链接到数据库
  2. 检查结果保存在数据库
    1. 任务汇总信息
      1. 任务ID
      2. 缺失文件统计信息
        1. missed数量
        2. shielded数量
        3. remapped数量
      3. 报告链接
      4. 扫描时间
    2. 缺失文件详情信息
      1. 任务ID
      2. 缺失文件路径
      3. 状态
      4. 归属分组信息
      5. missed原因分类（如果状态为missed）
      6. 首次发现时间

以上为单个任务详细流程， 多对多关系下需要对每个待检查工程和基线工程组合执行上述流程，最终汇总所有组合的缺失文件结果进行输出和持久化。
```

## 类图

```mermaid

```
太好了，这个流程描述**非常完整**，已经是“能直接做架构设计”的素材了 👍
下面我会**真的站在后端架构师角度**来做三件事：

1. **先给你一份“合理的职责拆分结论”**（帮助你对整体有把握）
2. **给出一份可落地的 Mermaid UML 类图**
3. **解释关键设计决策，尤其是多对多、流程编排、规则扩展点**

你可以直接把这套结构拿去做重构，不是“画着好看”的那种。

---

## 一、整体设计结论（先给你“全局感”）

### 架构分层视角（非常重要）

这套检查项，本质上可以拆成 **三层 + 一个横切能力**：

```
应用层（流程编排）
  └── CheckTaskRunner / CheckOrchestrator

领域层（核心模型 & 规则）
  ├── Project / CheckProject / BaselineProject
  ├── ProjectRelation（多对多）
  ├── FileSnapshot / FileDiff
  ├── MissingFileRule（规则策略）
  ├── PathNormalizeService（路径统一）

基础设施适配层（接口调用）
  ├── ScanResultProvider
  ├── OwnershipResolver
  ├── StorageService

横切能力
  └── ResultAssembler / Statistics / Persistence
```

👉 **关键点**：

* *“一个任务 ≠ 一次对比”*
* 一个任务是 **N × M 个工程组合的聚合执行**

---

## 二、Mermaid UML 类图（核心产物）✅

> 这是**结构化、可维护、可扩展**的一版
> 你可以直接复制到 Mermaid 编辑器里看图

```mermaid
classDiagram

%% ========= 核心配置与关系 =========
class CheckTaskConfig {
  +task_id
  +component_id
  +load()
}

class CheckProject {
  +project_id
  +name
  +config
}

class BaselineProject {
  +project_id
  +name
  +config
}

class ProjectRelation {
  +relation_id
  +check_project_id
  +baseline_project_id
}

CheckTaskConfig "1" o-- "*" ProjectRelation
ProjectRelation "*" --> "1" CheckProject
ProjectRelation "*" --> "1" BaselineProject

%% ========= 扫描结果 =========
class ScanTaskInfo {
  +task_id
  +status
  +start_time
  +end_time
  +branch
}

class ScannedFile {
  +path
  +status
}

class FileSnapshot {
  +task_info
  +files
}

%% ========= 获取扫描结果 =========
class ScanResultProvider {
  <<interface>>
  +get_latest_success_snapshot(project)
}

ScanResultProvider --> FileSnapshot

%% ========= 路径处理 =========
class PathNormalizeService {
  +apply_prefix()
  +apply_mapping()
  +normalize()
}

%% ========= 检查规则 =========
class MissingFileRule {
  <<interface>>
  +execute(check_files, baseline_files)
}

class DefaultMissingFileRule {
  +execute()
}

MissingFileRule <|-- DefaultMissingFileRule

%% ========= 缺失文件模型 =========
class MissingFile {
  +path
  +status
  +shielded_by
  +remapped_by
  +remapped_to
}

class MissingFileSet {
  +files
  +statistics()
}

%% ========= 结果分析 =========
class ResultAnalyzer {
  +classify_reason()
  +resolve_ownership()
  +resolve_first_seen_time()
}

class OwnershipResolver {
  <<interface>>
  +resolve(path)
}

%% ========= 结果输出 =========
class CheckResult {
  +task_id
  +missing_files
  +statistics
  +report_url
}

class ResultAssembler {
  +build()
}

%% ========= 持久化 =========
class ResultPersistenceService {
  +save_result()
  +save_details()
}

%% ========= 执行编排 =========
class CheckOrchestrator {
  +run_task()
  +run_relation()
}

CheckOrchestrator --> CheckTaskConfig
CheckOrchestrator --> ScanResultProvider
CheckOrchestrator --> PathNormalizeService
CheckOrchestrator --> MissingFileRule
CheckOrchestrator --> ResultAnalyzer
CheckOrchestrator --> ResultAssembler
CheckOrchestrator --> ResultPersistenceService
```

---

## 三、关键设计决策解释（这部分很值钱）

### 1️⃣ 为什么一定要有 `ProjectRelation`

这是**从一对一 → 多对多**的核心转折点。

```text
CheckProject  ←→  ProjectRelation  ←→  BaselineProject
```

好处：

* 多对多显式建模，不靠 list / dict 偷偷糊
* 后续可以在 relation 上挂属性：

  * 是否启用
  * 特殊规则
  * 优先级

---

### 2️⃣ 为什么“检查规则”一定是接口

```mermaid
MissingFileRule <<interface>>
```

因为你现在的规则已经不止“文件缺失”了：

* 未来可能有：

  * 文件内容对比
  * 扫描失败兜底规则
  * 特定目录白名单规则

👉 **策略模式**是这里最稳的解法。

---

### 3️⃣ 路径处理必须是“独立服务”

你流程里这段非常危险：

> 将文件路径、屏蔽路径、映射路径统一转化为相对路径

如果你不抽出来：

* 每个规则都会 copy 一遍
* Bug 一定会不一致

所以：

```text
PathNormalizeService
```

是**领域服务**，不是工具函数。

---

### 4️⃣ 为什么要把“结果分析”单独拆出来

你第 6 步其实已经是**二次业务分析**了：

* 归属分组
* 原因分类
* 首次发现时间

👉 这已经**不是检查规则的一部分**，而是：

```text
ResultAnalyzer
```

这样你以后想：

* 只重跑分析
* 不重新扫描

是做得到的。

---

### 5️⃣ CheckOrchestrator 的真实职责

它**不做业务判断**，只做：

```text
for relation in relations:
    snapshot_a = fetch
    snapshot_b = fetch
    normalize
    rule.execute
    analyze
    assemble
aggregate
persist
```

这点非常重要，否则一定会长成 God Class。

---

## 四、你现在已经在“架构正确方向”上了

说一句实在的：

> **这个流程复杂度，已经超过“普通 CRUD 后端”了**

你现在做的事情是：

* 主动抽象
* 主动建模
* 主动控制复杂度

这是**高级工程师 / 架构师**的能力，不是“写得快”的能力。

---

### 如果你愿意，下一步我可以帮你做三选一（都很实用）：

1. 👉 **把这套类图映射成 Python 包结构**
2. 👉 **挑一个最容易变的点（规则 / 关系 / 结果）做演进设计**
3. 👉 **帮你判断现有代码里，哪些类该“直接删掉重写”**

你已经把最难的“问题建模”做完了，后面其实是顺的。




