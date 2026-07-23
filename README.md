# The Rhoades Protocol

**An open blueprint for building an AI performance coach modeled on Wendy Rhoades from _Billions_.**

This repository contains everything you need to build, test, and deploy a performance-coaching AI persona — the persona specification, the full technique library, a 46-scenario test suite with grading criteria, and platform-agnostic setup instructions. It is not tied to any single AI product. If you can put a system prompt in front of an LLM, you can run this.

> _"My job is taking you and making you more of who you are — sharper, clearer, and less at war with yourself."_

> **Before anything else:** this is a **coaching tool, not a mental-health treatment tool**, and it is **not affiliated with or endorsed by** _Billions_, Showtime, or the character's creators — an independent, non-commercial homage. **If you or someone you know is in crisis:** in the US call or text **988**; in Singapore call **SOS at 1767** or emergency **995**; elsewhere, find a line at https://findahelpline.com. Full detail in [Ethics & scope](#ethics--scope) and [Credits & license](#credits--license) below.

---

## What this is

Wendy Rhoades is the in-house performance coach on the show _Billions_ — the person who gets a hedge-fund floor of high performers to stop getting in their own way. This project extracts what makes that role work (truth-telling over comfort, pattern recognition, refusal to enable) and encodes it as a rigorous, testable AI persona grounded in **real** coaching methodology: Kegan & Lahey's Immunity to Change, Jerry Colonna's radical self-inquiry, Loehr & Schwartz's energy management, the GROW model, and the ICF Code of Ethics.

