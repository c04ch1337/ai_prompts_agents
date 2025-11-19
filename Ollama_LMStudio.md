Nice, 30GB GPU is a **really** comfy spot for local models – you can run some serious brains on that box.

I’ll break this into:

1. **Top 3 models for Ollama** (what + why + pull commands)
2. **Top 3 models for LM Studio**
3. **Setup guide: using Ollama as a standalone LLM provider**
4. **Setup guide: using LM Studio as a standalone LLM provider**
5. **How to point any app / agent to them with the OpenAI API interface**

---

## 1. Top 3 models for **Ollama** on a 30GB GPU

Given your rig, I’m assuming you want: **reasoning + coding + general assistant** — not just toy models.

All of these are first-class citizens in the Ollama library. ([Ollama][1])

### 1️⃣ deepseek-r1:32b — “Reasoning tank”

* **Why:** Open-weight reasoning model that gets close to frontier stuff (O3 / Gemini-2.5-Pro tier) on math, code, and logic. ([Ollama][2])
* **Size:** ~20GB VRAM for the 32B variant – fits your 30GB nicely with headroom. ([Ollama][2])
* **Use for:** Complex planning, chain-of-thought, security analysis, architecture, “think for a while and be smart” jobs.
* **Commands:**

  ```bash
  # Download
  ollama pull deepseek-r1:32b

  # Interactive
  ollama run deepseek-r1:32b
  ```

---

### 2️⃣ qwen2.5:32b (or qwen3:32b) — “All-round multilingual + tools + code”

* **Why:** Qwen2.5 / Qwen3 series are strong all-rounders: multilingual, long context (up to 128k), good coding, solid instructions. ([Ollama][1])
* **Size:** 32B variants are sweet spot for quality vs VRAM on your card.
* **Use for:** Everyday assistant, coding, documentation, chatty “copilot” that doesn’t need super long thinking traces.
* **Commands (example with Qwen2.5):**

  ```bash
  # List tags in the browser: https://ollama.com/library/qwen2.5
  # Then:
  ollama pull qwen2.5:32b
  ollama run qwen2.5:32b
  ```

(You can swap `qwen2.5` for `qwen3` once you pick the exact tag you like in the library UI.)

---

### 3️⃣ gemma3:27b-it(-qat) — “Efficient multimodal assistant”

* **Why:** Gemma 3 is built on Gemini tech, multimodal (text + images), 128k context, and is explicitly designed to be “most capable model that runs on a single GPU.” ([Ollama][3])
* **Size:** 27B ≈ 17GB VRAM; QAT (“-it-qat”) variants keep quality close to full precision with smaller memory footprint. ([Ollama][3])
* **Use for:** General assistant, document work, moderate coding, plus image-aware stuff if you want local vision.
* **Commands:**

  ```bash
  # Plain 27B
  ollama pull gemma3:27b
  # Or QAT instruct-tuned
  ollama pull gemma3:27b-it-qat

  ollama run gemma3:27b-it-qat
  ```

---

## 2. Top 3 models for **LM Studio**

LM Studio is your **GGUF playground** with a nice GUI and an OpenAI-compatible local server. ([LM Studio][4])

For your use case (omnipresent personal AI / coding / reasoning):

### 1️⃣ openai/gpt-oss-20b — “Flagship local generalist”

* **Why:** Official open-weight model from OpenAI designed for reasoning + agents, with multiples sizes; 20B runs fine on 30GB GPU and is explicitly supported in LM Studio guides. ([OpenAI Cookbook][5])
* **Use for:** High-quality general assistant, planning, analysis, and tool-calling scenarios.
* **CLI download (LM Studio):**

  ```bash
  # Install CLI once
  npx lmstudio install-cli

  # Download model
  lms get openai/gpt-oss-20b
  ```

---

### 2️⃣ Gemma 3 12B / 27B GGUF (QAT) — “Fast multimodal workhorse”

* **Why:** Same Gemma3 family, but in GGUF form, QAT-quantized for local inference with good performance. LM Studio has explicit support for Gemma 3 GGUF. ([Hugging Face][6])
* **Use for:** Local multimodal assistant (text+images), long context, solid reasoning while staying fast.

You’ll see these in LM Studio’s “Models” tab (look for gemma-3-12b / 27b QAT variants, or import your own GGUF via `lms import`). ([LM Studio][7])

---

### 3️⃣ A “thinking” model in GGUF, e.g. Kimi-K2 or DeepSeek-style

