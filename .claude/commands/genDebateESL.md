# /genDebateESL — Generate a source-faithful ESL debate lesson

Use this workflow when creating a debate lesson for Vietnamese ESL learners aged 8–9.

## Core rule: source first

The files in `datainput/buoi N/` are the sole authority for curricular content.

Every item in the lesson must be one of these:

1. A teaching claim stated in the source.
2. A simpler paraphrase that keeps the same meaning.
3. A Vietnamese translation of source-backed content.
4. Vocabulary or neutral UI support that is traceable to source words or source prose.

Do not add debate rules, motion types, examples, metaphors, model answers, factual claims, homework clues, or teaching frameworks that are absent from the source. If useful new content is genuinely needed, ask the user first.

## Required input

Confirm:

1. Session number N.
2. Source files in `datainput/buoi N/`.
3. Output file `lessons/dayN.html`.

## Workflow

### Phase 1 — Extract the complete source

Read every relevant file, including all paragraphs, tables, text boxes, speaker notes, and slide text.

For DOCX, extract both paragraphs and tables. For PPTX, extract slide text and only create slide images when the lesson needs them.

Build a source outline with stable references such as paragraph, table, row, or slide number. Include:

- Original vocabulary.
- Lesson concepts and rules.
- Motion examples.
- Case-setup parts and sub-parts exactly as supplied.
- Class drafts or exercises.
- Teacher instructional feedback.
- Sample speeches.
- Homework wording.

Student handling:

- Remove all student names and personal assessment.
- Keep teacher comments that teach a reusable lesson point.
- Label anonymized work as “Class draft” or “Class example”.
- Label comments as “Teacher’s feedback”.
- Never present a student or sample speech claim as a neutral fact.

Show the source outline to the user before generation when they requested a review step.

### Phase 2 — Create a provenance ledger

Before writing HTML, map each planned section to the exact source location.

Use two lists:

- Source → lesson: every source teaching section that must appear.
- Lesson → source: every planned curricular claim and its source.

Allowed additions do not need a curricular source only when they are translations, short accessibility labels, interaction instructions, or vocabulary definitions for words found in the source.

### Phase 3 — Design only the sections the source supports

Choose tabs from the source. Vocabulary and Quiz may be included as study tools. Homework is included only when the source assigns it.

Do not require a Review or Summary tab. Do not import previous-session content unless the current source explicitly reviews it.

Typical source-driven structure:

- Lesson.
- Class Practice.
- Homework, if assigned.
- Vocabulary.
- Quiz.

### Phase 4 — Build the static lesson

Create a pure static, single-file SPA in `lessons/dayN.html`. Preserve the established design system, tab navigation, responsive layout, and working interaction patterns from existing lessons.

Use short A2/B1 English and concise Vietnamese support. Simplify wording without changing the teacher’s meaning.

For partial source samples:

- Render only the components actually supplied.
- Call them “Sample speech excerpt”, not a complete case setup.
- State which components are present.
- Never invent a missing component.

### Phase 5 — Vocabulary

The source vocabulary list must be complete.

Supplemental vocabulary is optional and may be added only when the word appears in source prose, class drafts, teacher feedback, homework, or sample speeches. Do not force a fixed word count.

For every entry include:

- Word.
- American IPA.
- SOUND-IT-OUT.
- Part of speech.
- Simple English meaning.
- Natural Vietnamese meaning.

Keep the existing vocabulary search and Merriam-Webster audio integration through `config.js`. Filters are optional unless already present in the chosen lesson template.

### Phase 6 — Homework

Follow the source assignment exactly.

- Preserve the required motion choice and required case-setup structure.
- A neutral worksheet may restate source questions.
- Do not add a Detective Step.
- Do not add password-gated hints.
- Do not reuse sample-speech ideas as clues.
- Do not define extra motion words or ask extra leading questions unless the source does.
- Do not provide model answers.
- Do not fabricate sub-steps missing from the source.

When two motions are offered and the learner must complete the unpractised one, show both motions once and use one shared worksheet.

### Phase 7 — Quiz

Every question, correct answer, and meaningful distractor must use concepts or vocabulary found in the source-backed lesson.

- Do not use untaught motion categories as distractors.
- Do not force a fixed number of questions.
- Prefer a shorter faithful quiz to filler.
- Word puzzles may use source or source-prose vocabulary.
- Grade only after submission.
- Do not reveal correct answers after a wrong response.
- Keep progress and reset controls.

### Phase 8 — Two-way QA

Re-extract the source and audit both directions.

Source → HTML:

- All original vocabulary is present.
- All concepts, rules, examples, setup parts, teacher instructional feedback, sample excerpts, and homework requirements are represented.
- Student names and personal evaluation are absent.
- Missing sample sections have not been filled in.

HTML → Source:

- Every curricular claim maps to a source location.
- Supplemental vocabulary appears in source prose.
- No imported recap, metaphor, model answer, homework hint, or motion category lacks a source.
- Translations do not add new claims.

Technical checks:

- Every nav target has one matching section.
- The first tab is visible and active.
- Vocabulary search and audio controls initialise safely.
- Quiz totals are derived from the question array.
- Submit, feedback, and reset controls work.
- No console errors.
- At desktop and mobile widths, content is readable and the page has no horizontal overflow.

Fix every discovered issue before reporting completion.

## HTML baseline

Keep the project conventions:

- `lessons/dayN.html` is self-contained except for `../config.js`.
- Use the existing CSS variables and components.
- Keep semantic headings, labels, and accessible button text.
- Preserve the recursive Merriam-Webster audio lookup and the `sd3` reference used by existing lessons.
- Update `index.html` only so the session card title and description accurately match the finished lesson.

## Completion report

Report:

- Files changed.
- Source coverage checklist.
- Removed unsupported content.
- Structural and interaction checks run.
- Any browser or manual verification still needed.
