# CodeGraph CLI 命令参考

> 官方来源：[README.md](https://github.com/colbymchenry/codegraph)

## 命令总览

```bash
codegraph                         # 运行交互式安装程序
codegraph install                 # 安装并配置 Agent（显式）
codegraph uninstall               # 从所有 Agent 移除配置
codegraph init [path]             # 初始化项目（加 -i 同时构建索引）
codegraph uninit [path]           # 移除项目索引（--force 跳过确认）
codegraph index [path]            # 完整索引（--force 重建，--quiet 减少输出）
codegraph sync [path]             # 增量同步
codegraph status [path]           # 显示统计信息
codegraph query <search>          # 搜索符号（--kind、--limit、--json）
codegraph files [path]            # 显示文件结构（--format、--filter、--max-depth、--json）
codegraph callers <symbol>        # 查找调用者（--limit、--json）
codegraph callees <symbol>        # 查找被调用者（--limit、--json）
codegraph impact <symbol>         # 分析影响范围（--depth、--json）
codegraph affected [files...]     # 查找受影响的测试文件
codegraph serve --mcp             # 启动 MCP 服务器
codegraph upgrade [version]       # 升级到最新版本（--check、--force）
```

## 命令详解

### `codegraph install`

运行安装程序，连接 CodeGraph 到你的 Agent。

```bash
codegraph install                 # 交互式
codegraph install --yes           # 非交互式，自动检测 Agent
codegraph install --target=cursor,claude --yes  # 指定 Agent
codegraph install --target=auto --location=local  # 检测到的 Agent，项目级配置
codegraph install --print-config codex          # 打印配置片段，不写入文件
```

**标志说明**：

| 标志 | 值 | 默认值 |
|------|---|--------|
| `--target` | `auto`、`all`、`none` 或 csv（`claude,cursor,...`） | 交互提示 |
| `--location` | `global`、`local` | 交互提示 |
| `--yes` | (boolean) | 每步都提示 |
| `--no-permissions` | (boolean) 跳过 Claude 自动允许列表 | 启用权限 |
| `--print-config <id>` | 打印指定 Agent 的配置片段 | — |

**自动检测的 Agent**：Claude Code、Cursor、Codex CLI、opencode、Hermes Agent、Gemini CLI、Antigravity IDE、Kiro

### `codegraph uninstall`

移除 CodeGraph 从所有已配置的 Agent。

```bash
codegraph uninstall               # 交互式
codegraph uninstall --yes         # 非交互式
codegraph uninstall --target=cursor  # 从指定 Agent 移除
```

**说明**：
- 移除 MCP 服务器配置、指令和权限
- 项目索引（`.codegraph/`）保持不变
- 使用 `codegraph uninit` 移除项目索引

### `codegraph init`

在项目中初始化 CodeGraph。

```bash
codegraph init                    # 仅创建 .codegraph/ 目录
codegraph init -i                 # 创建目录并构建初始索引
codegraph init -i --force         # 强制重建索引
```

**说明**：
- 仅创建本地 `.codegraph/` 索引目录
- 添加 `-i`（`--index`）同时构建初始图谱
- 不加 `-i` 则需后续运行 `codegraph index`

### `codegraph uninit`

移除项目的 CodeGraph 索引。

```bash
codegraph uninit                  # 交互式确认
codegraph uninit --force          # 跳过确认
```

### `codegraph index`

完整索引项目。

```bash
codegraph index                   # 索引当前目录
codegraph index /path/to/project  # 索引指定目录
codegraph index --force           # 强制重建
codegraph index --quiet           # 减少输出
```

### `codegraph sync`

增量同步索引。

```bash
codegraph sync                    # 同步当前目录
codegraph sync /path/to/project   # 同步指定目录
```

**说明**：
- 通常不需要手动运行——MCP 服务器自动同步
- 适用于：禁用文件监听器、CI 脚本、Agent 会话外操作

### `codegraph status`

显示索引统计信息。

```bash
codegraph status                  # 显示当前目录状态
codegraph status /path/to/project # 显示指定目录状态
```

**返回内容**：
- 文件数、符号数、边数
- 健康状态
- 待同步文件列表
- Journal 模式（应为 `wal`）

### `codegraph query`

搜索符号。

```bash
codegraph query "UserService"                 # 基本搜索
codegraph query "login" --kind function       # 按类型过滤
codegraph query "auth" --limit 20             # 限制结果数
codegraph query "User" --json                 # JSON 输出
```

### `codegraph files`

显示文件结构。

```bash
codegraph files                              # 默认树形格式
codegraph files --format flat                # 平面格式
codegraph files --format grouped             # 按语言分组
codegraph files --filter "*.ts"              # 过滤模式
codegraph files --max-depth 3                # 限制深度
codegraph files --json                       # JSON 输出
```

### `codegraph callers`

查找调用者。

```bash
codegraph callers "authenticate"             # 基本查询
codegraph callers "UserService.login" --limit 10  # 限制结果
codegraph callers "calculateTotal" --json    # JSON 输出
```

### `codegraph callees`

查找被调用者。

```bash
codegraph callees "handleRequest"            # 基本查询
codegraph callees "processOrder" --limit 10  # 限制结果
```

### `codegraph impact`

分析影响范围。

```bash
codegraph impact "UserService"               # 默认深度
codegraph impact "DatabaseConnection" --depth 3  # 指定深度
codegraph impact "AuthService" --json        # JSON 输出
```

### `codegraph affected`

查找受影响的测试文件。

```bash
codegraph affected src/utils.ts src/api.ts         # 传递文件作为参数
git diff --name-only | codegraph affected --stdin   # 从 git diff 管道输入
codegraph affected src/auth.ts --filter "e2e/*"     # 自定义测试文件模式
```

**选项**：

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `--stdin` | 从 stdin 读取文件列表 | `false` |
| `-d, --depth <n>` | 最大依赖遍历深度 | `5` |
| `-f, --filter <glob>` | 自定义 glob 来识别测试文件 | 自动检测 |
| `-j, --json` | JSON 输出 | `false` |
| `-q, --quiet` | 仅输出文件路径 | `false` |

**CI/hook 示例**：

```bash
#!/usr/bin/env bash
AFFECTED=$(git diff --name-only HEAD | codegraph affected --stdin --quiet)
if [ -n "$AFFECTED" ]; then
  npx vitest run $AFFECTED
fi
```

### `codegraph serve --mcp`

启动 MCP 服务器（供 Agent 使用）。

```bash
codegraph serve --mcp             # 启动 MCP 服务器
```

**说明**：
- 通常由 Agent 自动启动（通过 MCP 配置）
- 包含文件监听器和自动同步
- 交付使用指南给 Agent（通过 MCP `initialize` 响应）

### `codegraph upgrade`

升级 CodeGraph。

```bash
codegraph upgrade                 # 升级到最新版本
codegraph upgrade --check         # 检查是否有更新
codegraph upgrade --force         # 强制升级
codegraph upgrade 1.2.3           # 升级到指定版本
```

**说明**：
- 自动检测安装方式（bundle、npm、npx）并原地更新
