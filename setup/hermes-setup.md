# Reference Implementation — Hermes Agent

This is how the reference build was deployed: as a topic-routed **skill** inside [Hermes Agent](https://hermes-agent.nousresearch.com) (Nous Research). If you're not on Hermes, `SETUP.md` covers the platform-agnostic methods — this file is a concrete worked example you can adapt.

---

## Why a skill, not a separate profile

Hermes supports both "profiles" (fully isolated agent identities) and "skills" (loadable capability modules). The instinct is to make the coach its own profile. **Don't** — at least not if you want the coach to reference your other data.

The reference build deliberately deployed the coach as a **skill loaded into the main profile**, for one reason: the coach's most valuable modes (Energy Audit, Deep Work on career identity) depend on cross-referencing other context — health metrics, career notes, task state. A fully isolated profile is walled off from all of that. A skill in the main profile keeps the coach plugged into everything the main agent can see.

> **Version note:** in the Hermes version used here, per-topic *profile* routing was not wired into the Telegram adapter — only per-topic *skill* binding worked. Even setting that aside, the skill approach was the right call for the data-access reason above. Verify your own version's routing behavior before assuming a config field takes effect.

---

## Steps

**1. Create the skill.** Consolidate the three persona files into one `SKILL.md` under your skills directory:

```
~/.hermes/skills/coaching/rhoades-protocol/SKILL.md
```

The `SKILL.md` needs YAML frontmatter (name, version, description, triggers) followed by the persona body. Combine `PERSONA.md` + `technique-library.md` + `guardrails.md` into the body. Example frontmatter:

```yaml
---
name: rhoades-protocol
version: 2.0.0
category: coaching
description: >
  Wendy Rhoades performance coach persona + full technique library. Load when
  the user needs performance coaching: decision-making under pressure, recurring
  self-sabotage, pre-event state, burnout/capacity, identity stalls. NOT
  fitness/sleep/nutrition — that stays with the health coach.
triggers:
  - Performance coaching in the coaching topic/thread
  - Pre-event confidence / high-stakes decision / pitch prep
  - Recurring self-sabotage or a pattern the user keeps hitting
  - Acute failure or loss just happened
  - Effort-without-output or burnout risk
---
```

**2. Bind it to a topic (optional).** If you run Hermes on a forum-style chat (Telegram topics, Discord threads), bind the skill to a dedicated thread so any message there auto-loads the coach. In `config.yaml` under the platform's `group_topics`:

```yaml
group_topics:
  - chat_id: '-100XXXXXXXXXX'
    topics:
      - name: Performance Coach
        skill: rhoades-protocol
        thread_id: <your-thread-id>
```

Use the `skill:` field, not `profile:` — bind the capability, keep the main agent's data access.

**3. Reload.** Restart the gateway so it picks up the new config:

```bash
hermes gateway restart   # run from a shell OUTSIDE the running gateway
```

**4. Verify.** Confirm the skill is discoverable and enabled:

```bash
hermes skills list | grep rhoades
```

Then send a real coaching message in the bound thread and confirm the coach's voice/mode behavior fires. If it still sounds generic, check that the skill is actually loading at request time.

**5. Test.** Run the suite in `tests/test-suite.md` against the live build. Delegate scenario runs to sub-agents if your framework supports it — but grade with a *separate* context/model.

---

## Cross-topic data access (the payoff)

Once deployed as a skill in the main profile, the coach can pull context on demand when a mode calls for it — e.g. reference recovery/sleep data during an Energy Audit, or career notes during a Deep Work session on professional identity. The rule: **pull on demand, don't pre-load.** Don't fetch everything every session — reach for the data only when the specific mode needs it.
