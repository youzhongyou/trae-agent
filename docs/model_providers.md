# Model Providers Guide

Trae Agent 支持多种 LLM 提供商。本文档列出所有接入方式及配置示例。

所有配置均写在 `trae_config.yaml` 中。通用结构：

```yaml
agents:
  trae_agent:
    enable_lakeview: true
    model: trae_agent_model
    max_steps: 200
    tools:
      - bash
      - str_replace_based_edit_tool
      - sequentialthinking
      - task_done

model_providers:
  <provider_name>:
    api_key: <your_api_key>
    provider: <provider_type>
    base_url: <optional_base_url>

models:
  trae_agent_model:
    model_provider: <provider_name>
    model: <model_id>
    max_tokens: 4096
    temperature: 0.5
```

---

## 1. Anthropic（原生支持）

```yaml
model_providers:
  anthropic:
    api_key: your_anthropic_api_key
    provider: anthropic

models:
  trae_agent_model:
    model_provider: anthropic
    model: claude-4-sonnet          # 或 claude-4-opus, claude-3.5-sonnet
    max_tokens: 4096
    temperature: 0.5
```

- 注册：https://console.anthropic.com
- 付费，编程能力顶级

---

## 2. OpenAI（原生支持）

```yaml
model_providers:
  openai:
    api_key: your_openai_api_key
    provider: openai

models:
  trae_agent_model:
    model_provider: openai
    model: gpt-4o                   # 或 gpt-4o-mini, o1, o3-mini
    max_tokens: 4096
    temperature: 0.5
```

- 注册：https://platform.openai.com
- 付费，新用户有少量免费额度

---

## 3. Google Gemini（原生支持）

```yaml
model_providers:
  google:
    api_key: your_google_api_key
    provider: google

models:
  trae_agent_model:
    model_provider: google
    model: gemini-2.5-flash         # 或 gemini-2.5-pro, gemini-2.0-flash
    max_tokens: 8192
    temperature: 0.5
```

- 注册：https://aistudio.google.com
- **免费额度最大**（15 RPM，每天 1500 请求），编程能力强

---

## 4. Azure OpenAI（原生支持）

```yaml
model_providers:
  azure:
    api_key: your_azure_api_key
    provider: azure
    base_url: https://your-resource.openai.azure.com/

models:
  trae_agent_model:
    model_provider: azure
    model: your-deployment-name
    max_tokens: 4096
    temperature: 0.5
```

- 注册：https://portal.azure.com
- 企业级，需要先创建 Azure OpenAI 资源和部署

---

## 5. DeepSeek（OpenAI 兼容）

```yaml
model_providers:
  deepseek:
    api_key: your_deepseek_api_key
    provider: openai
    base_url: https://api.deepseek.com/v1

models:
  trae_agent_model:
    model_provider: deepseek
    model: deepseek-chat            # DeepSeek-V3
    max_tokens: 4096
    temperature: 0.5
```

可选模型：
- `deepseek-chat` — DeepSeek-V3，通用/编程
- `deepseek-reasoner` — DeepSeek-R1，推理增强

- 注册：https://platform.deepseek.com
- 新用户有免费额度（约 500 万 tokens）

---

## 6. Groq（OpenAI 兼容，超快推理）

```yaml
model_providers:
  groq:
    api_key: your_groq_api_key
    provider: openai
    base_url: https://api.groq.com/openai/v1

models:
  trae_agent_model:
    model_provider: groq
    model: llama-3.3-70b-versatile  # 或 mixtral-8x7b-32768
    max_tokens: 4096
    temperature: 0.5
```

- 注册：https://console.groq.com
- 免费额度（6000 tokens/min），推理速度极快

---

## 7. OpenRouter（聚合平台）

```yaml
model_providers:
  openrouter:
    api_key: your_openrouter_api_key
    provider: openai
    base_url: https://openrouter.ai/api/v1

models:
  trae_agent_model:
    model_provider: openrouter
    model: meta-llama/llama-3.3-70b-instruct:free
    max_tokens: 4096
    temperature: 0.5
```

