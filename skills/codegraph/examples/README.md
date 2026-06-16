# CodeGraph 快速入门工作流示例

> 本文件提供完整的 CodeGraph 使用工作流示例，供 Agent 和用户参考。

## 示例 1：理解代码库架构

**场景**：用户想了解项目的整体架构。

**工作流**：

```bash
# 1. 确认项目已初始化
ls .codegraph/
# 如果不存在：codegraph init -i

# 2. 使用 MCP 工具查询
# Agent 调用：codegraph_explore("How is this project structured?")
# 返回：项目架构概览、主要模块、入口点
```

**Agent 交互示例**：

```
用户：这个代码库的架构是怎样的？

Agent：我来查询代码库的结构。
→ 调用 codegraph_explore("How is this project structured?")
→ 返回：按文件分组的符号源码、关系图、影响范围

Agent：[基于返回结果给出架构总结]
```

## 示例 2：追踪调用链

**场景**：用户想知道"请求如何到达数据库"。

**工作流**：

```bash
# Agent 调用：codegraph_explore("How does a request reach the database?")
# 返回：从路由处理器到数据库查询的完整调用链
```

**Agent 交互示例**：

```
用户：用户登录请求是如何处理的？

Agent：我来追踪认证流程。
→ 调用 codegraph_explore("How does the authentication flow work?")
→ 返回：路由 → 控制器 → 服务 → 数据库 的完整调用链

Agent：[基于返回结果解释认证流程]
```

## 示例 3：变更影响分析

**场景**：用户要修改 `UserService`，想知道影响范围。

**工作流**：

```bash
# Agent 调用：codegraph_impact(symbol="UserService", depth=3)
# 返回：所有直接和间接依赖 UserService 的符号
```

**Agent 交互示例**：

```
用户：我要修改 UserService，会影响什么？

Agent：我来分析影响范围。
→ 调用 codegraph_impact(symbol="UserService", depth=3)
→ 返回：受影响的符号列表和依赖链

Agent：修改 UserService 会直接影响以下模块：[列表]
建议运行以下测试：[测试文件列表]
```

## 示例 4：查找受影响的测试文件

**场景**：CI 中根据 git diff 找到需要运行的测试。

**工作流**：

```bash
# 获取变更文件
git diff --name-only HEAD

# 查找受影响的测试文件
git diff --name-only HEAD | codegraph affected --stdin --quiet

# CI 脚本示例
AFFECTED=$(git diff --name-only HEAD | codegraph affected --stdin --quiet)
if [ -n "$AFFECTED" ]; then
  npx vitest run $AFFECTED
fi
```

## 示例 5：符号搜索与详情查看

**场景**：用户想找 `calculateTotal` 函数的实现。

**工作流**：

```bash
# 1. 搜索符号
# Agent 调用：codegraph_search(query="calculateTotal", kind="function")
# 返回：符号位置列表

# 2. 查看详情
# Agent 调用：codegraph_node(symbol="calculateTotal")
# 返回：完整源码、定义位置、调用者/被调用者
```

## 示例 6：框架路由分析

**场景**：用户想知道 Express 应用有哪些 API 路由。

**工作流**：

```bash
# Agent 调用：codegraph_explore("What API routes are defined in this Express app?")
# 返回：所有路由定义及其处理器函数
```

**Agent 交互示例**：

```
用户：这个 Express 应用有哪些 API 路由？

Agent：我来查询路由定义。
→ 调用 codegraph_explore("What API routes are defined in this Express app?")
→ 返回：路由列表、HTTP 方法、处理器函数、中间件

Agent：[基于返回结果列出所有路由]
```

## 示例 7：跨语言桥接追踪（React Native）

**场景**：用户想知道 JS 调用如何到达原生模块。

**工作流**：

```bash
# Agent 调用：codegraph_explore("How does the JS call reach the native module?")
# 返回：JS → NativeModule → ObjC/Swift 的完整调用链
```

## 完整工作流总结

| 步骤 | 操作 | 命令/工具 |
|------|------|----------|
| 1 | 初始化项目 | `codegraph init -i` |
| 2 | 选择工具 | 根据查询意图选择 MCP 工具 |
| 3 | 执行查询 | 调用相应 MCP 工具 |
| 4 | 处理结果 | 将返回源码视为已读 |
| 5 | 检查时效性 | 检查 `⚠️` banner |

## Agent 使用规则

1. **`codegraph_explore` 是首选** — 几乎所有问题都应使用它
2. **信任返回结果** — 不要用 grep/read 重复验证
3. **检查 staleness banner** — 编辑后检查 `⚠️` 标记
4. **若 `.codegraph/` 不存在** — 提示用户运行 `codegraph init -i`
