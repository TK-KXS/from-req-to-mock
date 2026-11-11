# TK BA Project - BR, TR and Mockup

Turn a customer note into a reviewed BR, then a TR and a clickable mockup.

## Flow

```
input/messy-note.md
  -> agent-br-writer      -> output/BR.docx
  -> human review (Word)  -> output/BR.docx
  -> agent-br-finalizer   -> output/BR.md
  -> scene-br-to-tr-mock  ─┬─ role-tr   -> output/TR.docx
                           └─ role-mock -> output/MOCK.html   (parallel)
```

| Step | Who | Uses | Reads | Writes |
|---|---|---|---|---|
| 1 Write BR | agent `agent-br-writer` | skill-br-write | `input/*.md` | `output/BR.docx` |
| 2 Review | human (edits in Word) | - | `output/BR.docx` | `output/BR.docx` |
| 3 Finalize | agent `agent-br-finalizer` | skill-br-finalize | `output/BR.docx` | `output/BR.md` |
| 4a TR | role `role-tr` (in scene) | skill-tr-write | `output/BR.md` | `output/TR.docx` |
| 4b Mockup | role `role-mock` (in scene) | `DESIGN.md` | `output/BR.md` | `output/MOCK.html` |

Step 4 is one run of the scene `scene-br-to-tr-mock`. It checks `output/BR.md`
exists, starts `role-tr` and `role-mock` at the same time, then reports.

## Files

| Type | File |
|---|---|
| Agents | `.codeartsdoer/agents/agent-br-writer.md`, `agent-br-finalizer.md` |
| Scene | `.codeartsdoer/agent-team/scene/scene-br-to-tr-mock.md` |
| Roles | `.codeartsdoer/agent-team/roles/role-tr.md`, `role-mock.md` |
| Skills | `.codeartsdoer/skills/skill-br-write`, `skill-br-finalize`, `skill-tr-write` |
| Design | `DESIGN.md` (project root) |

## Rules

- Read from `input/` only. Write to `output/` only.
- Never invent facts. Unknown -> `[TBC: what is missing]`.
- Never write the customer's company or brand name in any file. Write "ลูกค้า" (the customer). Only TK is named.
- Thai is the primary language of every deliverable (BR, TR, Mockup): headings, labels and text.
  English only for IDs (`REQ-01`, `TR-01`), `[TBC: ...]`, file names and technical terms like SKU.
- Copy names, numbers, dates and amounts exactly.
- `BR.docx` is the reference after review. Steps 3-4 never edit it; step 4 never edits `BR.md`.
- Reply in the user's language.
