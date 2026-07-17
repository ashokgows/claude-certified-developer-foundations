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

### Exercise: "Predict the behavior" — 4 scenarios (all confirmed correct)

**Each scenario names the ONE lever that controls the outcome. Memorize the lever per scenario:**

**S1 — Lever = sampling (temperature).** Classifier at temp 0 vs. high temp across repeated runs.
- ✅ **A:** Low temp concentrates probability on the most likely tokens → repeated runs return the same label far more consistently (**never guaranteed-deterministic, even at temp 0**). High temp spreads the distribution so wording *and even the chosen label* can vary. For a classifier, want low-temperature/repeatable.
- Note: on newest models, sampling params are omitted entirely; non-default values return an error; repeatability is managed through **prompt design**.

**S2 — Lever = prompting mode.** Zero-shot output keeps coming back in the wrong structure; switch to multi-shot.
- ✅ **B:** Adding 2–3 correct input→output examples shows the model the exact structure to match (fixes what instruction text didn't). Cost = extra tokens per call → add the **fewest** examples that make output reliable.
- Key myth-busters: examples are **not training** (don't change the model, not permanent) and do **not** lower cost — they sit in the prompt and add tokens every call.

**S3 — Lever = request shape.** Pipeline must process 50,000 documents overnight, no user waiting.
- ✅ **C:** **Message Batches API** — submit in a batch, poll for completion, accept longer latency for lower per-token cost. A sync loop hits rate limits at this volume; streaming buys nothing when no user is watching. → *Match request shape to whether a user is waiting.*

**S4 — Lever = context window (a fixed token budget).** Long multi-turn agent session whose window keeps filling.
- ✅ **B:** The context window is a **fixed token budget**; as history + tool results accumulate it fills. Two distinct failures:
  - An input **already oversized** → rejected with an **error before generation**.
  - A request that fits on input but **hits the ceiling mid-generation** → comes back **truncated** with stop reason `model_context_window_exceeded`.
  - The model does **NOT** silently drop the oldest turns. Fix = the app must **trim or summarize history**. Symptom: passes in testing, fails once inputs/turns grow.

#### Full verbatim Q&A (for self-testing)

**Scenario 1.** Consider a classification task run at temperature 0 versus the same task run at a high temperature. Predict how the outputs differ across repeated runs.
- ✅ **A.** At a low temperature, the model concentrates probability on the most likely tokens, so repeated runs return the same label far more consistently, though never with guaranteed determinism, even at temperature 0. At a high temperature, the distribution spreads out, so wording and even the chosen label can vary. For a classifier you want the low-temperature, repeatable behavior.
- B. Both configurations return identical output every run, because temperature only affects response length, not which tokens are chosen.
- C. The high-temperature run is more accurate, because spreading the distribution lets the model consider more of the correct answers.
- D. Temperature has no effect on a classification task, because classification always returns a fixed label regardless of sampling.

**Scenario 2.** Consider a task that keeps returning output in the wrong structure under a zero-shot prompt. Predict what changes if you switch to multi-shot.
- A. Switching to multi-shot retrains the model on the new structure, so the change is permanent across every future call once the examples are sent.
- ✅ **B.** Adding two or three correct input-output examples shows the model the exact structure to match, which usually fixes a structure problem that more instruction text did not. The cost is extra tokens on every call, so add the fewest examples that make the output reliable.
- C. Multi-shot will not help a structure problem; only raising the temperature changes the shape of the output.
- D. Multi-shot lowers the token cost per call, because examples let the model produce shorter responses.

**Scenario 3.** Consider a pipeline that must process 50,000 documents overnight with no user waiting. Predict which request shape fits and why.
- A. A synchronous loop fits best, because calling the API once per document is the simplest pattern and avoids the overhead of submitting a batch.
- B. Streaming fits best, because sending the response in pieces lets the pipeline start processing each document sooner.
- ✅ **C.** The batch pattern fits: submit the requests in a batch and poll for completion, accepting longer latency for a lower per-token cost. A synchronous loop would hit rate limits and tie up the application, and streaming buys nothing because no user is watching.
- D. A larger context window fits best, because fitting all 50,000 documents into one request avoids making repeated calls.

**Scenario 4.** Consider a long multi-turn agent session whose context window keeps filling. Predict the symptoms and name the budget at fault.
- A. The model silently drops the oldest turns to make room, so the session continues but quietly loses early context without any error.
- ✅ **B.** The context window is a fixed token budget; as history and tool results accumulate it fills. An input that is already oversized is rejected with an error before generation, while a request that fits on input but reaches the ceiling mid-generation comes back with truncated output and a `model_context_window_exceeded` stop reason. The symptom is a session that ran fine in testing failing once inputs grow, which is why the application must trim or summarize history.
- C. The symptom is slower sampling, and the budget at fault is the temperature setting, which must be lowered as the session grows.
- D. There is no fixed budget; the window expands automatically to hold whatever history accumulates, so a long session never fails for this reason.

---

## Module 2 — Production-Grade Prompting, Agents & Tool Use

### Pre-read (concepts to watch for)

**A. Production prompting & system prompts**
- **System prompt** = persistent role/instructions framing every turn (who Claude is, rules, format). Separate from the user message.
- **Structure that works:** role → task → context/data → rules → output format → (examples if needed). Put long documents **before** the question; use **XML tags** (`<document>`, `<instructions>`) to delimit sections — Claude respects them.
- **Be explicit & positive** — say what *to* do. Ambiguity is the #1 cause of bad output.
- **Prefill** the assistant turn to force format (start with `{` for JSON, `[` for a list).
- **Chain-of-thought** — ask it to reason step-by-step (or a `<thinking>` section) for complex tasks; strip from final answer if user shouldn't see it.

**B. Tool use (function calling)**
- **Loop:** define tools → Claude returns a `tool_use` block (JSON input) → *your code* runs the real function → you send back a `tool_result` → Claude continues. **Claude never executes anything itself** — it only asks.
- **Tool schema** = name + **description** + `input_schema` (JSON Schema). The **description is the most important part** — how Claude knows *when/how* to use it. Be verbose.
- **`tool_choice`:** `auto` (model decides), `any` (must call some tool), `tool` (force a specific one), `none`.
- **Parallel tool use** — Claude can request multiple tools in one turn. On failure, return `tool_result` with `is_error: true` so it can recover.

**C. Agents & context management**
- **Agent** = a loop where Claude uses tools repeatedly, observing results and deciding next steps until done (vs. single request/response).
- **Context management** (recurring exam theme) — finite token budget. Strategies: trim/summarize old turns, keep tool results lean, retrieve only what's relevant, track token usage. Ties back to M1's context-window lesson.
- **Extended thinking in agents** — use for planning multi-step tasks; interleave thinking between tool calls for harder problems.

> **Exam instinct:** most M2 questions are "given this situation, what's the right prompting/tool/agent design choice?" — same judgment pattern as M1's levers.

### Notes from videos

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