免费模型（模型名带 `:free` 后缀）：
- `meta-llama/llama-3.3-70b-instruct:free`
- `google/gemma-2-9b-it:free`
- `mistralai/mistral-7b-instruct:free`

付费模型可通过 OpenRouter 访问所有主流模型。

- 注册：https://openrouter.ai

---

## 8. Mistral（OpenAI 兼容）

```yaml
model_providers:
  mistral:
    api_key: your_mistral_api_key
    provider: openai
    base_url: https://api.mistral.ai/v1

models:
  trae_agent_model:
    model_provider: mistral
    model: codestral-latest         # 编程专用，或 mistral-large-latest
    max_tokens: 4096
    temperature: 0.5
```

- 注册：https://console.mistral.ai
- 有免费试用额度，Codestral 专为编程优化

---

## 9. 豆包 Doubao（原生支持，字节跳动）

```yaml
model_providers:
  doubao:
    api_key: your_doubao_api_key
    provider: doubao
    base_url: https://ark.cn-beijing.volces.com/api/v3

models:
  trae_agent_model:
    model_provider: doubao
    model: your-endpoint-id
    max_tokens: 4096
    temperature: 0.5
```

- 注册：https://www.volcengine.com/product/doubao
- 需要在火山引擎创建推理端点

---

## 10. Ollama（本地部署，原生支持）

```yaml
model_providers:
  ollama:
    provider: ollama

models:
  trae_agent_model:
    model_provider: ollama
    model: qwen2.5-coder:32b
    max_tokens: 4096
    temperature: 0.5
```

安装：https://ollama.com

推荐编程模型：

| 模型 | 显存需求 | 编程能力 |
|------|---------|---------|
| `qwen2.5-coder:7b` | ~6GB | 不错 |
| `qwen2.5-coder:14b` | ~10GB | 强 |
| `qwen2.5-coder:32b` | ~20GB | 很强 |
| `deepseek-coder-v2:16b` | ~10GB | 强 |
| `llama3.3:70b` | ~40GB | 很强 |
| `codellama:34b` | ~20GB | 不错 |

先拉取模型再使用：
```bash
ollama pull qwen2.5-coder:32b
```

---

## 11. 其他 OpenAI 兼容服务

任何兼容 OpenAI API 格式的服务都可以通过设置 `provider: openai` + `base_url` 接入：

```yaml
model_providers:
  custom:
    api_key: your_api_key
    provider: openai
    base_url: https://your-service.com/v1

models:
  trae_agent_model:
    model_provider: custom
    model: your-model-name
    max_tokens: 4096
    temperature: 0.5
```

适用于：
- vLLM 自部署
- LiteLLM 代理
- 硅基流动 (SiliconFlow)
- 零一万物 (01.ai)
- 智谱 AI (Zhipu)
- 阿里云百炼
- 其他兼容 OpenAI 格式的国产模型服务

---

## 免费方案推荐排序

| 排名 | 方案 | 优势 |
|------|------|------|
| 1 | Google Gemini 2.5 Flash | 免费额度最多，原生支持，编程能力强 |
| 2 | DeepSeek-V3 | 免费额度够用，中文好，性价比极高 |
| 3 | Groq + Llama 3.3 70B | 速度最快，体验流畅 |
| 4 | OpenRouter 免费模型 | 无需注册多个平台 |
| 5 | Ollama 本地部署 | 无限免费，完全隐私，需要好显卡 |

---

## 环境变量方式

除了写在配置文件中，API key 也可通过环境变量设置（在 `.env` 文件或 shell 中）：

```bash
export OPENAI_API_KEY="sk-..."
export ANTHROPIC_API_KEY="sk-ant-..."
export GOOGLE_API_KEY="AI..."
export DEEPSEEK_API_KEY="sk-..."
```

具体支持哪些环境变量名取决于各 client 实现，建议优先使用 `trae_config.yaml` 配置方式。
