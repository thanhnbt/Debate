# /genDebateESL — Generate Debate Lesson for ESL Children (8-9 years old)

Khi user gọi skill này, thực hiện workflow generate bài học debate hoàn chỉnh cho trẻ 8-9 tuổi (Vietnamese native, English L2).

---

## INPUT YÊU CẦU

Hỏi user (nếu chưa cung cấp):
1. **Số buổi (N)** — ví dụ: 3
2. **Source material** — confirm file có trong `datainput/buoi N/` (pptx hoặc docx)
3. **Chủ đề** — hoặc tự extract từ source material

---

## WORKFLOW (7 PHASES)

### Phase 1: Extract Source Material

1. Đọc tất cả files trong `datainput/buoi N/`
2. Nếu có `.pptx`:
   - Dùng python với `python-pptx` để extract text content
   - Extract slide images: `python -X utf8 -c "from pptx import Presentation; ..."` → save to `assets/slides/buoiN/source-slide-XX.png`
3. Nếu có `.docx`:
   - Dùng python với `python-docx`: `python -X utf8 -c "from docx import Document; ..."`
4. **BỎ QUA**: phần nhận xét/đánh giá học sinh, feedback cá nhân — chỉ lấy nội dung bài học
5. Tóm tắt nội dung đã extract thành outline cho user review trước khi generate

### Phase 2: Design Lesson Structure

Xác định tabs/pages dựa trên nội dung. Mỗi lesson **BẮT BUỘC** có:
- **Vocabulary** tab
- **Quiz** tab
- **Summary** tab (nếu đủ nội dung, 3-5 sections)

Tùy chọn thêm (dựa trên source):
- **Slides** tab (nếu có pptx/images)
- **Topic-specific tabs** (ví dụ: "WSDC", "Speaker Roles", "History"...)
- **Review** tab (ôn bài buổi trước)

### Phase 3: Generate HTML — Single-File SPA

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="description" content="Day N: [Topic]">
  <meta name="theme-color" content="#55c8ff">
  <title>Day N · [Topic]</title>
  <style>
    /* TOÀN BỘ CSS inline — copy design system từ lessons/day1.html hoặc day2.html */
  </style>
</head>
<body>

<header class="topbar">
  <div class="topbar-inner">
    <a href="../index.html" class="brand">
      <span class="brand-mark">D{N}</span>
      <span>English Debate Club</span>
    </a>
    <nav class="nav" id="mainNav">
      <a href="../index.html" class="home-link">← Trang chủ</a>
      <button class="active" data-page="[first-page]">[Label]</button>
      <button data-page="[page2]">[Label]</button>
      <!-- ... more tabs ... -->
    </nav>
  </div>
</header>

<main class="wrap">
  <section class="page" id="[first-page]">...</section>
  <section class="page" id="[page2]" hidden>...</section>
  <!-- ... -->
</main>

<footer class="footer">
  <a href="../index.html">← Về trang chủ</a> &nbsp;|&nbsp; English Debate Club &copy; 2025
</footer>

<script src="../config.js"></script>
<script>
  // Tab navigation
  // Vocabulary rendering + MW Audio
  // Quiz form
  // Slides viewer (if applicable)
