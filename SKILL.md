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
   - Save copied text for traceability:
     - `99_輸出/<student>_00基本資料_頁面文字.txt`
     - `99_輸出/<student>_01能力現況需求評估_頁面文字.txt`
     - `99_輸出/<student>_02特殊教育相關服務支持策略_頁面文字.txt`
     - `99_輸出/<student>_03學年與學期教育目標_頁面文字.txt`
     - `99_輸出/<student>_04行為功能介入方案_頁面文字.txt`
     - `99_輸出/<student>_05轉銜輔導及服務內容_頁面文字.txt`
     - `99_輸出/<student>_06相關測驗_頁面文字.txt`

3. **Review required sections**
   - 學生基本資料 and 參與訂定人員
   - 一、能力現況、家庭狀況及需求評估
   - 二、特殊教育、相關服務及支持策略
   - 三、學年與學期教育目標
   - 四、行為功能介入方案
   - 五、轉銜輔導及服務內容
   - 相關測驗

4. **Apply review rules**
   - Read `references/review-rules.md` before finalizing comments.
   - Do not treat system-inserted placeholder text as a correction if the user says it is built into the system.
   - Do not flag `授課教師：待定` when the timing makes teacher assignment unavailable.
   - Distinguish true correction items from optional reminders or future confirmations.

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

Keep comments direct and school-administration friendly. Avoid overloading the document with raw copied IEP data.

## Logic Alignment Check

When reviewing an IEP, do not only check whether required fields are filled. Always check whether these three sections form a coherent logic chain:

1. Ability/current-status, family situation, and needs assessment.
2. Special education, related services, and support strategies.
3. Annual and semester education goals.

If the ability/current-status section describes needs in cognition and intelligence, health and sensory, perception and motor, language and communication, self-care, emotion and social interaction, or basic learning skills and subject performance, the later support strategies and annual/semester goals should include corresponding planning.

Treat it as a correction when an identified need has no matching support/service, annual goal, semester goal, evaluation method, or instructional plan. The review should trace the IEP chain as:

Need -> Support/service -> Annual goal -> Semester goal/evaluation.
