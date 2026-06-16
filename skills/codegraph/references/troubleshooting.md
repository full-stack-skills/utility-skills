# CodeGraph 故障排除

> 官方来源：[README.md](https://github.com/colbymchenry/codegraph)

## 常见问题

### "CodeGraph not initialized"

**原因**：项目目录中没有 `.codegraph/` 目录。

**解决**：运行 `codegraph init -i` 初始化项目并构建索引。

```bash
cd your-project
codegraph init -i
```

---

### 索引缓慢

**原因**：大型目录（如 `node_modules`、`dist`、`build`）未被排除。

**解决**：
1. 确认这些目录在 `.gitignore` 中（CodeGraph 自动排除 `.gitignore` 中的内容）
2. 使用 `--quiet` 减少输出开销
3. 检查默认排除目录是否包含你的大型依赖目录

**默认排除的目录**：`node_modules`、`vendor`、`dist`、`build`、`target`、`.venv`、`Pods`、`.next` 等

---

### MCP `database is locked`

**原因**：旧版本（pre-0.9）使用文件锁，新版本使用 WAL 模式不会出现此问题。

**解决**：

1. **检查版本** — 运行 `codegraph --version`，如果不是最新版，重新安装：

```bash
# macOS / Linux
curl -fsSL https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.sh | sh

# Windows (PowerShell)
irm https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.ps1 | iex

# 或 npm
npm i -g @colbymchenry/codegraph@latest
```

2. **检查 Journal 模式** — 运行 `codegraph status`，确认 `Journal:` 为 `wal`

   - 如果不是 `wal`（常见于网络共享和 WSL2 `/mnt`），WAL 无法在此文件系统上启用，读取可能被写入阻塞
   - **解决方案**：将项目（含 `.codegraph/` 文件夹）移到本地磁盘

---

### MCP 服务器无法连接

**原因**：项目未初始化/索引，或 MCP 配置路径错误。

**解决**：
1. 确认项目已初始化：`ls .codegraph/`
2. 确认项目已索引：`codegraph status`
3. 检查 MCP 配置中的路径是否正确
4. 验证 `codegraph serve --mcp` 可从命令行运行

---

### 符号缺失

**原因**：文件尚未同步到索引。

**解决**：
1. 等待几秒——MCP 服务器自动同步（防抖窗口默认 2 秒）
2. 手动运行 `codegraph sync`
3. 检查文件语言是否支持
4. 检查文件是否在 `.gitignore` 或默认排除目录中（如 `node_modules`、`dist`）

---

### WSL/Windows 共享检出问题

**原因**：WSL 和 Windows 指向同一个 `.codegraph/` 目录，SQLite 锁定跨 WSL2/Windows 文件系统边界不可靠。

**解决**：不要让两侧指向同一个 `.codegraph/`。使用 `CODEGRAPH_DIR` 环境变量为每侧设置不同目录名：

```bash
# Windows 侧
export CODEGRAPH_DIR=.codegraph-win

# WSL 侧保持默认
# .codegraph
```

CodeGraph 会跳过任何兄弟 `.codegraph-*` 目录，两侧互不干扰。

---

## 诊断命令

```bash
# 检查索引状态
codegraph status

# 验证 MCP 服务器
codegraph serve --mcp

# 强制重建索引
codegraph index --force

# 手动同步
codegraph sync

# 检查版本
codegraph --version
```

## Agent 工具指南

CodeGraph 的 MCP 服务器通过 MCP `initialize` 响应自动交付使用指南给 Agent，无需手动编辑 CLAUDE.md / AGENTS.md / GEMINI.md。

指南告诉 Agent：
- 使用 CodeGraph 工具直接回答结构化问题（不要用 grep/read 循环重复已索引的工作）
- 按意图选择工具：`codegraph_explore` 用于几乎所有问题
- 信任返回结果——不要用 grep 重新验证
- 如果 `.codegraph/` 不存在，主动提示用户运行 `codegraph init -i`
