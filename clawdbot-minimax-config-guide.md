# Clawdbot 国内版 MiniMax M2.1 API 配置指南

本文档详细记录了如何在 Clawdbot 中配置国内版 MiniMax M2.1 API 的完整流程。

## 前置要求

- Node.js ≥ 22
- 已获取 MiniMax 国内版 API Key
- macOS、Linux 或 Windows (WSL2) 系统

## 安装步骤

### 1. 清理旧版本（如需重新安装）

```bash
# 卸载全局安装的 clawdbot
npm uninstall -g clawdbot

# 清理 npm 缓存
npm cache clean --force
```

### 2. 全局安装 Clawdbot

```bash
# 使用 npm 安装
npm install -g clawdbot@latest

# 或使用 pnpm
pnpm add -g clawdbot@latest
```

### 3. 运行初始化向导

```bash
# 运行 onboarding 向导，安装守护进程
clawdbot onboard --install-daemon
```

按照向导提示完成初始配置：
- 选择安全选项（理解风险）
- 配置工作区目录
- 设置 gateway 认证方式
- 其他基本配置

## 配置国内版 MiniMax API

### 4. 获取 API 信息

- **API Key**: 从 MiniMax 控制台获取
- **API 端点**: `https://api.minimaxi.com/anthropic`
- **模型 ID**: `MiniMax-M2.1`

### 5. 配置环境变量（推荐）

在 `~/.zshrc` 或 `~/.bashrc` 中添加：

```bash
export MINIMAX_CN_API_KEY="你的_API_KEY_替换此处"
```

使环境变量生效：

```bash
# 重新加载配置文件
source ~/.zshrc
# 或
source ~/.bashrc
```

### 6. 编辑主配置文件

配置文件位置：`~/.clawdbot/clawdbot.json`

添加或修改以下配置：

```json
{
  "env": {
    "MINIMAX_CN_API_KEY": "你的_API_KEY_替换此处"
  },
  "agents": {
    "defaults": {
      "workspace": "/Users/你的用户名/clawd",
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
        "apiKey": "你的_API_KEY_替换此处",
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

**配置说明**：

- `env.MINIMAX_CN_API_KEY`: 在配置文件中直接设置环境变量
- `agents.defaults.model.primary`: 设置默认模型为 `minimax-cn/MiniMax-M2.1`
- `models.mode`: 设置为 `merge`，合并内置和自定义 provider
- `models.providers.minimax-cn`: 定义国内版 MiniMax provider
  - `baseUrl`: 国内版 API 端点
  - `apiKey`: API 密钥（可直接使用值或引用环境变量）
  - `api`: 使用 Anthropic 兼容模式
  - `models`: 定义支持的模型列表

### 7. 重启 Gateway

```bash
# 重启 gateway 使配置生效
clawdbot gateway restart
```

### 8. 验证配置

#### 8.1 检查模型列表

```bash
clawdbot models list
```

预期输出应包含：

```
Model                                      Input      Ctx      Local Auth  Tags
minimax-cn/MiniMax-M2.1                    text       200k     no    yes   default,configured
```

#### 8.2 检查 Gateway 状态

```bash
clawdbot gateway status
```

确认 Gateway 正常运行。

#### 8.3 测试模型响应

```bash
# 发送测试消息
clawdbot agent --message "Hello, are you working?" --session-id main
```

预期：模型应返回正常回复。

## 配置说明

### Provider 配置结构

```json
{
  "models": {
    "mode": "merge",  // 或 "replace"，合并或替换内置 providers
    "providers": {
      "minimax-cn": {  // Provider 标识符
        "baseUrl": "https://api.minimaxi.com/anthropic",
        "apiKey": "你的_API_KEY",  // 或 "${MINIMAX_CN_API_KEY}"
        "api": "anthropic-messages",  // API 模式
        "models": [  // 支持的模型列表
          {
            "id": "MiniMax-M2.1",
            "name": "MiniMax M2.1 (China)",
            "reasoning": true,
            "input": ["text"],
            "cost": { "input": 0.3, "output": 1.2, "cacheRead": 0, "cacheWrite": 0 },
            "contextWindow": 204800,
            "maxTokens": 131072
          }
        ]
      }
    }
  }
}
```

### 关键参数说明

| 参数 | 说明 |
|------|------|
| `mode` | `merge` - 合并内置和自定义 provider<br>`replace` - 完全替换内置 provider |
| `baseUrl` | API 基础 URL，国内版为 `https://api.minimaxi.com/anthropic` |
| `apiKey` | API 密钥，可使用环境变量引用 `${VARIABLE_NAME}` |
| `api` | API 模式，MiniMax 支持 `anthropic-messages` 和 `openai-completions` |
| `models.id` | 模型唯一标识符 |
| `models.name` | 模型显示名称 |
| `models.reasoning` | 是否支持推理能力 |
| `models.input` | 支持的输入类型 |
| `models.cost` | 模型成本信息（可选） |
| `models.contextWindow` | 上下文窗口大小 |
| `models.maxTokens` | 最大输出 tokens |

### 国内版 vs 国际版

