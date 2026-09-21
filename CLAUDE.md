# English Debate Club — Project Instructions

## Project Overview
Bài học tranh biện tiếng Anh (ESL) cho trẻ Việt Nam 8-9 tuổi. Pure static HTML, deploy GitHub Pages.

## Skill chính
- `/genDebateESL` — Generate bài học debate hoàn chỉnh (`.claude/commands/genDebateESL.md`)

## Quy tắc quan trọng (KHÔNG ĐƯỢC VI PHẠM)

### Nội dung phải bám sát source data
- Mọi nội dung lấy từ đúng một thư mục khớp `datainput/buoi N*/` (có thể có hậu tố ngày), KHÔNG tự bịa concept/rule
- Chỉ được bổ sung bản dịch, giải nghĩa vocabulary có trong source, nhãn UI và hướng dẫn thao tác trung lập
- KHÔNG tự thêm metaphor, ví dụ, motion type, model answer, factual claim hoặc teaching framework nếu source không có
- Feedback cá nhân: bỏ tên/đánh giá học sinh, nhưng giữ feedback của thầy nếu nó dạy một điểm kiến thức có thể tái sử dụng

### Sub-steps phải đầy đủ cho TẤT CẢ mục
- Chỉ liệt kê sub-steps thật sự xuất hiện trong source; không tự điền phần còn thiếu
- Luôn đối chiếu source data cho TỪNG mục, TỪNG loại motion
- Các sub-steps thường gặp:
  - Context: `1a. Identify problems`, `1b. Consequences`
  - Definition: `2a. Define keywords`, `2b. Characterisation`
  - Policy/Model (THW): `4a. What would you do?`, `4b. Punishment (Optional)`
  - Declaration of Interests (Actor): `Point out and explain the main interests of X`

### Homework: trẻ phải tự tư duy
- Giữ nguyên yêu cầu, motion choice và cấu trúc bài tập trong source
- Không tự thêm Detective Step, password gate hoặc hints nếu source không yêu cầu
- Không lấy ý từ class draft/sample speech làm gợi ý, vì như vậy sẽ lộ hướng trả lời
- Có thể highlight từ khóa trong motion nếu việc highlight không thêm claim mới

### QA bắt buộc trước khi hoàn thành
- Phase 8 trong skill: rà soát HTML vs source data theo cả hai chiều
- Source → HTML: không bỏ sót vocabulary, concept, rule, example, teacher feedback, sample excerpt hoặc homework
- HTML → Source: mọi curricular claim phải truy được về source; ngoại lệ chỉ gồm dịch thuật, vocabulary definition và UI trung lập
- Phải cover TẤT CẢ sections/motion types, không assume cái nào đúng
- Phát hiện thiếu → fix ngay, không báo rồi để đó

## Cấu trúc project
```
index.html              — Landing page (session grid)
config.js               — MW API key config
lessons/dayN.html       — Bài học (single-file SPA)
datainput/buoi N*/      — Source material (có thể có hậu tố ngày; docx/pptx)
assets/slides/buoiN/    — Slide images (nếu có pptx)
.claude/commands/       — Skills (genDebateESL.md)
```
