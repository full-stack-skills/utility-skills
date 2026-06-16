# CodeGraph 安装指南

> 官方来源：[README.md](https://github.com/colbymchenry/codegraph)

## 快速安装（推荐）

### 1. 安装 CLI

**无需 Node.js** — 一条命令获取适合你 OS 的构建：

```bash
# macOS / Linux
curl -fsSL https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.sh | sh

# Windows (PowerShell)
irm https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.ps1 | iex
```

已有 Node？使用 npm：

```bash
npm i -g @colbymchenry/codegraph
```

**说明**：
- CodeGraph 内置运行时——无需编译，无原生构建，在所有平台上行为一致
- 安装程序将 `codegraph` 放到 PATH 上，但**不会改变当前 shell** — 开新终端后再执行下一步
- **升级**：`codegraph upgrade` 自动检测安装方式并原地更新

### 2. 配置 Agent

在**新终端**中运行安装程序：

```bash
codegraph install
```

**说明**：
- 自动检测并配置 Claude Code、Cursor、Codex CLI、opencode、Hermes Agent、Gemini CLI、Antigravity IDE、Kiro
- **这是连接 CodeGraph 到 Agent 的步骤**；仅安装 CLI 不会自动完成
- 快捷方式：`npx @colbymchenry/codegraph` 一步完成下载和安装

### 3. 初始化项目

```bash
cd your-project
codegraph init -i
```

**说明**：
- `codegraph init` 仅创建本地 `.codegraph/` 索引目录
- 添加 `-i`（`--index`）同时构建初始图谱
- 全局 `codegraph install` 只需运行一次——每个项目只需运行 `codegraph init`

## 手动配置

### Claude Code

在 `~/.claude.json` 中添加：

```json
{
  "mcpServers": {
    "codegraph": {
      "type": "stdio",
      "command": "codegraph",
      "args": ["serve", "--mcp"]
    }
  }
}
```

可选：在 `~/.claude/settings.json` 中添加自动允许权限：

```json
{
  "permissions": {
    "allow": [
      "mcp__codegraph__codegraph_search",
      "mcp__codegraph__codegraph_explore",
      "mcp__codegraph__codegraph_callers",
      "mcp__codegraph__codegraph_callees",
      "mcp__codegraph__codegraph_impact",
      "mcp__codegraph__codegraph_node",
      "mcp__codegraph__codegraph_status",
      "mcp__codegraph__codegraph_files"
    ]
  }
}
```

### 其他 Agent

运行 `codegraph install --print-config <agent-id>` 查看指定 Agent 的配置片段。

## 非交互式安装（CI/脚本）

```bash
codegraph install --yes                              # 自动检测 Agent，全局安装
codegraph install --target=cursor,claude --yes       # 指定 Agent
codegraph install --target=auto --location=local     # 检测到的 Agent，项目级配置
codegraph install --print-config codex               # 打印配置片段，不写入文件
```

**标志说明**：

| 标志 | 值 | 默认值 |
|------|---|--------|
| `--target` | `auto`、`all`、`none` 或 csv（`claude,cursor,...`） | 交互提示 |
| `--location` | `global`、`local` | 交互提示 |
| `--yes` | (boolean) | 每步都提示 |
| `--no-permissions` | (boolean) 跳过 Claude 自动允许列表 | 启用权限 |
| `--print-config <id>` | 打印指定 Agent 的配置片段 | — |

## 升级

```bash
codegraph upgrade                 # 升级到最新版本
codegraph upgrade --check         # 检查是否有更新
codegraph upgrade --force         # 强制升级
codegraph upgrade 1.2.3           # 升级到指定版本
```

## 卸载

```bash
codegraph uninstall               # 移除所有 Agent 配置
codegraph uninit                  # 移除项目索引
```

## 支持的平台

| 平台 | 架构 | 安装方式 |
|------|------|---------|
| Windows | x64, arm64 | PowerShell 安装程序或 npm |
| macOS | x64, arm64 | shell 安装程序或 npm |
| Linux | x64, arm64 | shell 安装程序或 npm |

## 环境变量

| 变量 | 说明 | 默认值 |
|------|------|--------|
| `CODEGRAPH_WATCH_DEBOUNCE_MS` | 文件监听器防抖窗口（范围 [100, 60000]） | `2000` |
| `CODEGRAPH_NO_DAEMON` | 设为 `1` 禁用后台守护进程 | 未设置 |
| `CODEGRAPH_DIR` | 自定义索引目录名 | `.codegraph` |

## 嵌入使用（Library Usage）

CodeGraph 可直接嵌入到你的应用中：

```typescript
import CodeGraph from '@colbymchenry/codegraph';

const cg = await CodeGraph.init('/path/to/project');
await cg.indexAll({
  onProgress: (p) => console.log(`${p.phase}: ${p.current}/${p.total}`)
});

const results = cg.searchNodes('UserService');
const callers = cg.getCallers(results[0].node.id);
const context = await cg.buildContext('fix login bug', { maxNodes: 20, includeCode: true, format: 'markdown' });
const impact = cg.getImpactRadius(results[0].node.id, 2);

cg.watch();   // 自动同步文件变化
cg.unwatch(); // 停止监听
cg.close();
```

**嵌入要求**：
- 从 npm 安装（`npm i @colbymchenry/codegraph`）
- API 运行在你的运行时上，需要 **Node 22.5+**（内置 `node:sqlite`）
- TypeScript 类型随包提供
