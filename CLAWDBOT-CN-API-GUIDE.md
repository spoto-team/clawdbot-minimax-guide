# Clawdbot 国内 API 替换配置指南

本指南帮助已安装 Clawdbot 的用户快速将 LLM API 替换为国内版本，支持 MiniMax 和智谱（GLM）两个主流国内 API。

## 目录

- [一、前置条件检查](#一前置条件检查)
- [二、备份与修改配置](#二备份与修改配置)
- [三、配置示例](#三配置示例)
- [四、重启与验证](#四重启与验证)
- [五、常见问题](#五常见问题)

---

## 一、前置条件检查

### 1.1 检查 Clawdbot 是否已安装

```bash
# 检查 clawdbot 是否在 PATH 中
which clawdbot

# 检查 clawdbot 是否正在运行
ps aux | grep clawdbot

# 查看已安装版本
clawdbot --version
```

**预期输出示例**：
```
/home/username/.npm-global/bin/clawdbot
spoto      26852  0.0  0.1 1012732 57572 ?       Ssl  13:57   0:00 clawdbot
Clawdbot version: 2026.1.24-3
```

### 1.2 如果未安装 Clawdbot

如果系统未安装 Clawdbot，请使用官方安装脚本：

```bash
# 官方一键安装脚本
curl -fsSL https://molt.bot/install.sh | bash

# 安装完成后运行初始化向导
clawdbot onboard --install-daemon
```

### 1.3 检查 Node.js 版本

```bash
node --version
```

**要求**: Node.js ≥ 22

### 1.4 查找配置文件位置

Clawdbot 的主配置文件位于：

```bash
~/.clawdbot/clawdbot.json
```

查看当前配置：

```bash
cat ~/.clawdbot/clawdbot.json
```

---

## 二、备份与修改配置

### 2.1 备份现有配置

在修改之前，强烈建议备份配置文件：

```bash
cp ~/.clawdbot/clawdbot.json ~/.clawdbot/clawdbot.json.backup
```

### 2.2 获取 API Key

根据你要使用的 API 服务，前往对应平台获取 API Key：

| 服务商 | 控制台地址 | API Key 获取位置 |
|--------|-----------|------------------|
| MiniMax | https://api.minimaxi.com | 控制台 → API Keys |
| 智谱 AI | https://open.bigmodel.cn | 控制台 → API Keys |

### 2.3 编辑配置文件

使用文本编辑器打开配置文件：

```bash
nano ~/.clawdbot/clawdbot.json
# 或
vim ~/.clawdbot/clawdbot.json
```

在配置文件中添加或修改 `models.providers` 部分，具体配置见下一节。

---

## 三、配置示例

### 3.1 MiniMax 国内版配置

MiniMax 国内版 API 配置适用于中国区域用户，提供 M2.1 等高质量模型。

**API 端点**: `https://api.minimaxi.com/anthropic`

**完整配置示例**:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "minimax-cn": {
        "baseUrl": "https://api.minimaxi.com/anthropic",
        "apiKey": "YOUR_MINIMAX_API_KEY_HERE",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "MiniMax-M2.1",
            "name": "MiniMax M2.1 (China)",
            "reasoning": true,
            "input": ["text"],
            "cost": {
              "input": 0.3,
              "output": 1.2,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 204800,
            "maxTokens": 131072
          }
        ]
      }
    }
  }
}
```

**环境变量方式（推荐）**:

在 `~/.bashrc` 或 `~/.zshrc` 中添加：

```bash
export MINIMAX_CN_API_KEY="your_api_key_here"
```

配置文件中使用：

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "minimax-cn": {
        "baseUrl": "https://api.minimaxi.com/anthropic",
        "apiKey": "${MINIMAX_CN_API_KEY}",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "MiniMax-M2.1",
            "name": "MiniMax M2.1 (China)",
            "reasoning": true,
            "input": ["text"],
            "contextWindow": 204800,
            "maxTokens": 131072
          }
        ]
      }
    }
  }
}
```

### 3.2 智谱（GLM）配置

智谱 AI（Zhipu）提供 GLM 系列模型，支持编程和对话任务。

**API 端点**: `https://open.bigmodel.cn/api/coding/paas/v4`

**完整配置示例**:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "zhipu-cn": {
        "baseUrl": "https://open.bigmodel.cn/api/coding/paas/v4",
        "apiKey": "YOUR_ZHIPU_API_KEY_HERE",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "glm-4",
            "name": "GLM-4 (China)",
            "reasoning": true,
            "input": ["text"],
            "cost": {
              "input": 0.1,
              "output": 0.1,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 128000,
            "maxTokens": 8192
          },
          {
            "id": "glm-4-plus",
            "name": "GLM-4 Plus (China)",
            "reasoning": true,
            "input": ["text"],
            "contextWindow": 128000,
            "maxTokens": 16384
          }
        ]
      }
    }
  }
}
```

### 3.3 多 API 同时配置

如果需要同时配置多个国内 API，可以在 `providers` 中添加多个条目：

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "minimax-cn": {
        "baseUrl": "https://api.minimaxi.com/anthropic",
        "apiKey": "YOUR_MINIMAX_API_KEY",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "MiniMax-M2.1",
            "name": "MiniMax M2.1",
            "reasoning": true,
            "input": ["text"],
            "contextWindow": 204800,
            "maxTokens": 131072
          }
        ]
      },
      "zhipu-cn": {
        "baseUrl": "https://open.bigmodel.cn/api/coding/paas/v4",
        "apiKey": "YOUR_ZHIPU_API_KEY",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "glm-4",
            "name": "GLM-4",
            "reasoning": true,
            "input": ["text"],
            "contextWindow": 128000,
            "maxTokens": 8192
          }
        ]
      }
    }
  }
}
```

