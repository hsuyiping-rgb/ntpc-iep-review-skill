# New Taipei IEP Review Rules

Use these rules when deciding whether an item is a correction, a reminder, or acceptable.

## Correction Items

Flag as a correction only after the Verification Requirement below has been satisfied. When it is:

- A student grade, school year, service period, course stage, or section narrative is internally inconsistent.
- A service or adjustment described in one section is missing or contradicted in another.
- A field explanation appears copied into the wrong adjustment category.
- An annual/semester goal's success criteria are not observable or measurable enough to track (e.g. "不出現明顯結巴" with no example of what counts) — but note this is not a correction if the goal already anchors the criterion with a concrete example or checklist.
- A required section is blank when it applies to the student's grade or service needs.
- For graduating grades, transfer planning is incomplete or lacks concrete next-stage supports.
- Related tests are referenced in narratives with specific findings, but the ability/needs-assessment section shows *no* test data anywhere and neither does the related-test tab.

Do NOT flag from wording alone:

- A goal saying the student will "independently" complete something (e.g. "獨立完成學科測驗") when the same sentence already names the accommodation strategy being used (e.g. "運用『報讀』與『口頭回答』的評量調整策略，獨立完成學科測驗"). Here "independently" means the student initiates/applies the accommodation without being prompted each time — it is not a contradiction with the accommodation existing. Only flag this pattern when the goal text implies the accommodation is being *removed or bypassed*, not merely used.
- A support-service checkbox left unchecked when the checkbox has its own printed qualifying condition (a tooltip, an asterisked note, or parenthetical scope text) that the student's situation doesn't actually meet — even though a narrative elsewhere describes something that sounds related. Example: 教育及運動輔具服務 → 電腦輔具 is marked with "*學生無法以手寫或使用一般鍵盤滑鼠輸入輸出者，請勾選" (check only if the student cannot handwrite or use a keyboard/mouse at all). A student who handwrites with difficulty and uses a tablet as a compensatory strategy does not meet that scope, so leaving it unchecked is a defensible reading, not a gap. Before flagging any checkbox as "should have been checked," read that field's own qualifying text — not just its label — and check it against the student's actual documented capability, not just whether a related-sounding device or strategy was mentioned somewhere.

## Usually Acceptable

Do not flag by default:

- `授課教師：待定` before the schedule or staffing can reasonably be finalized.
- System-built placeholder text, if the user confirms the system inserts it and the teacher cannot remove it.
- Transfer planning for non-graduating grades, unless the system requires a short not-applicable explanation.
- No behavior intervention plan when the record describes mild task avoidance, anxiety, or attention issues that are already handled through learning supports and do not require behavior function analysis.
- The related-test tab showing only category headers with no entries, if the ability/needs-assessment section already documents the specific test names, dates, scores, and interpretation for that student. The data exists and is reviewable; it is simply filed under a different tab. Only flag the related-test tab itself when neither location has the underlying test data.
- 身心障礙證明 (disability certificate) showing "無" even though 鑑輔會鑑定紀錄 confirms a disability category (e.g. 自閉症, 學習障礙). These are two independent systems run by different government bodies: the 鑑輔會/教育局 identification determines special-education eligibility and service category, while 身心障礙證明 is a separate social-welfare certification issued by 衛生局/社會局 after its own medical + needs assessment, applied for separately by the family. Neither one automatically triggers the other, so a confirmed special-education category with no disability certificate is a normal, legitimate combination — not a data error. Only flag this pair when the ability/needs-assessment narrative *itself* states the student already holds a disability certificate (or a specific grade of it) while the checkbox says 無 — that is a same-document contradiction, not a cross-system expectation.

## Verification Requirement (do this before writing any "inconsistency" finding)

A claim that two parts of the record disagree (grade vs. narrative, diagnosis vs. category, course hours vs. needs summary, exam service vs. transfer notes, etc.) must be traceable to the actual saved capture of **both** sides — not to a remembered summary from an earlier pass, and not to an assumption about what a field "should" say.

- If a required per-tab text capture (see SKILL.md step 2, including the basic-data tab) was skipped for this student, capture it now before asserting anything that depends on it. Do not publish a finding whose evidence is "I recall it being X" with no file to point to.
- When the user disputes a finding, re-open the original saved capture for that exact field before responding. There are three distinct outcomes, and they call for different fixes:
  1. The original claim had no real backing (misread, guessed, or the "prior" value was never actually captured) — retract it outright.
  2. The claim was accurate when captured, and the record has since been corrected by the case manager — confirm the diff between old and new, mark it resolved, and do not apologize for having raised it.
  3. The claim still holds against the freshest capture — restate it with the evidence cited.
