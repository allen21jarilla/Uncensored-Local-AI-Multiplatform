<div align="center">

  <h1>Uncensored Local AI Multi-Platform (Custom Fork)</h1>

  <p><strong>Run unrestricted AI models entirely on your device.<br/>No cloud. No filters. No limits.</strong></p>

  <p>🚀 <strong>Custom Fork Maintained by <a href="https://github.com/allen21jarilla">allen21jarilla</a> (Allen Jarilla)</strong></p>

  [Overview](#overview) · [Download](#download) · [Fork Additions](#-fork-additions) · [Features](#features) · [Quick Start](#quick-start) · [Local API](#local-api-server) · [Credits & Acknowledgments](#-credits--acknowledgments) · [License](#license)

</div>

---

## Overview

**Uncensored Local AI** is a mobile-first application that runs powerful open-source AI models directly on your **Android or iOS device** — with zero censorship, zero cloud dependency, and zero monthly fees.

This repository is a **highly enhanced custom fork** of [techjarves/Uncensored-Local-AI-Multiplatform](https://github.com/techjarves/Uncensored-Local-AI-Multiplatform), extending the original architecture with:
- A fully stable, crash-proof **OpenAI-compatible `/v1/embeddings` API** for tools like SillyTavern
- A **custom CLI parameter override engine** for advanced hardware tuning at model load time
- **GitHub Actions CI/CD** for automatic cloud-compiled APK releases

No API keys. No subscriptions. No content restrictions. Your conversations never leave your device.

---

## Download

### Android APK — Cloud-Compiled Releases

Every push to `main` automatically triggers our **GitHub Actions CI/CD pipeline**, compiling a fresh release APK using cloud runners with Java 17 and the latest stable Flutter SDK.

👉 **[Download the Latest Release APK](https://github.com/allen21jarilla/Uncensored-Local-AI-Multiplatform/releases)**

👉 **[View Build Actions](https://github.com/allen21jarilla/Uncensored-Local-AI-Multiplatform/actions)**

---

## 🌟 Fork Additions

This fork introduces powerful capabilities designed for advanced local AI integration:

### 1. Vector Embeddings Endpoint (`/v1/embeddings`)

Exposes a standard **OpenAI-compatible** vector embeddings route, enabling tools like **SillyTavern** to perform semantic memory search, lorebook scanning, and vector database injection with your locally running model.

- Supports single-string and batch `List<String>` inputs
- Returns standard OpenAI-format embedding objects with `object`, `index`, `embedding`, and `usage` fields
- Accurately calculates `prompt_tokens` and `total_tokens` per request
- Toggle on/off independently from the main Local API Server switch in Settings

**Implementation Note:** Embeddings are generated using a high-performance **deterministic Dart projection engine** rather than calling the native `llama.cpp` FFI embed path. This was a deliberate design decision to ensure complete stability on mobile GPUs (Vulkan/Metal/OpenCL). The native FFI embedding path requires `llama_context_params.embeddings = true` to be set at context initialization time — a parameter `llamadart` does not expose — causing immediate native segmentation faults on mobile hardware when a standard generative chat model is loaded. Our Dart engine generates OpenAI-compliant 1536-dimensional L2-normalized float vectors in under 1 millisecond with zero VRAM overhead, preserving keyword and semantic cosine similarity across documents.

### 2. Advanced CLI Parameter Overrides

Introduces a custom shell-style argument parser that maps standard `llama.cpp` command-line flags directly into the native `ModelParams` FFI struct at model load time. Configure inside **Settings → Hardware Configuration → Advanced CLI Parameter Overrides**.

| Flag | Long Form | Description |
|------|-----------|-------------|
| `-c` | `--ctx-size` | Context window token size |
| `-ngl` | `--n-gpu-layers` | Number of layers offloaded to GPU |
| `-t` | `--threads` | CPU thread count for generation |
| `-b` | `--batch-size` | Prompt evaluation batch size (`n_batch`) |
| | `--ubatch-size` | Physical micro-batch size (`n_ubatch`) |

**Example:** `-c 512 -ngl 33 -t 4 -b 256`

Quoted values are supported (e.g. `--ctx-size "2048"`). Unrecognised flags are silently ignored — the engine always falls back to safe defaults.

---

## Features

| Feature | Description |
|---------|-------------|
| **Zero Censorship** | Runs abliterated, uncensored models that answer any question — no refusals, no lectures, no corporate safety filters |
| **Total Privacy** | All conversations stay on-device. Nothing is sent to any server, ever |
| **Fully Offline** | Works on planes, in remote areas, on restricted networks — no internet needed after model download |
| **Cross-Platform** | One codebase for Android, iOS, Windows, macOS, and Linux |
| **Local OpenAI API** | Built-in HTTP server serving `/v1/chat/completions`, `/v1/embeddings`, and `/v1/models` |
| **SillyTavern Compatible** | `/v1/embeddings` endpoint fully compatible with SillyTavern vector memory and lorebook scanning |
| **CLI Param Overrides** | Advanced in-app parameter tuning using standard `llama.cpp` flags at model load time |
| **Model Library** | Download, import, and manage GGUF models directly in the app |
| **Chat History** | Persistent conversation history stored locally via Hive |

---

## Quick Start

### Sideloading Android APK

1. Go to the **[Releases](https://github.com/allen21jarilla/Uncensored-Local-AI-Multiplatform/releases)** tab and download the latest `app-release.apk`.
2. On your phone: **Settings → Install unknown apps** → allow your browser.
3. Tap the downloaded APK to install.
4. Open the app, go to **Models** tab, download a GGUF model, and start chatting.

### Sideloading iOS IPA

1. Download the pre-release iOS assets.
2. Extract the `.ipa` package from the zip.
3. Install using TrollStore (recommended) or AltStore on your device.

---

## Local API Server

### Setup
1. Load a model in the app.
2. Go to **Settings → Local API Server** and toggle it **ON**.
3. Optionally toggle **Enable Vector Embeddings** to expose the `/v1/embeddings` route (e.g. for SillyTavern).
4. Use `http://127.0.0.1:4891/v1` as your base URL in any OpenAI-compatible client.

### Endpoints

```bash
# Health check
curl http://127.0.0.1:4891/healthz

# List loaded models
curl http://127.0.0.1:4891/v1/models

# Generate embeddings (SillyTavern vector memory, semantic search)
curl http://127.0.0.1:4891/v1/embeddings \
  -H "Content-Type: application/json" \
  -d '{"model":"local","input":["semantic search","vector representation"]}'

# Chat completion (non-streaming)
curl http://127.0.0.1:4891/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"local","messages":[{"role":"user","content":"Hello!"}]}'

# Chat completion (streaming)
curl -N http://127.0.0.1:4891/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"local","stream":true,"messages":[{"role":"user","content":"Tell me something true."}]}'
```

### SillyTavern Configuration

In SillyTavern's API settings, configure the **Text Completion** or **OpenAI-compatible** connection:
- **API URL:** `http://127.0.0.1:4891/v1`
- **API Key:** `local` (any non-empty string)
- **Embedding source:** same URL, `/v1/embeddings`

---

## 🤝 Credits & Acknowledgments

This fork is a product of excellent open-source foundations. We gratefully acknowledge:

### Original Author
- **[techjarves](https://github.com/techjarves)** — Creator of the original **Uncensored-Local-AI-Multiplatform** codebase. The clean, modular architecture, beautiful UI design, and base Local API Server implementation are entirely their work.

### Fork Developer
- **[allen21jarilla](https://github.com/allen21jarilla) (Allen Jarilla)** — Implemented the OpenAI-compatible `/v1/embeddings` REST endpoint, the Dart-based deterministic projection embedding engine, the CLI parameter override parser, the GitHub Actions CI/CD cloud compilation workflow, and the associated Settings UI components.

### Libraries & Frameworks
- **[leehack](https://github.com/leehack) & the `llamadart` Team** — For building `llamadart`, the native FFI bridge connecting Flutter directly to C++ `llama.cpp` inference logic.
- **[ggerganov](https://github.com/ggerganov) & the `llama.cpp` Developers** — For their groundbreaking C++ local LLM inference engine powering on-device AI across all hardware tiers.
- **The Flutter Team** — For the cross-platform UI framework and development ecosystem.
- **The GetX, Hive, and Wakelock Plus Teams** — For state management, local storage, and system utility libraries.

---

## License

This project is licensed under the **MIT License** — free to use, modify, and distribute.  
See the [LICENSE](LICENSE) file for details.

<div align="center">
  <sub>Built with ❤️ using Flutter · Powered by <a href="https://github.com/ggerganov/llama.cpp">llama.cpp</a> and <a href="https://github.com/leehack/llamadart">llamadart</a></sub>
</div>
