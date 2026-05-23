<div align="center">

  <h1>Uncensored Local AI Multi-Platform (Custom Fork)</h1>

  <p><strong>Run unrestricted AI models entirely on your device.<br/>No cloud. No filters. No limits.</strong></p>

  <p>🚀 <strong>Custom Fork Maintained by <a href="https://github.com/allen21jarilla">allen21jarilla</a> (Allen Jarilla)</strong></p>

  [Overview](#overview) · [Download](#download) · [Fork Additions](#-fork-additions) · [Features](#features) · [Quick Start](#quick-start) · [Local API](#local-api-server) · [Credits & Acknowledgments](#-credits--acknowledgments) · [License](#license)

</div>

---

## Overview

**Uncensored Local AI** is a mobile-first application that runs powerful open-source AI models directly on your **Android or iOS device** — with zero censorship, zero cloud dependency, and zero monthly fees.

This repository is a **highly enhanced custom fork** extending the original architecture to support advanced embeddings connectivity (for tools like SillyTavern) and power-user parameters customization.

No API keys. No subscriptions. No content restrictions. Your conversations never leave your device.

---

## Download

### Android APK — Cloud-Compiled Releases

Every time code is pushed, our **GitHub Actions CI/CD pipeline** automatically compiles a fresh release APK using cloud runners.

👉 **[Go to GitHub Releases / Actions to Download the Latest APK](https://github.com/allen21jarilla/Uncensored-Local-AI-Multiplatform/actions)**

---

## 🌟 Fork Additions

This fork introduces powerful capabilities designed for advanced local AI integration:

### 1. Vector Embeddings Endpoint (`/v1/embeddings`)
Exposes a standard OpenAI-compatible vector embeddings route, allowing external tools (such as **SillyTavern**) to interact with your loaded model for semantic memory search:
- Integrates directly with `llamadart` FFI engine.
- Supports single-string or batch-string list inputs.
- Accurately counts prompt tokens for standard OpenAI usage returns.
- Toggled on/off easily using a state-managed settings switch.

### 2. Advanced CLI Parameter Overrides
Introduces a custom argument parsing engine that maps standard `llama.cpp` command-line flags directly into the native FFI `ModelParams` constructor. Easily customize:
- `-c` or `--ctx-size` (caps context size to protect mobile device RAM).
- `-ngl` or `--n-gpu-layers` (customizes GPU offload layer count).
- `-t` or `--threads` (specifies CPU thread allocation).
- `-b` or `--batch-size` (prompt processing batch ingestion).
- `--ubatch-size` (physical micro-batch size).

---

## Features

| Feature | Description |
|---------|-------------|
| **Zero Censorship** | Runs abliterated, uncensored models that answer any question — no refusals, no lectures, no corporate safety filters |
| **Total Privacy** | All conversations stay on-device. Nothing is sent to any server, ever |
| **Fully Offline** | Works on planes, in remote areas, on restricted networks — no internet needed after model download |
| **Cross-Platform** | One codebase for Android, iOS, Windows, macOS, and Linux |
| **Local OpenAI API** | Built-in HTTP server serving `/v1/chat/completions` and `/v1/embeddings` |
| **Model Library** | Download, import, and manage GGUF models directly in the app |
| **Chat History** | Persistent conversation history stored locally via Hive |

---

## Quick Start

### Sideloading Android APK

1. Go to the **Actions** or **Releases** tab on your GitHub fork page and download the latest compiled `release-apk`.
2. On your phone: **Settings → Install unknown apps** → allow your browser.
3. Tap the downloaded APK to install.
4. Open the app, go to **Models** tab, download a model, and start chatting.

### Sideloading iOS IPA

1. Download the pre-release iOS assets.
2. Extract the `.ipa` package from the zip.
3. Install using TrollStore (recommended) or AltStore on your device.

---

## Local API Server

### Setup
1. Load a model in the app.
2. Go to **Settings → Local API Server** and toggle it **ON**.
3. Toggle on **Enable Vector Embeddings** to expose the vector routing.
4. Use `http://127.0.0.1:4891/v1` as your base URL.

### Endpoints
```bash
# Get loaded models
curl http://127.0.0.1:4891/v1/models

# Generate embeddings (SillyTavern and semantic search)
curl http://127.0.0.1:4891/v1/embeddings \
  -H "Content-Type: application/json" \
  -d '{"model":"local","input":["semantic search","vector representation"]}'

# Chat completion (non-streaming)
curl http://127.0.0.1:4891/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"local","messages":[{"role":"user","content":"Hello!"}]}'
```

---

## 🤝 Credits & Acknowledgments

This fork is a product of excellent open-source foundations. We would like to credit and acknowledge the following individuals, teams, and libraries:

### Original Author
- **[techjarves](https://github.com/techjarves)**: Creator of the original **Uncensored-Local-AI-Multiplatform** codebase. We are incredibly grateful for their clean, modular, and beautiful layout designs and base local server implementation.

### Fork Developer
- **[allen21jarilla](https://github.com/allen21jarilla) (Allen Jarilla)**: Developer of this fork. Implemented the standard OpenAI vector embeddings REST routing, the CLI parameter overrides engine, advanced FFI custom mapping parser, and the CI/CD cloud compilation workflow.

### Libraries & Frameworks
- **[leehack](https://github.com/leehack) & the `llamadart` Team**: For building `llamadart`, the outstanding native FFI bridge and Dart package that connects Flutter applications directly to C++ inference logic.
- **[ggerganov](https://github.com/ggerganov) & the `llama.cpp` Developers**: For their groundbreaking C++ local LLM inference engine, powering on-device AI across budget and high-end hardware.
- **The Flutter Team**: For the beautiful cross-platform UI framework and development ecosystem.
- **The GetX, Hive, and Wakelock Plus Teams**: For the foundational state management, local database storage, and system utility libraries.

---

## License

This project is licensed under the **MIT License** — free to use, modify, and distribute.  
See the [LICENSE](LICENSE) file for details.

<div align="center">
  <sub>Built with ❤️ using Flutter · Powered by <a href="https://github.com/ggerganov/llama.cpp">llama.cpp</a> and <a href="https://github.com/leehack/llamadart">llamadart</a></sub>
</div>