- A diagnostic/clinical mismatch (e.g. 特教類別 says one condition, an ICD/ICF code on file says another) is not automatically an error — some categories are commonly comorbid. Before flagging it, check whether the two are a plausible co-occurring pair (research if uncertain) and phrase the finding as a request for the case manager to confirm the relationship, never as a flat assertion that one of the two is wrong.
- A garbled or "file not found" result from a shell tool when checking Chinese-named files/paths on Windows can be a tooling/encoding artifact, not proof the underlying file is broken — re-check with a different tool (e.g. PowerShell) before concluding data is corrupted.
- **Course-hours table reads of "0" are especially unreliable and must be double-checked before being used as evidence.** In one review batch, 16 out of 16 "特殊教育課程節數為 0" findings turned out to be false on re-verification — the course table (`1-1.普通班學生接受特殊教育課程`) is populated by an AJAX call after the tab renders, and reading it too early captures a transient `0`/empty state before the real numbers load. Before citing a "0 節" value in any finding: re-read the table a second time after confirming other dynamic content on the same tab has loaded (e.g. the exam-service or professional-service tables show real data, not just headers), or explicitly wait/re-click the tab and re-extract. Never treat a single read of that specific table as trustworthy on its own.
- **Concrete procedure before any course-hours finding.** A "0 節" claim is only citable once all four of these are on record:
  1. The `1.特殊教育課程` radio group itself — which of `1-1.普通班學生接受特殊教育課程` / `1-2.` / `1-3.` / `無` is actually selected. A page that renders `無`-looking emptiness while `1-1` is the selected radio is the exact failure mode behind the false positives; the radio state and the table body must agree before either is quoted.
  2. The `1-1` table body expanded, with its per-row columns read: 領域/科目, 特教課程提供節數, the three 特教課程使用時間 columns, 授課教師, 起訖日期.
  3. A second read of that table, per the rule above.
  4. A cross-check against what the narrative says the service should be (see the 抽離/外加 mapping below). Matching numbers are as reportable as mismatched ones — say so in 可保留內容.
- **Reading 抽離 vs 外加 from the 特教課程使用時間 columns.** The three sub-columns are not interchangeable and they tell you the service model, which is what narratives actually describe:
  - `原班原領域` > 0 → **抽離式**: the student leaves the general-education class during that same subject. This is what produces separate resource-room grades for that subject, so a narrative mentioning 光譜班/資源班 定期評量成績 for 國語/數學 should line up with 原班原領域 hours in those rows.
  - `領域學習總節數以外時段` > 0 → **外加式**: taught outside the regular subject timetable. Narratives phrase this as 外加課程 (社會技巧, 學習策略, etc.).
  - `原班其他領域` > 0 → pulled from a *different* subject's period; worth a confirmation request if the narrative does not explain the trade-off.
  Use this mapping before claiming a narrative and the course table disagree — a narrative saying "外加" against rows carrying only 原班原領域 hours (or the reverse) is a real, specific finding; a narrative saying "資源班課程" against any non-zero configuration is not.

## Common New Taipei IEP Checks

- Basic data should match the special education notification record.
- Ability status should describe strengths, limits, participation impact, and current-year evidence.
- Important needs should connect to courses, supports, evaluation adjustments, and goals.
- Special education courses should match needs and list weekly periods and dates.
- Related services should include provider and frequency when applicable.
- Assessment adjustment should cover assessment method, ratio, responsible evaluators, test services, and assignment adjustments when needed.
- Academic-year and semester goals should be concrete, observable, and measurable.
- Related tests should support the disability category and educational needs.
- For grade 6, grade 9, and grade 12 students, transfer planning should include next placement, service needs, concrete transition actions, and participating people or units.

## Opinion Tone

These opinions are read by the teacher who wrote the IEP. Every finding — not just the numbered correction list — should read as a respectful request for confirmation, not a declaration that the teacher made an error. This applies even to findings the reviewer is fairly confident about, because the reviewer is working from partial/second-hand data (screenshots, exported text) and the teacher has full context.

- Prefer: `請補明評量方式及觀察期間，使 115 學年度第 1 學期目標可具體評量。`
- Avoid: `內容不夠完整。`
- Prefer: `提請確認情緒行為障礙與 ICD F84.8 診斷分類是否一致或為共病。`
- Avoid: `特教類別與 ICD 診斷不一致，請修正。`
- Prefer: `能力現況記載已領有身心障礙證明，惟身心障礙證明欄位顯示「無」，提請個管教師確認並更新。`
- Avoid: `身心障礙證明欄位錯誤。`

Separate:

- **主要補正意見**: must be fixed before approval.
- **可保留內容**: content that is already adequate — include this section every time, not only when issues exist; acknowledging what a case manager did well is as much a part of the review as flagging gaps.
- **提醒事項**: useful but not blocking.
