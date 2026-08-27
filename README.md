# AE Text Translator — AE 批量文本翻译脚本

[English](README.en.md) · **v2.0.17** · MIT License

一个 Adobe After Effects 批量文本翻译脚本：选中若干图层，输入目标语言，脚本自动复制图层并调用 DeepSeek（或任意 OpenAI / Anthropic 兼容 API）逐层翻译文字，支持父子级 / 遮罩关系修复、自动预合成。

## ✨ 功能特性

- **批量翻译**：一次选中多个图层 → 复制 → 逐语言翻译 → 可选自动预合成（以语种名称命名，如「英语」「English」）
- **结构修复**：父子级自动重映射；遮罩完整性检查（漏选遮罩层会提前警告）
- **任意 OpenAI 兼容接口**：默认 DeepSeek V4（`deepseek-v4-flash` / `deepseek-v4-pro`），可自定义 API 地址与模型名
- **安全设计**：API Key 运行时输入，仅保存在 AE 自身设置中——**脚本文件里没有任何 Key**
- **中 / 英双语界面**：一键切换
- **按行翻译模式**：保持行数，行数不匹配自动逐行重试
- **可靠**：失败自动重试（次数 / 间隔可配置）、实时进度显示、设置中可导出 CSV 翻译日志留档（处理期间界面冻结，请等待完成）
- **贴心细节**：查重（同名代码图层询问跳过）、字符量预估确认、设置自动记忆（兼容迁移 v1 的记忆）

## 🔒 安全说明（重要）

- 脚本不包含、也不需要你把 API Key 写进文件。
- Key 在设置中只显示**掩码**（开头 4 位 + ⚫ + 结尾 4 位，如 `sk-12••••56d1`），完整 Key 仅在点「修改」输入时可见。
- Key 以 **XOR+Base64 混淆**后保存于 AE 设置（`app.settings`）。注意：ExtendScript 没有加密库，这是「防随手可见」的混淆而非银行级加密——不要因此放松对 prefs 文件的保管。
- 建议为脚本**单独申请一个 API Key** 并设置用量上限；不要在公共电脑上使用。
- 如果你使用过 **v1.0 及更早版本**：旧脚本曾把 Key 明文写在代码里——请前往 DeepSeek 控制台作废旧 Key 并重新签发。

## 📦 安装

1. 将 `AE-Text-Translator.jsxbin` 放入 AE 的 ScriptUI Panels 目录：

   ```
   C:\Program Files\Adobe\Adobe After Effects <版本>\Support Files\Scripts\ScriptUI Panels\
   ```

2. 在 AE 中：
   - **文件 → 脚本 → 运行脚本文件…** 选择该文件（一次性运行）；或
   - **窗口 → 扩展** 打开该面板（常驻使用，推荐）。
3. 首次使用：点击右上角「设置」按钮，填入 API Key，填写/选择模型，点「测试连接」验证。

> 本仓库发布的是 **`.jsxbin`（ExtendScript 二进制编码）** 版本，方便直接使用、防止源码被随手阅读/修改。源码 `.jsx` 未随仓库发布，需要开发/修改请与我们联系。

## ✨ 界面布局

主界面保持精简：目标语言输入 + 三个选项 + 开始处理。API 地址、Key、模型、请求参数、提示词、界面语言等全部收纳在右上角的「设置」按钮中（弹出对话框），不占用主界面空间。

## 🚀 快速开始

1. 打开合成，选中要翻译的图层（文本图层；可同时选中父层与子层）。
2. 输入目标语言，例如：`繁体中文, English, EN`——逗号 / 顿号分隔，支持：
   - 中文名：`英语`、`法语`
   - 英文名：`English`、`French`
   - 语言代码：`EN`、`cn`（大小写不敏感）
3. 点击「开始处理」。
4. 每个目标语言会生成一批副本图层（紫色标记）；开启「自动预合成」时，每个语言会打包成一个以语种名称命名的合成（如「英语」「English」）。