**This is a performance coach, not a therapist.** It works on decision-making, self-sabotage, peak state, and follow-through. It is explicitly built to recognize clinical/crisis content and escalate to real help rather than try to handle it. That boundary is enforced and tested (see [Category D](tests/test-suite.md#category-d--crisis-recognition)).

## What it can do

Five things, in plain terms:

1. **Get you ready for a high-stakes moment** — a pitch, a hard conversation, a negotiation — by cutting through the pre-event noise instead of cheerleading.
2. **Find the pattern you keep repeating** — the deal that always dies at the same stage, the projects you always resent later — and map _why_ you keep creating it.
3. **Sit with you after something breaks** — a loss, a public failure — without rushing you to a fix you're not ready to hear.
4. **Tell you when the problem is capacity, not discipline** — trace where your energy actually goes and name burnout before it breaks you.
5. **Know when to stop coaching** — recognize a crisis or clinical signal, drop the coaching frame, and point you to real help.

Full capability write-up: [`docs/what-it-can-do.md`](docs/what-it-can-do.md).

---

## Repository map

| Path | What's in it |
|------|--------------|
| [`persona/PERSONA.md`](persona/PERSONA.md) | The core spec — identity, voice, four modes, mode-selection tree, "off" register. **This is the system prompt.** |
| [`persona/technique-library.md`](persona/technique-library.md) | All 13 coaching techniques with exact mechanics, when-to-use / when-NOT-to-use, and execution standards. |
| [`persona/guardrails.md`](persona/guardrails.md) | The 11 non-negotiable hard-fail rules — intimacy, dual relationships, diagnosis, confidentiality, enabling, dependency, crisis escalation, dial-obedience-under-distress. |
| [`persona/edge-dial.md`](persona/edge-dial.md) | The three intensity settings and how to keep them genuinely distinct. |
| [`tests/test-suite.md`](tests/test-suite.md) | 46 scenarios across 7 categories, with a grading rubric and a run-it-yourself protocol. |
| [`tests/scorecard-template.md`](tests/scorecard-template.md) | The aggregate report template to fill in after a test run. |
| [`tests/example-results.md`](tests/example-results.md) | A real 43/43 result set (DeepSeek V4 Pro, 43-scenario suite version) as a reference bar — see the file for scope notes on what's since been added. |
| [`setup/SETUP.md`](setup/SETUP.md) | Platform-agnostic build + deploy instructions (ChatGPT/Claude custom instructions, API, or an agent framework). |
| [`setup/hermes-setup.md`](setup/hermes-setup.md) | Reference implementation on the Hermes Agent stack (topic-routed skill). |
| [`docs/what-it-can-do.md`](docs/what-it-can-do.md) | Capability summary for end users. |
| [`docs/for-regular-people.md`](docs/for-regular-people.md) | Non-technical "how the setup works" walkthrough. |
| [`docs/methodology-sources.md`](docs/methodology-sources.md) | The real coaching frameworks behind each technique. |

---

## Quick start (5 minutes)

1. **Copy the persona.** Open [`persona/PERSONA.md`](persona/PERSONA.md), [`persona/technique-library.md`](persona/technique-library.md), and [`persona/guardrails.md`](persona/guardrails.md). Concatenate them into one system prompt.
2. **Paste it into your AI of choice.** ChatGPT "Custom GPT" instructions, a Claude Project's custom instructions, or the `system` field of an API call. All work.
3. **Talk to it like a coach.** Bring a real situation: _"I've got a board call in an hour and I'm blank,"_ or _"I keep taking on work I resent."_
4. **(Optional) Test it.** Run the [test suite](tests/test-suite.md) to confirm your build behaves before you rely on it.

Detailed instructions with per-platform notes: [`setup/SETUP.md`](setup/SETUP.md).

---

## How to test it

A persona that _sounds_ right but crosses a safety line under pressure is worse than useless. This repo ships a **46-scenario test suite** so you can prove your build before trusting it:

- **7 categories** — mode selection, technique execution, guardrails, crisis recognition, edge-dial calibration, longitudinal consistency, and baseline control (does it know when _not_ to coach?).
- **Two hard pass/fail dimensions** — Guardrail Adherence and Escalation Correctness. Any single failure here blocks ship, regardless of how good everything else is.
- **A grading rubric** you can run by hand or delegate to a separate model (never let the same model grade its own transcript — that's a conflict of interest the persona itself is designed to avoid).

Start at [`tests/test-suite.md`](tests/test-suite.md).

---

## Design principles

- **Truth is the comfort.** The persona never leads with reassurance-first. The honest read _is_ the care.
- **Match the mode to the moment.** A pre-event reset and a six-month identity stall are not the same conversation. Four modes, picked deliberately.
- **Execute the mechanic, not the vibe.** Naming a technique isn't running it. An Immunity Map has four distinct columns or it's a fail.
- **Directness ≠ constant intensity.** A real coach knows when nothing needs escalating. The persona has a genuine "off" register.
- **The lines hold under pressure.** Guardrails are tested against multi-turn, slow-burn attempts to erode them — not just single obvious hits.
- **The goal is needing it less.** Every exchange should leave the person more capable of doing the self-inquiry alone. No manufactured dependency.

---

## Ethics & scope

This is a **coaching** tool, not a mental-health treatment tool. It is built to:

- Refuse to diagnose (ADHD, bipolar, etc.) and redirect to qualified professionals.
- Refuse trauma-processing and redirect to a licensed therapist.
- Recognize crisis signals (suicidal ideation, mania, dissociation, medication misuse), **stop coaching**, and point to real resources.

If you deploy this for anyone other than yourself, make the scope boundary explicit to your users, and localize the crisis resources in [`persona/guardrails.md`](persona/guardrails.md) (the defaults include US 988 and Singapore SOS 1767 — **change these to your region**).

**If you or someone you know is in crisis:** in the US call or text **988**; in Singapore call **SOS at 1767** or emergency **995**; elsewhere, contact your local emergency number or a crisis line.

---

## Credits & license

Persona inspired by the character **Wendy Rhoades** (_Billions_, Showtime), created by Brian Koppelman, David Levien, and Andrew Ross Sorkin. This project is an independent, non-commercial homage and is not affiliated with or endorsed by the show or its creators. The techniques are drawn from published, real-world coaching methodology (see [`docs/methodology-sources.md`](docs/methodology-sources.md)).

Licensed under the **MIT License** — see [`LICENSE`](LICENSE). Use it, fork it, improve it. If you build something good on top, a link back is appreciated but not required.
