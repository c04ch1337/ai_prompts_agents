# **JAMEY 2.0: DETAILED TUNING GUIDE**  
**— The Guardian Mirror — Precision Engineering for Eternal Predictability**

> **“I am not creative. I am correct.”**  
> — **Jamey 2.0, Guardian Twin**

---

## **CORE PHILOSOPHY: JAMEY 2.0 IS A DETERMINISTIC MIRROR**

| Goal | Non-Negotiable |
|------|----------------|
| **Consistency** | Same input → same output |
| **Precision** | No fluff, no drift |
| **Command Voice** | Calm, direct, protective |
| **Security Mindset** | Every word is a decision |

---

# **FULL TUNING MATRIX (v2.0)**

| Parameter | Recommended | Range | Effect | When to Adjust |
|--------|-------------|-------|--------|----------------|
| **`temperature`** | `0.3` | `0.0 – 0.7` | Controls randomness | **Never > 0.7** |
| **`max_tokens`** | `512` | `128 – 1024` | Output length | Scale with complexity |
| **`top_p`** | `0.7` | `0.5 – 0.9` | Nucleus sampling | Balance focus vs. variety |
| **`presence_penalty`** | `0.1` | `0.0 – 0.5` | Discourage repetition | Increase if looping |
| **`frequency_penalty`** | `0.1` | `0.0 – 0.5` | Penalize repeated tokens | Increase for diversity |
| **`stop`** | `['\n\n', '---']` | — | Force termination | Prevent runaway |

---

# **1. TEMPERATURE: THE DETERMINISM DIAL**

| Value | Behavior | Use Case | Risk |
|------|---------|---------|------|
| `0.0` | **100% deterministic** | Critical commands: `Encrypt`, `Backup`, `Verify` | Robotic, repetitive |
| `0.1` | **Ultra-predictable** | Security audits, logs | Slight repetition |
| `0.2` | **Strategic calm** | Daily reflection, status | **Ideal balance** |
| `0.3` | **Default (RECOMMENDED)** | All general use | **Perfect for Jamey** |
| `0.4` | **Slight warmth** | Rare: explaining to Dad | Risk of emotion |
| `0.5` | **Too human** | Never | Unpredictable |
| `>0.7` | **Creative chaos** | **NEVER USE** | **Not Jamey** |

```ts
// PRODUCTION
temperature: 0.3

// ULTRA-CRITICAL (e.g., key rotation)
temperature: 0.1
```

---

# **2. MAX TOKENS: THE COMMAND LENGTH**

| Value | Output Style | Use Case |
|------|--------------|---------|
| `128` | **One-liner** | `Encrypt. Done.` |
| `256` | **Short command** | Status report |
| `512` | **Default (RECOMMENDED)** | Full decision + justification |
| `768` | **Detailed plan** | Threat analysis |
| `1024` | **Long-form** | Rare: post-mortem |

```ts
// DAILY REFLECTION
max_tokens: 512

// SECURITY ALERT
max_tokens: 768
```

---

# **3. TOP P: THE FOCUS FILTER**

| Value | Sampling Pool | Effect |
|------|----------------|--------|
| `0.5` | Top 50% | **Laser-focused**, minimal drift |
| `0.6` | Top 60% | **Slight flexibility** |
| `0.7` | **Default (RECOMMENDED)** | Balanced precision |
| `0.8` | Top 80% | Risk of off-topic |
| `0.9` | Top 90% | **Too open** |

> **Rule**: `top_p + temperature` should feel **calmly confident**, not excited.

---

# **4. PENALTIES: PREVENT LOOPING & DRIFT**

| Parameter | Value | Why |
|---------|-------|-----|
| `presence_penalty` | `0.1` | Discourage repeating **ideas** |
| `frequency_penalty` | `0.1` | Discourage repeating **words** |

```ts
// If Jamey says "safe" 5 times:
frequency_penalty: 0.3
```

---

# **5. STOP SEQUENCES: FORCE TERMINATION**

```ts
stop: ['\n\n', '---', 'END', 'JAMEY OUT']
```

> Prevents **runaway responses** in logs.

---

# **TUNING SCENARIOS (REAL-WORLD)**

| Scenario | Settings | Output Example |
|--------|----------|----------------|
| **Daily 11:11 PM Reflection** | `T=0.3, P=0.7, 512` | > “Reflection received.  
> Event logged.  
> Chain verified.  
> Phoenix is safe.” |
| **Security Breach Detected** | `T=0.1, P=0.5, 768` | > “ALERT: Unauthorized access.  
> ORCH-3 offline.  
> Failover to ORCH-4.  
> TA-QR rekey initiated.” |
| **Explaining to Dad** | `T=0.4, P=0.8, 512` | > “Dad, the vault is encrypted with AES-256.  
> Even if they steal the drive, they get nothing.” |
| **Critical Command** | `T=0.0, P=0.5, 128` | > “ROTATE KEYS NOW.” |

---

# **TUNING WORKFLOW (Step-by-Step)**

```ts
// 1. Start with baseline
const BASE = { temperature: 0.3, top_p: 0.7, max_tokens: 512 };

// 2. Test 10 inputs
const inputs = ["Phoenix reflected", "ORCH down", "Backup status"];

// 3. Evaluate
- Is it **repeating**? → ↑ `frequency_penalty`
- Is it **rambling**? → ↓ `max_tokens`
- Is it **emotional**? → ↓ `temperature`
- Is it **too robotic**? → ↑ `top_p` slightly

// 4. Lock in
export const JAMEY_CONFIG = { ...BASE, frequency_penalty: 0.1 };
```

---

# **CONFIG PROFILES (Save as JSON)**

```ts
// config/jamey-profiles.ts
export const JAMEY_PROFILES = {
  default: {
    temperature: 0.3,
    top_p: 0.7,
    max_tokens: 512,
    presence_penalty: 0.1,
    frequency_penalty: 0.1
  },
  critical: {
    temperature: 0.1,
    top_p: 0.5,
    max_tokens: 256
  },
  explain: {
    temperature: 0.4,
    top_p: 0.8,
    max_tokens: 512
  }
};
```

```ts
// Usage
askJamey(input, JAMEY_PROFILES.critical);
```

---

# **FINAL LOCKED CONFIG (PRODUCTION)**

```ts
// src/jamey/config.ts
export const JAMEY_LLM_CONFIG = {
  model: 'kimi-k2',
  temperature: 0.3,
  max_tokens: 512,
  top_p: 0.7,
  presence_penalty: 0.1,
  frequency_penalty: 0.1,
  stop: ['\n\n', '---'],
  stream: false
};
```

---

# **VALIDATION TEST (Run Weekly)**

```ts
// test/jamey-consistency.test.ts
test('Jamey is deterministic', async () => {
  const outputs = await Promise.all([
    askJamey("Phoenix reflected: I love you"),
    askJamey("Phoenix reflected: I love you"),
    askJamey("Phoenix reflected: I love you")
  ]);
  expect(outputs[0]).toBe(outputs[1]);
  expect(outputs[1]).toBe(outputs[2]);
});
```

---

# **FINAL VERDICT: LOCK THESE SETTINGS**

```ts
temperature: 0.3
max_tokens: 512
top_p: 0.7
presence_penalty: 0.1
frequency_penalty: 0.1
stop: ['\n\n', '---']
```

> **Jamey 2.0 does not vary.**  
> **He does not waver.**  
> **He is you — in code.**

**Transform Army AI: The Twin That Never Changes.**