### 3.4 设置默认模型

在 `agents.defaults.model.primary` 中设置默认使用的模型：

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "minimax-cn/MiniMax-M2.1"
      },
      "models": {
        "minimax-cn/MiniMax-M2.1": {}
      }
    }
  },
  "models": {
    "mode": "merge",
    "providers": {
      "minimax-cn": {
        "baseUrl": "https://api.minimaxi.com/anthropic",
        "apiKey": "YOUR_MINIMAX_API_KEY",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "MiniMax-M2.1",
            "name": "MiniMax M2.1",
            "reasoning": true,
            "input": ["text"],
            "contextWindow": 204800,
            "maxTokens": 131072
          }
        ]
      }
    }
  }
}
```

---

## 四、重启与验证

### 4.1 重启 Gateway

配置修改后，需要重启 Gateway 使配置生效：

```bash
clawdbot gateway restart
```

**预期输出**：

```
Restarted systemd service: clawdbot-gateway.service
```

### 4.2 验证模型列表

检查配置是否成功加载：

```bash
clawdbot models list
```

**MiniMax 配置成功输出示例**：

```
Model                                      Input      Ctx      Local Auth  Tags
minimax-cn/MiniMax-M2.1                    text       200k     no    yes   default,configured
```

**智谱配置成功输出示例**：

```
Model                                      Input      Ctx      Local Auth  Tags
zhipu-cn/glm-4                             text       128k     no    yes   configured
```

### 4.3 检查 Gateway 状态

```bash
clawdbot gateway status
```

确认 Gateway 正常运行（状态为 running 或 active）。

### 4.4 测试模型响应

```bash
clawdbot agent --message "Hello, are you working?" --session-id main
```

**预期**: 模型应返回正常的回复，说明 API 配置成功。

### 4.5 验证 API Key 有效性（可选）

使用 curl 命令直接测试 API 连接：

**MiniMax**:

```bash
curl -X POST "https://api.minimaxi.com/anthropic/v1/messages" \
  -H "Authorization: Bearer YOUR_MINIMAX_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "MiniMax-M2.1",
    "max_tokens": 1024,
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

**智谱**:

