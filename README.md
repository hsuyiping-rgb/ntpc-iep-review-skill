# ntpc-iep-review skill

Codex skill for reviewing New Taipei City SEE system IEP records and producing concise correction opinions.

## Contents

- `SKILL.md`: workflow and usage instructions
- `references/review-rules.md`: review rules and decision criteria
- `scripts/create_review_docx.py`: helper for generating Word review documents
- `agents/openai.yaml`: skill metadata

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