</script>
</body>
</html>
```

### Phase 4: Generate Vocabulary (BẮT BUỘC)

**Data format** — mảng 6 phần tử:
```javascript
const vocabulary = [
  ["word", "/American IPA/", "SOUND-IT-OUT", "type", "English meaning", "Vietnamese meaning"],
  // 15-25 entries
];
```

**Rules KHÔNG ĐƯỢC VI PHẠM:**
- 15-25 từ liên quan TRỰC TIẾP đến bài học
- IPA: American English notation (ví dụ: `/dɪˈbeɪt/`)
- SOUND IT OUT: viết HOA, ngắt âm bằng gạch ngang (ví dụ: `dih-BAYT`)
- Type: `noun`, `verb`, `adjective`, `phrase`, `noun / verb`
- English meaning: 1 câu ĐƠN GIẢN, A2/B1 level, trẻ 8-9 tuổi hiểu được
- Vietnamese meaning: dịch tự nhiên + giải thích context nếu cần

**UI Components BẮT BUỘC:**
- Search input: `<input class="vocab-search" type="search">`
- Filter buttons: All / Nouns / Verbs / Adjectives / Phrases
- Table: Word | IPA | SOUND IT OUT | Type | Meaning (EN+VI) | Audio button
- Audio: Merriam-Webster API qua `config.js` (endpoint `sd3`)

**JS Audio pattern (COPY CHÍNH XÁC):**
```javascript
const mwApiKey = window.MW_CONFIG?.MW_API_KEY?.trim() || "";
const mwReference = window.MW_CONFIG?.REFERENCE || "sd3";
const hasMwApiKey = Boolean(mwApiKey && !mwApiKey.includes("PASTE_YOUR"));

function findAudioFile(value) {
  if (!value || typeof value !== "object") return "";
  if (typeof value.audio === "string" && value.audio) return value.audio;
  for (const child of Object.values(value)) {
    const found = findAudioFile(child);
    if (found) return found;
  }
  return "";
}

function audioSubdirectory(filename) {
  if (filename.startsWith("bix")) return "bix";
  if (filename.startsWith("gg")) return "gg";
  if (/^[^a-zA-Z]/.test(filename)) return "number";
  return filename[0].toLowerCase();
}

async function getMerriamWebsterAudioUrl(word) {
  if (audioCache.has(word)) return audioCache.get(word);
  const endpoint = `https://www.dictionaryapi.com/api/v3/references/${encodeURIComponent(mwReference)}/json/${encodeURIComponent(word)}?key=${encodeURIComponent(mwApiKey)}`;
  const response = await fetch(endpoint);
  if (!response.ok) throw new Error(`Dictionary request failed (${response.status}).`);
  const entries = await response.json();
  const filename = findAudioFile(entries);
  if (!filename) throw new Error("No recording found.");
  const folder = audioSubdirectory(filename);
  const url = `https://media.merriam-webster.com/audio/prons/en/us/mp3/${folder}/${filename}.mp3`;
  audioCache.set(word, url);
  return url;
}
```

### Phase 5: Generate Quiz (BẮT BUỘC)

**Format:**
```javascript
const quizQuestions = [
  // 10 concept questions
  { type: "choice", kind: "Concept", prompt: "Question?",
    options: ["A", "B", "C", "D"], answer: 0 },
  // 8 word puzzles
  { type: "word", kind: "Word puzzle",
    prompt: "Use the picture and first letter to complete the word.",
    word: "debate", emoji: "🎤", imageLabel: "Microphone for debate" }
];
```

**Rules KHÔNG ĐƯỢC VI PHẠM:**
- Tổng 18 câu: 10 choice + 8 word puzzles
- Choice: 4 options, chỉ 1 đáp án đúng, câu hỏi VỀ NỘI DUNG BÀI HỌC
- Word puzzles: chọn 8 từ quan trọng nhất từ vocabulary, có emoji liên quan
- KHÔNG reveal đáp án khi trả lời sai — chỉ nói "Review this one"
- Submit ALL trước khi grade — không grade từng câu
- Progress counter: "X of 18 answered"
- Audio hint checkbox cho word puzzles
- Reset button clear mọi thứ

**Quiz grading logic:**
```javascript
quizForm.addEventListener("submit", event => {
  event.preventDefault();
  // Check all answered
  // Grade: choice → check radio value vs answer index
  //        word → check first letter + typed letters === full word
  // Show score, headline based on percentage
  // Mark cards .correct/.incorrect
  // NEVER show correct answer
});
```

### Phase 6: Generate Summary (BẮT BUỘC)

**Structure: 3-5 sections**
```html
<section class="page" id="summary" hidden>
  <div class="page-header">
    <span class="eyebrow">Day N summary / Tóm tắt buổi N</span>
    <h2>[Engaging Title]</h2>
    <p>[Vietnamese subtitle + instruction]</p>
  </div>

  <div class="summary-section">
    <span class="section-label">EMOJI Part 1 — English Title / Tiếng Việt</span>
    <!-- Content: .info-card, .summary-grid, .takeaway, .highlight-box, .two-teams -->
  </div>
  <!-- More sections... -->
