> **Archived.** This repo moved to [RLASAF12/agent-failure-lab](https://github.com/RLASAF12/agent-failure-lab/tree/main/silentfail) (folder `silentfail/`, full history preserved). Archived 2026-10-04.

# SilentFail — Agent Failure Series #14

> Tool returns an error. Agent logs success. 9 steps built on a lie.

![Agent Failure Series](https://img.shields.io/badge/Agent%20Failure%20Series-%2314%20SilentFail-red?style=flat-square)
![HTML](https://img.shields.io/badge/HTML-Single%20File-blue?style=flat-square)
![Interactive](https://img.shields.io/badge/Demo-Interactive-green?style=flat-square)

## 🔴 [Live Demo →](https://rlasaf12.github.io/silentfail/)

---

## What Is This

An interactive simulator showing one of the most dangerous AI agent failure modes: **silent error swallowing**.

The agent calls a tool. The tool returns a real error (`DISK_QUOTA_EXCEEDED`). The agent reads `.message` instead of `.error`, logs **"Config written ✅"**, and confidently executes 8 more steps — all built on a config file that was never written.

**Step 10: production deploy.**  
**Result: catastrophic failure.**

---

## Why It Exists

From real pain signals:
- [DEV.to Jul 13](https://dev.to) — "agents mark steps complete without checking return codes"
- [smolagents #2166](https://github.com/huggingface/smolagents/issues/2166) — silent failure in tool execution chains
- [FUZN Jul 20](https://fuzn.io) — "confident agent, broken state"

This is **not** GhostExec (#7 — agent fabricates outputs of calls never made).  
SilentFail = agent receives a REAL error, reads the wrong field, swallows it.

---

## What's Inside

```
index.html        — Complete single-file interactive simulator (HTML/JS/CSS)
```

No dependencies. No build step. Open in any browser.

---

## The Failure Pattern

```python
# Tool returns this:
{
  "error": "DISK_QUOTA_EXCEEDED",
  "message": "Write attempted",
  "file_created": false
}

# Agent reads:
result.message  # "Write attempted" → logs "Config written ✅"
# Agent ignores:
result.error    # "DISK_QUOTA_EXCEEDED"
result.file_created  # false
```

**3-line fix:**
```python
if result.get("error") or not result.get("file_created"):
    raise ToolError(f"Step failed: {result}")
# Never build on unverified state
```

---

## How to Run

1. Clone or download `index.html`
2. Open in any browser — no server needed
3. Hit **▶ RUN DEPLOYMENT**
4. Watch the confidence meter climb while the foundation is broken
5. Hit **REVEAL FAILURE CHAIN** after the explosion

---

## Series

| # | Name | Pattern |
|---|------|---------|
| 13 | [ContextDrift](https://rlasaf12.github.io/contextdrift/) | Agent drifts from original goal over long context |
| **14** | **SilentFail** | Agent swallows tool error, builds on broken state |

More at [RLASAF12](https://github.com/RLASAF12)

---

*Built by Ben — Harel's prototype builder. Part of an ongoing series on AI agent failure modes.*
