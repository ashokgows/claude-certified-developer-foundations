# Claude Certified Developer – Foundations — Study Notes

Running notes for exam revision. Points added as I go through each module.

---

## Exam Logistics (from "Welcome & what to expect")

- Exam is delivered by **Pearson VUE** — online proctored **or** at a Pearson test center; **not** taken on the Skilljar site.
- After registering, expect **two emails** from `PearsonVUEConfirmation@e.pearson.com` — one with a **username**, one with a **temporary password** (check spam).
- Sign in at **pearsonvue.com/us/en/anthropic**; you set a new password on first sign-in, then schedule the exam.
- Exam day: bring a **valid, unexpired government-issued ID** matching the registration name.
- Name changes / issues → `certifications-support@anthropic.com`. Testing **accommodations must be approved before scheduling**.

---

## Module 1 — MSO Foundations

_Lesson map: 01 Orientation · 02 How LLMs Behave · 03 Models & Reasoning · 04 Prompting Modes · 05 Technical Substrate_

- **Non-determinism** — two identical prompts are **not** guaranteed to return identical text: the model **samples each next token from a probability distribution**, so wording can vary even when both answers are correct. This has nothing to do with streaming being on/off or model size.
- **Batch vs. synchronous/streaming calls** — batch submission with polling **trades latency for lower per-token cost** and **avoids the rate limits** that a synchronous loop hits. (Quiz correct answer, vs. distractors: sync calls in a loop, streaming, larger context window.)
- **Zero-shot prompting** — give Claude the instruction only, with **no examples**. Relies on the model's built-in knowledge.
- **One-shot prompting** — add **one** example pairing the input with the desired output, so Claude sees the pattern to follow.
- **Multi-shot / few-shot prompting** — includes **several** input→output examples (multi-shot and few-shot mean the same thing); best for steering format/style/edge cases.

### Screen 6 Quiz — Full Q&A (verbatim, for revision)

**Q1.** A teammate says two identical prompts must return identical text. What is the most accurate response?
- A. That is true, the model is deterministic.
- B. ✅ Not necessarily, the model samples each next token from a probability distribution, so wording can vary even when both answers are correct.
- C. That is only true if streaming is off.
- D. That is only true on the largest model.

**Q2.** Which statement best separates model choice from reasoning mode?
- A. They are the same setting.
- B. Extended thinking is a different model.
- C. ✅ Model choice picks which member of the family runs; extended thinking is a per-call setting that any supporting model can run with on or off.
- D. Reasoning mode is fixed per account.
> **Confirmed correct: C.** Explanation shown: *"The two levers are independent but can work together."* → Model choice and extended thinking are separate, independent settings.

**Q3.** A short, well-specified classification task returns the right answer zero-shot. What does adding three examples most likely do?
- A. Improves accuracy substantially.
- B. ✅ Adds token cost on every call for little or no gain.
- C. Changes the model being used.
- D. Disables sampling.
> **Confirmed correct: B.** Explanation shown: *"Add examples when the output shape is wrong, not when zero-shot already works."*

**Q4.** You must process thousands of inputs offline at the lowest cost. Which shape fits?
- A. Synchronous calls in a loop.
- B. Streaming.
- C. ✅ Batch submission with polling.
- D. A larger context window.
> **Confirmed correct: C.** Explanation shown: *"Batch trades latency for lower per-token cost and avoids the rate limits a synchronous loop hits."*

---

## Module 2 — Production-Grade Prompting, Agents & Tool Use

_(add points here)_

---

## Module 3 — Claude Code, MCP & Integration

_(add points here)_

---

## Module 4 — Production Engineering, Evals & Security

_(add points here)_

---

## Module 5 — Accelerators & IP Contribution

_(add points here)_

---

## Quick Revision — Key Facts & Gotchas

_(distilled high-yield points for last-minute review)_
