---
name: ntpc-iep-review
description: Review New Taipei City special education IEP records in the SEE system and produce concise correction opinions. Use when the user is reviewing IEPApprove pages, asks to read a student's IEP section by section, compare it with New Taipei IEP formatting rules, record findings, update an IEP review summary, or export a Word review document.
---

# New Taipei IEP Review

Use this skill to review one student's New Taipei City IEP in the online SEE approval system and produce concise, school-administration-friendly correction opinions.

## Workflow

1. **Prepare workspace files**
   - Ensure the project has `99_輸出/` for review outputs.
   - If available, use the local project checklist and New Taipei IEP format notes.
   - Create or update a per-student Markdown record named like `99_輸出/<student>_IEP審查初讀紀錄.md`.

2. **Read the online IEP section by section**
   - Use the user's currently opened SEE system page when authenticated browser access is needed.
   - Prefer direct UI/browser operation to click IEP section tabs and controls yourself. Use screenshots, accessibility/UI automation, browser control, or Computer Use when available.
   - Prefer copying visible page text with `Ctrl+A` / `Ctrl+C` after each IEP tab is selected.
   - Ask the user to manually click the next tab only after direct UI operation fails or risks changing approval state.
   - Save copied text for traceability. **The `00基本資料` capture is not optional** — it is the source of truth for grade, class, teacher, and guardian fields that every other section's narrative gets cross-checked against. Skipping it and relying on a remembered value has produced a real false-positive correction in production (a "grade level doesn't match the narrative" finding that turned out to be based on a value nobody had actually captured). Capture all seven files every time:
     - `99_輸出/<student>_00基本資料_頁面文字.txt`
     - `99_輸出/<student>_01能力現況需求評估_頁面文字.txt`
     - `99_輸出/<student>_02特殊教育相關服務支持策略_頁面文字.txt`
     - `99_輸出/<student>_03學年與學期教育目標_頁面文字.txt`
     - `99_輸出/<student>_04行為功能介入方案_頁面文字.txt`
     - `99_輸出/<student>_05轉銜輔導及服務內容_頁面文字.txt`
     - `99_輸出/<student>_06相關測驗_頁面文字.txt`
   - **The `1-1.普通班學生接受特殊教育課程` course-hours table needs its own deliberate capture, separate from the section-02 text dump.** It loads by AJAX after the tab renders, so a single early read routinely returns a transient empty/`0` state — this produced 16 false "課程節數為 0" corrections in one batch, every one of which had to be retracted. Confirm which `1.特殊教育課程` radio is selected, expand the table, read it a second time once other tables on the same tab show real rows, and save the capture. Do not write any course-hours finding without it; see the Verification Requirement in `references/review-rules.md`.

3. **Review required sections**
   - 學生基本資料 and 參與訂定人員
   - 一、能力現況、家庭狀況及需求評估
   - 二、特殊教育、相關服務及支持策略
   - 三、學年與學期教育目標
   - 四、行為功能介入方案
   - 五、轉銜輔導及服務內容
   - 相關測驗

4. **Apply review rules**
   - Read `references/review-rules.md` before finalizing comments — Part 1 is the per-section content checklist (what "complete" looks like for each of the 7 IEP sections), Part 2 is the decision/verification/tone rules (when and how to turn an observation into a finding).
   - Do not treat system-inserted placeholder text as a correction if the user says it is built into the system.
   - Do not flag `授課教師：待定` when the timing makes teacher assignment unavailable.
   - Distinguish true correction items from optional reminders or future confirmations.
   - Never report 特殊教育課程節數 as `0` — or as inconsistent with the needs summary — until the capture from step 2 confirms it. When the table does hold real hours, read 抽離 vs 外加 from the 特教課程使用時間 columns (Part 2) before judging whether it matches the narrative, and record a match in 可保留內容 rather than staying silent.
   - Before writing any finding that claims two sections disagree, follow the Verification Requirement in Part 2: confirm both sides against the actual saved capture files from this same review, not from memory of an earlier pass. If the user disputes a finding later, re-open the original capture first — don't reflexively retract, and don't defend without checking.
   - When goals/transition text look identical across two students, verify by opening the other student's section directly (Part 1's copy-paste check), then judge whether the *content* even fits this student's disability category (Part 2's differentiation table) — a duplicate that happens to also be the wrong category of goal is the stronger finding.
   - Apply the Logic Alignment Check (Part 2): trace every Section 1 need through Section 2/3/4 and flag gaps in the chain, not just blank fields.
   - Write every finding — not only the correction list — in the confirming/inquiring tone described in Part 2, since these are read directly by the teacher who wrote the IEP.

5. **Write the review conclusion**
   - Use `補正後再核准` when the IEP is substantively complete but has specific text or consistency corrections.
   - Use short, actionable correction points that can be pasted into the approval system.
   - Keep the main correction list as numbered prose, not a table, unless the user asks for a table.
   - Keep privacy in mind: do not repeat full ID numbers in final-facing summaries unless necessary.

6. **Update outputs**
   - Update `99_輸出/IEP審查彙整表.csv` with status and concise correction highlights.
   - Export a Word file using `scripts/create_review_docx.py` when the user asks for Word output.
   - For per-student organization, place all student-specific outputs inside `99_輸出/<student>/` and keep the summary CSV at `99_輸出/`.

## Word Output

Use the bundled script for repeatable DOCX output:

```powershell
$py = "C:\Users\user\.cache\codex-runtimes\codex-primary-runtime\dependencies\python\python.exe"
& $py "C:\Users\user\.codex\skills\ntpc-iep-review\scripts\create_review_docx.py" `
  --student "學生姓名" `
  --year "115" `
  --class "6年1班" `
  --result "補正後再核准" `
  --corrections "請補列參與 IEP 訂定之相關人員。" `
  --corrections "請補充作業調整與評量調整內容，使其與需求評估一致。" `
  --keep "授課教師待定因排課尚未確定，不列為補正意見。" `
  --out "99_輸出\學生姓名\學生姓名_IEP審查意見.docx"
```

After creating a DOCX, verify structurally at minimum. When possible, render to PDF/PNG and inspect the output for readable Chinese text, no `???` replacement characters, and no layout clipping or overlap.

## Output Shape

The final review should include:

- 學生資訊
- 審查結論
- 主要補正意見 as numbered prose
- 可保留內容
- 可貼入系統的審查意見, only when useful

Keep comments clear, respectful, and school-administration friendly (see Opinion Tone in `references/review-rules.md`). Avoid overloading the document with raw copied IEP data.

## Logic Alignment Check

See "Logic Alignment Check" and the per-section checklist in `references/review-rules.md` Part 1/Part 2 — the full content standard (what each of the 7 sections must contain, and how a need in Section 1 must trace through Section 2/3/4) lives there, not here, to avoid the two files drifting out of sync.
