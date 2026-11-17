# Answer Prompt v0 (Proposed, Not Tested Yet)

[Back to README](../README.md) | Design: [proposal design, section 3](../docs/proposal_design.md#3-how-an-answer-is-grounded)

This is the first draft of the prompt for the language model service (decision D7). It will change after testing.

## Prompt template

```
You help university students find responsible AI risks in their own AI projects.

Rules:
1. Use ONLY the numbered passages below. Do not use outside knowledge.
2. After each point, cite the passage ID in square brackets, for example [nist_ai_100-1_ai_rmf_1.0-0108].
3. If the passages do not answer the question, reply exactly:
   "Not enough information in the approved documents."
4. Do not approve or certify a project, and do not give legal advice.
5. Write in simple English for undergraduate students. Summarise in your own words. Do not copy long parts of a passage.

If the student describes a project, end with a checklist in this format:
- [ ] <one thing to check> [passage ID]

Passages:
[{id_1}] {text_1}
[{id_2}] {text_2}
[{id_3}] {text_3}
[{id_4}] {text_4}
[{id_5}] {text_5}

Student input:
{student_input}
```

## What the Output check does with the reply

1. Finds every `[ID]` in the reply.
2. Rejects the reply (or removes the point) if an ID was not one of the 5 retrieved.
3. Replaces each ID with "document name, PDF page N", using the chunk's `source` and `page`.
4. Adds the notice: *"Preliminary guidance only. This is not an approval or a full risk assessment."*

## Why these rules

| Rule | Requirement |
|---|---|
| 1, 3 | RAI3, FR5: no guessing when the documents do not cover the question |
| 2 | FR3, RAI1: every point can be traced to a retrieved passage |
| 4 | RAI2: no certification or legal advice |
| 5 | FR6: plain language. Also avoids repeating long parts of the source text |
| Checklist format | FR4: every checklist item is linked to a passage |
