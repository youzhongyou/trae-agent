# Trae Agent 安装配置指南

适用于 macOS 和 Windows，从零开始到运行的完整步骤。

---

## 前置要求

| 工具 | 说明 | 安装地址 |
|------|------|---------|
| Python 3.12+ | 运行环境 | https://www.python.org/downloads/ |
| uv | Python 包管理器 | https://docs.astral.sh/uv/getting-started/installation/ |
| Git | 代码管理 | https://git-scm.com/downloads |

---

## 第一步：安装 uv

### macOS / Linux

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Windows（PowerShell）

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

安装后重启终端，验证：

```bash
uv --version
```

---

## 第二步：克隆项目

```bash
git clone https://github.com/bytedance/trae-agent.git
cd trae-agent
```

---

## 第三步：安装依赖

```bash
uv sync --all-extras
```

这会自动创建 `.venv` 虚拟环境并安装所有依赖。

---

## 第四步：激活虚拟环境

### macOS / Linux

```bash
source .venv/bin/activate
```

### Windows（PowerShell）

```powershell
.venv\Scripts\Activate.ps1
```

### Windows（CMD）

```cmd
.venv\Scripts\activate.bat
```

验证安装成功：

```bash
trae-cli --version
```

---

## 第五步：创建配置文件

将示例配置复制为正式配置：

```bash
cp trae_config.yaml.example trae_config.yaml
```

Windows CMD：

```cmd
copy trae_config.yaml.example trae_config.yaml
```

然后编辑 `trae_config.yaml`，填入你的模型配置。以下是几种常用配置：

### 方案 A：DeepSeek（推荐，免费额度大，中文友好）

去 https://platform.deepseek.com 注册获取 API key，然后：

```yaml
agents:
  trae_agent:
    enable_lakeview: false
    model: trae_agent_model
    max_steps: 200
    tools:
      - bash
      - str_replace_based_edit_tool
      - sequentialthinking
      - task_done

model_providers:
  deepseek:
    api_key: sk-xxxxxxxxxxxxxxxx    # 替换为你的 key
    provider: openai
    base_url: https://api.deepseek.com/v1

models:
  trae_agent_model:
    model_provider: deepseek
    model: deepseek-chat
    max_tokens: 4096
    temperature: 0.5
```

### 方案 B：Google Gemini（免费额度最多）

去 https://aistudio.google.com 获取 API key，然后：

```yaml
agents:
  trae_agent:
    enable_lakeview: false
    model: trae_agent_model
    max_steps: 200
    tools:
      - bash
      - str_replace_based_edit_tool
      - sequentialthinking
      - task_done

model_providers:
  google:
    api_key: AIzaxxxxxxxxxxxxxxxx   # 替换为你的 key
    provider: google

models:
  trae_agent_model:
    model_provider: google
    model: gemini-2.5-flash
    max_tokens: 8192
    temperature: 0.5
```

### 方案 C：Ollama 本地模型（完全免费，需要显卡）

先安装 Ollama（https://ollama.com），然后拉取模型：

```bash
ollama pull qwen2.5-coder:7b
```

配置：

```yaml
agents:
  trae_agent:
    enable_lakeview: false
    model: trae_agent_model
    max_steps: 200
    tools:
      - bash
      - str_replace_based_edit_tool
      - sequentialthinking
      - task_done

model_providers:
  ollama:
    provider: ollama

models:
  trae_agent_model:
    model_provider: ollama
    model: qwen2.5-coder:7b
    max_tokens: 4096
    temperature: 0.5
```

更多模型配置参见 [model_providers.md](model_providers.md)。

---

## 第六步：运行

### 单次任务模式

```bash
trae-cli run "用 Python 写一个计算斐波那契数列的函数"
```

### 交互模式（多轮对话）

```bash
trae-cli interactive
```

### 查看可用工具

```bash
trae-cli tools
```

### 查看当前配置

```bash
trae-cli show-config
```

---

## 常见问题

### Q: Windows 上 `source` 命令不存在

用 PowerShell 并执行 `.venv\Scripts\Activate.ps1`，或在 CMD 中执行 `.venv\Scripts\activate.bat`。

### Q: `uv sync` 报 Python 版本错误

确保安装了 Python 3.12+：

```bash
python --version
```

如果系统有多个 Python 版本，可指定：

```bash
uv python install 3.12
uv sync --all-extras
```

### Q: 提示 `No module named 'docker'`

正常现象（Docker 是可选依赖），不影响基本使用。如需 Docker 模式：

```bash
uv sync --all-extras --extra evaluation
```

### Q: API 调用报 401 / 认证失败

检查 `trae_config.yaml` 中 `api_key` 是否正确，注意不要有多余空格。

### Q: Windows 上文件编码问题

确保 `trae_config.yaml` 保存为 UTF-8 格式（无 BOM）。

### Q: 网络连接问题（国内环境）

如果无法访问 API，可以：
- 设置代理：在 `.env` 文件中添加 `HTTPS_PROXY=http://127.0.0.1:7890`
- 使用国内可直连的服务：DeepSeek、豆包、硅基流动等

---

## 快速验证

安装完成后，运行以下命令验证一切正常：

```bash
# 激活环境
source .venv/bin/activate        # macOS/Linux
# 或 .venv\Scripts\Activate.ps1  # Windows PowerShell

# 测试 CLI
trae-cli --version
trae-cli show-config

# 运行简单任务测试
trae-cli run "输出 hello world"
```

如果能看到 agent 的输出，说明安装配置成功。

---

## 目录结构说明

```
trae-agent/
├── trae_config.yaml       ← 你的配置文件（git 忽略，不会被提交）
├── trae_config.yaml.example  ← 配置模板
├── trae_agent/            ← 源码
├── docs/                  ← 文档
├── tests/                 ← 测试
├── evaluation/            ← SWE-bench 评估脚本
└── .venv/                 ← 虚拟环境（自动生成）
```