| 特性 | 国内版 (minimax-cn) | 国际版 (minimax) |
|------|---------------------|------------------|
| 域名 | `api.minimaxi.com` | `api.minimax.io` |
| 环境变量 | `MINIMAX_CN_API_KEY` | `MINIMAX_API_KEY` |
| Provider 名称 | `minimax-cn` | `minimax` |
| 模型引用 | `minimax-cn/MiniMax-M2.1` | `minimax/MiniMax-M2.1` |

## 故障排除

### 问题 1: MissingEnvVarError

**错误信息**：
```
MissingEnvVarError: Missing env var "MINIMAX_CN_API_KEY"
```

**解决方案**：

1. 确保环境变量已设置：
```bash
echo $MINIMAX_CN_API_KEY
```

2. 或在配置文件中直接使用 API key 值：
```json
"apiKey": "你的_API_KEY_替换此处"
```

### 问题 2: Gateway 启动失败

**检查日志**：
```bash
# 查看 gateway 日志
tail -f /tmp/clawdbot/clawdbot-$(date +%Y-%m-%d).log

# 或查看 launchd 日志
cat ~/.clawdbot/logs/gateway.log
cat ~/.clawdbot/logs/gateway.err.log
```

**重启 Gateway**：
```bash
clawdbot gateway stop
clawdbot gateway start
```

### 问题 3: 模型未显示

**验证配置**：
```bash
# 检查配置文件语法
cat ~/.clawdbot/clawdbot.json | jq

# 验证模型列表
clawdbot models list
```

**修复步骤**：
1. 检查 JSON 语法是否正确
2. 确认 provider 名称与模型引用一致
3. 重启 gateway

### 问题 4: 认证失败

**验证 API Key**：
```bash
# 使用 curl 测试 API 连接
curl -X POST "https://api.minimaxi.com/anthropic/v1/messages" \
  -H "Authorization: Bearer 你的_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "MiniMax-M2.1",
    "max_tokens": 1024,
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

**检查配置**：
- API key 格式是否正确
- 是否使用了正确的环境变量
- 配置文件中的 API key 是否有效

## 高级配置

### 多模型配置

如需配置多个 MiniMax 模型：

```json
{
  "models": {
    "providers": {
      "minimax-cn": {
        "models": [
          {
            "id": "MiniMax-M2.1",
            "name": "MiniMax M2.1 (China)",
            "reasoning": true,
            "input": ["text"],
            "contextWindow": 204800,
            "maxTokens": 131072
          },
          {
            "id": "abab6.5s-chat",
            "name": "MiniMax abab6.5s (China)",
            "reasoning": false,
            "input": ["text"],
            "contextWindow": 32768,
            "maxTokens": 8192
          }
        ]
      }
    }
  }
}
```

### 会话级别模型配置

在特定会话中使用不同模型：

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "minimax-cn/MiniMax-M2.1"
      },
      "models": {
        "minimax-cn/MiniMax-M2.1": {},
        "minimax-cn/abab6.5s-chat": {
          "alias": "MiniMax abab6.5s"
        }
      }
    }
  }
}
```

在聊天中切换模型：
```
/model minimax-cn/abab6.5s-chat
```

### 成本控制配置

```json
{
  "models": {
    "providers": {
      "minimax-cn": {
        "models": [
          {
            "id": "MiniMax-M2.1",
            "cost": {
              "input": 0.3,      // 输入价格（元/百万 tokens）
              "output": 1.2,     // 输出价格（元/百万 tokens）
              "cacheRead": 0,    // 缓存读取价格
              "cacheWrite": 0    // 缓存写入价格
            }
          }
        ]
      }
    }
  }
}
```

## 维护与更新

### 更新 Clawdbot

```bash
# 更新到最新版本
npm update -g clawdbot

# 或重新安装
npm install -g clawdbot@latest

# 重启 gateway
clawdbot gateway restart
```

### 检查配置

```bash
# 运行诊断工具
clawdbot doctor

# 修复常见问题
clawdbot doctor --repair
```

### 查看 Gateway 日志

```bash
# 实时查看日志
tail -f /tmp/clawdbot/clawdbot-$(date +%Y-%m-%d).log

# 查看特定时间的日志
grep "ERROR" /tmp/clawdbot/clawdbot-2026-01-26.log
```

## 安全建议

1. **保护 API Key**
   - 不要将 API key 提交到版本控制系统
   - 使用 `.env` 文件或系统环境变量存储密钥
   - 定期轮换 API key

2. **限制访问**
   - Gateway 默认绑定到 loopback (127.0.0.1)
   - 如需远程访问，使用 Tailscale 或 SSH 隧道
   - 启用认证机制（token 或 password）

3. **监控使用**
   - 定期检查 API 使用情况
   - 设置使用限额
   - 监控异常活动

## 参考资源

- [Clawdbot 官方文档](https://docs.clawd.bot)
- [MiniMax API 文档](https://api.minimaxi.com)
- [Clawdbot GitHub 仓库](https://github.com/clawdbot/clawdbot)
- [模型配置参考](https://docs.clawd.bot/gateway/configuration)
- [Provider 配置文档](https://docs.clawd.bot/providers)

## 版本信息

- Clawdbot 版本: 2026.1.24-3
- Node.js 版本: ≥ 22
- MiniMax 模型: M2.1
- 文档日期: 2026-01-26