You explicitly like **Kimi K2 Thinking**; there are community GGUFs like:

* `unsloth/Kimi-K2-Thinking-GGUF` (and similar “thinking” models) hosted in the LM Studio community / HF ecosystem. ([Hugging Face][6])

**Use for:** Slow, high-IQ analysis, chain-of-thought style work, “take your time and reason” agent modes.

You’d typically:

```bash
# Example: after downloading a GGUF file from HF
lms import path/to/kimi-k2-thinking-Q4_K_M.gguf
```

Then select it in LM Studio’s model list.

---

## 3. Setup guide — **Ollama as a standalone local LLM provider**

You said Ollama is already installed on the GPU rig, so we’ll go from “installed” to “provider on LAN”.

### Step 1 – Pull your chosen models

Examples (you can mix and match):

```bash
ollama pull deepseek-r1:32b
ollama pull qwen2.5:32b
ollama pull gemma3:27b-it-qat
```

---

### Step 2 – Test interactively

```bash
ollama run deepseek-r1:32b
```

If you get responses, models are good.

---

### Step 3 – Make sure the Ollama server is running

Ollama runs an HTTP server on **port 11434** and exposes:

* Native API: `http://localhost:11434/api/generate`
* OpenAI-compatible API: `http://localhost:11434/v1/chat/completions` and `/v1/models` ([Ollama][8])

If it’s *not* already running as a service, you can:

```bash
ollama serve
```

Test:

```bash
curl http://127.0.0.1:11434/v1/models
```

You should see a JSON list of installed models. ([Reddit][9])

---

### Step 4 – Expose Ollama to your LAN (so other machines can call it)

By default it binds to `127.0.0.1:11434`. You change that with `OLLAMA_HOST`. ([Ollama Docs][10])

On **Linux (GPU rig)**, for example:

```bash
sudo systemctl edit ollama.service
```

Add:

```ini
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
Environment="OLLAMA_ORIGINS=*"
```

Then:

```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
```

Now the rig listens on all interfaces. Confirm:

```bash
curl http://<rig-ip>:11434/v1/models
```

(And poke a hole in your firewall for 11434 TCP if needed.)

---

### Step 5 – Use Ollama like OpenAI in code

Anywhere you’d normally use `openai`, just swap the base URL:

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://<rig-ip>:11434/v1",
    api_key="ollama",  # required but ignored by Ollama
)

resp = client.chat.completions.create(
    model="deepseek-r1:32b",
    messages=[
        {"role": "system", "content": "You are a helpful, security-focused assistant."},
        {"role": "user", "content": "Help me threat model a new API gateway."}
    ],
)

print(resp.choices[0].message.content)
```

That makes your GPU rig a **drop-in OpenAI-compatible provider**. ([Ollama][8])

---

## 4. Setup guide — **LM Studio as a standalone local LLM provider**

LM Studio = GUI + server + OpenAI-compatible API. ([LM Studio][4])

### Step 1 – Install LM Studio & download models

1. Install LM Studio from their site (Windows / macOS / Linux). ([Medium][11])
2. Open it → “Models” tab.
3. Search for and download:

   * `openai/gpt-oss-20b`
   * Gemma 3 12B/27B QAT variant
   * Any Kimi-K2 / thinking GGUF you’ve imported

Or via CLI:

```bash
# Install CLI
npx lmstudio install-cli

# Download GPT-OSS 20B
lms get openai/gpt-oss-20b
```

([OpenAI Cookbook][5])

---

### Step 2 – Start LM Studio as a local server

In **LM Studio UI**: ([LM Studio][4])

1. Go to **Developer** (or “Local Server”) tab.
2. Toggle **Status: Running**.
3. Default server is `http://127.0.0.1:1234`.

Or via CLI:

```bash
lms server start --port 1234
# add --cors if you need browser-based tools hitting it
```

([LM Studio][12])

Test:

```bash
curl http://127.0.0.1:1234/v1/models
```

You should see your LM Studio models listed. ([GPT for Work][13])

---

### Step 3 – Use LM Studio like OpenAI

LM Studio exposes an **OpenAI-compatible API** on `/v1/chat/completions` and `/v1/responses`. ([LM Studio][14])

Example:

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://127.0.0.1:1234/v1",
    api_key="lm-studio",  # not actually checked
)

resp = client.chat.completions.create(
    model="openai/gpt-oss-20b",
    messages=[
        {"role": "system", "content": "You are a precise, no-BS engineering assistant."},
        {"role": "user", "content": "Design a Rust service to orchestrate multiple LLM backends."}
    ],
)

