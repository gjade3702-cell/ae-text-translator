# AE Text Translator — AE Batch Text Translator

[中文](README.md) · **v2.0.17** · MIT License

An Adobe After Effects script that batch-translates text layers. Select layers, enter target languages, and the script duplicates the selection, translates every text layer via DeepSeek (or any OpenAI / Anthropic-compatible API), and repairs parenting / track-matte relationships. Optionally auto-precomposes each language into a comp named by the language name.

## ✨ Features

- **Batch translation**: select multiple layers → duplicate per language → translate → optional auto-precompose (named by the language name, e.g. English / 英语)
- **Structure repair**: automatic parenting remap; track-matte completeness check (warns when a matte layer is not selected)
- **Any OpenAI-compatible endpoint**: DeepSeek V4 by default (`deepseek-v4-flash` / `deepseek-v4-pro`); custom base URL and model supported
- **Secure by design**: API key is entered at runtime and stored only in AE settings — **no key in the script file**
- **Bilingual UI**: Chinese / English, one-click toggle
- **Line-by-line mode**: keeps the line count; falls back to per-line retry on mismatch
- **Reliability**: configurable retries with backoff, live progress display, CSV log export in Settings (the UI freezes while processing — please wait)
- **Nice touches**: duplicate-check (asks before overwriting an existing language comp), character-count estimate confirmation, settings remembered (migrates v1 memory)

## 🔒 Security Notes (Important)

- The script does not contain and never requires an API key inside the file.
- The key is shown **masked** in Settings (first 4 chars + ⚫ + last 4 chars, e.g. `sk-12••••56d1`); the full key is visible only while editing it.
- The key is stored **obfuscated (XOR + Base64)** in AE settings (`app.settings`). Note: ExtendScript has no crypto library — this is "not visible at a glance", not bank-grade encryption.
- Recommendation: create a **dedicated API key** with a spending limit; do not use on shared machines.
- If you used **v1.0 or earlier**: the old script hard-coded a key in plaintext — revoke that key at the DeepSeek console and issue a new one.

## 📦 Installation

1. Put `AE-Text-Translator.jsxbin` into AE's ScriptUI Panels folder:

   ```
   C:\Program Files\Adobe\Adobe After Effects <version>\Support Files\Scripts\ScriptUI Panels\
   ```

2. In AE:
   - **File → Scripts → Run Script File…** and pick the file (one-off), or
   - Open it under **Window → Extensions** to use as a dockable panel (recommended).
3. First run: click the **Settings** button (top-right), enter your API Key, pick a model preset (or type a model name), and click "Test connection".

## ✨ Layout

The main panel stays compact: target-language input, three options, and the Start button. API URL, key, model, request parameters, prompt and UI language all live in the Settings dialog — nothing crowds the main panel.

## 🚀 Quick Start

1. Open a composition and select the layers to translate (text layers; parent + children can be selected together).
2. Enter target languages, e.g. `English, 法语, EN` — four forms are supported, separated by commas or `、`:
   - Chinese name: `英语`, `法语`
   - English name: `English`, `French`
   - Language code: `EN`, `cn` (case-insensitive)
   - Custom (outside the dictionary): `Quechua=QU` (name=code)
3. Click **Start**.
4. Each language produces a batch of duplicated layers (purple label); with "Auto precompose" enabled, each language is packed into a comp named by the language name (e.g. English / 英语).

## ⚙️ Settings (top-right button)

| Setting | Description |
| --- | --- |
| API Protocol | Two built-in protocols: **OpenAI compatible** (`Bearer` auth, `/chat/completions`) and **Anthropic compatible** (`x-api-key` auth, `/v1/messages`, DeepSeek official `/anthropic` endpoint) |
| Base URL | Auto-filled per protocol (DeepSeek: `https://api.deepseek.com` for OpenAI, `https://api.deepseek.com/anthropic` for Anthropic); any provider works |
| API Key | Edit / clear directly; stored in AE settings only |
| Model | Free-text model name (no preset dropdown): `deepseek-v4-flash` / `deepseek-v4-pro` for DeepSeek, others as needed; "Fetch models" queries the API's model list and lets you pick |
| Disable thinking | V4 defaults to thinking on; disable it for translation (default on, sends `thinking:{"type":"disabled"}`) |
| Timeout / Retries / Delay | Per-request timeout (s), retry count, delay between requests (ms, to avoid rate limits) |
| Target languages | 44 common languages built in; enter a Chinese name, English name, or language code |
| UI language | Switch 中文 / English in the Settings dialog |
| Prompt | Strongly constrained by default (translated text only); line mode uses its own default prompt |

### Provider examples

| Provider | API Protocol | Base URL | Model examples |
| --- | --- | --- | --- |
| DeepSeek | OpenAI / Anthropic | `https://api.deepseek.com` / `…/anthropic` | `deepseek-v4-flash`, `deepseek-v4-pro` |
| OpenAI | OpenAI | `https://api.openai.com/v1` | `gpt-4o-mini`, `gpt-4.1-mini` |
| Moonshot (Kimi) | OpenAI | `https://api.moonshot.cn/v1` | `kimi-latest` |
| Zhipu GLM | OpenAI | `https://open.bigmodel.cn/api/paas/v4` | `glm-4-flash` |
| Ollama (local) | OpenAI | `http://localhost:11434/v1` | `qwen2.5:7b` etc. |

> Model availability depends on each provider. Note: DeepSeek's legacy aliases `deepseek-chat` / `deepseek-reasoner` were retired on 2026-07-24 — use the V4 model names.

## 💰 Cost Notice

DeepSeek bills per token. Since 2026-08-16, V4 models use peak/off-peak pricing (peak = 2× off-peak) — see the [official pricing page](https://api-docs.deepseek.com/quick_start/pricing). The script confirms when the estimated character count is large, so estimate costs before translating.

## ❓ FAQ

| Symptom | Fix |
| --- | --- |
| Timeout / network error | Check network & proxy; increase "Timeout" in advanced settings |
| HTTP 401 | Invalid or expired API key — set a new one |
| HTTP 429 | Rate limited — increase "Delay" and "Retries" |
| Line count mismatch | The script retries per line automatically; failures are logged |
| Poor translation quality | Tune the prompt; enable line-by-line mode for long texts |
| Track-matte warning | Select the matte layer (the one above the target layer) too |
| Undo | Everything is one undo group — a single Ctrl+Z restores all language copies |

> This repository publishes the binary script only (no testable source), so no unit tests are bundled.

## 📄 License

MIT © 2026 gjade3702-cell
