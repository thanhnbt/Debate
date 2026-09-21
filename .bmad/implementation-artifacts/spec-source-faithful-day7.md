---
title: 'Source-faithful Day 7 ESL debate lesson'
type: 'refactor'
created: '2026-09-21'
status: 'in-review'
review_loop_iteration: 0
baseline_commit: '09d5fc67125bf3a5bb26280840ae35e7248227df'
context:
  - 'CLAUDE.md'
  - '.claude/commands/genDebateESL.md'
---

<frozen-after-approval reason="human-owned intent — do not modify unless human renegotiates">

## Intent

**Problem:** The generated Day 7 lesson contains recap material, metaphors, homework hints, and quiz distractors not taught in the 15 September source. It also suppresses useful teacher feedback and presents partial sample speeches as complete case setups.

**Approach:** Rebuild the lesson from a two-way provenance audit of `15092026.docx`, retain only source-backed teaching claims plus translation, vocabulary support, and neutral UI, and harden the generator so later lessons follow the same source-first standard.

## Boundaries & Constraints

**Always:** Use `datainput/buoi 7 15092026/15092026.docx` as the sole curricular authority. Preserve the two motions, THR/THP rules, team roles, four case-setup parts, anonymized class practice, teacher instructional feedback, sample-speech fragments, homework intent, vocabulary, and source-backed quiz concepts. Keep English suitable for a Vietnamese ESL learner aged nine and provide concise Vietnamese support. Preserve the static single-file lesson architecture and existing visual system.

**Ask First:** Any proposal to add a new debate rule, model answer, factual claim, motion type, homework clue, or teaching example not traceable to the source.

**Never:** Include student names, Day 6 Actor Motion recap, AI-created metaphors, Detective Step, password-gated homework answers, concrete answer-leading homework hints, fabricated missing sections, or unsupported motion categories used as quiz distractors.

## I/O & Edge-Case Matrix

| Scenario | Input / State | Expected Output / Behavior | Error Handling |
|----------|--------------|---------------------------|----------------|
| Source-backed lesson | Source contains a teaching claim | Claim appears once in the appropriate lesson or practice section | Flag as missing during provenance QA |
| Partial sample | Source sample omits a setup component | Render only supplied components and label it a sample excerpt | Never fill the missing component |
| Homework | Student must complete the unpractised motion | Show both motions and one neutral four-step worksheet | Remove prompts that disclose model-speech ideas |
| Supplemental vocabulary | Word is absent from the original vocabulary list but present in lesson prose | It may appear with a simple definition and translation | Exclude any word not traceable to source prose |

</frozen-after-approval>

## Code Map

- `CLAUDE.md` -- project-level source-fidelity, homework, and QA invariants.
- `.claude/commands/genDebateESL.md` -- generator workflow and fidelity/QA rules.
- `lessons/day7.html` -- Day 7 single-file lesson, interactions, vocabulary, and quiz.
- `index.html` -- landing-page card for Day 7.
- `datainput/buoi 7 15092026/15092026.docx` -- authoritative lesson source; read only.

## Tasks & Acceptance

**Execution:**
- [x] `CLAUDE.md` -- align project-level rules with the approved source-first policy so generator rules are not contradicted.
- [x] `.claude/commands/genDebateESL.md` -- replace additive mandatory content rules with source-first provenance, feedback handling, homework, quiz, and two-way QA rules.
- [x] `lessons/day7.html` -- rebuild as Lesson, Class Practice, Homework, Vocabulary, and Quiz; remove unsupported and answer-leading material.
- [x] `index.html` -- keep the Day 7 card accurate to the rebuilt lesson.
- [x] Run structural and provenance checks; browser interaction and responsive visual checks were attempted but unavailable because no browser instance was connected.

**Acceptance Criteria:**
- Given the source document, when lesson claims are audited in both directions, then every curricular claim maps to source and all source teaching sections are represented.
- Given either sample speech is incomplete, when displayed, then no missing component is invented or implied to exist.
- Given the homework tab, when a child opens it, then it asks for the remaining motion using four neutral source-derived prompts and reveals no sample answer.
- Given the lesson is loaded, when tabs, vocabulary search/audio controls, and quiz controls are used, then existing static-site behavior remains functional.
- Given a mobile viewport, when the lesson is viewed, then navigation and content remain readable without horizontal page overflow.

## Spec Change Log

## Design Notes

Use one shared homework worksheet after motion selection instead of duplicating four guided sections per motion. Keep student drafts only as anonymized class examples, and label teacher comments explicitly so claims from a debate speech are not presented as neutral real-world facts.

## Verification

**Commands:**
- `rg` source-provenance checks -- expected: forbidden sections and phrases absent, required source concepts present.
- HTML/JavaScript structural check -- expected: balanced sections, valid script syntax, expected tab targets and quiz totals.
- Browser review at desktop and mobile widths -- expected: no console errors, clipped content, broken navigation, or unusable controls.
