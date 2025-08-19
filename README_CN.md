<div align="center">
  
### **Claude Code 完整指南**

> 本指南涵盖了截至2025年7月的**所有可发现的Claude Code命令**，  
> 包括许多在基础教程中未被广泛知晓或记录的功能。

**这是目前最完整的Claude Code命令参考文档。**

---

*如需更新和贡献，请访问[官方Claude Code文档](https://claude.ai/code)*

[![Claude Code](https://img.shields.io/badge/Claude%20Code-v1.0.38-blue?logo=anthropic)](https://claude.ai/code)
[![状态](https://img.shields.io/badge/状态-活跃-brightgreen)](https://github.com/anthropics/claude-code)
[![许可证](https://img.shields.io/badge/许可证-Anthropic-orange)](https://claude.ai/code)

</div>

<div align="center">

<kbd>

| 章节                                    | 状态          |
|---------------------------------------------|------------------------------|
| Windows、Linux、MacOS安装指南 | ✅ |
| 技巧与窍门                            | ✅ |
| MCP概览及使用方法              | ✅ |
| 社区指南                           | ✅ |
| 故障排除                            | ✅ |
| 如何以最优方式使用Claude Code| ✅ |
### 我通常使用 <code>Claude --dangerously-skip-permissions</code> 启动Claude，仅在你确切知道自己在做什么时使用此选项！

</kbd>

</div>

---

## 目录

1. [快速开始](#快速开始)
2. [安装与设置](#安装与设置)
3. [核心命令](#核心命令)
4. [会话命令](#会话命令)
5. [配置命令](#配置命令)
6. [标志参考](#标志参考)
7. [内置斜杠命令](#内置斜杠命令交互式)
8. [MCP集成](#MCP集成)
9. [配置](#配置)
10. [环境变量](#环境变量)
11. [安全与权限](#安全与权限)
12. [Claude CLI配置](#claude-cli配置)
13. [Claude ~/.claude.json配置指南](#claude-claudejson配置指南)
14. [自动化与脚本](#自动化与脚本指南)
15. [故障排除](#故障排除)
16. [高级功能](#高级功能)
17. [最佳实践](#最佳实践)

---

## 快速开始

```bash
# 快速安装

## 方法1 - NPM（全局）⭐️⭐️ 官方推荐
npm install -g @anthropic-ai/claude-code
# 需要 macOS / Linux / WSL 上的 Node 18+

## 方法2 MacOS
brew install node
npm install -g @anthropic-ai/claude-code
# 如果发现claude bot问题？运行 export PATH="$PATH:$(npm bin -g)"

## 方法3 - Arch Linux AUR
yay -S claude-code        # 或者 paru -S claude-code
# 与npm版本保持同步

## 方法4 - Docker（容器化）
mkdir -p ~/.docker/cli-plugins
curl -SL https://github.com/docker/buildx/releases/download/v0.11.2/buildx-v0.11.2.linux-amd64 -o ~/.docker/cli-plugins/docker-buildx
chmod +x ~/.docker/cli-plugins/docker-buildx
curl -O https://raw.githubusercontent.com/RchGrav/claudebox/main/claudebox
chmod +x claudebox
# 当你无法接触主机系统时很有用

## 方法5 - Windows通过WSL（Anthropic推荐路径）
# 1) 启用WSL 2并安装Ubuntu
# 2) 在Ubuntu中：
sudo apt update && sudo apt install -y nodejs npm
npm install -g @anthropic-ai/claude-code
# 分步指南

# 你也可以用以下命令在vscode或cursor中轻松打开claude code和你的整个项目：
cursor .
# 或者
code .
# cd进入你想要工作的目录并运行上面的命令！
# 记得安装claude code扩展

## 检查claude是否已安装
which claude
claude --version


# 交互模式
claude                      # 启动交互式REPL
claude "你的问题"          # 带初始提示启动

# 一次性模式
claude -p "分析这个"       # 快速查询并退出
cat file | claude -p "修复"  # 处理管道内容

# 管理
claude config              # 配置设置
claude update              # 更新到最新版本
claude mcp                 # 设置MCP服务器
```

---

## 安装与设置

系统要求

操作系统：macOS 10.15+，Ubuntu 20.04+/Debian 10+，或通过WSL的Windows
硬件：最低4GB内存，推荐8GB+
软件：
Node.js 18+
git 2.23+（可选）
用于PR工作流程的GitHub或GitLab CLI（可选）
- 需要网络连接进行API调用

**支持的平台：**
- macOS（Intel/Apple Silicon）
- Linux（Ubuntu、Debian、CentOS）
- Windows 10/11（**推荐WSL**，因为常规Windows终端目前不受支持）

### 安装方法

#### 方法1：NPM安装
```bash
# 全局安装
npm install -g @anthropic-ai/claude-code

# 验证安装
claude --version
```

### 初始设置

#### 1. API密钥配置
```bash
# 必需：从https://console.anthropic.com获取API密钥
export ANTHROPIC_API_KEY="sk-your-key-here"

# 永久设置（选择你的shell）
# Bash
echo 'export ANTHROPIC_API_KEY="sk-your-key-here"' >> ~/.bashrc
source ~/.bashrc

# Zsh
echo 'export ANTHROPIC_API_KEY="sk-your-key-here"' >> ~/.zshrc
source ~/.zshrc

# Fish
echo 'set -gx ANTHROPIC_API_KEY "sk-your-key-here"' >> ~/.config/fish/config.fish
```

#### 2. 基本配置
```bash
# 交互式设置
claude config

# 设置基本默认值
claude config set -g model claude-sonnet-4
claude config set -g verbose true
claude config set -g outputFormat text

# 测试安装
claude "Hello, Claude!"
claude /doctor  
```

#### 3. 要关闭的设置
```bash
export DISABLE_TELEMETRY=1
export DISABLE_ERROR_REPORTING=1
export DISABLE_NON_ESSENTIAL_MODEL_CALLS=1


# 安全默认值
claude config set allowedTools "Edit,View"
claude config set hasTrustDialogAccepted
claude config set hasCompletedProjectOnboarding 
claude config set ignorePatterns
claude config set --global
```

### 健康检查与测试
```bash
# 基本功能测试
claude "解释你能做什么"

# 打印模式测试
claude -p "2+2等于几？"

# 工具权限测试
claude "创建一个名为test.txt的文件，内容为'Hello World'"
# 应该提示编辑权限

# 会话连续性测试
claude -c  # 应该继续上一个会话
```

# 健康检查
```
claude /doctor
预期输出可能包括：
# ✅ API密钥：有效
# ✅ 网络：已连接
# ✅ 模型访问：可用
```

---

## 核心命令

| 命令                               | 描述                  | 示例                                  |
| ---------------------------------- | --------------------- | ------------------------------------- |
| `claude`                           | 启动交互式REPL        | `claude`                              |
| `claude "<提示>"`                 | 带初始提示的REPL      | `claude "帮助调试"`                   |
| `claude -p "<提示>"`              | 一次性打印模式        | `claude -p "解释函数"`                |
| `cat file \| claude -p "<提示>"`  | 将STDIN管道到Claude   | `cat logs.txt \| claude -p "解释"`   |
| `claude update`                    | 自我更新到最新版本    | `claude update`                       |
| `claude mcp`                       | 启动MCP向导           | `claude mcp`                          |

## 会话命令

| 命令                       | 描述                  | 示例                         |
| -------------------------- | --------------------- | ---------------------------- |
| `claude -c` / `--continue` | 继续上一个会话        | `claude -c`                  |
| `claude -c -p "<提示>"`    | 继续+新提示（打印）   | `claude -c -p "检查类型"`    |
| `claude -r <id>`           | 通过会话ID恢复        | `claude -r abc123`           |
| `claude --resume <id>`     | 长格式恢复            | `claude --resume abc123`     |
| `claude --resume <名称>`   | 通过保存的名称恢复    | `claude --resume 冲刺评审`   |

## 配置命令

| 命令                                 | 描述           | 示例                          |
| ------------------------------------ | -------------- | ----------------------------- |
| `claude config`                      | 交互式向导     | `claude config`               |
| `claude config list`                 | 列出所有键     | `claude config list`          |
| `claude config get <键>`             | 获取值         | `claude config get theme`     |
| `claude config set <键> <值>`        | 设置值         | `claude config set theme dark`|
| `claude config add <键> <值...>`     | 追加到数组     | `claude config add env DEV=1` |
| `claude config remove <键> <值...>`  | 移除项目       | `claude config remove env DEV=1` |

---

## 标志参考

### 基本标志

| 标志         | 简写 | 描述             | 示例               |
| ------------ | ---- | ---------------- | ------------------ |
| `--print`    | `-p` | 非交互模式       | `claude -p "帮助"` |
| `--continue` | `-c` | 恢复上一个会话   | `claude --continue` |
| `--resume`   | `-r` | 通过ID恢复       | `claude --resume abc` |
| `--help`     | `-h` | 显示帮助         | `claude --help`    |
| `--version`  | `-v` | 显示版本         | `claude --version` |

### 使用-p的输出与流程

| 标志                     | 选项                          | 示例                               |
| ------------------------ | ----------------------------- | ---------------------------------- |
| `--output-format`        | `text`, `json`, `stream-json` | `--output-format json`             |
| `--input-format`         | `text`, `stream-json`         | `--input-format stream-json`       |
| `--max-turns`            | 整数                          | `--max-turns 3`                    |
| `--system-prompt`        | 字符串                        | `--system-prompt "你是严格的"`   |
| `--append-system-prompt` | 字符串                        | `--append-system-prompt "添加测试"` |

### 安全与权限

| 标志                              | 描述               | 示例                                      |
| --------------------------------- | ------------------ | ----------------------------------------- |
| `--allowedTools <列表>`           | 白名单工具         | `--allowedTools "Read,View"`              |
| `--disallowedTools <列表>`        | 黑名单工具         | `--disallowedTools "Bash"`                |
| `--permission-mode <模式>`        | 以权限模式启动     | `--permission-mode plan`                  |
| `--permission-prompt-tool <工具>` | 权限检查的MCP工具  | `--permission-prompt-tool mcp__auth__prompt` |
| `--dangerously-skip-permissions`  | 跳过所有提示（⚠️） | `--dangerously-skip-permissions`          |

### 额外标志

| 标志                  | 目的               | 示例                        |
| --------------------- | ------------------ | --------------------------- |
| `--add-dir <路径>`    | 额外工作目录       | `--add-dir ../lib ../src`   |
| `--model <名称>`      | 选择模型           | `--model claude-opus-4-20250514` |
| `--verbose`           | 详细日志           | `--verbose`                 |
| `--mcp-config <文件>` | 加载MCP服务器      | `--mcp-config servers.json` |

---

## 内置斜杠命令（交互式）

| 斜杠命令            | 目的                     |
| ------------------- | ------------------------ |
| `/help`             | 列出斜杠命令             |
| `/add-dir`          | 添加更多工作目录         |
| `/bug`              | 向Anthropic报告错误      |
| `/clear`            | 清除聊天历史             |
| `/compact`          | 压缩对话                 |
| `/config`           | 配置菜单                 |
| `/cost`             | 令牌使用情况             |
| `/doctor`           | 健康检查                 |
| `/exit`             | 退出REPL                 |
| `/init`             | 生成CLAUDE.md            |
| `/login` / `/logout`| 身份验证切换             |
| `/mcp`              | 管理MCP服务器            |
| `/memory`           | 编辑记忆                 |
| `/model`            | 更改模型                 |
| `/permissions`      | 工具权限                 |
| `/pr_comments`      | 查看PR评论               |
| `/review`           | 请求代码审查             |
| `/sessions`         | 列出会话                 |
| `/status`           | 系统/账户状态            |
| `/terminal-setup`   | 安装Shift+Enter绑定      |
| `/vim`              | 切换vim模式              |

---

## 🔌 MCP集成

### 理解MCP（模型上下文协议）

**什么是MCP？**
MCP通过连接外部服务、数据库、API和工具来扩展Claude的功能。

**MCP架构：**
```
Claude Code ←→ MCP协议 ←→ MCP服务器 ←→ 外部服务
```

### MCP设置与配置

#### 基本MCP命令
```bash
claude mcp                    # 交互式MCP配置
claude mcp list              # 列出已配置的服务器           
claude mcp add <名称> <命令>  # 添加新服务器
claude mcp remove <名称>     # 移除服务器
```

#### MCP配置文件
**位置**：`~/.claude.json`

### 基于作用域的配置文件

用户/全局作用域：
全局MCP服务器
项目作用域：
项目作用域的服务器存储在项目根目录的`.mcp.json`文件中

```json
{
  "mcpServers": {
    "git": {
      "command": "git-mcp-server",
      "args": [],
      "env": {}
    },
    "postgres": {
      "command": "postgres-mcp-server", 
      "args": ["--host", "localhost", "--port", "5432"],
      "env": {
        "POSTGRES_USER": "developer",
        "POSTGRES_PASSWORD": "dev_password",
        "POSTGRES_DB": "myapp_development"
      }
    }
  }
}
```

### MCP服务器

**注意**：下面确切的包名和安装命令可能不准确。请查阅官方MCP文档获取当前服务器包。

#### 开发工具
```bash
# npm install -g git-mcp-server        

# claude mcp add git "git-mcp-server"
# claude mcp add github "github-mcp-server --token $GITHUB_TOKEN"
```

#### 数据库集成  
```bash
npm install -g postgres-mcp-server              
npm install -g mysql-mcp-server                 
npm install -g sqlite-mcp-server              

# 设置示例
# export POSTGRES_URL="postgresql://user:password@localhost:5432/mydb"
# claude mcp add postgres "postgres-mcp-server --url $POSTGRES_URL"
```

### MCP工具权限

```bash
# 允许特定MCP工具
claude --allowedTools "mcp__git__commit,mcp__git__push"

# 允许特定服务器的所有工具
claude --allowedTools "mcp__postgres__*"

# 与内置工具结合
claude --allowedTools "Edit,View,mcp__git__*"
```

---

## 配置⚙️ 

### 配置系统概述

Claude Code使用分层配置系统：
1. **命令行标志**（最高优先级）
2. **环境变量**
3. **项目配置**（位置可能不同）
4. **全局配置**（可能是`~/.claude.json`）
5. **内置默认值**（最低优先级）

### 配置文件

#### 全局配置
**位置**：`~/.claude.json`

```json
{
  "model": "claude-sonnet-4",
  "verbose": true,
  "outputFormat": "text", 
  "allowedTools": ["Edit", "View"],
  "disallowedTools": [],
}
```

#### 项目配置
**位置**：`settings.json`或类似文件

```json
{
  "model": "claude-sonnet-4",
  "systemPrompt": "你是这个项目的资深开发者",
  "allowedTools": [
    "Edit",
    "View",
    "Bash(git:*)",
    "Bash(npm:*)"
  ],
}
```

### 环境变量

#### 核心变量
| 变量 | 必需 | 目的 | 示例 |
|----------|----------|---------|---------|
| `ANTHROPIC_API_KEY` | **是** | API身份验证 | `sk-ant-api03-xxx` |
| `ANTHROPIC_MODEL` | 否 | 默认模型 | `claude-sonnet-4` |
| `ANTHROPIC_BASE_URL` | 否 | API端点覆盖 | `https://api.anthropic.com` |

## 环境变量

| 环境变量                             | 默认值    | 示例值 | 作用                                                                                       |
| ----------------------------------- | ---------- | ------------- | -------------------------------------------------------------------------------------------- |
| `DISABLE_NON_ESSENTIAL_MODEL_CALLS` | `0`        | `1`           | 跳过自动摘要、后台解释和git差异扫描 ⇒ 更快、更便宜。             |
| `MAX_THINKING_TOKENS`               | *约30-40k* | `50000`       | 为读取代码、分析差异和规划提供更高的令牌预算。                            |
| `DISABLE_TELEMETRY`                 | `0`        | `1`           | 阻止匿名使用+错误遥测。                                                     |
| `CLAUDE_CODE_USE_BEDROCK`           | `0`        | `1`           | 通过**AWS Bedrock**路由请求（需要IAM凭证；如果不存在则回退）。                  |
| `CLAUDE_CODE_USE_VERTEX`            | `0`        | `1`           | 通过**Google Vertex AI**路由请求（需要服务账户凭证；如果不存在则回退）。 |

```
```

![image](https://github.com/user-attachments/assets/d07be233-3322-4510-bb98-4165589d1924)



| 环境变量       | 默认值               | 示例值                       | 作用                                       |
| ------------- | --------------------- | ----------------------------------- | -------------------------------------------------- |
| `HTTP_PROXY`  | *(未设置)*             | `http://proxy.company.com:8080`     | 通过给定代理路由**HTTP**请求。  |
| `HTTPS_PROXY` | *(未设置)*             | `https://proxy.company.com:8443`    | 通过给定代理路由**HTTPS**请求。 |
| `NO_PROXY`    | `localhost,127.0.0.1` | `localhost,127.0.0.1,*.company.com` | 逗号分隔的绕过代理的主机/IP。   |

---

## 如何设置

<details>
<summary><strong>Shell（临时）</strong></summary>

```bash
export HTTP_PROXY="http://proxy.company.com:8080"
export HTTPS_PROXY="https://proxy.company.com:8443"
export NO_PROXY="localhost,127.0.0.1,*.company.com"
claude "通过代理测试请求"
```

</details>

<details>
<summary><strong>Shell配置文件（永久）</strong></summary>

```bash
# ~/.bashrc 或 ~/.zshenv
export HTTP_PROXY="http://proxy.company.com:8080"
export HTTPS_PROXY="https://proxy.company.com:8443"
export NO_PROXY="localhost,127.0.0.1,*.company.com"
```

使用 `source ~/.bashrc` 重新加载。

</details>

<details>
<summary><strong>GitHub Actions</strong></summary>

```yaml
env:
  HTTP_PROXY:  "http://proxy.company.com:8080"
  HTTPS_PROXY: "https://proxy.company.com:8443"
  NO_PROXY:    "localhost,127.0.0.1,*.company.com"
```

</details>

---

## 安全与权限

### 权限系统

**工作原理**：
- Claude在使用工具前会请求权限
- 权限按会话记忆
- 危险操作需要确认

#### 权限级别
| 级别 | 描述 | 风险 | 用例 |
|-------|-------------|------|----------|
| **交互式** | 每个操作都提示 | **低** | 开发工作 |
| **白名单** | 仅预先批准的工具 | **中** | 自动化脚本 |
| **危险** | 跳过所有权限 | **严重** | 仅容器 |

#### 工具权限模式
```bash
# 允许特定工具
claude --allowedTools "Edit,View"

# 允许工具类别
claude --allowedTools "Edit,View,Bash"

# 范围权限（仅限Git操作）
claude --allowedTools "Bash(git:*)"

# 多个范围
claude --allowedTools "Bash(git:*),Bash(npm:*)"
```

### 危险模式（关键安全功能）

```bash
# 危险 - 可能导致数据丢失
claude --dangerously-skip-permissions

# 仅在隔离环境中使用：
# ✅ 安全：隔离的Docker容器
# ❌ 绝不：生产系统、共享机器、有重要数据的系统
```

### 安全最佳实践

#### 1. 从限制开始
```bash
# 好：特定权限
claude --allowedTools "Edit,View,Bash(git:status)"

# 坏：宽泛权限
claude --allowedTools "Bash"
```

#### 2. 保护敏感数据
```bash
# 好：环境变量
export DATABASE_URL="postgresql://user:pass@host/db"

# 坏：命令中硬编码凭证
# claude "连接到 postgresql://user:password123@host/db"
```

#### 3. 定期安全审计
```bash
# 检查当前权限
claude config get allowedTools
claude config get disallowedTools

# 审查配置
claude config list
```

--### 思考关键词

提示词中的某些短语可以**向 CLI 暗示**你需要额外的推理时间。这些关键词不属于官方公共 API，因此使用时请知晓它们可能在未来版本中更改或移除。

| 等级       | 可使用的关键词（不区分大小写）                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------- |
| **等级 1** | `think`                                                                                                                     |
| **等级 2** | `think about it`、`think a lot`、`think deeply`、`think hard`、`think more`、`megathink`                                    |
| **等级 3** | `think harder`、`think intensely`、`think longer`、`think really hard`、`think super hard`、`think very hard`、`ultrathink` |

### 快速示例

```bash
claude -p "We have a tricky concurrency bug. ultrathink and propose a fix."
```

* 关键词可以出现在提示词的任何位置（不区分大小写）。
* 如果存在多个个关键词，以**最高等级**为准。

> ⚠️ **注意**：由于此机制未公开文档说明，Anthropic 可能会在不通知的情况下更改或移除它。

### Claude CLI 配置

> 配置键

---

## 前提条件

1. **首先进行身份验证**

   ```bash
   # 选项 1 – 环境变量（推荐用于脚本）
   export ANTHROPIC_API_KEY="sk-..."

   # 选项 2 – 在 Claude REPL 中交互式登录
   claude /login
   ```
2. **备份当前配置**

   ```bash
   cp ~/.claude/claude.json ~/.claude/claude.json.bak

   # 这取决于 .json 的安装位置，它也可能在 ~/.claude/local/package.json
   ```

如果 `apiKeyHelper` 配置错误或未找到 API 密钥，你会看到类似以下的错误：

```
Error getting API key from apiKeyHelper (in settings or ~/.claude.json):
```

在修改其他键之前修复身份验证问题。（[docs.anthropic.com](https://docs.anthropic.com/en/docs/claude-code/settings)）

---

## `claude config` 命令

| 命令模式                                | 用途                       | 示例                                                       |
| --------------------------------------- | -------------------------- | ---------------------------------------------------------- |
| `claude config list`                    | 显示所有当前设置           | `claude config list`                                       |
| `claude config get <key>`               | 显示单个设置               | `claude config get theme`                                  |
| `claude config set -g <key> <value>`    | **设置全局值**             | `claude config set -g theme dark`                          |
| `claude config add -g <key> <value>`    | 向数组类型的设置追加内容   | `claude config add -g env CLAUDE_CODE_ENABLE_TELEMETRY=1`  |
| `claude config remove -g <key> <value>` | 从数组类型的设置中移除内容 | `claude config remove -g env CLAUDE_CODE_ENABLE_TELEMETRY` |

（省略 `-g` 可将目标改为**当前项目**而非全局）。

---

## 可编辑的键

| 键                              | 典型值                                                 | 安全示例                                                    | 说明                                                                                                                     |
| ------------------------------- | ------------------------------------------------------ | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| `apiKeyHelper`                  | 可执行脚本的路径                                       | `claude config set -g apiKeyHelper ~/.claude/key_helper.sh` | 脚本必须输出新的 API 密钥；必须可执行。（[docs.anthropic.com](https://docs.anthropic.com/en/docs/claude-code/settings)） |
| `installMethod`                 | `npm`、`brew`、`binary`、`deb` 等                      | `claude config set -g installMethod npm`                    | 仅作信息用途。（[ainativedev.io](https://ainativedev.io/news/configuring-claude-code)）                                  |
| `autoUpdates`                   | `true` / `false`                                       | `claude config set -g autoUpdates false`                    | 开启/关闭自动更新。（[docs.anthropic.com](https://docs.anthropic.com/en/docs/claude-code/settings)）                     |
| `theme`                         | `dark`、`light`、`light-daltonized`、`dark-daltonized` | `claude config set -g theme dark`                           | CLI 配色方案。（[docs.anthropic.com](https://docs.anthropic.com/en/docs/claude-code/settings)）                          |
| `verbose`                       | `true` / `false`                                       | `claude config set -g verbose true`                         | 显示完整的 Bash 和工具输出。（[docs.anthropic.com](https://docs.anthropic.com/en/docs/claude-code/settings)）            |
|                                 |                                                        | `claude config set -g                                       | 警报显示位置。（[docs.anthropic.com](https://docs.anthropic.com/en/docs/claude-code/settings)）                          |
| `shiftEnterKeyBindingInstalled` | `true` / `false`                                       | `claude config set -g shiftEnterKeyBindingInstalled true`   | 启用 Shift+Enter 换行。（[ainativedev.io](https://ainativedev.io/news/configuring-claude-code)）                         |
| `hasUsedBackslashReturn`        | `true` / `false`                                       | `claude config set -g hasUsedBackslashReturn true`          | 内部标志；很少需要更改。（[ainativedev.io](https://ainativedev.io/news/configuring-claude-code)）                        |
| `supervisorMode`                | `true` / `false`                                       | `claude config set -g supervisorMode true`                  | 启用管理功能。（[ainativedev.io](https://ainativedev.io/news/configuring-claude-code)）                                  |
| `autoCompactEnabled`            | `true` / `false`                                       | `claude config set -g autoCompactEnabled true`              | 自动压缩聊天日志。（[ainativedev.io](https://ainativedev.io/news/configuring-claude-code)）                              |
| `diffTool`                      | 差异比较命令/路径                                      | `claude config set -g diffTool meld`                        | 供 `/diff` 使用。（[ainativedev.io](https://ainativedev.io/news/configuring-claude-code)）                               |
| `env`                           | `KEY=value` 或 JSON                                    | `claude config set -g env CLAUDE_CODE_ENABLE_TELEMETRY=0`   | 向每个会话注入环境变量。（[docs.anthropic.com](https://docs.anthropic.com/en/docs/claude-code/settings)）                |
| `tipsHistory`                   | `[]` 或 JSON 数组                                      | `claude config set -g tipsHistory []`                       | 清除提示弹窗历史。（[ainativedev.io](https://ainativedev.io/news/configuring-claude-code)）                              |
| `parallelTasksCount`            | ≥ 1 的整数                                             | `claude config set -g parallelTasksCount 4`                 | 限制并发任务数。（[ainativedev.io](https://ainativedev.io/news/configuring-claude-code)）                                |
| `todoFeatureEnabled`            | `true` / `false`                                       | `claude config set -g todoFeatureEnabled true`              | 启用实验性待办功能。（[ainativedev.io](https://ainativedev.io/news/configuring-claude-code)）                            |
| `messageIdleNotifThresholdMs`   | 整数（毫秒）                                           | `claude config set -g messageIdleNotifThresholdMs 60000`    | 触发警报前的空闲阈值。（[ainativedev.io](https://ainativedev.io/news/configuring-claude-code)）                          |
| `autoConnectIde`                | `true` / `false`                                       | `claude config set -g autoConnectIde true`                  | 启动时自动连接到 IDE。（[ainativedev.io](https://ainativedev.io/news/configuring-claude-code)）                          |

> 🔒 **尝试设置任何其他键（例如 `model`）将抛出** `Error: Cannot set '<key>'. Only these keys can be modified…`——通过 CLI 验证。（[docs.anthropic.com](https://docs.anthropic.com/en/docs/claude-code/settings)）

---

## 迁移到 `settings.json`

Anthropic 正逐步弃用 `claude config`，转而采用分层的 `settings.json` 文件：

```bash
# 全局（用户级）设置
vi ~/.claude/settings.json

# 项目级（可提交到 git）
vi .claude/settings.json
```

---

## 安全编辑检查清单

1. **备份** `~/.claude/claude.json`。
2. **进行身份验证**（`ANTHROPIC_API_KEY` 或 `/login`）。
3. **一次只更改一个键**→ 用 `claude config get` 验证。
4. **保持 CLI 更新**（`autoUpdates=true`）或通过包管理器更新。
5. **阅读发布说明**了解新增或移除的键。

---

# Claude `~/.claude.json` 配置指南

> **目的**——一个简洁、经过事实核查的参考，用于安全编辑个人配置文件。所有键和示例均直接来自 Anthropic 提供的默认值或 CLI 自身的输出——不包含推测性或未公开的字段。

---

## 1 ▸ 首先备份

```bash
cp ~/.claude.json ~/.claude.json.backup
```

如果出现任何问题，用以下命令恢复：

```bash
cp ~/.claude.json.backup ~/.claude.json
```

---

## 2 ▸ MCP 服务器

`mcpServers` 让 Claude Code 能够与外部工具（文件系统、网络、GitHub 等）交互。每个条目都遵循以下**精确**的 schema。

```jsonc
{
  "mcpServers": {
    "server-name": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "package-name"],
      "env": {}
    }
  }
}
```

### 2.1 Schema

| 字段      | 是否必需 | 示例值                                             | 说明                               |
| --------- | -------- | -------------------------------------------------- | ---------------------------------- |
| `type`    | ✅        | `"stdio"`                                          | 连接方式（CLI 仅支持 **stdio**）。 |
| `command` | ✅        | `"npx"`                                            | Claude Code 运行的可执行文件。     |
| `args`    | ✅        | `["-y", "@modelcontextprotocol/server-puppeteer"]` | CLI 参数（第一项通常是 `-y`）。    |
| `env`     | ✅        | `{ "API_KEY": "value" }`                           | 导出到子进程的键值对。             |

### 2.2 可直接复制的示例

```jsonc
{
  "mcpServers": {
    "sequential-thinking": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"],
      "env": {}
    },
    "puppeteer": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-puppeteer"],
      "env": {}
    },
    "fetch": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@kazuph/mcp-fetch"],
      "env": {}
    }
  }
}
```

#### 带 API 密钥的示例

```jsonc
{
  "mcpServers": {
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "<your‑token>" }
    },
    "brave-search": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-brave-search"],
      "env": { "BRAVE_API_KEY": "<your‑key>" }
    }
  }
}
```

#### 更多常用服务器

```jsonc
{
  "mcpServers": {
    "filesystem": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/allowed/directory"],
      "env": {}
    },
    "context7": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"],
      "env": {}
    }
  }
}
```

---

## 3 ▸ 功能标志

以下三个标志均可安全切换。**仅支持布尔值。**

| 标志                            | 用途                       | 默认值  |
| ------------------------------- | -------------------------- | ------- |
| `bypassPermissionsModeAccepted` | 确认你已了解绕过权限模式。 | `false` |
| `hasAcknowledgedCostThreshold`  | 首次确认后隐藏成本弹窗。   | `false` |
| `isQualifiedForDataSharing`     | 选择加入/退出匿名遥测。    | `false` |

示例：

```jsonc
{
  "bypassPermissionsModeAccepted": true,
  "hasAcknowledgedCostThreshold": true,
  "isQualifiedForDataSharing": false
}
```

---

## 4 ▸ 重置提示和入职流程

```jsonc
{
  "tipsHistory": {
    "new-user-warmup": 0,
    "ide-hotkey": 0,
    "shift-enter": 0
  },
  "hasCompletedOnboarding": false
}
```

*将计数器设为 `0` 或 `hasCompletedOnboarding` 设为 `false` 可重新显示入职屏幕。*

---

## 5 ▸ 不应手动编辑的内容

| 部分                                                                         | 原因                           |
| ---------------------------------------------------------------------------- | ------------------------------ |
| **身份验证数据**（`oauthAccount`、`primaryApiKey`、`customApiKeyResponses`） | 有锁定风险或泄露机密。         |
| **应用状态**（`numStartups`、`cachedChangelog` 等）                          | 无实际作用；会被应用覆盖。     |
| **Projects** 块                                                              | 自动填充并在每个会话重新计算。 |

仅在调试时展开这些块，并在之后从备份恢复。

---

## 6 ▸ 验证和重新加载

1. **验证 JSON**

   ```bash
   python -m json.tool ~/.claude.json
   # 或
   jq . ~/.claude.json
   ```
2. **重启 Claude Code**

   ```bash
   claude
   ```

---

## 7 ▸ 常见任务（快速检查清单）

| 任务                  | 步骤                                               |
| --------------------- | -------------------------------------------------- |
| **添加新 MCP 服务器** | 备份 → 插入服务器块 → 验证 → 重启 → 用 `/mcp` 确认 |
| **更改主题**          | 备份 → 编辑 `"theme"` → 重启                       |
| **启用 Vim 模式**     | 备份 → 设置 `"editorMode": "vim"` → 重启           |

---

## 8 ▸ 安全提示

* 保持 `~/.claude.json` 私密（`chmod 600`）。
* 优先使用环境变量存储 API 密钥，而非明文。
* 切勿将此文件提交到源代码控制。

---

# 自动化与脚本指南

> **目标** — 展示如何将Claude Code集成到**CI/CD管道**和**本地Git钩子**中，使用经过验证的、生产就绪的代码片段。所有示例都依赖于Anthropic的公共CLI（`@anthropic-ai/claude-code`）

---

## 1 ▸ CI/CD集成

### 1.1 GitHub Actions

```yaml
name: Claude代码审查
on:
  pull_request:
    branches: [main, develop]

jobs:
  claude-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: 设置Node.js 18
        uses: actions/setup-node@v4
        with:
          node-version: '18'

      - name: 安装Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: 审查PR
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          claude -p "审查变更中的安全问题和错误" \
            --allowedTools "View" \
            --output-format json > review-results.json

      - name: 上传结果构件
        uses: actions/upload-artifact@v4
        with:
          name: claude-review
          path: review-results.json
```

#### 要点

| 设置                        | 目的                                                    |
| --------------------------- | ---------------------------------------------------------- |
| `actions/checkout@v4`       | 获取拉取请求的差异。                           |
| `@anthropic-ai/claude-code` | 官方CLI（CI中禁用自动更新以提高速度）。      |
| `ANTHROPIC_API_KEY`         | **必须**存储为加密的仓库密钥。            |
| `--allowedTools "View"`     | **只读**工具集：防止运行器中写入文件。 |
| `--output-format json`      | 发出结构化发现以供下游解析。          |

> **安全提示：** 限制运行器的权限（例如`permissions: contents: read`）使CLI无法回推代码。

---

## 2 ▸ 本地Git自动化

### 2.1 预提交钩子

```bash
#!/usr/bin/env bash
# .git/hooks/pre-commit (chmod +x)

# 如果没有暂存内容则中止
staged=$(git diff --cached --name-only --diff-filter=ACM)
[ -z "$staged" ] && exit 0

# 聚合暂存文件内容
payload=$(echo "$staged" | xargs cat)

analysis=$(echo "$payload" | \
  claude -p "在提交前审查这些变更中的问题" \
    --allowedTools "View" \
    --output-format json)

# 在发现关键问题时阻止提交
if echo "$analysis" | jq -e '.critical_issues[]' >/dev/null 2>&1; then
  echo "❌ 发现关键问题 - 提交被阻止"
  exit 1
fi

echo "✅ Claude分析通过"
```

#### 工作原理

* **`git diff --cached`** 仅针对暂存的变更，避免噪音。
* **`xargs cat`** 将这些文件连接用于提示。
* **`jq`** 检查JSON中的非空`critical_issues`数组。
* 钩子以非零退出以在失败时阻止提交。

> ⚠️ **性能提示：** 对于大差异（>15 kB）使用`--stream`调用Claude以减少延迟。

---

## 3 ▸ 常见模式

| 用例                    | 标志组合                                        | 示例                                               |
| --------------------------- | ------------------------------------------------- | ----------------------------------------------------- |
| **安全审查**         | `--allowedTools "View"`                           | `claude -p "审计机密" --allowedTools "View"` |
| **自动修复（实验性）** | `--allowedTools "View,Write" --apply-patch`       | `claude -p "修复lint" --apply-patch`                  |
| **生成SBOM**           | `--allowedTools "View" --output-format cyclonedx` | `claude -p "生成SBOM"`                           |

> ℹ `--apply-patch`标志截至CLI v1.8为**测试版**。在CI中启用前检查发行说明。

---

## 4 ▸ 最佳实践

1. **速率限制** — 免费Anthropic层级每天限制100次请求。缓存结果或仅在大PR上运行。
2. **超时** — 使用`--timeout 120`防止CI作业挂起。
3. **构件保留** — 存储`review-results.json`以追踪。
4. **机密扫描** — GitHub高级安全可能重叠；去重通知。

---

## 5 ▸ 故障排除

| 症状                                  | 可能原因                         | 修复                                                                                   |
| ---------------------------------------- | ------------------------------------ | ------------------------------------------------------------------------------------- |
| `错误：缺少ANTHROPIC_API_KEY`       | 密钥未在仓库或本地环境中设置。 | 在**设置→密钥**中定义或本地`export`。                                 |
| CLI以`超出速率限制`退出1 | 24小时内调用过多。               | 升级计划或限制作业。                                                        |
| 钩子处理二进制文件缓慢                | 向Claude发送了大负载。        | 通过`git diff --cached --name-only --diff-filter=ACM -- '*.js' '*.ts'`过滤二进制文件。 |

---

## 🔧 故障排除

### 诊断命令

```bash
# 基本健康检查
claude --version             
claude --help                   
claude config list                 
claude /doctor                  
```

### 常见问题和解决方案

#### 1. 身份验证问题
```bash
# 检查API密钥
echo $ANTHROPIC_API_KEY

# 测试连接
claude -p "测试" --verbose

# 重置身份验证
```

#### 2. 安装问题  
```bash
# 重新安装
npm uninstall -g @anthropic-ai/claude-code     
npm install -g @anthropic-ai/claude-code      

# 检查Node.js版本
node --version  # 应为16+
```

#### 3. 权限问题
```bash
# 检查当前权限
claude config get allowedTools

# 重置权限
claude config set allowedTools "[]"
claude config set allowedTools '["Edit", "View"]'
```

#### 4. MCP问题
```bash
# 调试MCP 
claude --mcp-debug
claude mcp status  
claude mcp restart --all
```

### 调试模式
```bash
# 启用详细日志
claude --verbose

# 检查日志（验证日志位置）
```

---

## 🚀 高级功能

### 上下文管理

#### CLAUDE.md - 项目记忆
**用途**：存储 Claude 能够记住的持久性项目信息

**位置**：项目根目录

```markdown
# 项目：我的应用程序

## 概述
这是一个使用 PostgreSQL 数据库的 React/Node.js 应用程序。

## 架构
- 前端：React 18 搭配 TypeScript
- 后端：Node.js 搭配 Express
- 数据库：PostgreSQL 14

## 当前目标
- [ ] 实现身份验证
- [ ] 添加 API 文档
- [ ] 搭建 CI/CD 流水线

## 开发指南
- 所有新代码使用 TypeScript
- 遵循 ESLint 配置
- 为新功能编写测试
```

#### 记忆命令
```bash
claude /memory           # 编辑项目记忆
claude /memory view      # 查看当前记忆
```

### 高级思考
```bash
# 这些“思考”短语可能有效：
claude "深入思考这段代码的安全隐患"
claude "彻底分析并提供详细建议"
```

### 多目录工作区
```bash
# 添加多个目录
claude --add-dir ../frontend ../backend ../shared

# 项目级分析
claude "分析整个应用程序架构"
```

---

## 💡 最佳实践

### 高效提示
```bash
# 良好示例：具体且详细
claude "审查 UserAuth.js 中的安全漏洞，重点关注 JWT 处理"

# 不良示例：模糊不清
claude "检查我的代码"
```

### 安全最佳实践
1. **以最小权限起步**：`claude --allowedTools "View"`
2. **使用环境变量**：`export API_KEY="secret"`
3. **定期审计**：`claude config get allowedTools`
4. **避免危险模式**：仅在容器中使用 `--dangerously-skip-permissions`

### 性能提示
1. **使用合适的输出格式**：`--output-format json` 用于自动化场景
2. **提示要具体**：结果更优，执行更快
3. **定期清理**：移除旧会话和缓存

---

**最后更新**：基于包版本 1.0.38 和可用文档。许多功能需要在特定安装中验证。

#### 监控与告警

**1. 健康检查自动化**
```bash
# 定期健康检查
*/15 * * * * /usr/local/bin/claude /doctor > /dev/null || echo "Claude 健康检查失败" | mail -s "告警" admin@company.com
```

**2. 日志分析**
```bash
# 每日日志分析
0 6 * * * tail -1000 /var/log/app.log | claude -p "分析问题" --output-format json > /tmp/daily-analysis.json
```

### 协作最佳实践

#### 团队工作流

**1. 共享配置模板**
```bash
# 创建团队模板
mkdir -p ~/.claude/templates/
cat > ~/.claude/templates/team-frontend.json << EOF
{
  "allowedTools": ["Edit", "View", "Bash(npm:*)", "mcp__git__*"],
  "model": "claude-sonnet-4",
  "systemPrompt": "你正在处理我们的 React 前端。遵循我们的编码标准并使用 TypeScript。"
}
EOF

# 使用模板
claude config import ~/.claude/templates/team-frontend.json
```

**2. 文档自动化**
```bash
# 自动更新文档
claude "根据 API 端点的最新变更更新 README.md"
claude "根据新的数据库模式生成 TypeScript 定义"
```

**3. 代码审查标准**
```bash
# 标准化审查流程
claude --allowedTools "View,mcp__git__*" \
  "使用我们团队的标准审查 PR #123：
  - 安全最佳实践
  - 性能考量
  - 代码风格合规性
  - 测试覆盖率充分性"
```

#### 知识共享

**1. 创建项目运行手册**
```bash
# 生成运行手册
claude "创建此应用程序的部署运行手册，包括所有步骤和故障排除方法"
claude "记录新开发人员的入职流程"
```

**2. 架构文档**
```bash
# 维护架构文档
claude "更新架构文档以反映最近的微服务变更"
claude "为新的身份验证流程创建序列图"
```

### 需避免的常见陷阱

#### 安全陷阱

**❌ 不要**：
- 在生产系统上使用 `--dangerously-skip-permissions`
- 在命令或配置中硬编码机密信息
- 授予过于宽泛的权限
- 不必要地使用提升的权限运行

**✅ 要**：
- 使用环境变量存储机密信息
- 以最小权限起步
- 定期进行安全审计
- 隔离敏感操作

#### 性能陷阱

**❌ 不要**：
- 不必要地加载整个大型代码库
- 为简单任务使用最大思考预算
- 运行多个并发的 Claude 实例
- 忽略内存和缓存清理

**✅ 要**：
- 使用 `--add-dir` 聚焦上下文
- 根据任务复杂度匹配思考预算
- 监控资源使用情况
- 定期清理

#### 工作流陷阱

**❌ 不要**：
- 跳过项目上下文设置（CLAUDE.md）
- 使用模糊、歧义的提示
- 忽略错误消息和日志
- 未测试就自动化

**✅ 要**：
- 维护全面的项目上下文
- 在请求中做到具体详细
- 监控和分析日志
- 在安全环境中测试自动化

---