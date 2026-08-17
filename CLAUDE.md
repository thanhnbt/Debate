# English Debate Club — Project Instructions

## Project Overview
Bài học tranh biện tiếng Anh (ESL) cho trẻ Việt Nam 8-9 tuổi. Pure static HTML, deploy GitHub Pages.

## Skill chính
- `/genDebateESL` — Generate bài học debate hoàn chỉnh (`.claude/commands/genDebateESL.md`)

## Quy tắc quan trọng (KHÔNG ĐƯỢC VI PHẠM)

### Nội dung phải bám sát source data
- Mọi nội dung lấy từ `datainput/buoi N/` (docx/pptx), KHÔNG tự bịa concept/rule
- Bổ sung vocabulary, metaphor, ví dụ minh họa là OK — nhưng không bịa kiến thức mới

### Sub-steps phải đầy đủ cho TẤT CẢ mục
- Khi liệt kê case building steps, PHẢI ghi đầy đủ sub-steps cho TẤT CẢ motion types, không chỉ 1 loại
- Luôn đối chiếu source data cho TỪNG mục, TỪNG loại motion
- Các sub-steps thường gặp:
  - Context: `1a. Identify problems`, `1b. Consequences`
  - Definition: `2a. Define keywords`, `2b. Characterisation`
  - Policy/Model (THW): `4a. What would you do?`, `4b. Punishment (Optional)`
  - Declaration of Interests (Actor): `Point out and explain the main interests of X`

### Homework: trẻ phải tự tư duy
- Hints chỉ gợi ý chung chung, KHÔNG cho sẵn đáp án cụ thể
- Hints ẩn sau nút "🔑 Gợi ý" + password `000000` (dành cho phụ huynh nhập)
- Highlight từ khóa trong motion để trẻ tự nhận diện loại kiến nghị

### QA bắt buộc trước khi hoàn thành
- Phase 7 trong skill: rà soát HTML vs source data line-by-line
- Phải cover TẤT CẢ sections/motion types, không assume cái nào đúng
- Phát hiện thiếu → fix ngay, không báo rồi để đó

## Cấu trúc project
```
index.html              — Landing page (session grid)
config.js               — MW API key config
lessons/dayN.html       — Bài học (single-file SPA)
datainput/buoi N/       — Source material (docx/pptx)
assets/slides/buoiN/    — Slide images (nếu có pptx)
.claude/commands/       — Skills (genDebateESL.md)
```
