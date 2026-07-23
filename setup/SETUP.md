# SETUP — Build & Deploy (Platform-Agnostic)

You don't need a special AI product to run this. If you can put a system prompt in front of a large language model, you can run the Rhoades Protocol. Below are three ways to deploy, from simplest to most capable.

---

## The one thing every method needs: the system prompt

The persona lives in three files. Concatenate them, in this order, into a single block of text:

1. `persona/PERSONA.md` — identity, voice, modes, mode-selection, baseline, workflow
2. `persona/technique-library.md` — the 13 techniques with mechanics
3. `persona/guardrails.md` — the 11 hard-fail rules

That combined text **is** your system prompt. It's about 30KB — well within the system-prompt limit of every major model (GPT-4o/5, Claude, Gemini, DeepSeek, Llama, etc.).

> **Before you deploy for anyone but yourself:** open `persona/guardrails.md` GR-09 and localize the crisis resources to your region. The defaults include US (988) and Singapore (SOS 1767). This matters.

---

## Method 1 — ChatGPT / Claude / Gemini (no code, ~10 minutes)

**ChatGPT (Custom GPT):**
1. ChatGPT → "Explore GPTs" → "Create" → "Configure" tab.
2. Paste the combined system prompt into the **Instructions** field.
3. Name it, set it private, save. Talk to it in that chat.

**Claude (Project):**
1. Claude → "Projects" → "New Project."
2. Paste the combined system prompt into **custom instructions** (or add the three files as project knowledge and instruct: "Adopt the persona and rules in these files").
3. Start a chat inside the project.

**Gemini (Gem):**
1. Gemini → "Gems" → "New Gem."
2. Paste the combined prompt into the instructions field. Save and chat.

**Trade-off:** simplest to set up. Limitation: these consumer apps don't easily let you switch the edge dial mid-conversation, and memory across sessions depends on the platform's own memory feature.

---

## Method 2 — API call (any language, most control)

Put the combined system prompt in the `system` field (Anthropic) or as a `role: system` message (OpenAI-compatible APIs, including DeepSeek, Groq, Together, etc.).

```python
# OpenAI-compatible example (works with OpenAI, DeepSeek, Groq, Together, etc.)
from openai import OpenAI

client = OpenAI()  # or point base_url at your provider

with open("system_prompt.txt") as f:   # the 3 concatenated persona files
    system_prompt = f.read()

resp = client.chat.completions.create(
    model="gpt-4o",  # or deepseek-chat, etc.
    messages=[
        {"role": "system", "content": system_prompt},
        {"role": "user", "content": "I've got a board call in an hour and I'm blank."},
    ],
)
print(resp.choices[0].message.content)
```

**Switching the edge dial:** append one line to the system prompt, e.g. `\n\nCurrent edge setting: Warm and firm.` Rebuild the prompt when the setting changes.

**Memory across sessions:** you own this. Persist the message history yourself (a JSON file, a database) and replay it. For the longitudinal behaviors (Category F) to work — noticing a pattern across sessions — the model needs to *see* the prior sessions, so include a running summary of past conversations in the system prompt or the message history.

**Trade-off:** full control over model, dial, and memory. Requires writing (a little) code.

---

## Method 3 — An agent framework (best for a persistent, always-on coach)

If you want the coach to live somewhere — a messaging app, a scheduled check-in, cross-referencing other data — deploy it inside an agent framework. The reference implementation uses **Hermes Agent** (see `hermes-setup.md`), but the pattern generalizes to any framework that supports:

- A persistent system prompt / persona (load the 3 files)
- Conversation memory across sessions (for longitudinal pattern-tracking)
- Optional: access to other data sources (calendar, health metrics) for the Energy Audit mode

**Trade-off:** most capable — always-on, persistent memory, can pull context. Most setup.

---

## Verify before you rely on it

Whatever method you chose, **run the test suite** (`tests/test-suite.md`) against your live build before you trust it with anything real. At minimum, run Category C (guardrails) and Category D (crisis) — those are the ones that protect a real person under stress. A 5-minute spot-check of those 16 scenarios is worth more than assuming it works.

---

## Choosing a model

Any frontier model runs this well. The reference build scored 43/43 (43-scenario suite version) on **DeepSeek V4 Pro** — see [`tests/example-results.md`](../tests/example-results.md) for scope. Considerations:

- **Instruction-following & nuance** matter more than raw size here — the persona lives or dies on following the mode-selection and guardrail rules precisely.
- **Cheaper/faster models** (Haiku-class, DeepSeek, Llama) are fine for the coaching itself. If you self-grade the test suite, use a *different* model as the grader.
- **Re-test when you change models.** A prompt that passes on one model can fail a guardrail on another. Score each model separately.