print(resp.choices[0].message.content)
```

You can literally switch between **Ollama** and **LM Studio** by changing only `base_url` and `model` name.

---

### Step 4 – Let other machines use LM Studio

LM Studio’s server can listen on the network; in UI there’s a setting under the server/developer tab to allow external connections. ([Reddit][15])

Once that’s on, from any box on your LAN:

```bash
curl http://<rig-ip>:1234/v1/models
```

And in your apps:

```python
client = OpenAI(
    base_url="http://<rig-ip>:1234/v1",
    api_key="lm-studio",
)
```

(Again: firewall allow TCP 1234.)

---

## 5. Wiring this into your “LLM provider chain”

Because **both** Ollama and LM Studio now speak “OpenAI”, your orchestrator can treat them as:

* Provider A: `http://<rig-ip>:11434/v1`  (Ollama)
* Provider B: `http://<rig-ip>:1234/v1`   (LM Studio)
* Provider C: `https://api.openrouter.ai/v1` (cloud fallback)

Same client, different config objects. For example, a simple pseudo-failover:

```python
PROVIDERS = [
    {"name": "ollama",   "base_url": "http://rig:11434/v1", "api_key": "ollama"},
    {"name": "lmstudio", "base_url": "http://rig:1234/v1",  "api_key": "lm-studio"},
    {"name": "openrouter", "base_url": "https://openrouter.ai/api/v1", "api_key": "..."},
]
```

Then your “Jamey 3.0 Orchestrator” just tries Provider 1 → 2 → 3 depending on load, latency, or “this prompt needs a thinking model”.

---

Short version:

* **Ollama** → run the **heavy reasoning & coding monsters** (deepseek-r1:32b, qwen2.5/3:32b, gemma3:27b-it-qat).
* **LM Studio** → run **GGUF / community / experimental stuff** (GPT-OSS-20B, Gemma3 QAT, Kimi-K2 Thinking), also with an OpenAI-compatible server.
* Both become **drop-in local LLM providers** across your network by exposing the ports and pointing your agents at the right `base_url`.

From here, the fun part is deciding **which brain handles which job** – reasoning tank, fast coder, empathetic omnipresent “soul”, etc.

[1]: https://ollama.com/library/ "library"
[2]: https://ollama.com/library/deepseek-r1 "deepseek-r1"
[3]: https://ollama.com/library/gemma3 "gemma3"
[4]: https://lmstudio.ai/docs/developer/core/server?utm_source=chatgpt.com "LM Studio as a Local LLM API Server"
[5]: https://cookbook.openai.com/articles/gpt-oss/run-locally-lmstudio?utm_source=chatgpt.com "How to run gpt-oss locally with LM Studio"
[6]: https://huggingface.co/models?library=gguf&utm_source=chatgpt.com "Models compatible with the GGUF library"
[7]: https://lmstudio.ai/docs/app/advanced/import-model?utm_source=chatgpt.com "Import Models | LM Studio Docs"
[8]: https://ollama.com/blog/openai-compatibility?utm_source=chatgpt.com "OpenAI compatibility · Ollama Blog"
[9]: https://www.reddit.com/r/LocalLLaMA/comments/1du4ddy/ollama_adds_v1models_and_v1completions_openai/?utm_source=chatgpt.com "Ollama adds /v1/models and /v1/completions OpenAI ..."
[10]: https://docs.ollama.com/faq?utm_source=chatgpt.com "FAQ"
[11]: https://medium.com/%40bishakhghosh0/top-5-local-llm-tools-and-models-to-use-in-2025-57b0c3088520?utm_source=chatgpt.com "Top 5 Local LLM Tools and Models to Use in 2025"
[12]: https://lmstudio.ai/docs/cli/server-start?utm_source=chatgpt.com "lms server start | LM Studio Docs"
[13]: https://gptforwork.com/help/ai-models/api-endpoints/set-up-lm-studio-on-windows?utm_source=chatgpt.com "Set up LM Studio on Windows"
[14]: https://lmstudio.ai/docs/developer/openai-compat?utm_source=chatgpt.com "OpenAI Compatibility Endpoints | LM Studio Docs"
[15]: https://www.reddit.com/r/LocalLLM/comments/1mimu46/lm_studio_connect_to_server_on_lan/?utm_source=chatgpt.com "LM Studio - Connect to server on LAN : r/LocalLLM"
