# ntpc-iep-review skill

Codex skill for reviewing New Taipei City SEE system IEP records and producing concise correction opinions.

## Contents

- `SKILL.md`: workflow and usage instructions
- `references/review-rules.md`: **the full review standard** — a per-section content checklist plus decision/verification/tone rules (see summary below)
- `scripts/create_review_docx.py`: helper for generating Word review documents
- `agents/openai.yaml`: skill metadata

## Review Standards Summary

The full standard lives in `references/review-rules.md`. It has two parts:

**Part 1 — per-section content checklist.** For each of the 7 required IEP sections (基本資料, 能力現況/需求評估, 特殊教育與支持策略, 學年學期教育目標, 行為功能介入方案, 轉銜輔導, 相關測驗), it defines what "complete" looks like — e.g. the 能力現況 section needs current-status + need for all seven ability dimensions, 課程節數 must match the 需求評估摘要's 抽離/外加 model, every priority need in Section 1 must map to a goal in Section 3, transition planning needs distinct (non-templated) text across its five service areas, and so on. The core discipline running through it is the **Logic Alignment Check**: Need (Section 1) → Support/service (Section 2) → Annual goal (Section 3) → Semester goal/evaluation — a need with nothing downstream is a correction even if every field is technically "filled."

**Part 2 — decision, verification, and tone rules.** Key points, learned the hard way across real review batches:

- **Verify before asserting.** Every "these two sections disagree" claim must trace to an actual saved capture of both sides, not a remembered summary. When a user disputes a finding, re-check the original capture first — the outcome differs depending on whether the finding was never backed by evidence (retract), was true when made and has since been fixed by the case manager (mark resolved, don't apologize), or still holds (restate with evidence).
- **Course-hours reads of "0" are unreliable by default.** The course table loads by AJAX after the tab renders; an early read routinely returns a false `0`. One batch produced 16 false "課程節數為 0" corrections from this alone. A four-step verification procedure (radio state → expand table → second read → cross-check against 抽離/外加 narrative) is mandatory before citing any course-hours number.
- **Read a checkbox's own qualifying text, not just its label.** A field like 電腦輔具 can carry a printed scope condition ("學生無法以手寫...") narrower than what its label suggests — a narrative mentioning a related device doesn't automatically mean the box should be checked.
- **身心障礙證明 and 特教資格鑑定 are independent systems.** A confirmed disability category with no disability certificate is normal, not an error — only flag it if the ability-status narrative itself claims the student already holds one.
- **Differentiate goals by disability category, not just by whether the text differs from another student's.** Two students with different categories (e.g. reading-comprehension SLD vs. autism) should have qualitatively different priority goals; a goal set that's well-written for one category can still be the wrong content for another.
- **Tone.** Every finding is phrased as a respectful confirmation request to the case manager who wrote the IEP (`提請確認...`), never a flat assertion of error (`...不一致，請修正`) — even for findings the reviewer is fairly confident about.

## Privacy note

This repository contains only the reusable skill. Student IEP records, review outputs, screenshots, and personal data are intentionally excluded.

## Basic DOCX test

```powershell
$py = "C:\Users\user\.cache\codex-runtimes\codex-primary-runtime\dependencies\python\python.exe"
& $py scripts\create_review_docx.py `
  --student "測試學生" `
  --year "115" `
  --class "6年1班" `
  --result "補正後再核准" `
  --corrections "請補列參與訂定人員。" `
  --out test.docx
```
