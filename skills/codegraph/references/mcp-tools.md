# CodeGraph MCP 工具详解

> 官方来源：[README.md](https://github.com/colbymchenry/codegraph)

## 工具总览

CodeGraph 作为 MCP 服务器运行时，向 Agent 暴露以下工具：

| 工具 | 用途 |
|------|------|
| `codegraph_explore` | **首选工具**。一次调用回答几乎所有问题——"X 如何工作"、流程追踪、区域概览。返回相关符号的源码（按文件分组）、关系图和影响范围。能处理动态分发跳转（回调、React 重渲染、接口→实现） |
| `codegraph_search` | 按名称在代码库中搜索符号 |
| `codegraph_callers` | 查找调用指定函数/方法的代码 |
| `codegraph_callees` | 查找指定函数/方法调用的代码 |
| `codegraph_impact` | 分析修改某符号的影响范围（blast radius） |
| `codegraph_node` | 获取单个符号的完整详情和源码（对歧义名称返回所有重载） |
| `codegraph_files` | 获取已索引的文件结构（比文件系统扫描更快） |
| `codegraph_status` | 检查索引健康状态和统计信息 |

## 工具选择矩阵

| 查询意图 | 推荐工具 | 说明 |
|---------|---------|------|
| "X 如何工作" | `codegraph_explore` | 一次调用返回完整答案 |
| "X 如何到达 Y" | `codegraph_explore` | 流程追踪 |
| 查找符号位置 | `codegraph_search` | 按名称搜索 |
| 谁调用了 X | `codegraph_callers` | 调用者追踪 |
| X 调用了谁 | `codegraph_callees` | 被调用者追踪 |
| 修改 X 的影响 | `codegraph_impact` | 变更影响分析 |
| 获取符号详情 | `codegraph_node` | 完整源码和元数据 |
| 项目文件结构 | `codegraph_files` | 已索引文件列表 |
| 索引健康状态 | `codegraph_status` | 统计和健康度 |

## 各工具详解

### `codegraph_explore`

**用途**：回答几乎所有代码问题的主要工具。

**适用场景**：
- "X 如何工作"
- 从 A 到 B 的流程追踪
- 区域概览
- 理解复杂交互

**返回内容**：
- 相关符号的源码（按文件分组）
- 关系图
- 影响范围（blast radius）

**关键特性**：
- 能处理动态分发跳转（回调、React 重渲染、接口→实现），这是 grep 无法做到的
- 将冗余的可互换实现折叠为签名，响应大小基于"答案"而非文件数
- 通常一次调用即可返回完整答案，无需文件读取

**参数**：
- `task` — 查询描述（如 "How does the authentication flow work?"）
- `projectPath` — 可选，项目路径

**使用示例**：
```
codegraph_explore("How does a request reach the database?")
codegraph_explore("How does the payment processing flow work?")
```

### `codegraph_search`

**用途**：按名称在代码库中搜索符号。

**适用场景**：
- 查找特定函数/类/方法的位置
- 确认符号是否存在

**参数**：
- `query` — 搜索查询（符号名）
- `kind` — 可选，过滤类型（function、class、interface、method 等）
- `limit` — 可选，最大结果数（默认 10）
- `projectPath` — 可选，项目路径

**使用示例**：
```
codegraph_search(query="UserService", kind="class")
codegraph_search(query="calculateTotal", limit=5)
```

### `codegraph_callers`

**用途**：查找谁调用了指定函数/方法。

**适用场景**：
- 理解函数的使用上下文
- 重构前评估影响
- 安全审查：谁使用了这个 API

**参数**：
- `symbol` — 符号名称
- `limit` — 可选，最大结果数（默认 20）
- `projectPath` — 可选，项目路径

**使用示例**：
```
codegraph_callers(symbol="authenticate")
codegraph_callers(symbol="UserService.login", limit=10)
```

### `codegraph_callees`

**用途**：查找指定函数/方法调用了什么。

**适用场景**：
- 理解函数的依赖链
- 分析函数的复杂度
- 识别潜在的副作用

**参数**：
- `symbol` — 符号名称
- `limit` — 可选，最大结果数（默认 20）
- `projectPath` — 可选，项目路径

**使用示例**：
```
codegraph_callees(symbol="handleRequest")
codegraph_callees(symbol="processOrder", limit=10)
```

### `codegraph_impact`

**用途**：分析修改某符号的影响范围（blast radius）。

**适用场景**：
- 修改前评估影响
- 确定需要运行的测试
- 安全审查：变更的连锁反应

**参数**：
- `symbol` — 符号名称
- `depth` — 可选，依赖遍历深度（默认 2）
- `projectPath` — 可选，项目路径

**使用示例**：
```
codegraph_impact(symbol="UserService", depth=3)
codegraph_impact(symbol="DatabaseConnection")
```

### `codegraph_node`

**用途**：获取单个符号的完整详情和源码。

**适用场景**：
- 需要查看函数的完整实现
- 确认函数签名和参数
- 查看所有重载版本

**参数**：
- `symbol` — 符号名称
- `includeCode` — 可选，是否包含源码（默认 true）
- `projectPath` — 可选，项目路径

**返回内容**：
- 定义位置（文件、行号）
- 完整源码
- 调用者/被调用者线索
- 所有重载版本（对歧义名称）

**使用示例**：
```
codegraph_node(symbol="UserService.login")
codegraph_node(symbol="calculateTotal", includeCode=true)
```

### `codegraph_files`

**用途**：获取已索引的文件结构。

**适用场景**：
- 快速了解项目结构
- 查找特定类型的文件
- 确认文件是否被索引

**参数**：
- `format` — 可选，输出格式（tree、flat、grouped），默认 tree
- `pattern` — 可选，过滤模式（如 `*.ts`、`**/*.test.ts`）
- `maxDepth` — 可选，最大目录深度
- `includeMetadata` — 可选，是否包含元数据（默认 true）
- `projectPath` — 可选，项目路径

**使用示例**：
```
codegraph_files(format="tree", maxDepth=3)
codegraph_files(pattern="**/*.ts", format="flat")
```

### `codegraph_status`

**用途**：检查索引健康状态和统计信息。

**适用场景**：
- 验证索引是否正常
- 检查待同步文件
- 诊断索引问题

**参数**：
- `projectPath` — 可选，项目路径

**返回内容**：
- 索引统计（文件数、符号数、边数）
- 健康状态
- 待同步文件列表（若有）
- Journal 模式（应为 `wal`）

**使用示例**：
```
codegraph_status()
```

## 使用规则

1. **`codegraph_explore` 是首选** — 几乎所有"如何工作"类问题都应使用它
2. **信任返回结果** — 返回的源码视为已读，不要用 grep/read 重复验证
3. **检查 staleness banner** — 编辑后查询结果若含 `⚠️` 标记，直接 Read 对应文件
4. **若 `.codegraph/` 不存在** — 提示用户运行 `codegraph init -i`
