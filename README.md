# Clawdbot 国内 API 配置指南

本仓库提供 Clawdbot 配置国内版 LLM API 的快速指南，帮助用户将已安装的 Clawdbot 快速切换到国内 API 服务商。

## 支持的 API 服务商

- **MiniMax**: https://api.minimaxi.com/anthropic
- **智谱 AI (GLM)**: https://open.bigmodel.cn/api/coding/paas/v4

## 快速开始

1. 检查 Clawdbot 是否已安装
2. 获取国内 API Key
3. 编辑配置文件 `~/.clawdbot/clawdbot.json`
4. 重启 Gateway
5. 验证配置

## 文档

- [Clawdbot 国内 API 替换配置指南](./CLAWDBOT-CN-API-GUIDE.md)

包含以下内容：
- 前置条件检查
- 备份与修改配置
- MiniMax 配置示例
- 智谱 GLM 配置示例
- 多 API 同时配置
- 重启与验证
- 常见问题解答

## 系统要求

- Clawdbot 已安装 (版本 ≥ 2026.1.24-3)
- Node.js ≥ 22
- 国内 API Key (MiniMax 或智谱)

## 许可证

MIT License - 详见 [LICENSE](./LICENSE) 文件

## 注意事项

- 请妥善保管您的 API Key
- 不要将 API Key 提交到版本控制系统
- 定期检查 API 使用情况以控制成本