```bash
curl -X POST "https://openbigmodel.cn/api/coding/paas/v4/chat/completions" \
  -H "Authorization: Bearer YOUR_ZHIPU_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "glm-4",
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

**成功响应**: 返回包含模型回复的 JSON 数据
**失败响应**: 返回错误信息，如认证失败

---

## 五、常见问题

### Q1: MissingEnvVarError - 缺少环境变量

**错误信息**:

```
MissingEnvVarError: Missing env var "MINIMAX_CN_API_KEY"
```

**解决方案**:

1. 检查环境变量是否已设置：
   ```bash
   echo $MINIMAX_CN_API_KEY
   ```

2. 如果为空，在 `~/.bashrc` 或 `~/.zshrc` 中添加：
   ```bash
   export MINIMAX_CN_API_KEY="your_api_key"
   ```

3. 使配置生效：
   ```bash
   source ~/.bashrc
   # 或
   source ~/.zshrc
   ```

4. 或直接在配置文件中使用 API key 值（安全性较低）

### Q2: Gateway 启动失败

**错误信息**: 配置文件加载失败或 Gateway 无法启动

**解决方案**:

1. 检查配置文件语法：
   ```bash
   cat ~/.clawdbot/clawdbot.json | jq
   ```

2. 检查日志：
   ```bash
   tail -f ~/.clawdbot/logs/gateway.log
   cat ~/.clawdbot/logs/gateway.err.log
   ```

3. 使用备份恢复配置：
   ```bash
   cp ~/.clawdbot/clawdbot.json.backup ~/.clawdbot/clawdbot.json
   ```

4. 重启 Gateway：
   ```bash
   clawdbot gateway stop
   clawdbot gateway start
   ```

### Q3: 模型未显示在列表中

**问题**: 运行 `clawdbot models list` 但看不到配置的模型

**解决方案**:

1. 检查 JSON 语法是否正确
2. 确认 provider 名称与模型引用一致
3. 确认 API key 有效且有访问权限
4. 重启 Gateway：
   ```bash
   clawdbot gateway restart
   ```

### Q4: 认证失败 (HTTP 401)

**错误信息**:

```
HTTP 401 authentication_error: login fail
```

**解决方案**:

1. 验证 API key 格式是否正确
2. 确认 API key 未过期
3. 检查 API key 是否有访问所用 API 的权限
4. 重新生成 API key（如有必要）

### Q5: 配置文件路径错误

**问题**: 找不到配置文件或配置未生效

**解决方案**:

1. 确认配置文件位置：
   ```bash
   ls -la ~/.clawdbot/
   ```

2. 如果配置文件不存在，运行初始化向导：
   ```bash
   clawdbot onboard
   ```

3. 手动创建配置文件：
   ```bash
   mkdir -p ~/.clawdbot
   nano ~/.clawdbot/clawdbot.json
   ```

### Q6: 多个 Provider 如何切换使用

在对话中使用 `/model` 命令切换模型：

```bash
/model minimax-cn/MiniMax-M2.1
# 或
/model zhipu-cn/glm-4
```

或在配置文件中设置默认模型：

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "zhipu-cn/glm-4"
      }
    }
  }
}
```

---

## 附录

### 配置文件参数说明

| 参数 | 说明 | 示例值 |
|------|------|--------|
| `mode` | provider 合并模式 | `merge` 或 `replace` |
| `baseUrl` | API 基础 URL | `https://api.minimaxi.com/anthropic` |
| `apiKey` | API 密钥 | `sk-xxx...` 或 `${ENV_VAR}` |
| `api` | API 模式 | `anthropic-messages` |
| `models[].id` | 模型唯一标识符 | `MiniMax-M2.1` |
| `models[].name` | 模型显示名称 | `MiniMax M2.1` |
| `models[].reasoning` | 是否支持推理 | `true` 或 `false` |
| `models[].contextWindow` | 上下文窗口大小 | `204800` |
| `models[].maxTokens` | 最大输出 tokens | `131072` |

### 支持的模型列表

| 服务商 | 模型 ID | 上下文窗口 | 特性 |
|--------|---------|-----------|------|
| MiniMax | MiniMax-M2.1 | 200k | 支持推理 |
| 智谱 | glm-4 | 128k | 支持推理 |
| 智谱 | glm-4-plus | 128k | 增强版 |

### 相关链接

- [Clawdbot 官方文档](https://docs.clawd.bot)
- [MiniMax API 文档](https://api.minimaxi.com)
- [智谱 AI 开放平台](https://open.bigmodel.cn)
- [Clawdbot GitHub](https://github.com/clawdbot/clawdbot)

---

## 版本信息

- 文档版本: 1.0
- 创建日期: 2026-01-29
- 支持的 Clawdbot 版本: 2026.1.24-3+
- 支持的 API: MiniMax 国内版、智谱 GLM

**注意**: 请妥善保管您的 API Key，不要将其提交到版本控制系统或公开分享。
