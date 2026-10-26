# Anshin System Architecture & Air-Gap Verification Specification

- **Target Binary**: Anshin for macOS (Build `0.1.53`+)
- **Operating Environment**: macOS 13+ (Apple Silicon: M1, M2, M3, M4)
- **Local Model**: Qwen 2.5 (3B / 7B / 14B parameter quantizations) via Ollama
- **Local Runtime**: Tauri (Rust backend + Webview frontend)

---

## Architecture Overview

Anshin is architected around the principle of **Architectural Zero-Trust**: user privacy is enforced at the network protocol and kernel level rather than through legal promises.

```
┌─────────────────────────────────────────────────────────────┐
│                       YOUR MAC                              │
│                                                             │
│   ┌─────────────────────────────────────────────────────┐   │
│   │                 Anshin.app                          │   │
│   │  ┌───────────────────────┐ ┌─────────────────────┐  │   │
│   │  │ Webview Frontend      │ │ Tauri Rust Core     │  │   │
│   │  │ (HTML / React UI)     │ │ (Process sandbox)   │  │   │
│   │  └───────────┬───────────┘ └──────────┬──────────┘  │   │
│   └──────────────┼────────────────────────┼─────────────┘   │
│                  │ IPC: 127.0.0.1:11434   │                 │
│                  ▼                        ▼                 │
│   ┌──────────────────────────┐ ┌────────────────────────┐   │
│   │ Ollama Daemon            │ │ Encrypted SQLite       │   │
│   │ (Local Qwen 2.5 LLM)     │ │ (AES-256-GCM, Argon2id)│   │
│   │ Hardware: Neural Engine/ │ │ ~/Library/Containers/  │   │
│   │ Unified Apple RAM        │ │ com.anshin.app/Data/   │   │
│   └──────────────────────────┘ └────────────────────────┘   │
│                                                             │
└──────────────────────────────╳──────────────────────────────┘
                               ╳  ❌ ZERO OUTBOUND NETWORK CALLS
                               ╳  ──────────────────────────────
                                     THE PUBLIC INTERNET
```

---

## 1. Local IPC and Content Security Policy (CSP)

Anshin's internal browser engine enforces a strict, immutable Content Security Policy hard-coded into `src-tauri/tauri.conf.json`:

```
connect-src 'self' ipc: http://ipc.localhost http://localhost:11434 http://127.0.0.1:11434
```

### Protocol Analysis
- `'self'` & `ipc:`: Permits local message passing between the frontend UI and the native Rust backend.
- `http://127.0.0.1:11434`: Permits communication with the local Ollama HTTP server on the loopback adapter (`lo0`).
- **No Remote Hosts**: All requests to external protocols (`https://*`, `wss://*`) are immediately aborted by the WebKit networking layer before leaving user space.

---

## 2. On-Device AI Pipeline

1. **One-Time Model Pull (Setup Only)**:
   During initial setup, Ollama pulls the open-source Qwen 2.5 model weights directly from Ollama's public registry. Once downloaded, the model is stored on local disk at `~/.ollama/models`.
2. **Subsequent Sessions (Air-Gapped)**:
   During regular app operation, zero internet packets are transmitted. When a user writes a thought record or chats with Anshin:
   - The prompt is tokenized locally.
   - Inference runs locally on the Mac's GPU / Apple Neural Engine via Unified Memory.
   - Streamed response tokens return over `127.0.0.1:11434`.
   - The prompt and completion are stored directly in the local encrypted database.

---

## 3. Storage Layer & Kernel Sandboxing

- **File Container**: Anshin runs under Apple's mandatory `com.apple.security.app-sandbox`.
- **Database Engine**: Local SQLite database encrypted via **AES-256-GCM** with **Argon2id** key derivation.
- **Path**: `~/Library/Containers/com.anshin.app/Data/`
- **Isolation Guarantee**: Other non-sandboxed or sandboxed third-party applications cannot read, modify, or intercept data within Anshin's container.

---

## 4. Voice and Audio Subsystems

- **Voice Input (Transcription)**: Anshin utilizes OpenAI's open-source Whisper model executed 100% locally on your Mac's CPU/GPU. No audio streams are sent to speech-to-text cloud APIs.
- **Voice Output (Speech Synthesis)**: Anshin invokes macOS system text-to-speech framework (`NSSpeechSynthesizer` / `AVSpeechSynthesizer`) using local on-device voice models.