</section>
```

**Rules KHÔNG ĐƯỢC VI PHẠM:**
1. **SONG NGỮ**: Mọi concept → English chính + Vietnamese dưới
   - Tiêu đề: `<h3>English Title</h3>`
   - Hint: `<span class="vi-hint">Vietnamese subtitle</span>`
   - Nội dung: `<p>English explanation</p>`
   - Dịch: `<p class="vi">Vietnamese translation</p>`

2. **ĐƠN GIẢN**: A2/B1 English, câu ngắn (max 15 từ/câu nếu có thể)

3. **CỤ THỂ**: MỖI concept PHẢI có ít nhất 1 metaphor/ví dụ trẻ 8-9 tuổi hiểu:
   - Đội bóng đá (teamwork)
   - Nấu ăn (content/ingredients)
   - Hát bài hát (style/delivery)
   - Chơi cờ (strategy)
   - Vẽ bản đồ (planning)
   - Thám tử (critical thinking/investigation)
   - Xây nhà (building arguments)
   - Siêu anh hùng (defending/protecting)
   - Bình luận viên (summarizing/comparing)

4. **HIGHLIGHT BOXES** cho rules quan trọng:
   ```html
   <div class="highlight-box">
     <p>IMPORTANT RULE in English</p>
     <p class="vi">QUY TẮC QUAN TRỌNG bằng tiếng Việt</p>
   </div>
   ```

5. **COMPONENTS** sử dụng:
   - `.info-card` — thông tin chi tiết có heading + list
   - `.highlight-box` — callout quan trọng
   - `.two-teams` + `.team-card.prop/.opp` — so sánh 2 phía
   - `.summary-grid` + `.takeaway` — grid cards tóm tắt
   - `.takeaway-icon` — emoji hoặc text icon trong card

### Phase 6b: Generate Homework (BẮT BUỘC nếu source có BTVN)

**Rules KHÔNG ĐƯỢC VI PHẠM:**

1. **Highlight từ khóa** trong motion để học sinh tự nhận diện loại kiến nghị:
   ```html
   <mark style="background:rgba(255,112,133,.25); padding:2px 6px; border-radius:6px; font-weight:900;">as parents</mark>
   ```

2. **Detective Step** — Hướng dẫn trẻ tự tư duy xác định loại motion:
   - Hiển thị motion với keyword được highlight
   - Đặt câu hỏi "What type is this motion?" để trẻ tự tìm manh mối
   - Giải thích tại sao (ví dụ: "as X" → Actor Motion)

3. **Case Building Steps** — Liệt kê các bước, mỗi bước có:
   - Instruction chung chung (luôn hiển thị) — KHÔNG gợi ý đáp án cụ thể
   - Nút `🔑 Gợi ý` có password gate (mật khẩu: `000000`, dành cho phụ huynh)
   - Hint ẩn chỉ hiện khi nhập đúng mật khẩu — chỉ gợi ý chung, KHÔNG cho đáp án

4. **Sub-steps phải đầy đủ** theo source data:
   - Context: `1a. Identify problems`, `1b. Consequences`
   - Definition: `2a. Define keywords`, `2b. Characterisation`
   - Policy/Model (THW): `4a. What would you do?`, `4b. Punishment (Optional)`
   - Declaration of Interests (Actor): `Point out and explain the main interests of X`

5. **Password gate JS pattern:**
   ```javascript
   document.querySelectorAll(".hint-btn").forEach(btn => {
     btn.addEventListener("click", () => {
       const hintId = btn.dataset.hint;
       const hintDiv = document.getElementById(hintId);
       const errorP = btn.parentElement.querySelector(".hint-error");
       if (!hintDiv || btn.classList.contains("unlocked")) return;
       const pass = prompt("Nhập mật khẩu (dành cho phụ huynh):");
       if (pass === null) return;
       if (pass === "000000") {
         hintDiv.hidden = false;
         btn.textContent = "✓ Đã mở";
         btn.classList.add("unlocked");
         errorP.hidden = true;
       } else {
         errorP.textContent = "Sai mật khẩu. Hãy nhờ ba mẹ nhập giúp nhé!";
         errorP.hidden = false;
       }
     });
   });
   ```

6. **CSS cho hint system:**
   ```css
   .hint-btn {
     margin-top: 12px; padding: 8px 18px;
     border: 2px solid var(--coral); border-radius: 12px;
     background: white; color: var(--coral-dark);
     font-weight: 800; font-size: .82rem;
   }
   .hint-btn.unlocked { border-color: var(--teal); color: var(--teal-dark); pointer-events: none; }
   .hint-content { margin-top: 12px; }
   .hint-error { color: var(--coral); font-size: .8rem; font-weight: 700; margin-top: 6px; }
   ```

### Phase 7: QA — Rà soát nội dung HTML vs Source Data (BẮT BUỘC)

**Mục đích:** Đảm bảo KHÔNG thiếu sót, KHÔNG sai lệch nội dung so với tài liệu đầu vào.

**Quy trình:**

1. **Re-extract source data** — Đọc lại toàn bộ `datainput/buoi N/data.docx` (hoặc `.pptx`)
2. **So sánh từng mục** — Đối chiếu line-by-line giữa source và HTML đã generate:

   | Hạng mục | Kiểm tra |
   |----------|----------|
   | **Vocabulary** | Mọi từ trong source đều có trong `const vocabulary`? Nghĩa VI khớp? |
   | **Nội dung bài học** | Mọi concept/rule/step trong source đều xuất hiện trong HTML? |
   | **Sub-steps** | Các bước con (1a/1b, 2a/2b, 4a/4b…) đầy đủ cho TẤT CẢ mục, không bỏ sót loại nào? |
   | **Ví dụ** | Mọi example/motion mẫu trong source đều được liệt kê? |
   | **Homework/BTVN** | Đề bài khớp chính xác? Hướng dẫn đúng loại motion? |
   | **Ghi chú đặc biệt** | Các note/warning trong source (ví dụ: special cases cho actor) đều có? |

3. **Liệt kê kết quả QA** — Output checklist ngắn gọn cho user review:
   ```
   QA RESULT:
   ✅ Vocabulary: 10/10 từ từ source + 8 từ bổ sung liên quan
   ✅ Motion types: 3/3 loại đầy đủ (THW, THBT/THS/THO, TH as X)
   ✅ Case building sub-steps: đầy đủ cho cả 3 loại
   ✅ Examples: 5/5 motion mẫu từ source
   ✅ Homework: đề bài khớp, đúng loại Actor Motion
   ✅ Special notes: community care cho parents/leaders
   ⚠️ [Nếu có thiếu sót — ghi rõ cái gì thiếu và fix ngay]
   ```

4. **Fix ngay** nếu phát hiện thiếu — KHÔNG báo xong rồi để đó

**Rules KHÔNG ĐƯỢC VI PHẠM:**
- PHẢI chạy QA TRƯỚC khi báo hoàn thành cho user
- Nếu source có mà HTML không có → đó là bug, phải fix
- Nếu HTML có mà source không có → OK nếu là bổ sung hợp lý (vocab mở rộng, metaphor…), nhưng KHÔNG được bịa concept/rule mới
- QA phải cover TẤT CẢ motion types / sections, không chỉ kiểm tra 1 loại rồi assume các loại khác đúng

### Phase 8: Update Landing Page

Trong `index.html`:
1. Tìm session card number N (đang `class="session-card coming-soon"`)
2. Đổi thành: `<a href="lessons/dayN.html" class="session-card active">`
3. Update title và description nếu cần
4. Đổi badge: `<span class="badge">Đã hoàn thành</span>`
5. Nếu chưa có card cho session N → thêm mới

---

## DESIGN RULES TỔNG QUÁT (KHÔNG BAO GIỜ VI PHẠM)

| # | Rule | Lý do |
|---|------|-------|
| 1 | Target: trẻ 8-9 tuổi, Vietnamese native, English L2 | Mọi quyết định content phải filter qua lens này |
| 2 | Ngôn ngữ A2/B1, câu ngắn, từ đơn giản | Trẻ không đọc được câu phức tạp |
| 3 | SONG NGỮ bắt buộc cho MỌI concept | Đảm bảo trẻ hiểu 100% |
| 4 | Không jargon chưa giải thích | Từ debate phải được define ngay lần đầu dùng |
| 5 | Concrete > Abstract: LUÔN có ví dụ/metaphor | Trẻ học qua hình ảnh cụ thể |
| 6 | Visual: emoji + color cards + highlight | Giữ attention của trẻ |
| 7 | Interactive: quiz engaging, word puzzles | Gamification tăng retention |
| 8 | CSS consistent: dùng design system variables | Thống nhất visual language |
| 9 | MW API audio: `sd3` endpoint qua config.js | Pronunciation model chuẩn |
| 10 | Mobile responsive: collapse ở 768px | Trẻ có thể dùng tablet |
| 11 | No build tools: pure static HTML | Deploy GitHub Pages trực tiếp |
| 12 | Folder: `lessons/dayN.html`, `assets/slides/buoiN/` | Convention đã set |

---

## CSS DESIGN SYSTEM VARIABLES

```css
:root {
  --ink: #26335d;       /* Primary text */
  --ink-2: #40517f;     /* Secondary text */
  --paper: #fffaf0;     /* Background */
  --card: #ffffff;      /* Card bg */
  --muted: #65739a;     /* De-emphasized */
  --line: #dfe8f6;      /* Borders */
  --teal: #4ed6bd;      /* Accent 1 */
  --teal-dark: #138f86; /* Dark teal text */
  --orange: #ff9b62;    /* Accent */
  --yellow: #ffd84d;    /* Highlight */
  --sky: #55c8ff;       /* Primary action */
  --coral: #ff7085;     /* Error/Opposition */
  --purple: #8c7cff;    /* Accent */
  --mint: #a9efcb;      /* Accent */
  --shadow: 0 18px 48px rgba(78,103,159,.16);
  --radius: 28px;
}
```

---

## CHECKLIST TRƯỚC KHI HOÀN THÀNH

- [ ] File `lessons/dayN.html` tạo xong, SPA navigation hoạt động
- [ ] Vocabulary: 15-25 entries, IPA + SOUND IT OUT + bilingual meanings
- [ ] Quiz: 10 choice + 8 word puzzles, form-based, no answer reveal
- [ ] Summary: 3-5 sections, song ngữ, có metaphors cụ thể
- [ ] Audio: MW API integration đúng pattern (sd3, findAudioFile recursive)
- [ ] Landing page `index.html` updated (card activated)
- [ ] Mobile responsive tested (768px breakpoint)
- [ ] Slides (nếu có pptx): images extracted to `assets/slides/buoiN/`
- [ ] Homework (nếu có): keyword highlight, detective step, password-gated hints (000000)
- [ ] Sub-steps đầy đủ: 1a/1b (Context), 2a/2b (Definition), 4a/4b (Policy nếu THW)
- [ ] Homework hints chỉ gợi ý chung, KHÔNG cho đáp án cụ thể — trẻ phải tự tư duy
- [ ] Không có từ/câu nào vượt quá trình độ A2/B1
- [ ] Mọi thuật ngữ debate đều có Vietnamese translation