## ⚙️ 设置（右上角「设置」按钮）

| 设置 | 说明 |
| --- | --- |
| API 协议 | 两种常用协议（写死）：**OpenAI 兼容**（`Bearer` 认证，`/chat/completions`）与 **Anthropic 兼容**（`x-api-key` 认证，`/v1/messages`，DeepSeek 官方 `/anthropic` 端点） |
| API 地址 | 随协议自动填默认值（DeepSeek：OpenAI 协议 `https://api.deepseek.com`、Anthropic 协议 `https://api.deepseek.com/anthropic`）；也可填 OpenAI、Moonshot、智谱 GLM、Ollama 等 |
| API Key | 直接编辑 / 清除；仅保存在 AE 设置中 |
| 模型 | 纯文本自由填写（无预设下拉）：DeepSeek 用 `deepseek-v4-flash` / `deepseek-v4-pro`，其他服务商按需填写；点「获取模型」可查询 API 支持的模型列表并下拉选择 |
| 禁用思考模式 | V4 默认开启思考，翻译场景建议禁用（脚本默认勾选，附 `thinking:{"type":"disabled"}`） |
| 超时 / 重试 / 间隔 | 单请求超时（秒）、失败重试次数、请求间隔（毫秒，防止触发限流） |
| 目标语言 | 内置 44 种常见语言字典，输入中文名 / 英文名 / 语言代码均可识别 |
| 界面语言 | 设置对话框中切换 中文 / English |
| 提示词 | 默认强约束提示词（只返回译文、不加解释）；按行翻译模式使用独立默认提示词 |

### 支持的服务商示例

| 服务商 | API 协议 | API 地址 | 模型示例 |
| --- | --- | --- | --- |
| DeepSeek | OpenAI / Anthropic | `https://api.deepseek.com` / `…/anthropic` | `deepseek-v4-flash`, `deepseek-v4-pro` |
| OpenAI | OpenAI | `https://api.openai.com/v1` | `gpt-4o-mini`, `gpt-4.1-mini` |
| Moonshot (Kimi) | OpenAI | `https://api.moonshot.cn/v1` | `kimi-latest` |
| 智谱 GLM | OpenAI | `https://open.bigmodel.cn/api/paas/v4` | `glm-4-flash` |
| Ollama（本地） | OpenAI | `http://localhost:11434/v1` | `qwen2.5:7b` 等 |

> 模型名兼容性以各服务商官方文档为准；DeepSeek 的 `deepseek-chat` / `deepseek-reasoner` 旧别名已于 2026-07-24 退役，请使用 V4 模型名。

## 💰 费用提醒

DeepSeek API 按 token 计费。V4 系列自 2026-08-16 起实行**高峰 / 低峰时段计价**（高峰价格为低峰的 2 倍），具体见[官方定价页](https://api-docs.deepseek.com/quick_start/pricing)。脚本会在预计字符数较大时弹出确认框，翻译前请先估算成本。

## ❓ 常见问题

| 现象 | 处理 |
| --- | --- |
| 请求超时 / 网络错误 | 检查网络与代理；在高级设置中调大「超时」 |
| HTTP 401 | API Key 无效或已过期，重新设置 Key |
| HTTP 429 | 触发限流；调大「间隔」与「重试次数」 |
| 行数不匹配 | 脚本自动按行逐行重试；仍失败会在日志中标注 |
| 翻译质量不佳 | 调整「提示词」约束；长文本建议开启「按行翻译」 |
| 遮罩警告 | 请把遮罩层（目标图层上面一层）一起选中 |
| 想撤销 | 全程单个 Undo 组，一次 Ctrl+Z 还原所有语言副本 |

> 本仓库仅发布二进制脚本，不包含可测试的源码，故未附带单元测试。

## 📄 License

MIT © 2026 gjade3702-cell
