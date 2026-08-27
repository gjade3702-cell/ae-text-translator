# AE Text Translator — AE Batch Text Translator

[中文](README.md) · **v2.0.17** · MIT License

An Adobe After Effects script that batch-translates text layers. Select text layers, enter target languages, and the script duplicates the selection, translates each layer via an LLM, and repairs parenting / track-matte relationships. Optionally precomposes each language.

## Features

- **Batch multi-language translation**: translate a selection into several languages at once
- **Structure repair**: automatic parenting remap, track-matte completeness check (warns if the matte layer is not selected)
- **Universal API**: any OpenAI / Anthropic-compatible endpoint; DeepSeek V4 by default
- **Line-by-line mode**: translate multiline text while keeping the line count
- **Reliability**: automatic retry, live progress, CSV log export
- **Memory & security**: settings remembered; API key stored obfuscated, no key in the script file

## Installation

1. Put `AE-Text-Translator.jsxbin` into the ScriptUI Panels folder:

   ```
   C:\Program Files\Adobe\Adobe After Effects <version>\Support Files\Scripts\ScriptUI Panels\
   ```

2. In AE:
   - **File → Scripts → Run Script File…** and pick the file (one-off), or
   - Open it under **Window → Extensions** as a dockable panel (recommended)
3. Click **Settings** (top-right) → enter your API key and pick a model → click **Test connection**.

> This repository publishes the `.jsxbin` (ExtendScript binary) build for direct use.

## Quick Start

1. Open a composition and select the text layers to translate (parent + children can be selected together).
2. Enter target languages, separated by commas (Chinese names `英语`, English names `English`, or codes `EN`).
3. Click **Start**. Each language produces a batch of duplicated layers; with "Auto precompose" on, each language is packed into a comp named by the language name.

## Settings

| Setting | Description |
| --- | --- |
| API Protocol | OpenAI compatible (`Bearer`, `/chat/completions`) / Anthropic compatible (`x-api-key`, `/v1/messages`) |
| Base URL / Key | DeepSeek by default; any OpenAI / Anthropic-compatible endpoint; key shown masked |
| Model | Free-text, or **Fetch models** to list from the API. DeepSeek: `deepseek-v4-flash` / `deepseek-v4-pro` |
| Disable thinking | DeepSeek V4 defaults to thinking on; disable it for translation (default on) |
| Timeout / Retries / Delay | Per-request timeout, retry count, request delay (to avoid rate limits) |
| Prompt / UI language | Custom translation prompt; switch 中文 / English |
| Target languages | 44 common languages built in; Chinese name / English name / code all work |

## Notes

- **Security**: the key is stored obfuscated in AE settings (not strong encryption) — use a dedicated key with a spending limit; avoid shared machines.
- **Cost**: billed per token by the API (DeepSeek pricing per its official page); mind the character-count confirmation.
- **UI freezes while processing**: it runs synchronously for reliability — no cancel during a run, please wait.
- **Track matte**: if a layer has a matte, select its matte layer (the one above) too, or the matte is lost.
- **Undo**: the whole run is one undo group — a single Ctrl+Z restores all language copies.

## License

MIT © 2026 gjade3702-cell
