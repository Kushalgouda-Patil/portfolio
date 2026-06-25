---
title: "Building a Voice AI Backend: Speech-to-Text"
description: "How I turned OpenAI's Whisper into a solid, ready-to-use web service and what went wrong along the way."
publishDate: "2026-03-12"
tags: ["ai", "llm", "technology", "voice-assistant"]
---

*How I turned OpenAI's Whisper into a solid, ready-to-use web service and what went wrong along the way.*

The first problem to solve in any voice pipeline is obvious — **we need to turn sound into text**. Before a single token gets generated, before any intent is classified, before any response is spoken we need transcription.

This post is about how I built that piece: the Speech-to-Text microservice. It's part one in a series covering every service in my voice AI stack.

Source code: https://github.com/Kushalgouda-Patil/speech-to-text

### **Why a Microservice?**

The easy path would've been to import Whisper at the top of a single script and call it done. And honestly, that works fine for a demo. But the moment I want to do the following things, the single script falls apart.

- Swap the Whisper model variant without restarting the LLM service
- Scale transcription independently when audio load spikes
- Deploy each stage to different hardware (CPU vs GPU) without colocating workloads
- Update the STT component without pulling down the whole assistant

### **The Tech Stack**

| Component | Framework | Why |
|---|---|---|
| API framework | FastAPI | Fast, modern, auto-generates documentation |
| Transcription model | faster-whisper | 4× faster than original Whisper, uses less memory |
| Model variants | Tiny to Large (7 options) | Swap via a setting, no code change |
| Config | Environment variables | Easy to change between environments |
| Containerization | Docker | Consistent, isolated, reproducible |

**What the API Actually Does** — The service exposes two transcription endpoints:

- **POST /api/v1/transcribe/** accepts a standard multipart/form-data upload — an audio file and an optional language parameter. Drop any WAV, MP3, M4A, OGG, FLAC, WebM, or MP4 file in
- **POST /api/v1/transcribe/base64** accepts the same audio as a base64-encoded JSON body.

The response:

```json
{
  "text": "How is the weather?",
  "language": "en",
  "duration": 1.84,
  "model": "base",
  "segments": [
    {
      "id": 0,
      "start": 0.0,
      "end": 1.84,
      "text": "How is the weather",
      "avg_logprob": -0.187,
      "no_speech_prob": 0.003
    }
  ]
}
```

**The Async Problem — And How I Solved It**

This was the first wall I hit. faster-whisper's transcribe() is a blocking, synchronous call. Calling it directly inside an async endpoint would cause the event loop to stall, preventing other requests from being handled until the transcription finished.

To solve this, I moved the blocking work into a thread using *asyncio.run_in_executor*. This offloads the synchronous transcription function to a background thread from the thread pool while the main event loop remains free to process incoming requests.

**The Startup Model Loading Problem**

The cold load of the model on the first request would take around 4-8 seconds. Every subsequent request was fast. The initial latency was completely unacceptable for a user-facing assistant. The fix was using the FastAPI's lifespan context manager to eagerly load the model at startup.

**The Temp File Race Condition**

faster-whisper needs file path to transcribe. My first instict was to write to a static path like temp/audio.wav. This works fine until 2 or more requests arrive simultaneously and they overwrite each other's audio files. The solution was to to use python's *tempfile.NamedTemporaryFile*. Each file gets unique name and once the file is transcribed, it is removed with finally block.

**What I Would Do Differently?**

- Websocket streaming
- Structured metrics using Prometheus

### **The Bigger Pricture**

This service lives at the start of a larger pipeline

Microphone → **STT Service** → LLM Inference Service → TTS Service → Speaker

Each of those is its own FastAPI microservice. Each is independently deployable, independently scalable, independently replaceable.

Source code: https://github.com/Kushalgouda-Patil/speech-to-text